# Ejercicio 2 — Descripción PEAS de agentes inteligentes

### 1. Asistente virtual de voz

- **Performance:** tasa de reconocimiento de intención correcta, latencia entre orden y respuesta, porcentaje de tareas completadas sin repetir la orden, tasa de false triggers.
- **Environment:** el hogar del usuario, parcialmente observable (solo capta lo que llega al micrófono, no ve todo lo que pasa en el cuarto), estocástico (ruido de fondo, acentos, interrupciones impredecibles), secuencial (una orden puede depender del contexto de la conversación anterior), dinámico (otras personas hablan o dispositivos cambian de estado mientras el asistente procesa), discreto en el conjunto de comandos que reconoce pero con entrada de audio continua.
- **Actuators:** reproducir audio de respuesta por el altavoz, encender/apagar dispositivos IoT emparejados, enviar solicitudes a APIs externas (clima, música, compras), encender/apagar su propio micrófono, mostrar información en pantalla si el dispositivo tiene una.
- **Sensors:** micrófono, estado reportado de los dispositivos IoT conectados, reloj y calendario del sistema, historial de interacción reciente del usuario.

Justificación: es parcialmente observable porque el micrófono solo capta sonido, no intención ni contexto visual; es estocástico porque el mismo comando de voz puede variar en pronunciación, ruido o interrupciones; es secuencial porque responder "sí" después de "¿quieres que la repita?" solo tiene sentido si recuerda la pregunta anterior.

---

### 2. Robot aspirador doméstico

- **Performance:** porcentaje de área limpiada por sesión, consumo de batería por metro cuadrado cubierto, número de colisiones u obstáculos golpeados, veces que queda atascado sin poder volver a la base, tiempo total para completar la limpieza de un área conocida.
- **Environment:** el hogar del usuario o área de preferencia, parcialmente observable (el robot solo percibe lo que tiene alrededor, no el mapa completo de la casa en cada instante), dinámico (por si hay mascotas y personas se mueven mientras limpia, muebles pueden desplazarse), continuo en el movimiento físico pero discretizable en celdas para la planeación de ruta.
- **Actuators:** avanzar, girar hacia la izquierda/derecha, activar/desactivar el motor de succión, regresar a la base de carga, vaciar el contenedor.
- **Sensors:** sensor de choque, infrarrojo o láser, nivel de batería, sensor de caída, sensor de humedad.

Justificación: es parcialmente observable porque sus sensores solo cubren el área inmediata alrededor del robot, no toda la casa a la vez; es dinámico porque personas y mascotas se mueven mientras limpia, sin que el robot controle esos cambios; es continuo en el movimiento físico real pero se discretiza en celdas para poder planear la ruta de limpieza.

---

### 3. Sistema de recomendación de streaming

- **Performance:** tasa de clics sobre recomendaciones, tiempo total de reproducción por sesión, tasa de retención/cancelación de suscripción, diversidad del catálogo mostrado, error de predicción de calificación.
- **Environment:** la plataforma digital y el comportamiento del usuario, parcialmente observable (no conoce el contexto real del usuario, solo su actividad registrada), estocástico (las preferencias humanas no son perfectamente predecibles), secuencial (una recomendación influye en el historial que alimenta la siguiente), dinámico (el catálogo cambia y otros usuarios generan tendencias mientras el sistema decide), discreto (conjunto finito de títulos disponibles para recomendar).
- **Actuators:** ordenar la lista de resultados, mostrar u ocultar un título en portada, enviar notificación push, reproducir automáticamente el siguiente contenido (autoplay), elegir la miniatura mostrada.
- **Sensors:** historial de reproducción, calificaciones/likes explícitos, tiempo de visualización por título, consultas de búsqueda, dispositivo y hora de acceso.

Justificación: es parcialmente observable porque el sistema no conoce el estado de ánimo ni el contexto real del usuario, solo señales indirectas de su actividad; es secuencial porque lo que se recomienda hoy cambia el historial que se usa para recomendar mañana; es dinámico porque el catálogo y las tendencias de otros usuarios cambian de forma continua e independiente del sistema.

---

### 4. Vehículo autónomo en ciudad

- **Performance:** kilómetros recorridos sin intervención humana, número de colisiones o cuasi-colisiones, cumplimiento de límites de velocidad y señales de tránsito, tiempo de viaje comparado con la ruta óptima, suavidad de aceleración/frenado (confort del pasajero).
- **Environment:** calles urbanas con tráfico y peatones, parcialmente observable (oclusiones por otros vehículos, ángulos ciegos), estocástico (comportamiento impredecible de peatones y otros conductores), secuencial (cada decisión de manejo afecta la situación siguiente), dinámico (el tráfico cambia constantemente sin esperar la decisión del vehículo), continuo (posición, velocidad y ángulos de giro son valores continuos).
- **Actuators:** acelerar, frenar, girar el volante, activar direccionales, activar luces o bocina, cambiar de carril.
- **Sensors:** cámaras, LIDAR, radar, GPS, velocímetro/odómetro, sensores de proximidad/ultrasonido.

Justificación: es parcialmente observable porque otros vehículos y obstáculos pueden ocultar información relevante (un peatón detrás de un camión estacionado, por ejemplo); es estocástico porque no se puede predecir con certeza qué hará un peatón o conductor; es dinámico porque el tráfico sigue moviéndose mientras el vehículo calcula su siguiente acción.

---

### 5. Agente de trading algorítmico en bolsa

- **Performance:** retorno ajustado a riesgo, costos de transacción incurridos, tasa de profit.
- **Environment:** el mercado financiero, parcialmente observable (no conoce las órdenes ni intenciones de otros participantes), estocástico (los precios no son perfectamente predecibles), secuencial (una operación afecta la posición y las decisiones futuras), dinámico (los precios cambian en tiempo real sin esperar la decisión del agente), continuo (precios y volúmenes son valores continuos).
- **Actuators:** enviar orden de compra, enviar orden de venta, definir la cantidad a operar, definir el tipo de orden (mercado/límite), cancelar una orden pendiente.
- **Sensors:** cotizaciones en tiempo real, volumen de operaciones, libro de órdenes, feeds de noticias, indicadores técnicos históricos.

Justificación: es parcialmente observable porque el agente no conoce las órdenes ocultas ni las intenciones de otros participantes del mercado; es estocástico porque el precio futuro de un activo no está determinado por la información disponible; es dinámico porque el mercado sigue moviéndose en tiempo real mientras el agente decide qué operación ejecutar.

---

### 6. Sistema de diagnóstico médico asistido por IA

- **Performance:** sensibilidad y especificidad del diagnóstico sugerido, tiempo hasta emitir la recomendación, tasa de falsos negativos (uslamente críticas en medicina), tasa de aceptación de la sugerencia por parte del médico.
- **Environment:** historial clínico e imágenes de un paciente, parcialmente observable (no tiene toda la información clínica, solo lo registrado o lo que el médico ingresa), estocástico (los mismos síntomas no siempre corresponden a la misma enfermedad), episódico en la evaluación de un estudio puntual (cada imagen o análisis puede juzgarse de forma relativamente independiente) aunque el historial del paciente le da un componente secuencial.
- **Actuators:** mostrar el diagnóstico probable con nivel de confianza, resaltar regiones sospechosas en una imagen médica, generar una alerta de urgencia, sugerir exámenes adicionales.
- **Sensors:** imágenes médicas (rayos X, resonancia, tomografía), historial clínico electrónico, resultados de laboratorio, síntomas reportados por el paciente o médico.

Justificación: es parcialmente observable porque el sistema solo ve lo que quedó registrado digitalmente, no el estado real completo del paciente; es estocástico porque los mismos síntomas o hallazgos en una imagen pueden corresponder a distintas condiciones con distinta probabilidad; tiene un componente secuencial porque el historial previo del paciente puede cambiar la interpretación de un nuevo estudio.

---

### 7. Dron de inspección de infraestructura

- **Performance:** porcentaje de la estructura efectivamente inspeccionada, tasa de defectos detectados correctamente, tiempo de batería usado por inspección, número de colisiones o incidentes de vuelo.
- **Environment:** la infraestructura física (puente, tubería, línea eléctrica) y su entorno, parcialmente observable (la cámara solo cubre una parte de la estructura a la vez), estocástico (viento e iluminación cambiante afectan el vuelo y la calidad de la imagen), dinámico (las condiciones climáticas cambian durante el vuelo sin que el dron lo controle), continuo (posición y movimiento en el espacio 3D).
- **Actuators:** controlar los motores/rotores para moverse en los tres ejes, ajustar el zoom y ángulo de la cámara, capturar imagen o video, encender luces, regresar a la base.
- **Sensors:** cámara visible/térmica, GPS, Acelerómetro, giroscopio, sensores de proximidad/altura, nivel de batería.

Justificación: es parcialmente observable porque el dron solo capta la parte de la estructura que su cámara enfoca en cada momento, no la vista completa; es estocástico porque el viento y la iluminación varían de forma no controlada y afectan tanto el vuelo como la calidad de detección; es dinámico porque las condiciones climáticas siguen cambiando mientras el dron ejecuta su recorrido de inspección.

---

### 8. Agente jugador de ajedrez

- **Performance:** partidas ganadas/perdidas/empatadas, rating Elo alcanzado, profundidad de búsqueda lograda dentro del tiempo de juego asignado.
- **Environment:** el tablero de ajedrez, totalmente observable (ambos jugadores ven la posición completa de todas las piezas), determinista (no hay elementos de azar en las reglas), secuencial (cada jugada afecta directamente las opciones futuras), estático (el tablero no cambia mientras el agente decide su jugada), discreto (número finito de piezas, casillas y movimientos posibles).
- **Actuators:** mover una pieza de una casilla a otra, capturar una pieza, enrocar, coronar un peón, ofrecer o aceptar tablas, rendirse.
- **Sensors:** representación del estado del tablero (posición de cada pieza), reloj de la partida, último movimiento realizado por el oponente.

Justificación: es totalmente observable porque ambos jugadores tienen acceso a toda la información del tablero, sin partes ocultas; es determinista porque no hay azar, cada jugada produce un único resultado posible; es estático porque el tablero permanece igual mientras el agente calcula su siguiente movimiento, a diferencia de un entorno en tiempo real.

---
