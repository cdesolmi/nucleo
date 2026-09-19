# Núcleo · 06 — Accesibilidad y foco

No es una capa que se agrega al final: son nueve requisitos que se verifican en cada tramo. Todos
salieron de defectos reales, no de una lista genérica.

---

## 1. La tabla de verificación

| Requisito | Cómo se cumple | Cómo se verifica |
|---|---|---|
| Texto normal ≥ 4.5:1 | `--tinta` ≥ 12:1 · `--tinta-media` ≥ 4.5:1 · `--tinta-tenue` **nunca** en texto que haya que leer | medir los tres sobre `--fondo` y sobre `--panel` |
| Texto sobre `--bloque` | `--bloque-texto` ≥ 12:1 sobre `--bloque`; el rótulo atenuado ≥ 4.5:1 | medir el rótulo, que es el que falla |
| Ningún texto invisible por herencia | el par fondo/texto se cambia completo, nunca a medias | correr el barrido de §8 en cada pantalla |
| Área táctil ≥ 44 y separación ≥ 8 | `--tap` y `--tap-sep` en fila, nav y botones | medir en el DOM, no a ojo |
| Foco de teclado visible | `:focus-visible` global, anillo 2 px `--foco` | recorrer la pantalla entera con Tab |
| Color nunca único indicador | todo estado lleva texto | leer la pantalla en escala de grises |
| Sin zoom al enfocar en iPhone | `--t-campo: 16px` | enfocar un campo en Safari iOS real |
| Se ve bien sin las fuentes | respaldos de métricas parecidas | bloquear ambas familias y mirar la pantalla principal |
| Máximo un elemento en alerta | los problemas se agrupan en una línea con cifra | contar los rojos por pantalla |

---

## 2. Contraste

- **`--tinta-tenue` no es un color de texto.** Es para placeholders y para el `—` de un dato ausente.
  Cualquier texto que haya que leer va en `--tinta` o `--tinta-media`.
- **El texto nunca hereda el color de un dato elegido por el usuario.** Es el defecto que más veces
  aparece: una categoría en amarillo da 2:1 sobre blanco. La identidad la carga el cuadrado de color;
  el nombre va en `--tinta`. (Ver `01-tokens.md` §3.)
- Cuando un color necesita fondo, se usa al 8% con el borde al 30%, y el texto sigue en `--tinta`.
- El acento se mide **sobre `--panel`**, no sobre el fondo: es donde vive.

---

## 3. Foco y teclado

- `:focus-visible` global con anillo de 2 px y `outline-offset: 2px`. Nunca `outline: none` sin
  reemplazo.
- **Orden de foco = orden visual.** Si el carril de navegación va al final del HTML y se coloca a la
  izquierda con `order: -1`, el orden de tabulación deja de coincidir con lo que se ve. Se resuelve con
  el orden del DOM, no con `tabindex` positivos.
- En una hoja o modal: foco atrapado adentro, `Esc` cierra, y al cerrar el foco **vuelve al elemento
  que la abrió**.
- Los atajos de teclado no se disparan con el foco en un campo de texto.
- Todo control de solo ícono lleva `aria-label` **y** `title`. El `title` es para el mouse, el
  `aria-label` para el lector.

---

## 4. Objetivos táctiles

`--tap: 44px` con `--tap-sep: 8px` de separación, en todos los anchos. La única excepción autorizada es
`--tap-fila: 36px` para los controles internos de una fila de un renglón con puntero fino
(`02-anchos.md` §5).

**Contar los controles antes de diseñar la fila.** El presupuesto de una fila es: casilla + contenido +
tres controles. Con más de tres, se pasa al menú. En FOST había siete controles de 14 px a 1 px de
distancia: el rediseño no fue estético, fue que no se podían tocar.

---

## 5. Movimiento y sensibilidad

```css
@media (prefers-reduced-motion:reduce) {
  *, *::before, *::after { animation:none !important; transition:none !important; }
}
```

Se anima opacidad y `transform`. Nada de parpadeos, nada que se mueva solo en bucle salvo el latido del
esqueleto de carga, que también se apaga con `reduce`.

---

## 6. Un solo tema

**Sin modo oscuro**, salvo que una app tenga una razón de dominio para tenerlo. Un solo tema es la
mitad de superficie que verificar, y el modo oscuro mal hecho —contrastes que no se midieron en la
segunda paleta— es peor que no tenerlo.

Si una app lo necesita, se declara como una segunda paleta completa con los mismos nombres de token, y
**se verifica la tabla de §1 dos veces**. No es un filtro de inversión.

---

## 7. Cuándo se verifica

En cada tramo que se sube a producción, no al final del proyecto. La tabla de §1 son ocho revisiones de
dos minutos; el rediseño de una pantalla que no las pasó son dos días.

La prueba más barata y la que más encuentra: **recorrer la pantalla completa con Tab, y mirarla en
escala de grises.** Si con Tab te pierdes, el orden de foco está mal. Si en grises no distingues un
estado, ese estado depende del color.

---

## 8. El barrido de contraste

Un texto que hereda un color equivocado **no rompe nada**: el maquetado queda intacto y el texto
simplemente deja de leerse. Por eso no se encuentra mirando, y por eso hay un barrido. Se pega en la
consola del navegador, con la pantalla abierta:

```js
const lum = c => {
  const [r,g,b] = c.match(/[\d.]+/g).map(Number);
  const f = v => (v/=255) <= .03928 ? v/12.92 : ((v+.055)/1.055)**2.4;
  return .2126*f(r) + .7152*f(g) + .0722*f(b);
};
const fondo = el => {
  for (let n = el; n; n = n.parentElement) {
    const b = getComputedStyle(n).backgroundColor;
    if (b && b !== 'transparent' && !b.startsWith('rgba(0, 0, 0, 0')) return b;
  }
  return 'rgb(255, 255, 255)';
};
console.table(
  [...document.querySelectorAll('body *')]
    .filter(e => !e.children.length && e.textContent.trim())
    .map(e => {
      const a = lum(getComputedStyle(e).color), b = lum(fondo(e));
      const contraste = (Math.max(a,b) + .05) / (Math.min(a,b) + .05);
      return { texto: e.textContent.trim().slice(0,40), contraste: +contraste.toFixed(2) };
    })
    .filter(r => r.contraste < 4.5)
    .sort((x,y) => x.contraste - y.contraste)
);
```

Devuelve todo texto de hoja con menos de 4.5:1 contra el primer fondo opaco que tiene encima, ordenado
de peor a mejor. **Un 1.0 es texto del mismo color que su fondo**, o sea invisible.

Es una prueba de humo, no un reemplazo de medir: ignora la opacidad heredada, las imágenes de fondo y
los degradados, y marca como falla texto decorativo que a veces es legítimo. Sirve para lo que sirve:
en una pantalla sana devuelve una tabla corta y conocida. El día que devuelve dieciséis filas nuevas,
algo se rompió en silencio.
