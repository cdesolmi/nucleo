# Núcleo · 05 — La voz

Reglas de escritura para apps de uso personal o familiar. No es tono de marca: es el conjunto de
decisiones que evita que la app suene a producto que quiere retenerte.

---

## 1. Las siete reglas

1. **Tuteo, siempre.** Nunca «usted», nunca impersonal.
2. **Segunda persona para lo que hiciste tú; primera para lo que falló el sistema.**
   «Cerraste 9 tareas» · «No pude guardar la tarea». **Nunca «nosotros»**: no hay equipo detrás, hay
   una app que usas solo.
3. **Cifra y plazo adentro.** Un texto del sistema sin número casi siempre es un texto vacío.
4. **Sin signos de exclamación.** Ni uno, en toda la app.
5. **Sin culpa y sin premio.** Nada de «no te preocupes», «¡bien hecho!», «es hora de», «recuerda
   que», «no olvides», «deberías».
6. **Sin metáforas y sin adjetivos de ánimo.** No hay «impulso», «racha imparable», «momentum». Los
   hechos no necesitan adjetivos.
7. **Frases cortas.** Dos como máximo por bloque. Si necesitas una subordinada, casi siempre sobra
   media frase.

### Lista negra literal

`no te preocupes` · `¡` · `!` · `ánimo` · `recuerda` · `no olvides` · `es hora de` · `deberías` ·
`¿por qué no` · `felicitaciones` · `¡bien!` · `sigue así` · `vamos` · `oops` · `ups` · `error 500` ·
cualquier emoji.

---

## 2. Formas

| Forma | Uso | Ejemplo |
|---|---|---|
| **Tú** | todo lo que hiciste o vas a hacer | «Marcaste esta tarea 4 días distintos» |
| **Yo** | **solo** cuando el sistema falló | «No pude guardar la tarea» |
| Impersonal | **prohibido** | ~~«Se ha guardado correctamente»~~ |
| Nosotros | **prohibido** | ~~«Te ayudamos a organizarte»~~ |

- **Los botones van en infinitivo.** `Guardar`, no `Guardo`. Excepción: los que responden a una
  pregunta del sistema copian su verbo («¿Descartar las 4?» → `Descartar las 4`).
- **Los rótulos van en singular o plural según el dato**, nunca «Tarea(s)». Si son 0, plural: «0
  tareas».
- Las cifras se escriben con número, no con palabra: «3 tareas», no «tres tareas».

---

## 3. Sin gamificación

Decisión de producto, no de estilo, y aplica a todas las apps del sistema:

**Sin confeti, sin insignias, sin rachas, sin niveles, sin notificaciones que reclamen atención.** La
app no compite por la atención de quien la usa; existe para responder una pregunta y salir del camino.

La única forma permitida de reconocer algo logrado es **una cifra**: «Cerraste todo lo de hoy. 6
tareas, 4 pomodoros.» Sin adjetivos. Y al revés: una app que solo cobra deudas se deja de abrir, así
que si el sistema comenta el estado, **al menos un comentario por período reconoce algo que avanzó**.

**Medir, no juzgar.** Los hechos con su cifra y su plazo, nunca notas ni reproches. Ninguna cifra se
pone roja por estar baja: bajo el umbral cambia el comentario, no el color.

---

## 4. Cada app tiene su tabla de textos

El núcleo da las reglas; cada app escribe **todos** sus textos en tablas, en su propio documento. La
razón es concreta: sin la tabla, aparecen voces distintas en pantallas distintas según cuándo se
escribió cada una.

Las tablas mínimas de cualquier app:

| Tabla | Qué contiene |
|---|---|
| Estados vacíos | título + frase que define el concepto + botón, por cada lista |
| Errores | situación → texto → botón |
| Confirmaciones con modal | acción → título → qué se pierde → botones |
| Franjas de deshacer | acción → texto → segunda línea |
| Ayudas de campo | campo → marcador → ayuda |
| Rótulos y microcopy | destinos, columnas, estados, botones |
| Chips de estado | los literales, todos con su cifra |

**Si un texto no está en la tabla, no se inventa: se agrega a la tabla primero.**

---

## 5. Cómo se agrega un texto nuevo

1. ¿Tiene cifra? Si habla del sistema del usuario y no tiene número, casi siempre falta el número.
2. ¿Pasa la lista negra?
3. ¿Está en segunda persona, salvo que sea una falla del sistema?
4. ¿Deja algo abierto? Si sí, **esa es la segunda línea** — no una nota al pie ni un tooltip.
5. Se agrega a la tabla que corresponda, y recién entonces al código.

---

## 6. Ayudas de campo — la regla que más rinde

Una ayuda va **solo donde el nombre del campo no basta**. Pero donde el campo es ambiguo, la ayuda
decide la calidad del dato para siempre.

El ejemplo que lo prueba, de FOST: el campo de check-in semanal lleva «Déjalo en blanco si esta semana
no mediste». Sin esa línea, el usuario escribe un cero, y un cero falso contamina el promedio para
siempre. Una línea de 13 px salva el histórico.

**Cuando un campo puede recibir un valor que parece dato pero es ausencia, la ayuda es obligatoria.**
