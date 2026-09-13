# Ejercicio 1 — Más capas en el perceptrón multicapa (Iris)

## 1. Qué se hizo

Se trabajó sobre copias de las dos notebooks (`04 Multilayer perceptron.ipynb`
a mano en NumPy, y `05 Keras - multilayer perceptron - iris.ipynb`), sin
modificar los originales del repositorio. En ambas copias se corrió primero
la topología original 4x3x3 y luego se agregaron **dos capas ocultas más**
(4x3x3x3x3), con sigmoide en todas las capas, `alpha = 0.03` y **500 épocas**
en ambos casos, para que la comparación fuera justa.

En la notebook a mano, en vez de duplicar las funciones de 2 capas fijas, se
generalizaron `forward` y el ciclo de entrenamiento para aceptar cualquier
lista de tamaños `[n_entradas, n_capa1, ..., n_salida]`: tanto la propagación
como la retropropagación (los `delta` de cada capa, calculados de la salida
hacia la entrada) recorren la lista completa de capas, así que la versión
profunda usa el mismo código que la original, solo con una lista de tamaños
distinta — no se trata de declarar dos listas de pesos más sin que el
backprop las use.

Las ejecuciones se corrieron con las mismas versiones de librerías que usa
Colab (NumPy, scikit-learn, TensorFlow/Keras), con semilla fija (`seed = 42`)
para que los números fueran reproducibles al comparar original vs. profunda.

## 2. Red original (4 x 3 x 3)

| Implementación | Error/loss final (época 500) | Tiempo de entrenamiento |
|---|---|---|
| NumPy (a mano) | **0.0596** | — |
| Keras | **0.1509** | 18.8 s |

Ambas curvas bajan de forma monótona durante las 500 épocas y ninguna llega
a aplanarse del todo — seguirían bajando un poco más con más épocas.

## 3. Red profunda (4 x 3 x 3 x 3 x 3)

| Implementación | Error/loss final (época 500) | Tiempo de entrenamiento |
|---|---|---|
| NumPy (a mano) | **0.4064** | — |
| Keras | **0.2217** | 18.7 s |

`model.summary()` de la versión profunda en Keras confirma las 4 capas
`Dense`:

```
Model: "sequential_1"
┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━┓
┃ Layer (type)                    ┃ Output Shape           ┃       Param # ┃
┡━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━━┩
│ layer1 (Dense)                  │ (None, 3)              │            15 │
├─────────────────────────────────┼────────────────────────┼───────────────┤
│ layer2 (Dense)                  │ (None, 3)              │            12 │
├─────────────────────────────────┼────────────────────────┼───────────────┤
│ layer3 (Dense)                  │ (None, 3)              │            12 │
├─────────────────────────────────┼────────────────────────┼───────────────┤
│ layer4 (Dense)                  │ (None, 3)              │            12 │
└─────────────────────────────────┴────────────────────────┴───────────────┘
 Total params: 51 (204.00 B)
```

En NumPy la curva profunda se queda **estancada en ~0.67** durante las
primeras ~400 épocas (una meseta larga, típica de gradientes que se
desvanecen al apilar sigmoides) y solo empieza a bajar después de la época
~430, terminando en 0.406 — mucho peor que el 0.0596 de la red original. En
Keras el patrón es más suave pero la conclusión es la misma: la curva
profunda converge más rápido al principio, pero se aplana alrededor de la
época 150-200 en ~0.222, peor que el 0.151 de la red original.

## 4. Comparación original vs. profunda

| | NumPy | Keras |
|---|---|---|
| Original 4x3x3 | 0.0596 | 0.1509 |
| Profunda 4x3x3x3x3 | 0.4064 | 0.2217 |
| ¿Bajó el error al agregar capas? | **No**, empeoró | **No**, empeoró |

## 5. Reporte

**¿Bajar más el error al añadir dos capas, o se estancó / empeoró? ¿Igual en
NumPy y en Keras?**
En ambas implementaciones el error **empeoró** al agregar las dos capas
extra, no mejoró: en NumPy pasó de 0.0596 a 0.4064, y en Keras de 0.1509 a
0.2217. El patrón es el mismo en ambos casos aunque con distinta forma: la
red profunda muestra una meseta prolongada antes de moverse (más marcada en
NumPy, donde se estanca en ~0.67 durante 400 épocas), consistente con
gradientes que se desvanecen al apilar varias sigmoides.

**¿Las curvas de la notebook a mano y de Keras se parecen con la misma
topología? Si no, ¿qué diferencias de implementación podrían explicarlo?**
Se parecen en la tendencia general (la versión profunda siempre termina peor
que la original), pero no en la forma ni en los valores absolutos. Tres
diferencias de implementación explican buena parte de esto:

1. **Tamaño de lote:** la notebook a mano actualiza los pesos ejemplo por
   ejemplo (SGD "en línea", batch size = 1), mientras que `model.fit` de
   Keras usa por defecto `batch_size=32` — con 150 ejemplos de Iris eso son
   ~5 actualizaciones por época en vez de 150. Esto cambia por completo la
   trayectoria del descenso, aunque la tasa de aprendizaje nominal sea la
   misma.
2. **Inicialización de pesos:** la notebook a mano inicializa con
   `uniform(-0.5, 0.5)`, mientras que `Dense` de Keras usa por defecto
   inicialización de Glorot/Xavier (rango mucho más chico y ajustado al
   número de entradas de cada capa). Esto afecta cuánto se saturan las
   sigmoides desde el arranque.
3. **Orden de los datos:** en la notebook a mano los 150 ejemplos siempre se
   recorren en el mismo orden (agrupados por clase, tal como vienen en
   Iris), mientras que Keras mezcla (`shuffle=True` por defecto) los datos en
   cada época.

Esto se ve claramente en el reto opcional (sección 6b): con la misma
topología ancha (4x8x8x8x3), la versión a mano sí se benefició bastante de
más neuronas (bajó a 0.065, casi igual que la red original), pero la versión
en Keras con el mismo ancho **no mejoró** (se quedó en 0.223, prácticamente
igual que la profunda angosta) — la diferencia de tamaño de lote y de
inicialización pesa más que el ancho de la red en este caso.

**Con sigmoides apiladas y MSE, ¿tiene sentido que una red más profunda no
aprenda mejor en Iris? Relaciónalo con lo que viste en las gráficas.**
Sí tiene sentido. Iris es un problema pequeño (150 ejemplos, 4 atributos) y
casi linealmente separable por pares de clases: una sola capa oculta de 3
neuronas ya alcanza para resolverlo razonablemente bien (error final 0.06 en
NumPy), así que agregar profundidad no aporta capacidad que el problema
necesite. En cambio, cada sigmoide adicional multiplica el riesgo de que su
derivada `h(1-h)` sea pequeña (la sigmoide se satura fácilmente fuera de un
rango angosto de su entrada), y esos factores pequeños se van multiplicando
capa a capa durante la retropropagación —es justamente el gradiente que se
desvanece—. Eso es exactamente lo que muestran las curvas: la red profunda
no falla por falta de capacidad, sino porque tarda cientos de épocas en que
el gradiente llegue con fuerza suficiente hasta las primeras capas (la
meseta en ~0.67 en NumPy, o en ~0.222 en Keras, donde el modelo básicamente
no se mueve).

## 6. Reto opcional

### a) ReLU + softmax + categorical_crossentropy (Keras)

Se repitió la topología profunda 4x3x3x3x3 en Keras, cambiando sigmoide por
**ReLU** en las tres capas ocultas, **softmax** en la salida, y la pérdida a
`categorical_crossentropy`.

| Configuración | Loss final | Accuracy final |
|---|---|---|
| Sigmoide + MSE (4x3x3x3x3) | 0.2217 | — |
| ReLU + softmax + CCE (4x3x3x3x3) | **1.0987** | **0.333** |

El resultado con ReLU fue peor, no mejor: la pérdida se queda fija en
~1.0987 (≈ ln 3, la pérdida esperada de predecir las 3 clases con la misma
probabilidad) y la exactitud queda en 0.333 — es decir, el modelo termina
prediciendo al azar. Con solo 3 neuronas por capa oculta y ReLU, es fácil
que la inicialización aleatoria deje varias neuronas con entrada negativa de
forma permanente ("ReLU muertas": su gradiente es 0 y nunca se recuperan),
y con capas tan angostas basta con que eso le pase a la mayoría de las
neuronas de una capa para que la señal no llegue a la salida. Este resultado
muestra que cambiar sigmoide por ReLU no es una mejora automática: en capas
muy angostas puede ser incluso más frágil.

### b) Más neuronas por capa oculta (4 x 8 x 8 x 8 x 3)

Se probó la misma profundidad (3 capas ocultas + salida) pero con 8 neuronas
por capa oculta en vez de 3, en ambas implementaciones.

| Implementación | Error/loss final | Comentario |
|---|---|---|
| NumPy, 4x8x8x8x3 | **0.0650** | Casi igual a la red original (0.0596); escapa de la meseta antes que la versión angosta |
| Keras, 4x8x8x8x3 (train) | 0.2228 | Prácticamente igual a la profunda angosta |
| Keras, 4x8x8x8x3 (val, 30% held-out) | 0.2228 | Igual al train — sin señal de sobreajuste |

Con Iris (150 ejemplos) ensanchar las capas ocultas **no produjo
sobreajuste** en ninguna de las dos implementaciones: en Keras las curvas de
entrenamiento y validación son prácticamente idénticas todo el camino (no
hay separación entre ellas), y en NumPy no hay conjunto de validación pero
el error de entrenamiento tampoco baja de forma sospechosa (baja hasta un
nivel similar al de la red original de 1 sola capa oculta). Lo que sí se
observó fue una diferencia entre implementaciones: ensanchar ayudó mucho en
NumPy (de 0.406 a 0.065) pero no ayudó nada en Keras (de 0.222 a 0.223) —
otra vez, el tamaño de lote y la inicialización explican más la diferencia
que el ancho de la red en sí.

### c) Error cada 50 épocas (NumPy, ambas topologías)

| Época | Original 4x3x3 | Profunda 4x3x3x3x3 |
|---|---|---|
| 50 | 0.3465 | 0.6706 |
| 100 | 0.2487 | 0.6704 |
| 150 | 0.1535 | 0.6702 |
| 200 | 0.1120 | 0.6701 |
| 250 | 0.0911 | 0.6698 |
| 300 | 0.0790 | 0.6694 |
| 350 | 0.0713 | 0.6683 |
| 400 | 0.0660 | 0.6622 |
| 450 | 0.0623 | 0.5406 |
| 500 | 0.0596 | 0.4064 |

Se ve con números exactos lo mismo que en la gráfica: la red original baja
de forma constante desde la época 50, mientras que la profunda se queda
prácticamente sin moverse (0.6706 → 0.6622) durante las primeras 400 épocas
y recién entre las épocas 400 y 500 empieza a bajar con fuerza — el tramo
donde el gradiente finalmente "atraviesa" las capas saturadas.
