# Núcleo · 09 — Cómo una app le devuelve aprendizaje al núcleo

El núcleo no se escribe de una vez: se escribe hacia abajo, desde las apps. `02-anchos.md` existe
porque adaptar FOST a monitor costó doce preguntas. `04-estados.md` existe porque FOST se veía caída
en red lenta. Ninguna de esas dos cosas se pensó en el núcleo primero.

Este documento es la **vía de vuelta**: qué hace una app cuando descubre algo, para que ese
descubrimiento no se quede en la app ni termine en un archivo suelto en el escritorio.

---

## 1. El buzón

Hay un solo lugar: la sección **«Anotado para la próxima versión»**, arriba de todo en `CHANGELOG.md`.

Un hallazgo entra ahí **el día que se descubre**, no el día que se arregla. Anotar es barato y no
obliga a nada: la sección es una lista de espera, no una lista de tareas. Lo caro es el hallazgo que
se descubrió, se resolvió en una app, y nunca se escribió — porque la segunda app lo vuelve a
descubrir desde cero, y esa es exactamente la plata que el núcleo existe para no gastar dos veces.

**El buzón no es el CSS.** Anotar nunca cambia `src/nucleo.css`. Lo que pasa del buzón al archivo lo
decide §4.

---

## 2. Antes de anotar: de quién es el hallazgo

Tres preguntas, en orden. La primera que dé «no» cierra el caso.

**1 · ¿Es comportamiento o es apariencia?**
Comportamiento es qué hace algo, cuándo, y en qué orden: un quiebre, un gesto, un área de toque, qué
se ve mientras carga, dónde va el foco al cerrar una hoja. Apariencia es qué color, qué tipografía,
qué ícono, qué palabras. **La apariencia no sube nunca.** Un color que se vio mal en una app es de esa
app.

*El filo:* «este azul no se lee» es apariencia. «Cualquier texto sobre una superficie llena necesita
que el par fondo/texto se cambie completo» es comportamiento. Lo que sube es la regla, no el caso.

**2 · ¿Lo tenía que haber resuelto el núcleo?**
Si la app se equivocó usando bien el núcleo, es de la app. Si la app hizo algo razonable y el núcleo
la dejó caer en una trampa sin avisar, es del núcleo — aunque técnicamente la app lo hizo mal. Una
trampa fácil de pisar es un defecto de diseño del núcleo, no del que la pisó.

**3 · ¿Se puede nombrar el problema real?**
Si la entrada no puede terminar la frase «esto resolvió \_\_\_ en \_\_\_», el hallazgo todavía no está
maduro. No entra. Es la misma regla de gobierno de `00-lectura-del-nucleo.md` §6, aplicada antes y no
después.

---

## 3. La forma de una entrada

Cuatro cosas, en prosa, sin viñetas de relleno:

| | |
|---|---|
| **Qué se observó** | medido, con cifras y dónde. No «se veía raro» |
| **Qué lo causa** | la línea o la regla del núcleo, nombrada |
| **Por qué es del núcleo** | el resultado de §2, en una frase |
| **Qué falta para que entre** | casi siempre: la segunda app |

Ejemplo real, de la app de ajedrez:

> **`70ch` no da 78 caracteres, da unos 105.** La regla escrita en `02-anchos.md` §2 dice que ninguna
> línea de prosa pasa de 78 caracteres, y la traduce a `.prosa { max-width: 70ch }`. Medido en pantalla,
> ese `70ch` deja líneas de 92 a 108. El motivo es que `ch` mide el ancho del cero, que es de los
> caracteres más gruesos. El valor que da 78 exactos, medido, es `50ch`. Falta una segunda app que
> confirme el número, porque depende de la tipografía.

Lo que hace buena a esa entrada: tiene un número medido, nombra el archivo y la línea, explica la
causa, y dice sola por qué todavía no entra.

**Lo que no sirve:** «habría que revisar el tema de los anchos». Eso no es un hallazgo, es una
sensación. Una sensación se guarda en la app hasta que alguien la mide.

---

## 4. Los tres estados de un hallazgo

```
anotado  →  confirmado  →  adoptado
```

**Anotado.** Vive en el buzón. Una sola app lo vio. No toca el CSS ni las directrices. Puede quedarse
ahí meses, y no pasa nada.

**Confirmado.** Una segunda app se topó con lo mismo, o la primera lo lleva usando lo suficiente como
para que ya no sea un accidente. Es la **regla de admisión** de `00-lectura-del-nucleo.md` §3, y es la
que impide que el núcleo se llene de decisiones de una sola app — que es exactamente cómo v0.1.0
terminó con un bloque oscuro que no era del sistema sino de FOST.

**Adoptado.** Sale del buzón, entra a `src/nucleo.css` y a la directriz que corresponda, y baja al
`CHANGELOG` como línea de la versión, con el problema nombrado. La entrada del buzón se borra: no se
duplica.

### La excepción de una sola app

Un hallazgo entra con una sola app cuando **el núcleo se contradice a sí mismo**. No se está agregando
una decisión nueva: se está arreglando una que ya estaba escrita y que el archivo no cumplía. Ahí la
segunda app no aporta nada, porque la regla ya existe.

El caso de v0.2.0: `01-tokens.md` decía que el núcleo no impone color, y `.bloque .rotulo` tenía un
blanco literal que ninguna app podía cambiar. Una sola app bastó.

Fuera de eso, se esperan dos.

---

## 5. Cuándo se escribe

Al cerrar un tramo de trabajo en una app, no al final del proyecto. Un hallazgo a los tres días es una
frase; a los tres meses es una arqueología, y casi siempre se pierde el número medido, que es lo único
que lo hacía útil.

**La instrucción que se le da al proyecto de una app**, junto a la de leer el repo al empezar:

> Al cerrar un tramo, revisa si algo de lo que resolvimos es del núcleo y no de esta app.
> Si lo es, redáctalo con la forma de `09-devolucion.md` §3 y déjalo listo para pegar en el buzón
> del CHANGELOG. Si no lo es, dilo también, en una línea.

«Si no lo es, dilo también» importa: obliga a la pregunta en cada tramo, y evita que el silencio se
confunda con que no hubo nada.

---

## 6. Qué pasa con lo que se resolvió en la app

Casi siempre, un hallazgo del núcleo ya viene con una solución funcionando en la app que lo encontró.
Esa solución **se queda donde está**. No se copia al núcleo por adelantado.

La app sigue con su arreglo local hasta que el hallazgo llegue a *adoptado*; ahí lo borra y toma el
del núcleo, en el mismo movimiento en que sube de versión. Si la versión nueva no le sirve, se queda
en la anterior y el hallazgo vuelve al buzón: eso es información, no un fracaso.

Sacar algo del núcleo cuesta cinco veces más que meterlo. Esta espera es lo que paga esa diferencia.

---

## 7. Antipatrones de la devolución

- **Anotar una preferencia.** «Me gustaría que las tarjetas tuvieran más aire» no es un hallazgo.
- **Subir la solución sin el problema.** Una entrada que describe qué hacer y no qué pasaba no se
  puede evaluar después, y nadie va a saber si sigue vigente.
- **Saltarse la segunda app porque el arreglo es obvio.** Los arreglos obvios de una sola app son
  justamente los que el núcleo arrastra por años. El bloque oscuro era obvio.
- **Arreglarlo directo en una versión ya publicada.** Una carpeta de versión no se toca nunca; es lo
  único que sostiene la promesa de que nada se rompe solo.
- **Dejar el hallazgo en un documento aparte.** Un archivo suelto con hallazgos no es el buzón: es un
  hallazgo que todavía no llegó.
