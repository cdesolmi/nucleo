# Núcleo · 01 — Tokens y escalas

Lo que fija el núcleo son **los nombres y las escalas**. Los valores de color y las familias
tipográficas los define cada app. Un token con el mismo nombre significa lo mismo en las cuatro apps,
aunque su valor sea distinto.

---

## 1. El bloque de contrato

Toda app declara este `:root`. Los marcados **APP** los rellena la app; los marcados **NÚCLEO** se
copian tal cual y casi nunca se tocan.

```css
:root {
  /* ---------- superficie · APP ---------- */
  --fondo:        ;   /* fondo de la app */
  --panel:        ;   /* tarjetas, listas, formularios */
  --fondo-hondo:  ;   /* barras de filtro, zonas hundidas */
  --linea:        ;   /* bordes y separadores de zonas */
  --linea-suave:  ;   /* separadores dentro de una tarjeta */

  /* ---------- tinta · APP ---------- */
  --tinta:        ;   /* texto principal   · ≥ 12:1 sobre fondo */
  --tinta-media:  ;   /* metadatos, íconos · ≥ 4.5:1 sobre fondo */
  --tinta-tenue:  ;   /* placeholders y dato ausente. NUNCA texto que haya que leer */

  /* ---------- bloque · APP ---------- */
  --bloque:       ;   /* la superficie llena, una por pantalla */
  --bloque-texto: ;
  --bloque-grafico: ;

  /* ---------- acento · APP ---------- */
  --acento:       ;   /* acción y elemento seleccionado · ≥ 4.5:1 sobre panel */
  --acento-hondo: ;   /* hover / pressed */
  --acento-tenue: ;   /* fila seleccionada, chip de acento */

  /* ---------- semánticos · valores APP, nombres NÚCLEO ---------- */
  --logro:     ;  --logro-tenue:     ;
  --atencion:  ;  --atencion-tenue:  ;
  --alerta:    ;  --alerta-tenue:    ;
  --pausa:     ;
  --foco:      ;  /* anillo de foco de teclado, 2 px */

  /* ---------- tipografía · familias APP, escala NÚCLEO ---------- */
  --fuente-cifra: ;   /* cifras, títulos y rótulos */
  --fuente-ui:    ;   /* cuerpo e interfaz */

  --t-cifra:   clamp(44px, 2rem + 1.6vw, 56px);
  --t-cifra-ch:clamp(36px, 1.7rem + 1.2vw, 44px);
  --t-h1:      clamp(26px, 1.2rem + 1.1vw, 34px);
  --t-h2:      20px;
  --t-titulo:  16px;
  --t-cuerpo:  clamp(15px, 0.9rem + 0.2vw, 16px);
  --t-meta:    13px;   /* fijo */
  --t-chip:    12px;   /* fijo */
  --t-rotulo:  11px;   /* fijo */
  --t-campo:   16px;   /* fijo — bajo esto Safari hace zoom al enfocar */

  /* ---------- espacio y forma · NÚCLEO ---------- */
  --e1:4px; --e2:8px; --e3:12px; --e4:16px; --e5:24px; --e6:32px; --e7:48px;
  --r-control:10px; --r-tarjeta:16px; --r-chip:8px; --r-marca:2px;
  --sombra-1:0 1px 2px rgba(0,0,0,.05);
  --sombra-2:0 12px 32px rgba(0,0,0,.12);
  --curva:cubic-bezier(.2,.8,.2,1);

  /* ---------- interacción · NÚCLEO ---------- */
  --tap:44px; --tap-sep:8px; --barra-inf:64px; --franja:56px;

  /* ---------- duraciones · NÚCLEO ---------- */
  --d-estado:120ms; --d-aparece:200ms; --d-hoja:260ms;
}
```

---

## 2. Reglas de las escalas

**Espaciado.** Siete pasos, base 4, y **solo esos siete**. Si un espacio necesita un valor intermedio,
el problema es el layout, no la escala. Los tres del medio (`--e3` `--e4` `--e5`) hacen el 80% del
trabajo.

**Texto.** Diez tamaños, ni uno más. Cuatro escalan solos con `clamp()` (cifra, cifra de tarjeta, h1,
cuerpo) y el resto son fijos. Los fijos son fijos a propósito: un metadato de 13 px que crece a 15 en
un monitor grande empieza a competir con el cuerpo.

- **Nunca bajar de 13 px** en texto que haya que leer.
- **`--t-campo` es 16 px en todos los anchos.** No es estético: bajo 16, Safari en iPhone hace zoom al
  enfocar un campo y descoloca la pantalla.
- Todo número que cambia lleva `font-variant-numeric: tabular-nums`.
- El `clamp()` va sobre el token, nunca en el componente. Un componente que declara su propio `clamp`
  es un token que faltaba.

**Forma.** Cuatro radios. `--r-marca` de 2 px es para el cuadrado de identidad (categoría, frente,
etiqueta de color); es lo que evita que un color de dato parezca un botón.

**Sombra.** Dos, y la segunda solo para lo que flota sobre el contenido (hoja, franja, arrastre). Una
tarjeta en reposo no lleva sombra: lleva borde.

**Interacción.** `--tap: 44px` gobierna botones, campos y filas de menú, **en todos los anchos**. La
única excepción autorizada está en `02-anchos.md` §5: los controles internos de una fila de un
renglón, con puntero fino, bajan a 36 px mediante `--tap-fila`. Ningún otro control baja de 44.

---

## 3. Color — lo que sí manda el núcleo

Los valores son de cada app; estas cuatro reglas no:

1. **El acento es uno y es para la acción.** Ese rango de color queda reservado: ningún dato, ninguna
   categoría, ningún estado usa el color de acento, porque entonces parece pulsable.
2. **El color nunca es el único indicador.** Todo estado lleva texto además de color. Sin excepción.
3. **El texto nunca hereda el color de un dato.** La identidad de una categoría la carga un cuadrado
   de 8–9 px con `--r-marca`; el nombre va en `--tinta`. Si el usuario puede elegir el color, esta
   regla es lo único que evita un contraste de 2:1.
4. **Un solo bloque lleno por pantalla.** `--bloque` es la superficie de más peso visual del sistema y
   se usa **una vez**. Cuando hay cuatro, ninguno dirige la mirada. Qué zona se lo queda lo decide
   cada app, pero es una.

Los filos de color de 3 px, en orden de prioridad cuando compiten en una misma tarjeta:
`--alerta` › `--atencion` › color del dato › `--logro` › `--acento`.

---

## 4. Base

Se copia tal cual en toda app.

```css
*, *::before, *::after { box-sizing:border-box; }
html, body { height:100%; margin:0; }
body {
  font:var(--t-cuerpo)/1.45 var(--fuente-ui);
  color:var(--tinta); background:var(--fondo);
  -webkit-text-size-adjust:100%; overscroll-behavior:none;
}
button { font:inherit; cursor:pointer; }
input, select, textarea { font-family:var(--fuente-ui); font-size:var(--t-campo); color:inherit; }
[hidden] { display:none !important; }
:focus-visible { outline:2px solid var(--foco); outline-offset:2px; }
a { color:var(--acento); text-decoration:none; }
a:hover { color:var(--acento-hondo); text-decoration:underline; }
@media (prefers-reduced-motion:reduce) {
  *, *::before, *::after { animation:none !important; transition:none !important; }
}
```

---

## 5. Tipografía — lo que el núcleo pide de cada app

La app elige las dos familias. El núcleo pide cuatro cosas:

1. **Dos familias, no una ni tres.** Una para cifras, títulos y rótulos; otra para cuerpo e interfaz.
   Con una sola, las cifras no se despegan; con tres, la app deja de tener voz.
2. **La familia de cifras necesita cifras anchas y bien diferenciadas.** Es la que va a mostrar un
   número que se lee de un vistazo.
3. **La app tiene que verse bien sin las fuentes descargadas.** Son una dependencia de red. Se declara
   un respaldo de métricas parecidas para que la caja no salte, y se verifica la pantalla principal con
   las dos bloqueadas.
4. **Las dos familias van al caché del service worker**, aceptando que la primera carga sin señal usa
   los respaldos.

---

## 6. Movimiento

`--d-estado` 120 ms para cambios de estado, `--d-aparece` 200 ms para lo que entra, `--d-hoja` 260 ms
para una hoja. Curva única.

Se anima **opacidad y `transform`**, nunca alto, ancho, ni fondo de listas completas. Ninguna animación
celebra nada: completar algo cambia el estado y aparece la franja de deshacer.
`prefers-reduced-motion: reduce` deja solo cambios instantáneos de opacidad.

---

## 7. Antipatrones

- Un valor literal en un componente que ya existe como token (`padding: 12px` en vez de `--e3`).
- Un `clamp()` escrito dentro de un componente.
- Un color de acento usado para un dato o una categoría.
- Dos bloques llenos en la misma pantalla.
- Un estado que se distingue solo por color.
- Un token nuevo agregado antes de necesitarlo dos veces.
