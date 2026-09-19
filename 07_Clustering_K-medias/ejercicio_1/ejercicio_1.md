# Ejercicio 1 — Separar los blobs y volver a elegir k

## 1. Qué se hizo

Se trabajó sobre una copia de `01 K-medias.ipynb`, sin modificar el
original. Se corrió primero tal cual (los 5 blobs de Géron, ajuste de
K-Means con `k=5`, diagrama de Voronoi, codo con `kmeans_per_k` para
`k=1..9` y silueta para `k=2..9`), y después se repitió exactamente el
mismo experimento con los centros separados. En ambos casos se mantuvo
`n_samples=2000`, `random_state=7` en `make_blobs`, y `random_state=42` en
todos los `KMeans` del bucle — el único cambio real fue `blob_centers` (y,
para el reto opcional, `blob_std`).

## 2. Centros usados

| | Original (Géron) | Separado |
|---|---|---|
| Centro 1 | `[0.2, 2.3]` | `[0.2, 2.3]` (sin cambios) |
| Centro 2 | `[-1.5, 2.3]` | `[-1.5, 2.3]` (sin cambios) |
| Centro 3 | `[-2.8, 1.8]` | `[-2.8, 1.8]` (sin cambios) |
| Centro 4 | `[-2.8, 2.8]` | `[-4.5, 3.0]` |
| Centro 5 | `[-2.8, 1.3]` | `[-1.0, -0.5]` |
| `blob_std` | `[0.4, 0.3, 0.1, 0.1, 0.1]` | `[0.4, 0.3, 0.1, 0.1, 0.1]` (sin cambios) |

Se dejaron dos de los tres centros "pegados" (`x=-2.8`) tal cual, y se
movieron los otros dos a coordenadas bien distintas en `x` y en `y`, lejos
de cualquier otro centro (todas las distancias entre centros quedan muy por
encima de la suma de los `std` involucrados). El resultado en el scatter:
cinco nubes claramente separadas a ojo, sin solapamiento visible.

## 3. Resultados con números

| k | Inertia (original) | Inertia (separado) |
|---|---|---|
| 1 | 3534.8 | 8272.7 |
| 2 | 1149.9 | 3711.6 |
| 3 | 653.2 | 2412.1 |
| **4** | **261.8** | 601.2 |
| **5** | **224.1** | **212.6** |
| 6 | 173.9 | 170.2 |
| 7 | 141.8 | 142.4 |
| 8 | 127.1 | 119.4 |
| 9 | 109.9 | 103.0 |

- **Original:** la caída más grande es de `k=3` a `k=4` (653.2 → 261.8,
  −391.4), y de `k=4` a `k=5` la caída es chica (261.8 → 224.1, −37.7) —
  el codo está claramente en `k=4`.
- **Separado:** la caída de `k=3` a `k=4` sigue siendo grande (2412.1 →
  601.2, −1810.9), pero la de `k=4` a `k=5` **también** es grande (601.2 →
  212.6, −388.6) y recién de `k=5` a `k=6` la caída se vuelve chica (212.6
  → 170.2, −42.4) — el codo se corrió a `k=5`.

| k | Silueta (original) | Silueta (separado) |
|---|---|---|
| 2 | 0.5954 | 0.5064 |
| 3 | 0.5724 | 0.5510 |
| **4** | **0.6885** | 0.7194 |
| **5** | 0.6268 | **0.7828** |
| 6 | 0.5940 | 0.7283 |
| 7 | 0.6074 | 0.7312 |
| 8 | 0.5459 | 0.6768 |
| 9 | 0.5536 | 0.6815 |

La silueta máxima pasa de `k=4` (0.6885) en el original a `k=5` (0.7828) en
el separado — y además el valor máximo sube bastante (clusters más
compactos y mejor separados).

## 4. Reporte

**En los datos de Géron, ¿por qué el codo "prefiere" k=4 si `make_blobs`
usó 5 centros?**
Porque la inercia mide varianza intra-cluster total, y separar los dos
blobs más cercanos de la izquierda (a `y=1.3` y `y=1.8`, con `std=0.1` cada
uno) no reduce mucho esa varianza total: ya son nubes muy compactas
(`std=0.1`) comparadas con las dos grandes de la derecha (`std=0.4` y
`0.3`), así que la mayor parte de la inercia total viene de esas dos nubes
anchas, no de las angostas. Partir el grupo angosto en dos (pasar de `k=4`
a `k=5`) apenas mueve la inercia total (−37.7 sobre un total de 261.8),
mientras que ir de `k=3` a `k=4` sí ahorra mucho (−391.4) porque ahí se
separa por primera vez un blob grande. El codo no "ve" objetos, ve caída de
inercia, y esta caída depende tanto de la distancia entre centros como del
tamaño relativo de los grupos que se están separando.

**Con tus blobs separados, ¿el codo y la silueta coinciden en el mismo k?
¿Ese k es 5?**
Sí a ambas. Con los centros separados, la caída de inercia entre `k=4` y
`k=5` (−388.6) es del mismo orden que la de `k=3` a `k=4` (−1810.9, la
mayor), y se vuelve claramente chica recién en `k=6` (−42.4) — el codo cae
en `k=5`. La silueta confirma exactamente lo mismo: su máximo pasa de
`k=4` a `k=5` (0.7828, el valor más alto de toda la tabla). Antes tenía que
"adivinarse" entre 4 y 5 mirando la curva; ahora ambos criterios apuntan al
mismo `k` sin ambigüedad.

**Si el codo sigue en 4, ¿qué te falta mover (distancia entre centros vs.
`blob_std`)?**
Lo que hay que mover es la **distancia entre centros**, no el `blob_std`.
Esto se confirma con el reto opcional (a): dejar los centros de Géron
intactos y solo subir los tres `std=0.1` a `0.4` no solo no ayuda, sino que
**empeora** todo — la silueta máxima cae a apenas 0.5329 en `k=2` (ver
sección 5a). Agrandar la dispersión sin alejar los centros hace que las
nubes se solapen más, no menos. La separación entre centros es la variable
que realmente determina si k-means (y el codo) puede distinguir los
grupos.

## 5. Reto opcional

### a) Mismos centros de Géron, solo subir `std` de 0.1 a 0.4

| k | Inertia | Silueta |
|---|---|---|
| 3 | 941.9 | 0.5058 |
| 4 | 544.5 | 0.4525 |
| 5 | 451.4 | 0.3955 |
| 8 | 329.2 | 0.3756 |

**Mejor k por silueta: 2** (0.5329) — mucho peor que el 0.6885 del
original y que el 0.7828 del separado. El codo tampoco mejora: la caída
`k=4→5` (−93.1) sigue siendo chica comparada con `k=3→4` (−397.4), igual de
ambiguo que antes. Es decir: **no**, subir el `std` no mueve el codo hacia
`k=5` como sí lo hace alejar los centros — al contrario, degrada la
separación porque agranda las nubes sin darles más espacio entre sí.

### b) Segmentación de imagen con una foto propia

En vez de `ladybug.png` se usó una fotografía real de una taza de café
(imagen de muestra de `scikit-image`, con colores bien diferenciados:
blanco de la taza, café, y madera de la mesa). Se comparó con 10, 8, 6, 4 y
2 colores.

Con **10 a 6 colores** la taza, el café y la cuchara se distinguen bien.
Con **4 colores** ya se pierden los reflejos metálicos de la cuchara, pero
la taza y el plato siguen siendo reconocibles. Con **2 colores** el objeto
deja de tener detalle: la imagen queda reducida a una silueta clara/oscura,
pero **todavía se reconoce la forma de la taza** (el hueco blanco del café
recortado contra el resto oscuro) — es el límite de reconocibilidad para
esta foto en particular, con 6 se pierde poco y con 2 se pierde casi todo
salvo la silueta general.

### c) Codo y silueta sobre Iris

| k | Inertia (Iris) | Silueta (Iris) |
|---|---|---|
| 1 | 681.37 | — |
| **2** | **152.35** | **0.681** |
| 3 | 78.86 | 0.5512 |
| 4 | 57.35 | 0.4976 |
| 5 | 46.47 | 0.4931 |

El `k` "bueno" según silueta es **2**, no 3, aunque Iris tenga 3 especies
reales. Esto coincide exactamente con lo que se ve en el scatter de la
sección de introducción de la propia notebook original (pétalo largo vs.
ancho): *Iris setosa* forma una nube totalmente separada de las otras dos,
mientras que *versicolor* y *virginica* se solapan bastante entre sí en
esas dos variables. K-means (y la silueta) "ven" naturalmente **dos**
grupos bien definidos —setosa vs. el resto— porque la frontera entre
versicolor y virginica no es tan nítida como la frontera entre setosa y
cualquiera de las otras dos. La silueta en `k=3` (0.5512) sigue siendo
razonable, solo que menos "limpia" que la de `k=2`.
