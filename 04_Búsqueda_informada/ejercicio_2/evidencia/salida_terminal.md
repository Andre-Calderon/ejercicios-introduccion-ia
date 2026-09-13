# Evidencia de ejecución — `find_route.py`

Todas las corridas se hicieron desde `Mexico map/`:

```bash
python find_route.py --from-city ORIGEN --to DESTINO [--from-state ESTADO] [--to-state ESTADO] [--compare]
```

## 0. Desambiguación de nombres repetidos (sin elegir en silencio)

```
$ python find_route.py --from-city "Puebla" --to "Guadalajara"
'Puebla' is ambiguous (2 matches). Pass --from-state/--to-state to disambiguate:
  - id=4: Puebla, Puebla (population 1434062)
  - id=580: Puebla, Baja California (population 15168)
```

```
$ python find_route.py --from-city "Puebla" --from-state "Puebla" --to "Guadalajara"
Algorithm: A* search (h = haversine straight-line km)
Problem:   Puebla (Puebla) -> Guadalajara (Jalisco)
Status:    success
Path:      Puebla -> Santiago Momoxpan -> Coronango -> San Miguel Xoxtla -> Santa Ana Xalmimilulco -> ... -> Chapala -> Capulín -> Las Pintitas -> Tlaquepaque -> Guadalajara
Depth:     47 hops (48 cities)
Cost:      736.3 km
Expanded:  368 nodes
Generated: 1874 nodes
Max frontier: 62
```

## 1. Ruta larga: Tijuana → Cancún (península a península)

```
$ python find_route.py --from-city "Tijuana" --to "Cancún" --compare
Algorithm: A* search (h = haversine straight-line km)
Problem:   Tijuana (Baja California) -> Cancún (Quintana Roo)
Status:    success
Path:      Tijuana -> Villa del Prado 2da Sección -> Terrazas del Valle -> Tecate -> Progreso -> ... -> Peto -> Felipe Carrillo Puerto -> Tulum -> Playa del Carmen -> Cancún
Depth:     124 hops (125 cities)
Cost:      4528.2 km
Expanded:  949 nodes
Generated: 4886 nodes
Max frontier: 89

--- Comparison (reto opcional) ---
A* (h=haversine):  cost 4528.2 km, 124 hops, expanded 949
A* with h=0 (UCS-equivalent): cost 4528.2 km, 125 hops, expanded 998
Greedy (h only):   cost 5535.0 km, 106 hops, expanded 141
A* and Greedy same path: False
```

## 2. Segunda pareja: Mexico City → Monterrey

```
$ python find_route.py --from-city "Mexico City" --to "Monterrey" --compare
Algorithm: A* search (h = haversine straight-line km)
Problem:   Mexico City (Mexico City) -> Monterrey (Nuevo León)
Status:    success
Path:      Mexico City -> Gustavo Adolfo Madero -> Puerto Escondido (Tepeolulco Puerto Escondido) -> Coacalco -> San Pablo de las Salinas -> ... -> Camargo -> China -> Cadereyta Jiménez -> Jardines de la Silla (Jardines) -> Monterrey
Depth:     27 hops (28 cities)
Cost:      1041.9 km
Expanded:  428 nodes
Generated: 2174 nodes
Max frontier: 56

--- Comparison (reto opcional) ---
A* (h=haversine):  cost 1041.9 km, 27 hops, expanded 428
A* with h=0 (UCS-equivalent): cost 1041.9 km, 27 hops, expanded 783
Greedy (h only):   cost 1330.4 km, 29 hops, expanded 33
A* and Greedy same path: False
```

## 3. Verificación adicional (dos parejas más, reto opcional b)

Corridas usadas solo para el reporte del reto opcional (comparación Greedy
vs. A*):

```
Guadalajara -> Mérida        | A* 1984.8 km, 70 hops, exp 736 | Greedy 2261.9 km, 65 hops, exp 85 | mismo camino: false
Hermosillo  -> Oaxaca        | A* 2361.5 km, 62 hops, exp 408 | Greedy 2564.0 km, 47 hops, exp 63 | mismo camino: false
```

## 4. Validación del puerto a JavaScript (mismo algoritmo en el mapa)

Sin depender de abrir el navegador, la lógica de A*/Greedy en JavaScript se
validó así:

1. `node --check` sobre el `<script>` completo del HTML (incluido el JSON de
   1000 ciudades embebido) — sin errores de sintaxis.
2. Se extrajo la misma lógica (`haversineKm`, `bestFirstSearch`) a un script
   Node.js independiente que carga `mexico_cities_graph.json` y corre las
   mismas 4 parejas de arriba. Resultado: **coincide exactamente** en costo
   (km) y nodos expandidos con la salida de `find_route.py` en Python:

```
Tijuana -> Cancún        | A* cost 4528.2 hops 125 exp 949 | Greedy cost 5535.0 hops 106 exp 141 | same: false
Mexico City -> Monterrey | A* cost 1041.9 hops 27 exp 428 | Greedy cost 1330.4 hops 29 exp 33 | same: false
Guadalajara -> Mérida    | A* cost 1984.8 hops 70 exp 736 | Greedy cost 2261.9 hops 65 exp 85 | same: false
Hermosillo -> Oaxaca     | A* cost 2361.5 hops 62 exp 408 | Greedy cost 2564.0 hops 47 exp 63 | same: false
```

**Verificación visual en el navegador:** confirmado. Al abrir el mapa, el
panel lateral carga correctamente la sección "Find a route (A*)" con los dos
selectores `Nombre — Estado`, la casilla "Also show Greedy" y el botón
"Find route".
