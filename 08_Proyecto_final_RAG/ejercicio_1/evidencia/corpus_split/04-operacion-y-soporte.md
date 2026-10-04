# Catálogo de Reglas de Negocio — Operación y Soporte

Extracto temático de [`corpus_catalog_2026-10-04.md`](../corpus_catalog_2026-10-04.md) (copia íntegra del corpus). Este archivo agrupa los módulos 9, 11, 12, 13, 14, 15, 16 (56 reglas) como documento independiente, para satisfacer el criterio de ≥5 documentos si se exige literalmente ≥5 archivos separados en vez de un solo archivo con módulos internos.

---

## Módulo 9: Gastos

| ID | Tipo | Regla | Prioridad |
|----|------|-------|-----------|
| EXP-R-001 | Restricción | Si un gasto está marcado como "aprobado para pago", entonces no puede modificarse el monto | Alta |
| EXP-R-002 | Restricción | Si se elimina un tipo de gasto, entonces solo puede eliminarse si no tiene gastos asociados registrados | Media |
| EXP-V-001 | Validación | Si se registra un gasto, entonces debe estar asociado a una propiedad o contrato existente y activo | Alta |
| EXP-V-002 | Validación | Si se registra un gasto, entonces el monto debe ser positivo | Alta |
| EXP-V-003 | Validación | Si se programa un gasto recurrente, entonces la frecuencia debe ser válida (mensual, trimestral, anual) | Media |
| EXP-D-001 | Derivación | Si se registra un gasto en una propiedad con múltiples contratos activos, entonces el sistema puede distribuir el gasto entre los contratos de forma proporcional | Media |
| EXP-D-002 | Derivación | Si un gasto programado llega a su fecha de ejecución, entonces el sistema genera automáticamente el registro del gasto y notifica al arrendador | Media |
| EXP-EDGE-001 | Caso de Borde | Si se registra un gasto en una moneda diferente a la del contrato, entonces el sistema aplica la tasa de cambio vigente del día del registro | Media |
| EXP-EDGE-002 | Caso de Borde | Si un gasto programado no puede ejecutarse porque la propiedad fue inactivada, entonces el sistema cancela los gastos programados futuros y notifica al arrendador | Media |

---

## Módulo 11: Servicios

| ID | Tipo | Regla | Prioridad |
|----|------|-------|-----------|
| SRV-R-001 | Restricción | Si un tipo de servicio es eliminado, entonces solo puede eliminarse si no tiene servicios activos en contratos | Alta |
| SRV-R-002 | Restricción | Si un servicio está incluido en un contrato activo, entonces no puede ser desactivado sin actualizar el contrato primero | Alta |
| SRV-V-001 | Validación | Si se asigna un servicio a un contrato, entonces el costo del servicio debe ser mayor a cero | Alta |
| SRV-V-002 | Validación | Si se configura la distribución de un servicio entre múltiples inquilinos, entonces la suma de los porcentajes debe ser igual al 100% | Alta |
| SRV-D-001 | Derivación | Si se asigna un servicio a un contrato con múltiples inquilinos sin distribución explícita, entonces el sistema distribuye el costo equitativamente | Media |
| SRV-D-002 | Derivación | Si el costo de un servicio cambia, entonces el sistema recalcula el monto a cobrar a cada inquilino para el siguiente período | Media |
| SRV-EDGE-001 | Caso de Borde | Si un inquilino cubre el 100% del costo de un servicio, entonces los demás inquilinos quedan exentos de ese cargo sin configuración adicional | Media |

---

## Módulo 12: Incidencias

| ID | Tipo | Regla | Prioridad |
|----|------|-------|-----------|
| INC-R-001 | Restricción | Si una incidencia está cerrada/resuelta, entonces no puede reabrirse; debe crearse una nueva incidencia | Media |
| INC-R-002 | Restricción | Si un inquilino no tiene contrato activo, entonces no puede crear incidencias en el sistema | Alta |
| INC-V-001 | Validación | Si se crea una incidencia, entonces debe tener al menos un título descriptivo y estar asociada a un contrato o propiedad | Alta |
| INC-V-002 | Validación | Si se agrega un mensaje a una incidencia, entonces el mensaje no puede estar vacío | Baja |
| INC-D-001 | Derivación | Si se crea una incidencia, entonces el sistema notifica automáticamente al arrendador o administrador correspondiente | Alta |
| INC-D-002 | Derivación | Si se agrega un mensaje a una incidencia, entonces el sistema marca el mensaje como "no leído" para el destinatario | Media |
| INC-D-003 | Derivación | Si el arrendador responde a una incidencia, entonces el sistema notifica al inquilino que hay una nueva respuesta | Media |
| INC-EDGE-001 | Caso de Borde | Si una incidencia lleva más de 7 días sin respuesta del arrendador, entonces el sistema envía una notificación de escalamiento | Media |
| INC-EDGE-002 | Caso de Borde | Si el inquilino envía múltiples mensajes en menos de 1 minuto, entonces el sistema aplica un límite de envío para evitar saturación | Baja |

---

## Módulo 13: Inventario

| ID | Tipo | Regla | Prioridad |
|----|------|-------|-----------|
| INV2-R-001 | Restricción | Si un ítem de inventario está asignado a una propiedad activa, entonces no puede eliminarse del sistema | Media |
| INV2-R-002 | Restricción | Si una categoría de inventario tiene ítems asociados, entonces no puede eliminarse | Media |
| INV2-V-001 | Validación | Si se registra un ítem de inventario, entonces debe tener nombre, categoría y una cantidad mayor o igual a cero | Media |
| INV2-V-002 | Validación | Si se asigna un ítem a un almacén, entonces el almacén debe existir y estar activo | Media |
| INV2-D-001 | Derivación | Si se registra una entrada de inventario, entonces el sistema actualiza automáticamente el stock disponible | Media |
| INV2-D-002 | Derivación | Si se registra una salida de inventario, entonces el sistema verifica que el stock disponible sea suficiente antes de reducirlo | Media |
| INV2-EDGE-001 | Caso de Borde | Si el stock de un ítem llega a cero, entonces el sistema genera una alerta de reabastecimiento si así está configurado | Baja |
| INV2-EDGE-002 | Caso de Borde | Si se transfiere inventario entre almacenes, entonces el sistema registra ambos movimientos (salida del origen y entrada al destino) de forma atómica | Media |

---

## Módulo 14: Archivos

| ID | Tipo | Regla | Prioridad |
|----|------|-------|-----------|
| FILE-R-001 | Restricción | Si un archivo está vinculado a un contrato activo o factura timbrada, entonces no puede eliminarse permanentemente | Alta |
| FILE-R-002 | Restricción | Si el archivo excede el tamaño máximo permitido, entonces la carga es rechazada | Media |
| FILE-V-001 | Validación | Si se carga un archivo, entonces el tipo de archivo debe estar en la lista de tipos permitidos por el sistema | Alta |
| FILE-V-002 | Validación | Si se asigna un archivo a una entidad (contrato, propiedad, etc.), entonces la entidad debe existir y estar activa | Alta |
| FILE-D-001 | Derivación | Si se carga un archivo exitosamente, entonces el sistema genera una URL de acceso y registra los metadatos (nombre, tamaño, tipo, fecha) | Alta |
| FILE-D-002 | Derivación | Si el estado de un archivo cambia (e.g., de "borrador" a "firmado"), entonces el sistema registra el cambio en el historial de estados | Media |
| FILE-EDGE-001 | Caso de Borde | Si se intenta acceder a un archivo eliminado lógicamente mediante su URL, entonces el sistema retorna un mensaje de "archivo no disponible" | Media |
| FILE-EDGE-002 | Caso de Borde | Si se intenta cargar un archivo con el mismo nombre que uno existente, entonces el sistema genera un nombre único automáticamente para evitar sobreescritura | Baja |

---

## Módulo 15: Notificaciones

| ID | Tipo | Regla | Prioridad |
|----|------|-------|-----------|
| NOTIF-R-001 | Restricción | Si un usuario tiene desactivadas las notificaciones por correo, entonces el sistema no le envía correos pero sí registra la notificación en el sistema | Media |
| NOTIF-R-002 | Restricción | Si el correo electrónico de destino está en la lista de correos bloqueados, entonces el envío se omite y se registra la omisión | Alta |
| NOTIF-V-001 | Validación | Si se genera una notificación, entonces debe estar asociada a un usuario existente y activo | Alta |
| NOTIF-D-001 | Derivación | Si se genera un evento de negocio (pago registrado, contrato creado, etc.), entonces el sistema crea las notificaciones correspondientes para los usuarios involucrados | Alta |
| NOTIF-D-002 | Derivación | Si una notificación es enviada, entonces el sistema registra la fecha/hora de envío y el canal utilizado (correo, en sistema) | Media |
| NOTIF-D-003 | Derivación | Si el usuario marca una notificación como leída, entonces el sistema actualiza el estado y elimina el indicador de no leído | Baja |
| NOTIF-EDGE-001 | Caso de Borde | Si el servicio de correo falla, entonces el sistema reintenta el envío hasta 3 veces antes de marcar la notificación como "fallo de entrega" | Alta |
| NOTIF-EDGE-002 | Caso de Borde | Si se generan múltiples eventos en un período corto para el mismo usuario, entonces el sistema puede agrupar las notificaciones para evitar saturación | Media |

---

## Módulo 16: Bitácora (Binnacle)

| ID | Tipo | Regla | Prioridad |
|----|------|-------|-----------|
| BIN-R-001 | Restricción | Si un registro de bitácora ha sido creado, entonces nunca puede modificarse ni eliminarse (inmutabilidad del log) | Alta |
| BIN-R-002 | Restricción | Si la dirección IP real del cliente no puede determinarse, entonces el sistema registra la IP del proxy más cercano disponible | Alta |
| BIN-V-001 | Validación | Si se registra un movimiento en bitácora, entonces debe incluir: usuario, acción, fecha/hora, IP de origen y recurso afectado | Alta |
| BIN-D-001 | Derivación | Si se realiza cualquier operación de escritura en recursos sensibles (contratos, pagos, facturas), entonces el sistema crea automáticamente un registro en bitácora | Alta |
| BIN-D-002 | Derivación | Si el sistema detecta un intento de acceso fallido o sospechoso, entonces genera un registro de bitácora con nivel de alerta elevado | Alta |
| BIN-D-003 | Derivación | Si se registra un movimiento de dinero (pago, reembolso, comisión), entonces el sistema crea un registro en el módulo de movimientos financieros | Alta |
| BIN-EDGE-001 | Caso de Borde | Si el sistema no puede escribir en la bitácora por fallo de almacenamiento, entonces la operación original debe fallar para mantener la consistencia del registro | Alta |

---
