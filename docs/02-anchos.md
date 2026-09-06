# Núcleo · 02 — La escalera de anchos

La parte más rentable del núcleo: es lo que en FOST costó doce preguntas de implementación y dos
rondas de corrección. Se copia entera.

---

## 1. Cuatro cortes, y solo cuatro

```css
@media (min-width: 768px)  { /* tableta  */ }
@media (min-width: 1000px) { /* laptop   */ }
@media (min-width: 1280px) { /* monitor  */ }
@media (min-width: 1680px) { /* grande   */ }
```

| Escalón | Ancho | El único cambio que introduce |
|---|---|---|
| Teléfono | `< 768` | La base. Barra de destinos al pie, una columna, fila alta. |
| Tableta | `768–999` | Los destinos salen del pie y pasan a **carril lateral de 72 px, solo íconos**. |
| Laptop | `1000–1279` | El carril **gana rótulos** y se ensancha. La fila de lista **se aplana a un renglón**. |
| Monitor | `1280–1679` | Aparece el **segundo carril** (rail). |
| Grande | `≥ 1680` | Nada crece: el contenido **topa** y el sobrante se vuelve margen. |

Cada escalón introduce **un solo** cambio y hereda todo lo anterior. Eso es lo que hace que se pueda
probar: si algo se ve mal, sabes en qué escalón se rompió.

**Si algo se rompe en un ancho intermedio, se arregla dentro de su escalón.** No se agrega un quiebre
nuevo. Cinco escalones se pueden verificar a mano; nueve, no.

**Si una app ya tiene un quiebre en producción cerca de uno de estos, se conserva el que existe.** En
FOST el corte era 1000 y no se movió a 1024: mover un quiebre existente quita funciones que ya
funcionaban en la franja intermedia. El número exacto importa menos que no romper lo que anda.

---

## 2. Variables

```css
:root {
  --nav-w: 0px;               /* 0 = la navegación está al pie */
  --contenido-max: 100%;
  --gutter: 16px;
  --rail-w: 0px;              /* 0 = sin segundo carril */
  --row-h: 128px;             /* alto de fila de lista */
  --tap-fila: 44px;           /* controles DENTRO de una fila */
}
@media (min-width: 768px)  { :root { --nav-w:72px;  --contenido-max:720px;  --gutter:24px; } }
@media (min-width: 1000px) { :root { --nav-w:216px; --contenido-max:1040px; --row-h:72px; --tap-fila:36px; } }
@media (min-width: 1280px) { :root { --contenido-max:1280px; --gutter:28px; --rail-w:320px; } }
@media (min-width: 1680px) { :root { --contenido-max:1600px; --rail-w:380px; } }
```

**Qué mide `--contenido-max`**: el ancho de la zona de contenido, **sin contar el carril de
navegación** y **con el rail incluido**. Es el contenedor hermano del carril:

```css
.contenido {
  width: min(100% - 2*var(--gutter), var(--contenido-max));
  margin-inline: auto;
}
```

Es la pregunta que siempre aparece en implementación, y por eso está escrita: sumar `--nav-w` a
`--contenido-max` y compararlo con el viewport da un número que no cabe, y hace pensar que el token
está mal.

**El aire lateral crece por escalón, no por proporción.** 16 → 24 → 28 px y ahí se queda. Un margen
proporcional al monitor deja la app flotando en el medio de una pantalla de 32″.

**Ninguna línea de prosa pasa de 78 caracteres.** Los bloques de texto seguido llevan
`max-width: 70ch` aunque su carril sea más ancho.

---

## 3. El shell — el error que hay que no repetir

Este es el defecto exacto que apareció en FOST al implementar: los destinos recibieron el aspecto de
carril (íconos, rótulos, activo en color) pero **el contenedor seguía apilando en columna**, así que la
navegación quedó debajo del contenido, a ancho completo, en todas las pantallas. Se le cambió el
contenido al carril, no el eje del layout.

```css
.shell {
  display: flex;
  flex-direction: column;       /* móvil: contenido y luego barra al pie */
  min-height: 100dvh;
}

@media (min-width: 768px) {
  .shell { flex-direction: row; }

  .nav {
    order: -1;                  /* al costado izquierdo aunque en el HTML vaya al final */
    width: var(--nav-w);
    flex: 0 0 var(--nav-w);
    height: 100dvh;
    position: sticky;
    top: 0;
    bottom: auto;               /* anular el fixed/bottom de la barra móvil */
    display: flex;
    flex-direction: column;     /* los destinos se apilan DENTRO del carril */
    border-right: 1px solid var(--linea);
    border-top: none;
  }

  .contenido { flex: 1; min-width: 0; }
}
```

Tres cosas que hay que revisar en el mismo cambio, porque son las que delatan el defecto:

1. **Los hijos del carril son `width: 100%` del carril**, no del viewport. Un `100vw` o un ancho fijo
   heredado de la barra móvil rompe todo.
2. **La cuenta y «Salir» van al pie del carril** con `margin-top: auto` dentro del carril, no al pie de
   la página.
3. **El `position: fixed` de la barra móvil se anula** desde 768. Si queda, el carril se despega del
   flujo y el contenido se le mete debajo.

`min-width: 0` en el contenido no es opcional: sin él, un título largo estira el flex y desborda.

---

## 4. Navegación

| | `< 768` | `768–999` | `≥ 1000` |
|---|---|---|---|
| Posición | pie, ancho completo | izquierda | izquierda |
| Medida | alto `--barra-inf` | ancho 72 px | ancho 216 px |
| Contenido | ícono + rótulo | solo ícono, 44×44 | ícono + rótulo + contador |
| Acción principal | botón flotante | ícono al pie del carril | botón sólido con rótulo + atajo |
| Cuenta y Salir | en cabecera | al pie del carril | al pie del carril |

- **El destino activo se marca igual en todos los anchos**: fondo `--acento-tenue` y texto `--acento`.
  No con una barra lateral de color: en el carril de 72 px no hay dónde ponerla.
- **Los contadores aparecen solo en el carril con rótulos.** Un badge de tres dígitos sobre un ícono de
  20 px no se lee.
- En 72 px cada destino es un cuadro de 44×44 con `aria-label` **y** `title`; el tooltip sale al
  costado, no debajo.
- Respetar `env(safe-area-inset-*)` arriba y abajo en móvil.
- **La acción principal desaparece donde compite.** Si una pantalla ya tiene su propio botón de crear
  por columna o por sección, el botón global se oculta ahí — en todos los anchos — y su atajo de
  teclado tampoco dispara. Dos botones que crean cosas distintas es la confusión más caras de
  desarmar después.

**Atajos de teclado solo desde 1000 px**, donde hay teclado con certeza. Ninguno se dispara con el foco
en un campo de texto. El mínimo: la acción principal, los destinos numerados, y buscar.

---

## 5. Filas de lista

| | `< 1000` | `≥ 1000` |
|---|---|---|
| Alto | `--row-h` 128 px (o menos si faltan metadatos) | 72 px, un renglón |
| Estructura | dos o tres renglones apilados | todo en un renglón |
| Controles | los principales visibles + menú | los mismos, siempre visibles, `--tap-fila` |

En un renglón, de izquierda a derecha: casilla → bloque de texto (título sobre metadatos, ambos con
`white-space: nowrap; text-overflow: ellipsis`) → estado → controles.

`--tap-fila: 36px` es **la única** excepción al mínimo de 44 px, y solo aplica a los controles internos
de la fila cuando el puntero es fino. El objetivo es que la fila mida 72 px; todo lo demás en la app
sigue en 44.

Criterio de aceptación de cualquier lista: a 1280×800 entran **al menos ocho** filas en el pliegue.

---

## 6. El segundo carril (rail)

Desde 1280, una pantalla puede tener rail. Tres reglas:

1. **El rail es condicional.** Si no hay nada que poner, no se renderiza y el carril principal ocupa
   todo (`grid-template-columns: minmax(0,1fr)`). **Nunca** una columna de un tercio de la pantalla
   con el texto «no hay nada pendiente»: ese texto pasa a una línea de `--t-meta` bajo el contenido.
2. **Lo del rail es secundario, no un segundo tema.** Va lo que acompaña a la decisión principal:
   pendientes, contexto, lo último que pasó, listas adyacentes. Si algo del rail es tan importante
   como el carril principal, no era rail.
3. **Bajo 1280 el contenido del rail se intercala en la columna única**, en un orden declarado. No
   desaparece.

```css
.dos-carriles {
  display: grid;
  grid-template-columns: minmax(0,1fr) var(--rail-w);
  gap: var(--e5);
}
@media (max-width: 1279px) { .dos-carriles { grid-template-columns: minmax(0,1fr); } }
```

Pantallas que **no** llevan rail: las bitácoras y listados cronológicos (no tienen nada secundario), y
los formularios largos (ver §7).

---

## 7. Formularios

Un formulario **no crece con el monitor**. Se queda centrado en 720 px (760 en `≥1680`), no toma
`--contenido-max`. Un campo de texto de 1400 px de ancho se lee peor, no mejor, y las decisiones de un
formulario se toman de arriba hacia abajo, no de lado a lado.

Lo que sí cambia desde 1000 px: el carril lateral queda visible durante el formulario (no se toma la
pantalla completa), y los botones de paso quedan pegados al pie del bloque de 720, no al pie de la
ventana.

---

## 8. Columnas paralelas

Cuando una app muestra niveles de una jerarquía en columnas lado a lado:

```css
.columnas {
  display: grid;
  grid-template-columns: repeat(var(--n), minmax(0, 1fr));
  gap: var(--e4);
}
```

- **Todas las columnas al mismo ancho.** `minmax(0, 1fr)` y no `1fr`: sin el `minmax`, un nombre largo
  estira su columna y rompe la igualdad. Fijar la primera columna en un ancho menor fue un error en
  FOST — es justo la que suele tener los nombres más largos.
- **Ninguna columna vacía con «elige algo».** Una columna sin selección se colapsa a 44 px con su
  rótulo en vertical (`writing-mode: vertical-rl`) y se expande al hacer clic o cuando el nivel
  anterior recibe selección. Lo que suelta se reparte en partes iguales entre las activas.
- **La columna activa se marca**, con borde superior de 2 px en `--acento`. Es lo que dice dónde va a
  caer lo que escribas en el campo del pie; sin esa marca, escribir parece no hacer nada.
- Cada columna tiene su propio `overflow-y: auto`, y su campo de creación **pegado al pie de su
  columna** (`position: sticky; bottom: 0`), nombrando al padre: «Nuevo X en Y». Un campo que crea dos
  cosas distintas no existe.
- Alto: contenedor de `100dvh − cabecera`, columnas con `min-height: 0`. Nunca alto de contenido.
- Bajo 1000 px, un nivel por pantalla con navegación hacia adentro.

---

## 9. Gestos táctiles

**Los gestos se apagan por tipo de puntero, no por ancho.** Hay tabletas de 900 px y laptops táctiles
de 1440.

```css
@media (hover: none) and (pointer: coarse) { /* gestos activos */ }
```

Donde hay dedo, el gesto funciona en cualquier ancho. Donde solo hay mouse, no existe. En un equipo
híbrido conviven el gesto y los controles visibles sin conflicto.

**Todo gesto es un atajo, nunca el único camino.** Cada cosa que se puede hacer deslizando tiene que
estar también en un menú visible. Umbral 64 px, se anima solo `transform`.

---

## 10. Verificación

Un cambio de layout se revisa en estos seis anchos, y en ninguno menos:

| Ancho | Qué se verifica |
|---|---|
| 375 | Nada de lo nuevo bajó al teléfono. |
| 900 | Carril de íconos, sin pérdida de destinos, gestos vivos. |
| 1000 | El escalón donde el carril gana rótulos y la fila se aplana. |
| 1280 | Aparece el rail. Ocho filas en el pliegue a 800 de alto. |
| 1680 | El contenido topa y el sobrante es margen. |
| 2560 | Nada desproporcionado, ninguna línea sobre 78 caracteres. |

Y una prueba que atrapa el error más común: **a 1900 px, ningún elemento de navegación debajo del
contenido.**
