# Núcleo

Sistema compartido de estructura, usabilidad y comportamiento para las apps de `cdesolmi`.

> **El núcleo dice cómo se comporta algo. Cada app dice qué hay dentro y de qué color.**

No es un manual de marca y no es una librería de componentes visuales. Es el conjunto de
decisiones que ya se pagaron caro una vez y no hay que volver a descubrir: la escalera de
anchos, el área de toque, el comportamiento de una fila, qué hace una app mientras carga.

---

## Conectar un proyecto

Una línea en el `<head>`, **antes** de los estilos propios de la app:

```html
<link rel="stylesheet" href="https://cdesolmi.github.io/nucleo/v0.1.0/nucleo.css">
<link rel="stylesheet" href="app.css">
```

El orden importa: el núcleo pone los valores por defecto y la app los sobreescribe.

**La dirección lleva la versión adentro y su contenido nunca cambia.** Cuando el núcleo
mejora se publica `v0.2.0` en una dirección nueva, y `v0.1.0` sigue ahí intacta. Por eso una
mejora no puede romperte una app mientras no estás mirando.

Ninguna app apunta a la rama principal. Nunca.

### Actualizar una app

1. Leer el `CHANGELOG` de la versión nueva.
2. Cambiar el número en esa línea.
3. Abrir la app y mirarla.
4. Si algo quedó raro, volver al número anterior y avisar.

Cada app se actualiza cuando tú decides. Nunca las cuatro a la vez.

---

## Lo que cada app tiene que definir

El núcleo trae valores neutros de relleno para que un proyecto nuevo arranque viéndose
decente, pero están ahí para ser reemplazados. En el `app.css` de cada app:

```css
:root {
  /* superficie */
  --fondo: ; --panel: ; --fondo-hondo: ; --linea: ; --linea-suave: ;
  /* tinta */
  --tinta: ; --tinta-media: ; --tinta-tenue: ;
  /* la superficie llena, una por pantalla */
  --bloque: ; --bloque-texto: ; --bloque-grafico: ;
  /* acento — reservado para la acción */
  --acento: ; --acento-hondo: ; --acento-tenue: ;
  /* semánticos */
  --logro: ; --logro-tenue: ; --atencion: ; --atencion-tenue: ;
  --alerta: ; --alerta-tenue: ; --pausa: ; --foco: ;
  /* dos familias: una para cifras y títulos, otra para cuerpo e interfaz */
  --fuente-cifra: ; --fuente-ui: ;
}
```

Además define: su símbolo e iconografía, cuántos destinos tiene el menú y cómo se llaman,
la densidad de sus listas, su modelo de datos y todos sus textos.

**Lo que no se sobreescribe sin discusión:** la escalera de anchos, el área de toque mínima
y las reglas de accesibilidad. Son las tres cosas que existen para no volver a equivocarse.

---

## Las directrices

Los nueve documentos de `docs/` son la parte que más rinde, y valen aunque el proyecto no
cargue el CSS. Un proyecto nuevo se conecta al aprendizaje con una instrucción: *«lee el repo
`nucleo` antes de empezar»*.

| | |
|---|---|
| [`00-lectura-del-nucleo.md`](docs/00-lectura-del-nucleo.md) | Qué es, qué entra y qué no, versionado, gobierno |
| [`01-tokens.md`](docs/01-tokens.md) | El bloque de contrato, las escalas, las reglas de color |
| [`02-anchos.md`](docs/02-anchos.md) | La escalera, el shell, navegación, filas, rail, gestos |
| [`03-componentes.md`](docs/03-componentes.md) | Fila, chip, botón, campo, tarjeta, bloque, cifra, hoja |
| [`04-estados.md`](docs/04-estados.md) | Carga, vacío, error, sin conexión, deshacer vs. modal |
| [`05-voz.md`](docs/05-voz.md) | Las siete reglas de la copy y la lista negra |
| [`06-accesibilidad.md`](docs/06-accesibilidad.md) | Los ocho requisitos y cómo se verifica cada uno |
| [`07-datos.md`](docs/07-datos.md) | SQL idempotente, migraciones, borrado suave, eventos, RLS |
| [`08-app-nueva.md`](docs/08-app-nueva.md) | Checklist de montaje y de migración de una app existente |

---

## Cómo se gobierna

- **Nada entra al núcleo sin haber funcionado en dos apps**, o en una con uso real sostenido.
  Sacar algo del núcleo cuesta cinco veces más que meterlo: si dudas, déjalo en la app.
- Todo cambio se escribe primero como una línea del `CHANGELOG` nombrando **qué problema real
  resolvió**. Si no se puede nombrar el problema, el cambio no entra.
- Las apps pueden sobreescribir cualquier token localmente. La sobreescritura **es parte del
  diseño**, no una deuda. Lo que sí es señal: si tres apps sobreescriben el mismo token con el
  mismo valor, ese valor debería ser el del núcleo.
- **Los nombres se definen bien una vez y después casi no se tocan.** Mejorar un valor no
  cuesta nada; renombrar un token cuesta una edición en cada app.

### Versiones

| Cambio | Sube | Trabajo en cada app |
|---|---|---|
| Un valor mejora (`--e3` de 12 a 13) | parche | ninguno, se adopta solo |
| Se agrega un token o un componente | menor | ninguno, opcional usarlo |
| Se renombra o elimina un token | mayor | edición en cada app |
| Cambia un comportamiento (un quiebre, un gesto) | mayor | revisar cada app |

### Publicar una versión

`src/nucleo.css` es donde se edita. Publicar es congelar una copia:

```
cp src/nucleo.css v0.2.0/nucleo.css
```

La carpeta de una versión publicada **no se vuelve a tocar nunca**. Es lo único que hace que
la promesa de «esto no se te va a romper solo» sea verdad.

---

## Estado

`v0.1.0` — extraído de la app de objetivos. **Probado en una sola app**, así que todo aquí es
candidato, no ley. La segunda prueba es el entrenador de ajedrez: tiene cifras, series y
tablas, o sea estresa el núcleo justo donde una app de listas no lo estresa. Lo que sobreviva
a las dos es núcleo de verdad; lo que no, vuelve a su app.
