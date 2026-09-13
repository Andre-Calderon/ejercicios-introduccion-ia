# Ejercicio 1 — Comparar Greedy y A* en el mapa de Rumania

## 1. Pareja origen–destino elegida

**Timisoara → Bucharest** (distinta de la pareja por defecto Arad → Bucharest).

Se eligió a propósito porque Greedy y A* **discrepan** claramente: Greedy se
deja llevar por la ciudad que "se ve" más cerca en línea recta (Lugoj) y paga
más km, mientras que A* sí encuentra el óptimo. Como destino es Bucharest, la
heurística usada es la **tabla AIMA de distancia en línea recta** (admisible y
consistente).

### Subgrafo relevante (ciudades usadas en los caminos, con km y `h(n)` hacia Bucharest)

```
h=329                    h=253         h=193              h=100
Timisoara --118-- Arad --140-- Sibiu --80--- Rimnicu Vilcea --97-- Pitesti --101-- Bucharest
     |    h=244                                                                  h=0
     |
    111
     |
   Lugoj --70-- Mehadia --75-- Drobeta --120-- Craiova --138-- Pitesti --101-- Bucharest
  h=244        h=241          h=242            h=160
```

- Camino de **Greedy** (6 carreteras): `Timisoara → Lugoj → Mehadia → Drobeta → Craiova → Pitesti → Bucharest`
  → 111 + 70 + 75 + 120 + 138 + 101 = **615 km**
- Camino de **A\*** (5 carreteras): `Timisoara → Arad → Sibiu → Rimnicu Vilcea → Pitesti → Bucharest`
  → 118 + 140 + 80 + 97 + 101 = **536 km**

## 2. Tabla comparativa

| Algoritmo | Heurística | Status | Path | Depth (roads) | Cost (km) | Expanded | Generated |
|---|---|---|---|---|---|---|---|
| Greedy | tabla AIMA (SLD a Bucharest) | success | Timisoara → Lugoj → Mehadia → Drobeta → Craiova → Pitesti → Bucharest | 6 | 615 | 6 | 15 |
| A* | tabla AIMA (SLD a Bucharest) | success | Timisoara → Arad → Sibiu → Rimnicu Vilcea → Pitesti → Bucharest | 5 | 536 | 10 | 27 |
| UCS (referencia, ejercicio de búsqueda no informada) | — | success | Timisoara → Arad → Sibiu → Rimnicu Vilcea → Pitesti → Bucharest | 5 | 536 | 12 | 31 |

## 3. Reporte

**¿A* encontró el camino de menos km? ¿Greedy coincidió o se desvió?**
Sí, A* encontró el óptimo real: 536 km por `Arad → Sibiu → Rimnicu Vilcea →
Pitesti`, exactamente el mismo camino y costo que UCS (búsqueda no informada)
encontró para esta pareja — esperado, porque `h` es admisible y consistente.
Greedy se desvió: en el primer paso, desde Timisoara, prefirió Lugoj
(`h=244`) sobre Arad (`h=366`) porque solo mira "qué tan cerca se ve" el
vecino del destino, sin importar el costo de la carretera. Esa elección
inicial lo encierra en la rama Lugoj→Mehadia→Drobeta→Craiova→Pitesti, 79 km
más cara (615 vs 536) y con una carretera de más (6 vs 5).

**¿Por qué Greedy puede devolver un camino más caro aunque `h` sea admisible?**
Porque Greedy ordena la frontera **solo por `h(n)`**, ignorando por completo
`g(n)` (el costo acumulado). Que `h` sea admisible garantiza que nunca
sobreestime la distancia restante, pero no le da a Greedy ninguna noción de
cuánto ya costó llegar hasta ahí ni de si existe una ruta alternativa más
barata. Un vecino puede "verse" muy cerca del destino en línea recta y aun
así estar conectado por una carretera larga o llevar a un tramo de rodeo
—como Lugoj→Mehadia→Drobeta→Craiova—, y Greedy no tiene forma de detectarlo
porque nunca compara costos acumulados entre ramas.

**En el camino de A*, ¿`f` tiende a no disminuir a lo largo de la ruta?**
Sí: en la tabla de A* (`Timisoara(329) → Arad(484) → Sibiu(511) → Rimnicu
Vilcea(531) → Pitesti(535) → Bucharest(536)`), `f` crece de forma monótona
(329 → 484 → 511 → 531 → 535 → 536), nunca baja. Esto es consecuencia directa
de que `h` sea **consistente** (además de admisible): para cualquier arista
`n → n'`, se cumple `h(n) ≤ costo(n, n') + h(n')`, lo que implica
`f(n) ≤ f(n')` a lo largo de cualquier camino. Como el destino es Bucharest y
se usa la tabla AIMA (que es consistente por construcción para este grafo),
`f` no disminuye en ningún paso del camino óptimo — es justamente lo que le
permite a A* detenerse en cuanto extrae el destino de la frontera, sabiendo
que ya es el óptimo.

**Punto de decisión clave (Greedy vs. A*, en Timisoara):**
En Timisoara, Greedy compara `h(Lugoj)=244` contra `h(Arad)=366` y expande
Lugoj primero (menor h, decisión final e irreversible: nunca vuelve a
Timisoara). A* en cambio compara `f(Lugoj) = g(111)+h(244) = 355` contra
`f(Arad) = g(118)+h(366) = 484` — en ese primer paso también prioriza Lugoj
por tener menor `f`, pero **no se compromete**: sigue manteniendo Arad en la
frontera. A medida que expande la rama de Lugoj, su `g` acumulado crece
(Drobeta: g=256, Craiova: g=376, Pitesti: g=514) y sus valores de `f` superan
eventualmente a los de la rama de Arad (Pitesti vía Lugoj: f=614 > Pitesti vía
Arad/Sibiu/Rimnicu Vilcea: f=535), así que A* termina retomando y completando
la rama de Arad, que resulta ser la óptima. Ahí se ve la diferencia esencial:
Greedy decide y no reconsidera; A* decide expandir por `f` pero mantiene
abiertas todas las alternativas hasta confirmar cuál es realmente la más
barata.

## 4. Reto opcional

### a) Pareja con discrepancia clara + nodos expandidos

Timisoara → Bucharest (la pareja principal) ya cumple esto: Greedy "se
acerca" en línea recta vía Lugoj pero paga 615 km; A* paga el desvío por
Arad-Sibiu y obtiene 536 km. En nodos expandidos, **A* trabajó más**
(10 nodos) que Greedy (6 nodos): Greedy se compromete con la primera rama que
parece prometedora y para en cuanto la termina, mientras que A* explora y
descarta parcialmente la rama de Lugoj antes de confirmar la de Arad como
óptima.

### b) Comparación con UCS

Corriendo `03_uniform_cost_search.py --from-city Timisoara --to Bucharest`
en el proyecto de búsqueda no informada se obtiene el **mismo** camino y
costo que A* (536 km, `Arad → Sibiu → Rimnicu Vilcea → Pitesti`), confirmando
que con `h` admisible A* iguala el óptimo de UCS. Además, A* expandió
**menos** nodos que UCS: **10** contra **12**. La heurística le permite a A*
descartar antes ramas que se alejan del destino (por ejemplo, no necesita
profundizar tanto en direcciones que UCS sí explora solo por tener bajo costo
acumulado), lográndolo con menos trabajo que UCS mientras Greedy, aunque
expandió aún menos nodos (6), sacrificó la optimalidad del resultado.

### c) Variar solo el destino (mismo origen Timisoara)

Se cambió el destino a **Neamt** (solo alcanzable vía Iasi), manteniendo el
origen Timisoara. Aquí el destino ya no es Bucharest, así que la heurística
cambia de etiqueta: pasa de *"straight-line distance to Bucharest (AIMA
table)"* a *"Euclidean distance to Neamt (map coordinates)"*.

| Algoritmo | Path (resumen) | Depth | Cost (km) |
|---|---|---|---|
| Greedy | Timisoara→Arad→Sibiu→Fagaras→Bucharest→Urziceni→Vaslui→Iasi→Neamt | 8 | 974 |
| A* | Timisoara→Arad→Sibiu→Rimnicu Vilcea→Pitesti→Bucharest→Urziceni→Vaslui→Iasi→Neamt | 9 | 942 |

Con Neamt como destino, Greedy **deja de coincidir** con A* igual que antes,
pero esta vez la discrepancia aparece más adelante en la ruta: ambos salen
igual (Timisoara→Arad→Sibiu), y es en Sibiu donde Greedy prefiere Fagaras
(`h=134`) sobre Rimnicu Vilcea (`h=215`) por verse más cerca de Neamt en línea
recta, terminando en 974 km; A* vuelve a pagar el desvío por Rimnicu
Vilcea/Pitesti y llega en 942 km — el mismo costo y camino que BFS/UCS
reportaron para esta pareja en el ejercicio de búsqueda no informada. Es
decir: cambiar el destino cambia *dónde* ocurre la bifurcación entre Greedy y
A* (antes en Timisoara, ahora en Sibiu), pero el patrón se repite porque la
heurística, al ser solo una estimación en línea recta, sigue sin "ver" los
rodeos reales del mapa de carreteras.
