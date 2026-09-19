# Núcleo · CHANGELOG

Toda entrada nombra **el problema real que resolvió**. Si no se puede nombrar el problema, el cambio no
entra.

---

## Anotado para la próxima versión

**Este es el buzón.** Un hallazgo de una app se anota acá el día que aparece; anotar no toca el CSS y no
obliga a nada. Lo que está en esta sección **no está en ninguna versión publicada** y espera la regla de
admisión: una segunda app. Cómo se redacta una entrada y cuándo pasa de acá al archivo, en
`docs/09-devolucion.md`.

**Un teléfono acostado no existe en el núcleo.** `nucleo.css` tiene solo consultas de `min-width`, en
cuatro cortes (768 · 1000 · 1280 · 1680), y **ni una sola regla de `orientation` ni de `max-height`**.
Para un sistema que se define como de comportamiento, es un hueco de comportamiento, no de estética: un
teléfono acostado deja unos 390 px de alto útil, y el núcleo ya reserva `--barra-inf: 64px` más
`--franja: 56px` de ese alto. Cualquier contenido alto —en el entrenador de ajedrez, un tablero
cuadrado— no cabe, y el sistema no dice qué hacer. Falta la convención: cuándo la barra inferior pasa a
ser lateral, cuándo se colapsan las cabeceras, y qué decide cada app. El entrenador de ajedrez está
resolviéndolo ahora; la convención que le funcione en un teléfono real se propone acá, medida. Hasta
entonces esto es un hueco anotado, no una solución pendiente de copiar.

**Los cortes de pantalla del núcleo y los de la app de ajedrez no coinciden.** El núcleo usa
768 · 1000 · 1280 · 1680; el entrenador usa 600 · 1000 · 1400. Coincide uno de tres. La pregunta de
fondo es cuál manda: si los cortes son comportamiento, le tocan al núcleo y la app debería alinearse; si
la app tiene una razón de dominio, hay que escribirla. Sin urgencia, pero sin resolver no se sabe si la
escalera de `02-anchos.md` está probada en dos apps o en una y media.

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

**`.btn.prim` tiene un `#fff` literal, igual que lo que arregló v0.2.0.** Una app con un acento claro
se queda con un botón primario de texto blanco sobre fondo claro, y no lo puede corregir cambiando
tokens. Es el mismo defecto de `.bloque .rotulo`, en el mismo archivo, encontrado por el mismo barrido.
**No entró a v0.2.0 a propósito:** todavía no le ha pasado a ninguna app, y la regla dice que un cambio
sin problema real nombrado no entra. Queda anotado para el día que pase, o para cuando se decida que
la consistencia del archivo vale por sí sola. Candidato: `--acento-texto`.

---

## v0.2.0 — 2026-09-19

Segunda versión, y la primera corregida **desde afuera**: todo esto salió de conectar el entrenador de
ajedrez, la segunda app. Cambio **menor**: se agrega un token, no se renombra ni se elimina ninguno.
Una app puede subir de v0.1.0 a v0.2.0 cambiando el número y sin tocar nada más.

**El núcleo dejó de imponer un color que ninguna app podía cambiar** (`01-tokens.md` §3,
`03-componentes.md` §5) — `.bloque .rotulo` tenía escrito `rgba(255,255,255,.62)`, un blanco literal
dentro de una regla. El mecanismo que el núcleo le ofrece a una app es sobreescribir tokens, así que un
literal es un color que no se puede corregir por ningún camino. Ahora es `--bloque-texto-suave`, que se
deriva sola de `--bloque-texto`: una app que cambia el texto del bloque arregla también sus rótulos, sin
saber que existen. Es el primer caso de la excepción de una sola app: el núcleo se contradecía a sí
mismo, la regla ya estaba escrita y el archivo no la cumplía.

**El par fondo/texto quedó escrito como regla** (`01-tokens.md` §3, regla 5) — El problema medido, en el
entrenador de ajedrez: **dieciséis textos ilegibles repartidos en tres pantallas**, todos en blanco
sobre un fondo color papel. La app usa `.bloque` como panel claro, redefinió el `background` en su hoja
de estilo, y no redefinió el `color`; el blanco del núcleo sobrevivió y se heredó hacia adentro, a todo
título, rótulo y nombre de lista que no traía color propio. El fallo es **silencioso**: el maquetado no
se rompe, el texto solo deja de leerse. Entra la regla de que fondo y texto de una superficie son una
sola decisión, y el corolario de que una app sobreescribe **los tokens, nunca la regla**.

**El barrido de contraste** (`06-accesibilidad.md` §8) — Los dieciséis textos no los encontró nadie
mirando la pantalla, porque un texto invisible no se ve. Entra el noveno requisito y el trozo de código
que lo verifica: lista todo texto con menos de 4.5:1 contra su fondo real, ordenado de peor a mejor.
Un 1.0 es texto del mismo color que su fondo.

**La vía de vuelta** (`09-devolucion.md`, nuevo) — El problema que resuelve: estos tres hallazgos
existían hace días en un documento suelto en el escritorio, fuera del repositorio, porque el núcleo
tenía escrito **que** las apps le devuelven aprendizaje pero no **cómo**. Entra el procedimiento
completo: dónde se anota, las tres preguntas que deciden si un hallazgo es del núcleo o de la app, qué
forma tiene una entrada, los tres estados (anotado → confirmado → adoptado), la excepción de una sola
app, y la instrucción que se le pega al proyecto de una app para que la pregunta se haga en cada tramo.

**El README y `00-lectura-del-nucleo.md` decían cosas distintas** (`00-lectura-del-nucleo.md` §4) — Uno
decía enlazar la dirección publicada y el otro decía copiar la carpeta al repo de la app y **nunca**
referenciar en vivo. Las dos son seguras, pero por una razón que el documento no nombraba: lo que
protege no es que el archivo sea local, es que **la versión va adentro de la dirección**. Queda el
enlace como forma estándar, la copia local como la salida para una app que necesita funcionar sin red, y
la razón escrita.

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
