# Núcleo — lectura del sistema

> Versión 0.2 · 2026-09-19 · Extraído de la app de objetivos (FOST) después de dos meses de uso real,
> y corregido con lo que encontró la segunda app.
> Este documento es el índice y el contrato. Los demás son las directrices.

## 1. Qué es esto

Un conjunto de decisiones de **estructura, usabilidad y comportamiento** que sirven para cualquier app
personal o familiar, sin importar de qué trate. No es un manual de marca y no es una librería de
componentes visuales.

La distinción que gobierna todo:

> **El núcleo dice cómo se comporta algo. Cada app dice qué hay dentro y de qué color.**

El núcleo define que hay un carril de navegación, cuánto mide en cada ancho, dónde va la cuenta y qué
pasa con el botón de captura. Cada app define cuántos destinos tiene, cómo se llaman y qué ícono usan.

## 2. Por qué existe

Escribir el CSS nunca fue el trabajo caro. El trabajo caro es **descubrir el problema**. En FOST, la
adaptación a monitor costó doce preguntas de implementación y dos rondas de corrección: que el carril
tenía que salir del pie, que el quiebre existente no se rompe, que el área de toque no baja en todo,
que el gesto de deslizar se apaga por tipo de puntero y no por ancho. Esas doce preguntas ya están
contestadas. En la segunda app no vuelven a aparecer.

El segundo ahorro es hacia atrás: cuando la app de ajedrez descubra que la fila de un renglón no
aguanta cifras largas, ese arreglo entra al núcleo y FOST lo hereda. Con cuatro apps, cada una mejora
a las otras tres.

## 3. Qué entra y qué no

**Entra al núcleo:**

| Área | Documento |
|---|---|
| Nombres de tokens y escalas (no sus valores de color) | `01-tokens.md` |
| La escalera de anchos, layout y navegación responsiva | `02-anchos.md` |
| Comportamiento de fila, chip, botón, campo, tarjeta, hoja | `03-componentes.md` |
| Carga, vacío, error, sin conexión, deshacer | `04-estados.md` |
| Reglas de la voz y de la copy | `05-voz.md` |
| Accesibilidad y foco | `06-accesibilidad.md` |
| Datos, eventos y migraciones | `07-datos.md` |
| Cómo arranca una app nueva encima | `08-app-nueva.md` |
| Cómo una app le devuelve aprendizaje al núcleo | `09-devolucion.md` |

**No entra, y es de cada app:** la paleta, la tipografía concreta, el símbolo, la iconografía, cuántos
destinos tiene el menú, los nombres de las pantallas, la densidad de las listas, el modelo de datos
del dominio, y cualquier componente visual terminado.

**La regla de admisión.** Nada entra al núcleo sin haber funcionado en **dos apps**, o en una app con
uso real sostenido. Lo que solo funcionó en FOST es de FOST hasta que se demuestre lo contrario.
Sacar algo del núcleo cuesta cinco veces más que meterlo: si dudas, déjalo en la app.

La única excepción: cuando el núcleo **se contradice a sí mismo**, una app basta, porque no se está
agregando una decisión sino cumpliendo una que ya estaba escrita. El procedimiento completo de cómo un
hallazgo de una app llega hasta acá está en `09-devolucion.md`.

## 4. Cómo se dispone

Todas las apps viven en la misma cuenta de GitHub, el mismo Vercel y el mismo Supabase, así que no
hace falta npm ni monorepo.

```
github.com/<cuenta>/nucleo
  src/nucleo.css      ← donde se edita
  v0.1.0/nucleo.css   ← congelado, no se toca nunca
  v0.2.0/nucleo.css   ← congelado, no se toca nunca
  docs/               ← estos documentos
  CHANGELOG.md
```

Cada app lo consume **con la versión adentro de la dirección**, nunca apuntando a la rama principal:

```html
<link rel="stylesheet" href="https://<cuenta>.github.io/nucleo/v0.2.0/nucleo.css">
<link rel="stylesheet" href="app.css">
```

El orden importa: el núcleo pone los valores por defecto y la app los sobreescribe después.

**Lo que hace segura esa línea no es que el archivo sea local, es que la dirección lleva la versión.**
Apuntar a un CSS en vivo sin versión propaga los cambios al instante, lo que suena mejor y es peor: un
ajuste que se veía bien en objetivos rompe la app de gastos y te enteras cuando la abres. Con la
versión en la dirección eso no puede pasar: el contenido de `v0.2.0/` nunca cambia.

**Actualizar una app** es cambiar ese número, leer el `CHANGELOG` y mirar la app. Si algo quedó raro,
se vuelve al número anterior. Cada app se actualiza cuando tú decides; nunca las cuatro a la vez.

Una app que necesite funcionar sin red desde la primera carga copia la carpeta de la versión a su
propio repo (`vendor/nucleo/`) en vez de enlazarla. Es la misma promesa por otro camino, y la
condición es la misma: se copia **una versión publicada**, nunca `src/`.

## 5. Versiones

`MAYOR.MENOR.PARCHE`, y el criterio es qué trabajo obliga en cada app:

| Cambio | Sube | Trabajo en la app |
|---|---|---|
| Un valor de token mejora (`--e3` de 12 a 13) | parche | ninguno, se adopta solo |
| Se agrega un token o un componente | menor | ninguno, opcional usarlo |
| Se **renombra** o se elimina un token | mayor | edición en cada app |
| Cambia un comportamiento (un quiebre, un gesto) | mayor | revisar cada app |

De ahí sale la disciplina más importante del sistema: **los nombres se definen bien una vez y después
casi no se tocan.** Un valor mejorado no cuesta nada; un nombre cambiado cuesta cuatro ediciones.

## 6. Cómo se gobierna

- El núcleo tiene **un dueño de la decisión** (tú) y **un laboratorio** (la app con más uso real).
- Un cambio al núcleo se escribe primero como una línea en el `CHANGELOG` explicando **qué problema
  resolvió en una app real**. Si no se puede nombrar el problema, el cambio no entra.
- **Todo hallazgo de una app se anota el día que aparece**, en la sección «Anotado para la próxima
  versión» del `CHANGELOG`. Anotar no obliga a nada y no toca el CSS: es una lista de espera. El
  procedimiento está en `09-devolucion.md`.
- Las apps pueden sobreescribir cualquier token localmente. La sobreescritura **es parte del diseño**,
  no una excepción ni una deuda. Lo que sí es señal: si tres apps sobreescriben el mismo token con el
  mismo valor, ese valor debería ser el del núcleo.
- Lo que **no** se puede sobreescribir sin discusión: la escalera de anchos, el área de toque mínima,
  y las reglas de accesibilidad. Son las tres cosas que existen para no volver a equivocarse.

## 7. Estado de esta versión

Extraído de FOST y **empezando la segunda prueba**, el entrenador de ajedrez: tiene cifras, series y
tablas, o sea estresa el núcleo justo donde FOST no lo estresa. Lo que sobreviva a las dos es núcleo
de verdad; lo que no, vuelve a FOST.

La segunda app ya dejó su primera lección, y es sobre este documento: v0.1.0 traía **decisiones de
color de FOST disfrazadas de comportamiento**. El bloque oscuro con texto blanco no era del sistema,
era de una app, y la primera app de fondo claro que lo tocó se quedó con dieciséis textos ilegibles.
La lista de §3 de lo que **no** entra al núcleo ya lo decía; el archivo no la cumplía.

Documentos de origen, para rastrear cualquier decisión: `01-sistema-visual.md`, `10-textos.md`,
`11-escritorio.md`, `12-correcciones-escritorio.md` de la app de objetivos.
