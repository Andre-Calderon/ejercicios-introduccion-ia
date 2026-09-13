# Ejercicio 1 — Comparar BFS, UCS, DFS, DLS e IDS en el mapa de Rumania

## 1. Pareja origen–destino elegida

**Timisoara → Bucharest**.

Ambas ciudades existen en el mapa y hay camino entre ellas. Se eligió a
propósito porque BFS y UCS **discrepen** y aprovechando  que cumple también el reto
opcional.

### Subgrafo relevante (ciudades y aristas usadas en los caminos obtenidos)

```
Timisoara --118-- Arad --140-- Sibiu --99--- Fagaras --211-- Bucharest
                            \            \
                             \            \--80--- Rimnicu Vilcea --97-- Pitesti --101-- Bucharest
                              \                          |
                               \                        146
                                \                         |
                                 \-----------------   Craiova
```

- Camino de **BFS/DFS** (4 carreteras): `Timisoara → Arad → Sibiu → Fagaras → Bucharest`
  → 118 + 140 + 99 + 211 = **568 km**
- Camino de **UCS** (5 carreteras): `Timisoara → Arad → Sibiu → Rimnicu Vilcea → Pitesti → Bucharest`
  → 118 + 140 + 80 + 97 + 101 = **536 km**
Como podemos observar el camino con menos carreteras no es el más barato en km.

## 2. Tabla comparativa

| Algoritmo | Status | Path | Depth (roads) | Cost (km) | Expanded | Generated |
|---|---|---|---|---|---|---|
| BFS | success | Timisoara → Arad → Sibiu → Fagaras → Bucharest | 4 | 568 | 7 | 17 |
| UCS | success | Timisoara → Arad → Sibiu → Rimnicu Vilcea → Pitesti → Bucharest | 5 | 536 | 12 | 31 |
| DFS | success | Timisoara → Arad → Sibiu → Fagaras → Bucharest | 4 | 568 | 4 | 12 |
| DLS (limit=2) | **cutoff** | — | — | — | 3 | 8 |
| DLS (limit=3) | **cutoff** | — | — | — | 6 | 16 |
| DLS (limit=4) | success | Timisoara → Arad → Sibiu → Fagaras → Bucharest | 4 | 568 | 4 | 6 |
| IDS | success | Timisoara → Arad → Sibiu → Fagaras → Bucharest | 4 | 568 | 14 | 34 |

La tabla resume el status, path, depth, cost, expanded y generated de cada
corrida.

## 3. Reporte

**¿BFS encontró el camino con menos carreteras? ¿UCS el de menos km?**
Sí a ambas. BFS devuelve el camino de 4 aristas (Timisoara→Arad→Sibiu→
Fagaras→Bucharest), el mínimo número de carreteras posible, con un costo de
568 km. UCS ignora el número de aristas y ordena la frontera por costo
acumulado, por lo que encuentra el camino de 536 km (Timisoara→Arad→Sibiu→
Rimnicu Vilcea→Pitesti→Bucharest), que tiene una carretera más (5) pero es
32 km más barato. En esta instancia BFS y UCS **discrepan** tanto en el
camino como en el costo, justo el comportamiento esperado: BFS optimiza
profundidad, UCS optimiza costo, y no son la misma métrica.

**¿Por qué DFS puede devolver un camino más largo aunque el grafo sea el mismo?**
DFS no compara caminos ni usa ninguna noción de costo o profundidad óptima:
simplemente baja por la primera rama disponible (en orden alfabético) hasta
toparse con el objetivo o un callejón sin salida, y solo entonces retrocede.
No tiene ninguna garantía de optimalidad ni en hops ni en km. En este caso
particular DFS coincidió con BFS (Fagaras) porque, expandiendo en orden
alfabético, la primera rama profunda que probó terminó siendo también la de
menor profundidad; pero eso es casualidad de este grafo/orden, no una
propiedad de DFS. Con otra pareja o otro criterio de expansión, DFS podría
devolver perfectamente un camino mucho más largo que el de BFS.

**¿Con qué `--limit` DLS pasó de `cutoff` a solución, y cómo se relaciona eso
con la profundidad del camino de BFS/IDS?**
Con `--limit 2` y `--limit 3` DLS reporta `cutoff`: el límite es menor que la
profundidad de la solución más superficial alcanzable (4 carreteras), así que
DLS agota el árbol hasta esa cota sin encontrar el objetivo. En `--limit 4`
DLS por fin encuentra `Timisoara → Arad → Sibiu → Fagaras → Bucharest`, con
depth = 4. Ese 4 es exactamente la profundidad que BFS e IDS reportan como
óptima en número de carreteras — IDS internamente prueba límites
crecientes (0, 1, 2, 3, 4) hasta que en el límite 4 encuentra la misma
solución que BFS, coincidiendo con ella en número de carreteras (aunque IDS
expande más nodos en total, 14 vs 7, por repetir las capas superiores en
cada iteración).

## 4. Reto opcional

### a) Discrepancia BFS vs. UCS (nodos expandidos)

Con la misma pareja Timisoara → Bucharest, BFS expandió **7** nodos y UCS
expandió **12** nodos para llegar a soluciones distintas. UCS "trabajó" más:
tuvo que mantener y reordenar una frontera de prioridad más grande (Generated:
31 vs 17) porque no puede parar en cuanto alcanza el nodo meta como hace BFS
— debe seguir expandiendo mientras existan nodos en la frontera con costo
acumulado menor al del primer camino que llegó al objetivo, para garantizar
que el que finalmente devuelve es el más barato.

### b) Variar solo el destino (mismo origen Timisoara)

Se cambió el destino a **Neamt** (solo alcanzable vía Iasi), manteniendo el
origen Timisoara. Subgrafo relevante (extiende el de la sección 1 desde
Bucharest hasta Neamt):

```
                    ...--211-- Bucharest --85--- Urziceni --142-- Vaslui --92--- Iasi --87--- Neamt
                                   ^
                    (viene de Fagaras en BFS/DFS/IDS,
                     o de Pitesti en UCS, ver diagrama de la sección 1)
```

| Algoritmo | Path (resumen) | Depth | Cost (km) |
|---|---|---|---|
| BFS | Timisoara→Arad→Sibiu→Fagaras→Bucharest→Urziceni→Vaslui→Iasi→Neamt | 8 | 974 |
| UCS | Timisoara→Arad→Sibiu→Rimnicu Vilcea→Pitesti→Bucharest→Urziceni→Vaslui→Iasi→Neamt | 9 | 942 |

Para este destino, DLS pasó de `cutoff` en `--limit 7` a `success` en
`--limit 8`, es decir, el límite mínimo necesario **subió de 4 a 8** al
alejar el destino de 4 a 8 carreteras de profundidad. El límite mínimo de
DLS que produce solución siempre coincide con la profundidad del camino
óptimo en hops (el mismo que reportan BFS e IDS): al aumentar la distancia
en carreteras del destino, aumenta en la misma medida el límite mínimo
necesario. IDS también resolvió Neamt en `last_limit=8`, coincidiendo de
nuevo con BFS en profundidad, aunque a costa de expandir muchos más nodos
(128) por repetir las iteraciones de límites 0 a 7 antes de la definitiva.
