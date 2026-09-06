# Núcleo — lectura del sistema

> Versión 0.1 · 2026-08-24 · Extraído de la app de objetivos (FOST) después de dos meses de uso real.
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

**No entra, y es de cada app:** la paleta, la tipografía concreta, el símbolo, la iconografía, cuántos
destinos tiene el menú, los nombres de las pantallas, la densidad de las listas, el modelo de datos
del dominio, y cualquier componente visual terminado.

**La regla de admisión.** Nada entra al núcleo sin haber funcionado en **dos apps**, o en una app con
uso real sostenido. Lo que solo funcionó en FOST es de FOST hasta que se demuestre lo contrario.
Sacar algo del núcleo cuesta cinco veces más que meterlo: si dudas, déjalo en la app.

## 4. Cómo se dispone

Todas las apps viven en la misma cuenta de GitHub, el mismo Vercel y el mismo Supabase, así que no
hace falta npm ni monorepo.

```
github.com/<cuenta>/nucleo
  nucleo.css          ← tokens con valores neutros + base + componentes
  docs/               ← estos documentos
  CHANGELOG.md
```

Cada app lo consume **con versión fijada**, nunca apuntando a la rama principal:

```
apps/ajedrez/
  vendor/nucleo/      ← copia de una versión concreta (v0.1.0), commiteada
  app.css             ← sobreescribe lo que esta app define
```

Se copia la versión, no se referencia en vivo. Un archivo CSS servido desde una URL propaga los
cambios al instante, lo que suena mejor y es peor: un ajuste que se veía bien en objetivos rompe la
app de gastos y te enteras cuando la abres.

**Actualizar una app** es reemplazar la carpeta `vendor/nucleo` por la versión nueva, leer el
`CHANGELOG` y revisar la app. Cada app se actualiza cuando tú decides; nunca las cuatro a la vez.

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
- Las apps pueden sobreescribir cualquier token localmente. La sobreescritura **es parte del diseño**,
  no una excepción ni una deuda. Lo que sí es señal: si tres apps sobreescriben el mismo token con el
  mismo valor, ese valor debería ser el del núcleo.
- Lo que **no** se puede sobreescribir sin discusión: la escalera de anchos, el área de toque mínima,
  y las reglas de accesibilidad. Son las tres cosas que existen para no volver a equivocarse.

## 7. Estado de esta versión

Extraído de FOST. **Probado en una sola app**, así que todo aquí es candidato, no ley. La segunda
prueba es el entrenador de ajedrez: tiene cifras, series y tablas, o sea estresa el núcleo justo donde
FOST no lo estresa. Lo que sobreviva a las dos es núcleo de verdad; lo que no, vuelve a FOST.

Documentos de origen, para rastrear cualquier decisión: `01-sistema-visual.md`, `10-textos.md`,
`11-escritorio.md`, `12-correcciones-escritorio.md` de la app de objetivos.
