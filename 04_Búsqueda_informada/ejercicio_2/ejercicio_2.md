# Ejercicio 2 — A* para encontrar rutas en el mapa de México

## 1. Qué se implementó

- **`Mexico map/find_route.py`** (nuevo): CLI que calcula la ruta de menor
  costo en km entre dos ciudades del grafo `mexico_cities_graph.json` usando
  **A\***, reutilizando directamente `Node` (`romania/node.py`) y
  `a_star_search` (`search/astar.py`) del proyecto
  `Búsqueda informada/project` — se importan vía `sys.path`, sin copiar el
  código, porque ambos son genéricos (el estado solo necesita ser algo
  hashable con `.actions/.result/.step_cost/.is_goal`, nunca asumen que es
  una ciudad de Rumania). También reutiliza `greedy_best_first_search`
  (`search/greedy.py`) para la comparación del reto opcional.
- **`Mexico map/mexico_map.html`** (editado, no regenerado): se agregó una
  sección "Find a route (A*)" en el panel lateral con dos selectores de
  ciudad (`Nombre — Estado`, para evitar ambigüedad con nombres repetidos),
  una casilla "Also show Greedy" y un botón "Find route". Al hacer clic se
  calcula la ruta con un **puerto en JavaScript del mismo A\*** (usa el JSON
  del grafo que ya venía embebido en el HTML) y se **pinta sobre el mapa**
  como una línea gruesa verde (A*) y, si difiere, una línea naranja para
  Greedy; el panel muestra costo en km, hops y nodos expandidos de cada uno.
  **No se volvió a correr `generate_mexico_graph.py`** (habría reescrito y
  borrado esta UI); el 4-NN/MST del grafo no se tocó.

## 2. Qué se usó como estado y cómo se resolvieron duplicados

El **estado es el `id` entero** de cada nodo en `mexico_cities_graph.json`
(no el nombre): hay ~39 nombres repetidos (p. ej. `Puebla` aparece en Puebla
y en Baja California), así que un `str` como estado sería ambiguo. `id` es
único y ya viene en el JSON (índice de `nodes[]`).

En el CLI (`MexicoGraph.resolve`), si el nombre buscado tiene más de un
candidato **no se elige ninguno en silencio**: se aborta con un mensaje que
lista todos los matches (`id`, estado, población) y sugiere pasar
`--from-state`/`--to-state`. Ejemplo real:

```
'Puebla' is ambiguous (2 matches). Pass --from-state/--to-state to disambiguate:
  - id=4: Puebla, Puebla (population 1434062)
  - id=580: Puebla, Baja California (population 15168)
```

En el mapa, los dos selectores muestran siempre `Nombre — Estado`, así que
la desambiguación es automática ahí (no hace falta un flag extra).

## 3. Por qué haversine es admisible aquí

El grafo de México se construyó como **4-NN ∪ MST usando haversine** como
distancia entre ciudades (`generate_mexico_graph.py:haversine`), y el costo
de cada arista (`edges[].km`) **es exactamente esa misma distancia
haversine** entre los dos extremos, no una distancia real de carretera con
curvas. Por la desigualdad del triángulo, la distancia en línea recta entre
cualquier nodo `n` y el destino nunca puede ser mayor que la suma de las
distancias en línea recta a lo largo de cualquier camino de aristas hasta
ese destino — y como cada arista **es** su propia distancia en línea recta,
`h(n)` (haversine a la meta) nunca sobreestima el costo real acumulado
restante. Eso la hace **admisible**, y de hecho **consistente** (la misma
razón que hace admisible/consistente la tabla AIMA para Rumania, pero aquí
calculada al vuelo con `lat/lon` en vez de una tabla fija), por lo que A*
devuelve el óptimo en km en este grafo.

## 4. Ruta larga: costo, hops y nodos expandidos

**Tijuana → Cancún** (península a península):

| Algoritmo | Cost (km) | Hops | Expanded |
|---|---|---|---|
| A* (h = haversine) | **4528.2** | 124 | 949 |
| A* con h=0 (equivalente a UCS) | 4528.2 | 125 | 998 |
| Greedy (solo h) | 5535.0 | 106 | 141 |

A* expandió **949** nodos para encontrar la ruta óptima de **4528.2 km** en
**124** tramos. También se probó una segunda pareja, Mexico City → Monterrey,
con resultados equivalentes (ver la tabla de la sección 5b).

## 5. Reto opcional

### a) A* vs. UCS (A* con h=0)

Con la misma pareja Tijuana → Cancún, A* con `h=0` (equivalente a UCS: la
frontera se ordena solo por `g`) encuentra el **mismo costo** (4528.2 km,
la heurística no cambia cuál es el óptimo, solo el orden de exploración) pero
expande **más** nodos: **998** contra los **949** de A* con la heurística
haversine. La heurística le permite a A* descartar antes ramas que se alejan
geográficamente del destino, evitando trabajo que UCS sí hace por explorar
solo en función del costo acumulado. Se repite el mismo patrón en Mexico
City → Monterrey (783 nodos con h=0 vs. 428 con haversine).

### b) Greedy además de A*, mostrando ambas rutas si discrepan

Se agregó Greedy best-first (ordena la frontera **solo** por `h`, ignorando
`g`) tanto al CLI (`--compare`) como al mapa (casilla "Also show Greedy").
En las cuatro parejas probadas, Greedy **siempre discrepó** de A*:

| Pareja | A* (km / hops / exp) | Greedy (km / hops / exp) |
|---|---|---|
| Tijuana → Cancún | 4528.2 / 124 / 949 | 5535.0 / 106 / 141 |
| Mexico City → Monterrey | 1041.9 / 27 / 428 | 1330.4 / 29 / 33 |
| Guadalajara → Mérida | 1984.8 / 70 / 736 | 2261.9 / 65 / 85 |
| Hermosillo → Oaxaca | 2361.5 / 62 / 408 | 2564.0 / 47 / 63 |

El patrón es consistente: Greedy siempre **expande muchos menos nodos**
(entre 6 y 10 veces menos) porque se compromete de inmediato con el vecino
que "se ve" más cerca del destino en línea recta y nunca reconsidera, pero
eso lo lleva a caminos entre un 8% y un 22% más caros en km que el óptimo de
A*. En el mapa, cuando ambas rutas difieren, se dibujan superpuestas: la de
A* en verde (gruesa) y la de Greedy en naranja, para poder comparar
visualmente dónde se separan.

### c) Desambiguación forzada por estado en la UI

Ya cubierto en la sección 2: tanto el CLI (`--from-state`/`--to-state`,
obligatorio si hay ambigüedad) como los selectores del mapa (que siempre
muestran `Nombre — Estado`) resuelven el caso de nombres repetidos sin
adivinar en silencio cuál ciudad se quiso decir.

## 6. Verificación del puerto a JavaScript

La validación de `mexico_map.html` se hizo en dos pasos: (1) `node --check`
sobre el `<script>` completo del HTML, sin errores de sintaxis; y (2) la
misma lógica de A*/Greedy extraída a un script Node.js aparte, corrida
contra el JSON real del grafo, dando **exactamente** el mismo costo y nodos
expandidos que el CLI en Python para las cuatro parejas de prueba.

Además, confirmé visualmente en el navegador que la interfaz carga
correctamente: el panel lateral muestra la sección "Find a route (A*)" con
los dos selectores `Nombre — Estado`, la casilla "Also show Greedy" y el
botón "Find route", integrados junto al resto de controles del mapa
original (buscador de ciudad, filtro por estado) sin romper nada existente.
