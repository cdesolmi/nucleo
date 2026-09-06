# Núcleo · 08 — Cómo arranca una app nueva

Checklist para montar una app encima del núcleo, o para migrar una que ya existe. El orden importa: lo
de arriba es lo que hace que lo de abajo no se rehaga.

---

## 1. Antes de escribir CSS — cuatro decisiones

Se escriben en un documento de la app, no se dejan implícitas:

1. **La pregunta que responde la pantalla de entrada.** Una, en una frase. «Qué practico hoy», «cómo
   voy este mes», «qué falta comprar». Si no se puede escribir en una frase, la pantalla de entrada va
   a ser un tablero de todo y no va a servir para nada.
2. **Los destinos.** Cuántos y cómo se llaman. Entre tres y cinco; con seis, dos no se usan. Y cuál es
   el aterrizaje al abrir la app.
3. **Quién se queda el bloque.** Una sola zona lleva `--bloque` en cada pantalla, y qué zona es se
   decide con una condición de datos escrita, no con «la que quede mejor».
4. **Las tres ranuras de la fila**, por pantalla, en una tabla. Cuáles son las acciones de un toque.

---

## 2. Montaje

```
apps/<app>/
  vendor/nucleo/     ← copia de una versión fijada (v0.1.0), commiteada
  app.css            ← :root de la app + lo propio
  docs/              ← los documentos de esta app
```

1. Copiar `vendor/nucleo` de una versión concreta. **No referenciar la rama principal.**
2. Rellenar en `app.css` los tokens marcados **APP** en `01-tokens.md` §1: superficie, tinta, bloque,
   acento, semánticos, las dos familias tipográficas.
3. Copiar tal cual los tokens marcados **NÚCLEO**: espaciado, radios, escala de texto, interacción,
   duraciones.
4. Copiar el `:root` responsivo y el shell de `02-anchos.md` §2 y §3, completos, sin editar.
5. Verificar los seis anchos de `02-anchos.md` §10 con la app vacía, antes de poner contenido. Un
   layout que no pasa esa prueba en blanco no la va a pasar con datos.

---

## 3. Lo que define la app (y no se pide al núcleo)

| Decisión | Nota |
|---|---|
| Paleta completa | acento propio; el esqueleto es el mismo |
| Las dos familias tipográficas | pueden ser distintas por app |
| Símbolo e iconografía | 20×20, trazo 1.6, `currentColor`, con variante chica si va en barra |
| Cuántos destinos y cómo se llaman | tres a cinco |
| Densidad de las listas | una app de datos es más apretada que una de texto |
| El modelo de datos del dominio | entero |
| Los textos | todos, en tablas, siguiendo `05-voz.md` |

**Sobre la personalidad:** la forma que funciona es una familia — mismo esqueleto y misma estructura en
todas, acento propio por app. Se reconocen como del mismo autor al verlas juntas, y cada una se lee
bien sola. Imponer el mismo acento y la misma densidad a cuatro dominios distintos hace que tres se
sientan forzadas.

---

## 4. Orden de construcción

1. **Tokens y base.** Se ve fea y funciona. Verificar los seis anchos.
2. **El shell**: carril de navegación en sus tres formas, cabecera, y la acción principal.
3. **La pantalla de entrada**, con datos reales aunque sean pocos.
4. **Las listas**, con sus cuatro estados (`04-estados.md`) desde el primer día. No después: la app
   con estados desde el principio nunca «parece rota».
5. **El detalle** — el «ver» que no es el «editar» (`03-componentes.md` §1).
6. **Lo demás**, un tramo por vez, cada uno con su criterio de «listo cuando».

---

## 5. Antes de decir que está listo

- [ ] Los seis anchos de `02-anchos.md` §10, incluido: **a 1900 px ningún elemento de navegación
      debajo del contenido**.
- [ ] La tabla de accesibilidad de `06-accesibilidad.md` §1, las ocho filas.
- [ ] Los cuatro estados existen en cada lista: cargando, vacío, error, sin conexión.
- [ ] Cada acción reversible tiene franja de deshacer; cada irreversible tiene hoja con las pérdidas
      nombradas una por una.
- [ ] Ningún texto fuera de la tabla de textos.
- [ ] Ninguna cifra sin explicación de una frase.
- [ ] Un solo `--bloque` por pantalla. Un solo primario por vista. Un solo elemento en alerta.
- [ ] Recorrido completo con Tab, y una mirada en escala de grises.

---

## 6. Migrar una app que ya existe

El caso del entrenador de ajedrez. El orden que evita el rehacer:

1. **Inventario primero.** Qué pantallas hay, qué usas de verdad, y qué te molesta de cada una. Lo que
   no usas no se rediseña: se saca.
2. **Tokens y base.** Es el tramo más barato y el más reversible: cambia todo el aspecto sin tocar
   datos ni lógica. Se sube solo.
3. **El shell y la navegación**, con la escalera completa.
4. **Recién ahí, la pantalla que más te molesta.** No la primera del menú: la que peor funciona.
5. Una pantalla por tramo, cada tramo subible solo.

**Lo que sale de esa app hacia el núcleo.** Una app con cifras, series y tablas va a encontrar huecos
que una app de listas no tiene — visualización de datos, densidad, comparaciones. Cada hueco se
resuelve **primero en la app**; si al terminar sigue pareciendo general, entra al núcleo con su línea
en el `CHANGELOG` nombrando el problema que resolvió. Si no, se queda donde está.
