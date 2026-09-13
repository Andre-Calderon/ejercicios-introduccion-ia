# Evidencia de ejecución

## Pareja principal: Timisoara → Bucharest

### BFS

```
Algorithm: Breadth-first search
Problem:   Timisoara → Bucharest
Status:    success
Path:      Timisoara → Arad → Sibiu → Fagaras → Bucharest
Depth:     4 roads
Cost:      568 km
Expanded:  7 nodes
Generated: 17 nodes
Frontier:  max size 5
```

### UCS

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

### DFS

```
Algorithm: Depth-first search
Problem:   Timisoara → Bucharest
Status:    success
Path:      Timisoara → Arad → Sibiu → Fagaras → Bucharest
Depth:     4 roads
Cost:      568 km
Expanded:  4 nodes
Generated: 12 nodes
Frontier:  max size 5
```

### DLS, limit=2 (cutoff)

```
Algorithm: Depth-limited search
Problem:   Timisoara → Bucharest
Status:    cutoff
Detail:    limit=2
Expanded:  3 nodes
Generated: 8 nodes
Frontier:  max size 5
```

### DLS, limit=3 (cutoff)

```
Algorithm: Depth-limited search
Problem:   Timisoara → Bucharest
Status:    cutoff
Detail:    limit=3
Expanded:  6 nodes
Generated: 16 nodes
Frontier:  max size 7
```

### DLS, limit=4 (success)

```
Algorithm: Depth-limited search
Problem:   Timisoara → Bucharest
Status:    success
Detail:    limit=4
Path:      Timisoara → Arad → Sibiu → Fagaras → Bucharest
Depth:     4 roads
Cost:      568 km
Expanded:  4 nodes
Generated: 6 nodes
Frontier:  max size 7
```

### IDS

```
Algorithm: Iterative deepening search
Problem:   Timisoara → Bucharest
Status:    success
Detail:    last_limit=4
Path:      Timisoara → Arad → Sibiu → Fagaras → Bucharest
Depth:     4 roads
Cost:      568 km
Expanded:  14 nodes
Generated: 34 nodes
Frontier:  max size 7
```

## Reto opcional: mismo origen (Timisoara), destino distinto (Neamt)

### BFS

```
Algorithm: Breadth-first search
Problem:   Timisoara → Neamt
Status:    success
Path:      Timisoara → Arad → Sibiu → Fagaras → Bucharest → Urziceni → Vaslui → Iasi → Neamt
Depth:     8 roads
Cost:      974 km
Expanded:  19 nodes
Generated: 45 nodes
Frontier:  max size 5
```

### UCS

```
Algorithm: Uniform-cost search
Problem:   Timisoara → Neamt
Status:    success
Path:      Timisoara → Arad → Sibiu → Rimnicu Vilcea → Pitesti → Bucharest → Urziceni → Vaslui → Iasi → Neamt
Depth:     9 roads
Cost:      942 km
Expanded:  19 nodes
Generated: 46 nodes
Frontier:  max size 4
```

### DLS, limit=7 (cutoff)

```
Algorithm: Depth-limited search
Problem:   Timisoara → Neamt
Status:    cutoff
Detail:    limit=7
Expanded:  45 nodes
Generated: 124 nodes
Frontier:  max size 11
```

### DLS, limit=8 (success)

```
Algorithm: Depth-limited search
Problem:   Timisoara → Neamt
Status:    success
Detail:    limit=8
Path:      Timisoara → Arad → Sibiu → Fagaras → Bucharest → Urziceni → Vaslui → Iasi → Neamt
Depth:     8 roads
Cost:      974 km
Expanded:  17 nodes
Generated: 36 nodes
Frontier:  max size 11
```

### IDS

```
Algorithm: Iterative deepening search
Problem:   Timisoara → Neamt
Status:    success
Detail:    last_limit=8
Path:      Timisoara → Arad → Sibiu → Fagaras → Bucharest → Urziceni → Vaslui → Iasi → Neamt
Depth:     8 roads
Cost:      974 km
Expanded:  128 nodes
Generated: 337 nodes
Frontier:  max size 11
```

**Nota de entorno:** en PowerShell/consola con codificación cp1252 hay que forzar
UTF-8 antes de correr los scripts (usan flechas `→`), por ejemplo:

```powershell
$env:PYTHONIOENCODING = "utf-8"
```

o en bash:

```bash
export PYTHONIOENCODING=utf-8
```
