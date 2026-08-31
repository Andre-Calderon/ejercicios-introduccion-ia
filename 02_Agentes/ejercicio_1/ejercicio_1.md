# Reporte — Ejercicio 1: `config/mi_cueva_4x4.yaml`

## Diagrama de la cueva

```
 y=4 | P  G  .  . |
 y=3 | .  .  .  P |
 y=2 | .  .  W  . |
 y=1 | >  .  .  . |
       x=1 x=2 x=3 x=4
```

- `>` agente, inicia en `[1,1]` mirando al este.
- `W` Wumpus en `[3,2]`.
- `P` pits en `[1,4]` y `[4,3]`.
- `G` oro en `[2,4]`.
- Camino seguro de ida y vuelta: `(1,1) -> (1,2) -> (1,3) -> (2,3) -> (2,4)=oro`,
  y el mismo camino de regreso.

## Resultados por agente (mismo mapa)

| Agente | Resultado | Score |
|---|---|---|
| `02_simple_reflex_agent.py` | Se queda girando en `(3,1)` percibiendo *stench*; nunca sale | -200 (max_steps) |
| `03_model_based_agent.py` | Sale con el oro | +971 |
| `04_goal_based_agent.py` | Sale con el oro | +971 |
| `05_utility_based_agent.py` | Sale con el oro | +971 |
| `06_learning_agent.py` (1500 episodios) | Sale con el oro (episodio final, con política aprendida) | +985 |

## ¿Qué agentes lograron salir con el oro y cuáles no?

Los agentes basado en modelo, basado en metas y basado en utilidad lograron
salir con el oro y con score positivo (+971). El agente de reflejo simple
**no** lo logró: quedó atrapado dando vueltas en `(3,1)` sin memoria del
recorrido, hasta agotar los 200 pasos con score -200.

## ¿Por qué el agente de reflejo simple falla en este diseño?

El agente de reflejo simple decide solo a partir del percepto actual, sin
recordar casillas ya visitadas ni construir un mapa. Al llegar cerca del
Wumpus (percibe *stench* en `(3,1)`, adyacente a `[3,2]`) su regla de
reacción ante el hedor lo hace girar en vez de avanzar, y como no tiene
memoria de haber probado ya esa dirección, entra en un ciclo de giros que
nunca rompe. En este mapa esa trampa está en el camino "natural" que
explora primero (avanzar hacia el este desde `(1,1)`), así que no depende de
suerte: falla de forma consistente por diseño del agente, no del mapa.

## ¿Cómo cambia el resultado del agente basado en modelo si acercas/alejas un pit de la casilla inicial?

Con el pit en `[4,3]` (lejos del inicio) el agente basado en modelo explora
con normalidad y llega al oro en 29 pasos con score +971. Si se acerca un
pit a la casilla inicial (por ejemplo moviéndolo a `[2,1]`, adyacente a
`[1,1]`), el agente detecta *breeze* desde el primer paso y debe evitar esa
celda marcándola como insegura, lo que lo obliga a tomar una ruta alternativa
más larga (más pasos, score final más bajo por la penalización `step: -1`
acumulada) o, si no queda ninguna ruta segura alternativa hacia el oro,
puede quedarse sin poder avanzar de forma segura y no completar el objetivo.
Alejar el pit del inicio, en cambio, deja más celdas seguras cerca del punto
de partida y generalmente reduce el número de pasos necesarios porque el
agente puede explorar en línea más directa hacia el oro.

## Reto opcional — `config/mi_cueva_dificil_4x4.yaml`

### Diagrama

```
 y=4 | ## ## ## G  |
 y=3 | ## ## ## W  |
 y=2 | ## ## ## .  |
 y=1 | >  .  .  .  |
       x=1 x=2 x=3 x=4
```

Un corredor en "L" (paredes `##` en todo lo demás) obliga a recorrer la fila 1
completa y subir por la columna 4. El Wumpus se sienta en `[4,3]`: es la
única celda entre el corredor conocido y el oro en `[4,4]`. No hay otra
ruta posible, ni siquiera arriesgada: el Wumpus tapona el único camino.

### Resultados

| Agente | Resultado | Score |
|---|---|---|
| `03_model_based_agent.py` | Llega hasta `[4,2]`, agota las celdas seguras conocidas y se queda girando en el sitio; nunca cruza `[4,3]` porque nunca dispara | -200 (max_steps) |
| `04_goal_based_agent.py` | Explora el corredor, detecta *stench* en `[4,2]`, deduce que el Wumpus solo puede estar en `[4,3]` (su único vecino no visitado), se alinea, dispara (`wumpus_killed`), cruza, agarra el oro y regresa a climbar | +971 |

### Observación

El agente basado en modelo trata "seguro" como "confirmado sin riesgo de
muerte" y nunca considera disparar: por diseño solo evita celdas peligrosas,
no las despeja. Al no tener ninguna celda segura restante por explorar más
allá de `[4,2]`, su única acción válida es girar indefinidamente, tal como
predice el enunciado. El agente basado en metas, en cambio, incluye
`_shoot_if_blocking`: cuando el Wumpus es la única incógnita conocida con
posición única (`known_wumpus`) y hay una línea de tiro despejada, dispara
para destrabar el paso antes de seguir con su meta. Esa diferencia de
diseño —evitar peligros vs. eliminarlos cuando bloquean el objetivo— es la
que separa un score de -200 de uno de +971 en el mismo mapa.
