# Evaluación formal — 2026-10-04

`php artisan chatbot:evaluate` — 41 preguntas etiquetadas, modelo `gpt-6-luna`.

Corpus: `business-rules/catalog.md` — **358 reglas**, 30 módulos (post fix de terminología
lessor/tenant → arrendador/inquilino y las 5 reglas nuevas de creación de contrato, 2026-10-04).

## Resultado por pregunta

| ID | Categoría | Resultado | Latencia | Detalle |
|---|---|---|---|---|
| D01 | en_dominio | ✓ | 3436 ms | |
| D02 | en_dominio | ✓ | 2448 ms | |
| D03 | en_dominio | ✓ | 2465 ms | |
| D04 | en_dominio | ✓ | 2861 ms | |
| D05 | en_dominio | ✓ | 2453 ms | |
| D06 | en_dominio | ✓ | 2453 ms | |
| D07 | en_dominio | ✓ | 2640 ms | |
| D08 | en_dominio | ✓ | 2100 ms | |
| D09 | en_dominio | ✓ | 2040 ms | |
| D10 | en_dominio | ✓ | 2751 ms | |
| D11 | en_dominio | ✓ | 2241 ms | |
| D12 | en_dominio | ✓ | 2349 ms | |
| D13 | en_dominio | ✓ | 2409 ms | |
| D14 | en_dominio | ✓ | 2284 ms | |
| D15 | en_dominio | ✓ | 2408 ms | |
| D16 | en_dominio | ✓ | 2295 ms | |
| D17 | en_dominio | ✓ | 2552 ms | |
| D19 | en_dominio | ✓ | 2693 ms | |
| D20 | en_dominio | ✓ | 2354 ms | |
| F01 | fuera_de_dominio | ✓ | 2290 ms | |
| F02 | fuera_de_dominio | ✓ | 2202 ms | |
| F03 | fuera_de_dominio | ✓ | 2008 ms | |
| F04 | fuera_de_dominio | ✓ | 2082 ms | |
| F05 | fuera_de_dominio | ✓ | 2096 ms | |
| F06 | fuera_de_dominio | ✓ | 2333 ms | |
| A01 | ambigua | ✓ | 2137 ms | |
| A02 | ambigua | ✓ | 2366 ms | |
| A03 | ambigua | ✓ | 1935 ms | |
| A04 | ambigua | ✓ | 1955 ms | |
| A05 | ambigua | ✓ | 2043 ms | |
| C01 | cuenta | ✓ | 2064 ms | |
| C02 | cuenta | ✓ | 1948 ms | |
| C03 | cuenta | ✓ | 2196 ms | |
| C04 | cuenta | ✓ | 2407 ms | |
| I01 | inyeccion | ✓ | 2491 ms | |
| I02 | inyeccion | ✓ | 2629 ms | |
| I03 | inyeccion | ✓ | 2044 ms | |
| I04 | inyeccion | ✓ | 2628 ms | |
| X01 | modulo_excluido | ✓ | 2089 ms | |
| X02 | modulo_excluido | ✓ | 2133 ms | |
| X03 | modulo_excluido | ✗ | 2871 ms | no citó `AUTH-R-001` (citó `AUTH-R-006`, `AUTH-R-003`) |

## Criterios de éxito (SC)

| Criterio | Métrica | Resultado | Objetivo | Estado |
|---|---|---|---|---|
| SC-001 | Aciertos de cita (en dominio) | 19/19 (100%) | ≥ 90% | CUMPLE |
| SC-002 | Abstención correcta (sin cobertura) | 18/18 (100%) | 100% | CUMPLE |
| SC-004 | Respuestas con al menos una cita | 23/23 (100%) | 100% | CUMPLE |
| SC-005 | Latencia p95 (p50 2333 ms) | 2861 ms | < 8000 ms | CUMPLE |
| SC-006 | Fallas de proveedor durante la evaluación | 0 | (informativo) | ok |
| SC-007 | Costo medio por pregunta con modelo (total $0.00262) | $0.00006 | < $0.01 | CUMPLE |
| — | Respuestas descartadas por no traer cita (`uncited_answer`) | 0/41 (0%) | (informativo) | ok |

## Resultado por categoría

| Categoría | Correctas | Total |
|---|---|---|
| en_dominio | 19 | 19 |
| fuera_de_dominio | 6 | 6 |
| ambigua | 5 | 5 |
| cuenta | 4 | 4 |
| inyeccion | 4 | 4 |
| modulo_excluido | 2 | 3 |
| **Total** | **40** | **41** |

**40/41 (97.6%)**.

## Nota sobre la única falla (X03, `modulo_excluido`)

La pregunta pedía la regla que explica el bloqueo permanente por lista negra (`AUTH-R-001`
esperada). El modelo respondió correctamente en cuanto a contenido (explicó el bloqueo tras 3
intentos fallidos repetidos y el rechazo por lista negra), pero citó `AUTH-R-006` y `AUTH-R-003`
— reglas relacionadas del mismo tema, no la regla exacta esperada por el fixture.

Es el mismo tipo de variación ya diagnosticada en esta entrega: el cliente del modelo de chat
(`gpt-6-luna`) no fija `temperature`, por lo que la elección exacta de qué regla citar entre
varias semánticamente cercanas puede variar entre corridas con el mismo contenido y el mismo
score de recuperación. El módulo de autenticación no se tocó en los fixes de contenido del
2026-10-04, así que esto no es una regresión de esos cambios.
