# Núcleo · 03 — Componentes base

Siete piezas. El núcleo define **su estructura y su comportamiento**; cada app define el color y decide
qué pone dentro. No son componentes terminados: son el contrato de cómo se comporta cada uno.

---

## 1. La fila

La pieza más usada de cualquier app de listas, y donde se concentran los errores de usabilidad.

```
[marca] [contenido ..............................] [controles]
  │        título                                    │
  │        metadatos: ruta + chips + datos           └─ máx. 3
  └─ cuadrado de color, casilla o check
```

```css
.fila {
  display:flex; align-items:flex-start; gap:var(--e3);
  padding:var(--e3) var(--e4); min-height:var(--tap);
  background:var(--panel); border:1px solid var(--linea); border-radius:14px;
  transition:background var(--d-estado) var(--curva);
}
.fila + .fila { margin-top:6px; }        /* aire entre filas, no líneas divisorias */

.fila-ver { flex:1; min-width:0; text-align:left; background:none; border:0;
            color:inherit; padding:0; font:inherit; }
.fila-titulo { font-family:var(--fuente-cifra); font-weight:500;
               font-size:var(--t-titulo); line-height:1.35; overflow-wrap:anywhere; }
.fila-meta { display:flex; flex-wrap:wrap; gap:var(--e2); align-items:center;
             margin-top:5px; font-size:var(--t-meta); color:var(--tinta-media); }

.fila-marca { width:9px; height:9px; border-radius:var(--r-marca); flex:none; margin-top:8px; }
.fila-casilla { width:22px; height:22px; flex:none; margin-top:2px;
                border:1.6px solid var(--tinta-tenue); border-radius:6px;
                background:var(--panel); display:grid; place-items:center; color:transparent; }

.fila-controles { display:flex; gap:var(--tap-sep); flex:none; }
.fila-controles button { width:var(--tap-fila); height:var(--tap-fila);
                         display:grid; place-items:center; background:none; border:0;
                         border-radius:var(--r-control); color:var(--tinta-media); }
.fila-controles button.activa { color:var(--acento); }
.fila-controles button:disabled { color:var(--tinta-tenue); cursor:default; }

@media (hover:hover) { .fila:hover { background:var(--fondo); } }
.fila.sel { background:var(--acento-tenue); }
```

Reglas:

- **Máximo tres controles visibles.** El resto va en un menú. Con más de tres, ninguno se encuentra.
- **Los controles miden 44 px con dedo y 36 con puntero fino** (`--tap-fila`), con `--tap-sep` de
  separación. El antipatrón que esto corrige, medido en FOST: siete botones de 14 px a 1 px de
  distancia, con 1.3:1 de contraste, invisibles hasta un hover que en celular no existe.
- **La zona de contenido es UN objetivo táctil y abre el detalle, no el editor.** Una app que tiene
  «editar» y no tiene «ver» no tiene dónde poner las notas, el histórico ni el contexto. Editar es una
  acción **dentro** del detalle.
- El detalle es hoja de alto completo en celular y panel derecho en computador.
- **Cuáles son las tres ranuras se decide por pantalla**, y se escribe en una tabla. Que cambien de
  pantalla a pantalla está bien; que cambien sin criterio escrito, no.
- Las acciones más frecuentes son **un solo toque**, sin menú intermedio. Si una acción se usa todos
  los días y está a dos toques, está mal puesta.

---

## 2. Chip

Un dato, no una etiqueta. Rectángulo de `--r-chip`, nunca píldora de 999 px.

```css
.chip {
  display:inline-flex; align-items:center; gap:6px; padding:5px 8px;
  border-radius:var(--r-chip); font-family:var(--fuente-cifra);
  font-size:var(--t-chip); font-weight:500; line-height:1; white-space:nowrap;
  border:1px solid var(--linea); background:var(--fondo-hondo); color:var(--tinta);
  font-variant-numeric:tabular-nums;
}
.chip .marca { width:8px; height:8px; border-radius:var(--r-marca); flex:none; }
.chip.logro    { background:var(--logro-tenue);    color:var(--logro); }
.chip.atencion { background:var(--atencion-tenue); color:var(--atencion); }
.chip.alerta   { background:var(--alerta-tenue);   color:var(--alerta); }
```

- **Todo chip lleva su cifra adentro.** «3 pomodoros», «vencida hace 9 días», «2 de 5». Un chip sin
  número casi siempre es una etiqueta que no informa.
- **Ninguno depende del color para entenderse.** El texto dice el estado.
- Los textos de los chips son cerrados y viven en el documento de textos de la app. No se improvisan.

---

## 3. Botón

```css
.btn { min-height:var(--tap); padding:10px 14px; border-radius:var(--r-control);
       border:1px solid var(--linea); background:var(--panel); color:var(--tinta);
       font-size:var(--t-meta); }
.btn.prim { background:var(--acento); border-color:var(--acento); color:#fff; font-weight:600; }
.btn.prim:hover { background:var(--acento-hondo); border-color:var(--acento-hondo); }
.btn.tinta { background:var(--bloque); border-color:var(--bloque); color:#fff; font-weight:600; }
.btn.peligro { color:var(--alerta); font-weight:600; }
.btn.tenue { color:var(--tinta-media); }
.btn:disabled { opacity:.45; cursor:default; }
```

**Un solo primario por vista.** Dos primarios es una decisión que no se tomó. La acción destructiva va
a la derecha y separada del resto.

Todo botón de solo ícono lleva `aria-label` **y** `title`.

---

## 4. Campo

```css
.campo { width:100%; padding:11px 12px; font-size:var(--t-campo);
         border:1px solid var(--linea); border-radius:var(--r-control);
         background:var(--panel); color:var(--tinta); }
.campo::placeholder { color:var(--tinta-tenue); }
.campo[aria-invalid="true"] { border-color:var(--alerta); }
.ayuda { font-size:var(--t-meta); color:var(--tinta-media); margin-top:var(--e1); }
```

- **16 px siempre**, en todos los anchos. Bajo eso Safari en iPhone hace zoom al enfocar.
- La ayuda va **bajo** el campo, no en un tooltip, y **solo donde el nombre no basta**. Una ayuda por
  campo obvio es ruido; una ayuda ausente en el campo ambiguo es un dato mal capturado para siempre.
- El error del campo aparece **junto al campo**, no arriba del formulario.
- **La captura rápida cuesta un campo y un toque.** Si la app tiene una acción de «anotar algo antes de
  que se me olvide», tiene un solo campo enfocado, guarda con Enter, no obliga a elegir categoría, y
  ofrece recolocar desde la franja de confirmación. Cada campo obligatorio extra es una nota que no se
  va a escribir.

---

## 5. Tarjeta y bloque

```css
.tarjeta { background:var(--panel); border:1px solid var(--linea);
           border-radius:var(--r-tarjeta); padding:var(--e4); }
.tarjeta.filo { border-left:3px solid var(--filo, var(--acento)); }

.bloque { background:var(--bloque); color:var(--bloque-texto);
          border-radius:var(--r-tarjeta); padding:var(--e4); }
.bloque .rotulo { color:rgba(255,255,255,.62); }
```

`--filo` se setea por JS (`el.style.setProperty("--filo", color)`) cuando la tarjeta necesita señal de
color. **El texto de la tarjeta no toma ese color**: el filo lleva la identidad.

**Un solo `.bloque` por pantalla** (ver `01-tokens.md` §3). Si dos zonas se pelean el bloque, la regla
de desempate se escribe en el documento de la app, con la condición de datos que la resuelve — no «la
que quede mejor».

---

## 6. Tarjeta de cifra

Para cualquier app que muestre métricas. Orden vertical **fijo**: rótulo → cifra + unidad →
comparación → micro-gráfico → cobertura.

```css
.cifra-rotulo { font-family:var(--fuente-cifra); font-size:var(--t-rotulo); line-height:1;
                font-weight:500; letter-spacing:.1em; text-transform:uppercase;
                color:var(--tinta-media); }
.cifra { font-family:var(--fuente-cifra); font-weight:700; font-size:var(--t-cifra-ch);
         line-height:.95; letter-spacing:-.03em; font-variant-numeric:tabular-nums; }
.cifra.ausente { color:var(--tinta-tenue); }
.tendencia { display:flex; align-items:center; gap:6px; font-size:var(--t-meta);
             font-variant-numeric:tabular-nums; }
.tendencia.sube { color:var(--logro); }
.tendencia.baja { color:var(--tinta-media); }   /* nunca rojo: es un termómetro */
```

Cinco reglas que valen para cualquier dominio:

1. **Una cifra que no se puede explicar en una frase no va.** Cada cifra se toca y muestra de dónde
   salió.
2. **La cifra ausente es `—` con la razón escrita**, nunca un cero. Un cero falso contamina el
   histórico para siempre.
3. **La tendencia lleva flecha y signo**, no solo color. Sin historia: «sin comparación todavía».
4. **Nunca rojo en una cifra.** Bajo el umbral cambia el comentario, no el color. Una app que castiga
   con color se deja de abrir.
5. **Cobertura declarada cuando el cálculo es parcial**: «calculado sobre 2 de 5 objetivos con
   métrica». Sin eso, una cifra parcial se lee como total.

---

## 7. Hoja de decisión

```css
.fondo-hoja { position:fixed; inset:0; z-index:50; background:rgba(0,0,0,.5);
              display:flex; align-items:flex-end; justify-content:center; }
.hoja { background:var(--panel); width:100%; max-width:520px;
        border-radius:var(--r-tarjeta) var(--r-tarjeta) 0 0;
        padding:var(--e5) var(--e5) calc(var(--e5) + env(safe-area-inset-bottom));
        max-height:88vh; overflow-y:auto; box-shadow:var(--sombra-2);
        animation:subir var(--d-hoja) var(--curva); }
.hoja .pie { display:flex; justify-content:flex-end; align-items:center; gap:var(--e2);
             margin-top:var(--e5); }
@media (min-width:1000px) { .fondo-hoja { align-items:center; }
                            .hoja { border-radius:var(--r-tarjeta); } }
```

- Celular: entra desde abajo a ancho completo. Computador: caja centrada.
- **Hoja solo para lo irreversible.** Todo lo reversible va con franja de deshacer (`04-estados.md`).
- **Los irreversibles muestran la lista exacta de lo que se pierde**, nombrada una por una, en un
  bloque `--atencion-tenue`: no «se perderán datos asociados» sino «la fecha objetivo del 30/09»,
  «4 pomodoros dejan de estar asociados a esta tarea».
- **Cuando hay una salida sin pérdida, va como casilla marcada por defecto** en `--logro-tenue`, con la
  razón visible.
- La acción destructiva va a la derecha y separada. Foco atrapado dentro de la hoja; `Esc` cierra.
