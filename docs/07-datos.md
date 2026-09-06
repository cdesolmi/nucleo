# Núcleo · 07 — Datos, eventos y migraciones

Todas las apps corren sobre el mismo Supabase y la misma cuenta. Estas reglas no son de esquema —cada
app tiene su dominio— son de **cómo se cambia una base que está en producción con datos reales que no
se pueden perder.**

---

## 1. Toda migración es idempotente

Correr el mismo SQL dos veces no puede fallar ni duplicar nada.

```sql
alter table tareas add column if not exists descartada_at timestamptz;
create index if not exists tareas_descartada_idx on tareas (descartada_at);

do $$ begin
  alter table areas add constraint areas_estado_chk check (estado in ('activo','en_espera'));
exception when duplicate_object then null; end $$;
```

- `if not exists` / `if exists` en todo lo que lo acepte.
- Para lo que no lo acepta (constraints), bloque `do $$` que tolera el duplicado.
- **Ninguna migración se revierte.** Si algo salió mal, la corrección es una migración nueva hacia
  adelante. Un `down` que borra una columna con datos es una pérdida esperando el momento.
- Las migraciones van numeradas y en el repo. La que está en producción y la que está en el archivo
  son la misma, o el sistema no es confiable.

---

## 2. La migración va antes que la UI

**Siempre**, y con una separación de al menos un despliegue:

1. Se sube la migración. La columna existe y está vacía o con su valor por defecto.
2. **Todo código nuevo tolera la columna ausente** (`fila.estado ?? 'activo'`), porque durante unos
   minutos hay clientes viejos corriendo contra el esquema nuevo y clientes nuevos contra caché viejo.
3. Se sube la UI que la usa.

Al revés —UI primero— la app se rompe para quien la tenga abierta, y en una app personal eso es
justamente el momento en que la estabas usando.

---

## 3. Nada se borra de verdad

**Borrado suave por defecto.** Un `timestamptz` con el momento (`descartada_at`, `archivada_at`), no un
booleano: la fecha responde «cuándo» y el booleano no.

- Las consultas filtran por `is null`. Se escribe una vista o un helper para no repetir el filtro en
  veinte lugares y olvidarlo en el veintiuno.
- El borrado duro existe solo donde el usuario lo pide explícitamente y la app le nombró exactamente lo
  que pierde (`04-estados.md` §6).
- **El esfuerzo registrado nunca se borra con el objeto.** Si se descarta una tarea, las sesiones de
  trabajo que se le dedicaron siguen en el histórico. Se dejan de contar en el avance, pero no
  desaparecen: son horas que la persona realmente puso.

---

## 4. Bitácora de eventos

Una tabla de eventos con un `tipo` acotado, desde el primer día. Es lo que permite responder «qué pasó
con esto» sin reconstruir nada.

```sql
create table if not exists eventos (
  id bigserial primary key,
  tipo text not null,
  entidad text not null,
  entidad_id bigint not null,
  usuario_id uuid not null,
  datos jsonb,
  creado_at timestamptz not null default now()
);
create index if not exists eventos_fecha_idx on eventos (usuario_id, creado_at desc);
```

- **El `tipo` crece, no se reinterpreta.** Agregar `descartado`, `convertido`, `movido` es una
  migración de un renglón; cambiar el significado de un tipo existente corrompe el histórico.
- **El histórico no se reescribe.** Si hoy cambia la fórmula de una cifra, los eventos pasados quedan
  como están y la app lo dice cuando corresponda. Una app que recalcula el pasado no tiene pasado.
- `datos` en `jsonb` para el contexto del evento, no para el estado actual del objeto.
- La bitácora es de solo escritura desde la app. Nada la edita.

---

## 5. Cálculos deterministas primero

Todo número que la app muestre tiene que poder calcularse **sin IA y sin servicios externos**, con SQL o
con código propio, y ser reproducible: mismos datos, mismo resultado.

- **Se calcula desde los datos primarios**, no desde una bitácora de conveniencia. En FOST la actividad
  se calcula desde las tablas de tareas y sesiones, no desde un registro de 60 días que habría sido más
  fácil y menos cierto.
- **Cada cifra se puede explicar en una frase** y la app muestra de dónde salió (`03-componentes.md`
  §6).
- Si hay una capa de IA, **es opcional y encima**: reescribe o comenta lo que las reglas ya calcularon,
  nunca lo reemplaza, se guarda con su atribución y su hora, se puede silenciar, y si falla la app
  funciona igual. Ninguna pantalla depende de que una llamada externa responda.

---

## 6. Autenticación y aislamiento

Mismo proyecto Supabase para varias apps: el aislamiento **no es opcional**.

- Row Level Security activa en toda tabla con datos de usuario. Sin excepción, ni «por ahora».
- La política se escribe junto con la tabla, en la misma migración. Una tabla creada hoy sin RLS y «con
  RLS mañana» es una tabla pública hoy.
- Toda tabla de datos de usuario lleva `usuario_id uuid` y la política compara contra `auth.uid()`.
- Prefijos de tabla por app, o esquemas separados, para evitar colisiones de nombres entre apps.

---

## 7. Orden de despliegue de un rediseño

El orden que funcionó en FOST y que conviene repetir:

1. **Tokens y estilos.** Autocontenido, no toca datos, no necesita migración, es lo más barato de
   revertir. Primero.
2. **Migraciones de estado**, con código tolerante a la columna ausente.
3. **Las pantallas**, una por una, cada una subible sola.
4. **Lo que depende de servicios externos**, al final. Es lo único que puede fallar por razones fuera
   de tu control.

Cada tramo se sube solo y con su criterio de «listo cuando». Un tramo que no se puede subir sin otro
está mal cortado.
