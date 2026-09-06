# Núcleo · CHANGELOG

Toda entrada nombra **el problema real que resolvió**. Si no se puede nombrar el problema, el cambio no
entra.

---

## v0.1.0 — 2026-08-24

Primera extracción, desde la app de objetivos (FOST) después de dos meses de uso real. **Probado en una
sola app**: todo aquí es candidato, no ley. Segunda prueba pendiente: entrenador de ajedrez.

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
