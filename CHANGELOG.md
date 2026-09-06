# Núcleo · CHANGELOG

Toda entrada nombra **el problema real que resolvió**. Si no se puede nombrar el problema, el cambio no
entra.

---

## Anotado para la próxima versión

Nada de esto está en v0.1.0, que no se toca. Son hallazgos de la segunda app que esperan la regla de
admisión.

**`70ch` no da 78 caracteres, da unos 105.** La regla escrita en `02-anchos.md` §2 dice que ninguna
línea de prosa pasa de 78 caracteres, y la traduce a código como `.prosa { max-width: 70ch }`. Medido
en pantalla en el entrenador de ajedrez, ese `70ch` deja líneas de 92 a 108 caracteres. El motivo es
que `ch` mide el ancho del cero, que es de los caracteres más gruesos, y en una tipografía proporcional
la letra media es bastante más angosta. El valor que da 78 caracteres exactos, medido, es `50ch`.

El entrenador de ajedrez usa `50ch` y cumple la regla escrita. Falta una segunda app que confirme el
número antes de cambiar `.prosa` en el núcleo, porque el valor depende de la tipografía y con una sola
medición no se sabe si `50ch` es general o es propio de DM Sans. Candidato a `--prosa-max`, para que
cada app pueda ajustarlo sin reescribir el selector.

**Los campos de formulario necesitan una regla, no solo un token.** El núcleo define `--t-campo: 16px`
pero no lo aplica a ningún selector, así que una app puede cargar el núcleo y seguir teniendo campos de
11 px que hacen zoom en Safari. Fue exactamente lo que pasó en el entrenador. Candidato: que el núcleo
aplique `font-size: var(--t-campo)` y `min-height: var(--tap)` a `input`, `select` y `textarea` de
entrada, con la salvedad de las casillas de verificación.

---

## v0.1.0 — 2026-08-24

Primera extracción, desde la app de objetivos (FOST) después de dos meses de uso real. **Probado en una
sola app**: todo aquí es candidato, no ley. Segunda prueba: entrenador de ajedrez, 2026-09-06.

**`nucleo.css`** — Las directrices dejan de ser solo prosa y pasan a ser un archivo que una app puede
cargar. Contiene los tokens con valores neutros de relleno, la base, la escalera de anchos completa, el
shell, la navegación, los componentes base y los cuatro estados. El problema que resuelve: sin archivo,
«adoptar el núcleo» era copiar y pegar a mano leyendo los documentos, que es exactamente lo que el
núcleo existe para evitar.

**Escalera de anchos** (`02-anchos.md`) — Adaptar FOST a monitor costó doce preguntas de implementación
y dos rondas de corrección. Entran resueltas: cuatro cortes y no más; conservar el quiebre que ya
existe en vez de moverlo; qué mide exactamente el tope de contenido; el shell que cambia de eje y no
solo de contenido; los gestos que se apagan por puntero y no por ancho; columnas paralelas todas al
mismo ancho.

**Área de toque** (`01-tokens.md`, `06-accesibilidad.md`) — 44 px en todo el sistema, con una única
excepción nombrada para los controles internos de una fila de un renglón. El error que corrige: bajar
el mínimo global al aplanar la fila, y arrastrar 36 px a botones y campos donde no correspondía.

**Los cuatro estados** (`04-estados.md`) — FOST no tenía ninguno: en red lenta la app quedaba en blanco
y parecía caída. Entran esqueletos con la forma real, vacíos que definen el concepto, errores en
primera persona con «nada cambió», y la regla de franja vs. modal.

**La fila** (`03-componentes.md` §1) — El defecto medido: siete controles de 14 px a 1 px de distancia,
con 1.3:1 de contraste, invisibles hasta un hover que en celular no existe. Entran tres controles como
máximo, y la distinción entre «ver» y «editar» que le da lugar a las notas y al histórico.

**El texto nunca hereda el color de un dato** (`01-tokens.md` §3) — Una categoría en amarillo elegida
por el usuario daba 2:1 sobre blanco. La identidad la carga el cuadrado de color.

**Un solo bloque por pantalla** (`01-tokens.md` §3) — Con cuatro superficies llenas ninguna dirige la
mirada.

**La voz** (`05-voz.md`) — Había voces distintas en pantallas distintas según cuándo se escribió cada
una. Entran las siete reglas, la lista negra, y la obligación de que todo texto viva en una tabla antes
de vivir en el código.

**Ayuda de campo obligatoria cuando el valor ausente parece dato** (`05-voz.md` §6) — Sin la línea
«déjalo en blanco si esta semana no mediste», el usuario escribe un cero y contamina el promedio para
siempre.

**Datos** (`07-datos.md`) — SQL idempotente, ninguna migración se revierte, la migración va antes que
la UI con código tolerante a la columna ausente, borrado suave con fecha, bitácora de eventos con tipo
que crece, el esfuerzo registrado nunca se borra con el objeto, RLS en la misma migración que la tabla.

**Orden de despliegue** (`07-datos.md` §7) — Tokens primero porque no tocan datos; lo que depende de
servicios externos al final porque es lo único que falla por razones ajenas.
