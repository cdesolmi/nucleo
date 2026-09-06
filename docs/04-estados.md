# Núcleo · 04 — Carga, vacío, error, deshacer

Los cuatro estados que casi ninguna app tiene al empezar y que definen si se siente sólida o rota. En
FOST no existía ninguno: durante la carga la pantalla quedaba en blanco y en red lenta parecía caída.

---

## 1. Cargando

**Esqueletos con la forma y el alto real del contenido.** Tres, del alto exacto de la fila.

```css
.esq { display:flex; gap:var(--e3); align-items:flex-start; padding:var(--e3) var(--e4);
       background:var(--panel); border:1px solid var(--linea); border-radius:14px; }
.esq + .esq { margin-top:6px; }
.esq .marca { width:22px; height:22px; border-radius:6px; background:var(--linea-suave); flex:none; }
.esq .l1 { height:14px; width:70%; border-radius:4px; background:var(--linea-suave); }
.esq .l2 { height:11px; width:42%; border-radius:4px; background:var(--fondo-hondo); margin-top:var(--e2); }
.esq .marca, .esq .l1, .esq .l2 { animation:latir 1.4s ease-in-out infinite; }
@keyframes latir { 0%,100% { opacity:.55 } 50% { opacity:1 } }
```

Prohibido: spinner centrado, pantalla en blanco, y cualquier cosa que haga saltar el layout cuando
llegan los datos. El esqueleto existe para que **nada se mueva** al llegar el contenido.

---

## 2. Vacío

Tres partes, siempre las tres: **qué va ahí** + **una frase que lo defina** + **el botón que lo crea**.

```css
.vacio { padding:var(--e4); background:var(--panel); border:1px solid var(--linea);
         border-radius:var(--r-tarjeta); }
.vacio .que { font-family:var(--fuente-cifra); font-weight:500; font-size:var(--t-titulo); }
.vacio .por { font-size:var(--t-meta); line-height:1.45; color:var(--tinta-media); margin-top:var(--e1); }
```

- **La frase define el concepto, no anima a usarlo.** «Un frente es una parte de tu vida a la que
  quieres dedicarle atención» enseña el modelo de datos; «¡Empieza a organizarte!» no dice nada.
- **Nunca una ilustración.** Ni de stock, ni dibujada.
- Con datos reales escasos, la pantalla casi vacía tiene que verse **deliberada, no rota**.
- Los vacíos por **filtro** son distintos de los vacíos por **falta de datos**, y el botón lo refleja:
  «Quitar el filtro» ≠ «Crear el primero».
- Cada estado vacío de la app se escribe en su tabla del documento de textos. Si no está en la tabla,
  no se inventa: se agrega ahí primero.

---

## 3. Error

Tres partes: **qué pasó** (en primera persona) + **qué hacer** + **un botón**.

```css
.error { display:flex; gap:var(--e3); align-items:center; padding:var(--e3) var(--e4);
         background:var(--panel); border:1px solid var(--linea);
         border-left:3px solid var(--alerta); border-radius:var(--r-tarjeta);
         font-size:var(--t-meta); }
```

- **Nunca un código, nunca el mensaje del servidor.** «No pude cargar tus datos. Revisa la conexión.
  [Reintentar]».
- **«Nada cambió» y «quedó como estaba» son obligatorias** cuando la operación falló sin efectos. Es
  la información que baja la ansiedad, y es más útil que el motivo técnico.
- **Máximo un elemento en alerta por pantalla.** Si hay varios problemas, se agrupan en una línea con
  su cifra: «5 tareas vencidas hace más de 7 días ›». Una pantalla con cinco avisos rojos no tiene
  ninguno.
- El error de un campo va junto al campo, no arriba del formulario.

---

## 4. Sin conexión

- Se muestra **el último valor guardado con su fecha visible**: «Última actualización: ayer 21:40».
  Un dato viejo sin fecha es peor que ningún dato.
- Un aviso sobrio, no un modal que bloquee: «Sin conexión. Ves lo último que alcancé a cargar.»
- Lo que se pueda escribir sin red, se escribe y se sincroniza. Lo que no, lo dice antes de que el
  usuario escriba, no después de que apriete guardar.

---

## 5. Deshacer

```css
.deshacer { position:fixed; left:var(--e3); right:var(--e3); z-index:12;
            bottom:calc(var(--barra-inf) + var(--e3) + env(safe-area-inset-bottom));
            display:flex; align-items:center; gap:var(--e3);
            padding:var(--e3) var(--e4); border-radius:var(--r-control);
            background:var(--bloque); color:#fff; font-size:var(--t-meta);
            box-shadow:var(--sombra-2); animation:subir var(--d-aparece) var(--curva); }
.deshacer button { flex:none; background:none; border:0; color:var(--foco);
                   font-weight:600; font-size:var(--t-meta); min-height:var(--tap); }
@keyframes subir { from { transform:translateY(16px); opacity:0 } to { transform:none; opacity:1 } }
```

**La regla que decide entre franja y modal:**

| Acción | Qué usa |
|---|---|
| Reversible sin pérdida (marcar, mover, cambiar estado) | franja de deshacer, 6 s |
| Irreversible o con pérdida nombrable | hoja de decisión (`03-componentes.md` §7) |

Una franja que promete deshacer algo que no se puede deshacer es peor que un modal.

**La segunda línea de la franja no es decoración.** Cada acción deja una pregunta abierta, y la segunda
línea la responde antes de que se haga:

| Acción | Franja | Segunda línea |
|---|---|---|
| Sacar del día | Fuera del día: pagar la matrícula | La fecha objetivo no cambió. |
| Descartar | Descartada: ordenar el archivo | Sale del avance de Trabajo. |
| Mover | Movida a «Salud al día» | Los pomodoros siguen contando en Hijos. |
| Poner en espera | En espera: Pasatiempos | Su peso sale del reparto. |

Si una acción cambia una cifra que el usuario está mirando, **la franja lo dice**. Ese es el patrón
general: cualquier efecto secundario sobre un número visible se avisa en la franja, y el histórico no
se reescribe.

- Desde 1000 px la franja no se ancla al pie de la ventana: se ancla al pie del contenido, dentro del
  carril principal.
- Una sola franja a la vez. Una acción nueva reemplaza la anterior, no la apila.

---

## 6. Confirmaciones

- **Modal solo para lo irreversible.** Todo lo demás, franja.
- El título nombra el objeto: «Borrar "Escribir la primera página"», no «¿Estás seguro?».
- El cuerpo lista **lo que se pierde**, contado: «Se borran también 3 subobjetivos y 12 tareas».
- **«Esto no se puede deshacer» se escribe una sola vez en toda la app**, en la única operación
  verdaderamente irrecuperable. Ponerlo en todo lo vacía de sentido.
- Los botones copian el verbo de la pregunta: «¿Descartar las 4?» → `Descartar las 4`.
