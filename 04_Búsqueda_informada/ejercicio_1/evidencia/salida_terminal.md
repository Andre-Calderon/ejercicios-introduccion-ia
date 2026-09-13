# Evidencia de ejecución

Todas las corridas se hicieron desde `Búsqueda informada/project` con:

```bash
python 0X_algoritmo.py --from-city ORIGEN --to DESTINO
```

## Pareja principal: Timisoara → Bucharest

### Heurística (`02_heuristics.py`)

```
Heuristic: straight-line distance to Bucharest (AIMA table)

  h(n)  city
      0  Bucharest  <- goal
     77  Giurgiu
     80  Urziceni
    100  Pitesti
    151  Hirsova
    160  Craiova
    161  Eforie
    176  Fagaras
    193  Rimnicu Vilcea
    199  Vaslui
    226  Iasi
    234  Neamt
    241  Mehadia
    242  Drobeta
    244  Lugoj
    253  Sibiu
    329  Timisoara  <- start
    366  Arad
    374  Zerind
    380  Oradea
```

### Greedy best-first search

```
Algorithm: Greedy best-first search
Problem:   Timisoara → Bucharest
Heuristic: straight-line distance to Bucharest (AIMA table)
Status:    success
Path:      Timisoara → Lugoj → Mehadia → Drobeta → Craiova → Pitesti → Bucharest
Depth:     6 roads
Cost:      615 km

  city                  g     h     f
  Timisoara                0   329   329
  Lugoj                  111   244   355
  Mehadia                181   241   422
  Drobeta                256   242   498
  Craiova                376   160   536
  Pitesti                514   100   614
  Bucharest              615     0   615

Expanded:  6 nodes
Generated: 15 nodes
Frontier:  max size 3
```

### A* search

```
Algorithm: A* search
Problem:   Timisoara → Bucharest
Heuristic: straight-line distance to Bucharest (AIMA table)
Status:    success
Path:      Timisoara → Arad → Sibiu → Rimnicu Vilcea → Pitesti → Bucharest
Depth:     5 roads
Cost:      536 km

  city                  g     h     f
  Timisoara                0   329   329
  Arad                   118   366   484
  Sibiu                  258   253   511
  Rimnicu Vilcea         338   193   531
  Pitesti                435   100   535
  Bucharest              536     0   536

Expanded:  10 nodes
Generated: 27 nodes
Frontier:  max size 5
```

### Referencia: UCS sobre la misma pareja (de `Búsqueda no informada/project`)

```
Algorithm: Uniform-cost search
Problem:   Timisoara → Bucharest
Status:    success
Path:      Timisoara → Arad → Sibiu → Rimnicu Vilcea → Pitesti → Bucharest
Depth:     5 roads
Cost:      536 km
Expanded:  12 nodes
Generated: 31 nodes
Frontier:  max size 4
```

## Reto opcional: mismo origen (Timisoara), destino distinto (Neamt)

### Heurística (`02_heuristics.py`)

```
Heuristic: Euclidean distance to Neamt (map coordinates)

  h(n)  city
      0  Neamt  <- goal
     74  Iasi
    134  Fagaras
    139  Vaslui
    190  Pitesti
    194  Urziceni
    210  Bucharest
    214  Sibiu
    215  Rimnicu Vilcea
    227  Hirsova
    269  Giurgiu
    277  Oradea
    288  Lugoj
    290  Eforie
    292  Craiova
    298  Zerind
    310  Mehadia
    318  Arad
    337  Timisoara  <- start
    339  Drobeta
```

### Greedy best-first search

```
Algorithm: Greedy best-first search
Problem:   Timisoara → Neamt
Heuristic: Euclidean distance to Neamt (map coordinates)
Status:    success
Path:      Timisoara → Arad → Sibiu → Fagaras → Bucharest → Urziceni → Vaslui → Iasi → Neamt
Depth:     8 roads
Cost:      974 km

  city                  g     h     f
  Timisoara                0   337   337
  Arad                   118   318   436
  Sibiu                  258   214   472
  Fagaras                357   134   491
  Bucharest              568   210   778
  Urziceni               653   194   847
  Vaslui                 795   139   934
  Iasi                   887    74   961
  Neamt                  974     0   974

Expanded:  11 nodes
Generated: 30 nodes
Frontier:  max size 8
```

### A* search

```
Algorithm: A* search
Problem:   Timisoara → Neamt
Heuristic: Euclidean distance to Neamt (map coordinates)
Status:    success
Path:      Timisoara → Arad → Sibiu → Rimnicu Vilcea → Pitesti → Bucharest → Urziceni → Vaslui → Iasi → Neamt
Depth:     9 roads
Cost:      942 km

  city                  g     h     f
  Timisoara                0   337   337
  Arad                   118   318   436
  Sibiu                  258   214   472
  Rimnicu Vilcea         338   215   553
  Pitesti                435   190   625
  Bucharest              536   210   746
  Urziceni               621   194   815
  Vaslui                 763   139   902
  Iasi                   855    74   929
  Neamt                  942     0   942

Expanded:  17 nodes
Generated: 43 nodes
Frontier:  max size 5
```

**Nota de entorno:** en PowerShell/consola con codificación cp1252 hay que forzar
UTF-8 antes de correr los scripts (usan flechas `→`):

```powershell
$env:PYTHONIOENCODING = "utf-8"
```
