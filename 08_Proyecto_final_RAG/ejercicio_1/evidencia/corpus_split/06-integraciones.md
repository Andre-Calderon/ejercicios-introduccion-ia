# Catálogo de Reglas de Negocio — Integraciones

Extracto temático de [`corpus_catalog_2026-10-04.md`](../corpus_catalog_2026-10-04.md) (copia íntegra del corpus). Este archivo agrupa los módulos 28, 29 (29 reglas) como documento independiente, para satisfacer el criterio de ≥5 documentos si se exige literalmente ≥5 archivos separados en vez de un solo archivo con módulos internos.

---

## Módulo 28: API de Integración

| ID | Tipo | Regla | Prioridad |
|----|------|-------|-----------|
| INTAPI-R-001 | Restricción | Si una credencial de integración consulta cualquier recurso, entonces solo puede acceder a datos de la cuenta a la que pertenece la credencial | Alta |
| INTAPI-R-002 | Restricción | Si se solicita cualquier operación de escritura (crear, actualizar o eliminar) bajo la API de Integración, entonces la operación no existe y no puede realizarse | Alta |
| INTAPI-R-003 | Restricción | Si una credencial de integración consulta un recurso que no tiene habilitado en su lista de accesos, entonces la solicitud es rechazada aunque la credencial sea válida y esté vigente | Alta |
| INTAPI-V-001 | Validación | Si una solicitud a la API de Integración no incluye una credencial válida (API Key y Secret), entonces la solicitud es rechazada | Alta |
| INTAPI-V-002 | Validación | Si una credencial de integración está revocada o expirada, entonces la solicitud es rechazada aunque la credencial exista | Alta |
| INTAPI-D-001 | Derivación | Si se genera o regenera el secreto de una credencial de integración, entonces el valor en texto plano se muestra una única vez y no puede recuperarse posteriormente | Alta |
| INTAPI-D-002 | Derivación | Si se revoca una credencial de integración, entonces el rechazo de sus solicitudes aplica de forma inmediata, sin periodo de gracia | Alta |
| INTAPI-D-003 | Derivación | Si se crea una credencial de integración sin especificar qué recursos puede consultar, entonces se habilitan todos los recursos disponibles por default; el administrador puede restringir o ampliar esta lista en cualquier momento posterior | Media |
| INTAPI-D-004 | Derivación | Si se crea una credencial de integración sin especificar un modo, entonces se emite en modo "en vivo"; el identificador de la credencial siempre indica visualmente si es de prueba o en vivo | Media |
| INTAPI-EDGE-001 | Caso de Borde | Si una cuenta no tiene registros para el recurso o filtro solicitado, entonces la API de Integración responde con una lista vacía y no con un error | Media |
| INTAPI-EDGE-002 | Caso de Borde | Si el identificador de un recurso solicitado pertenece a otra cuenta, entonces la API de Integración responde como si el recurso no existiera | Alta |

---

## Módulo 29: Gestión de Proveedores

| ID | Tipo | Regla | Prioridad |
|----|------|-------|-----------|
| SUPP-R-001 | Restricción | Si un proveedor tiene gastos vinculados, entonces no se le puede eliminar del directorio | Alta |
| SUPP-R-002 | Restricción | Si una categoría de servicio tiene proveedores asociados, entonces no se le puede eliminar del catálogo | Alta |
| SUPP-R-003 | Restricción | Si una categoría de servicio es una categoría por defecto del sistema, entonces no se le puede eliminar | Media |
| SUPP-R-004 | Restricción | Si una categoría de servicio es una categoría por defecto del sistema, entonces no se le puede modificar | Media |
| SUPP-R-005 | Restricción | Si un proveedor es eliminado por una vía distinta al flujo normal, entonces los gastos que tenía vinculados conservan su registro y pierden únicamente la referencia al proveedor | Media |
| SUPP-V-001 | Validación | Si el proveedor es de tipo "Persona", entonces el nombre es obligatorio | Alta |
| SUPP-V-002 | Validación | Si el proveedor es de tipo "Empresa", entonces el nombre de la empresa es obligatorio | Alta |
| SUPP-V-003 | Validación | Si se asignan categorías de servicio a un proveedor, entonces cada categoría debe existir previamente en el catálogo de categorías de proveedor | Media |
| SUPP-V-004 | Validación | Si se captura la CLABE interbancaria del proveedor, entonces debe tener exactamente 18 caracteres | Media |
| SUPP-V-005 | Validación | Si se captura una calificación del proveedor, entonces debe ser un valor entre 1 y 5 | Baja |
| SUPP-V-006 | Validación | Si se captura el correo electrónico del proveedor, entonces debe tener formato de email válido | Baja |
| SUPP-D-001 | Derivación | Si un proveedor es de tipo "Empresa" y tiene nombre de empresa capturado, entonces el nombre a mostrar del proveedor es el nombre de la empresa; en cualquier otro caso, es el nombre de la persona de contacto | Media |
| SUPP-D-002 | Derivación | Si se consulta el historial de gastos de un proveedor, entonces el sistema deriva automáticamente el total de gastos y el monto acumulado a partir de los gastos vinculados | Media |
| SUPP-D-003 | Derivación | Si se edita un proveedor sin enviar la lista de categorías, entonces se conservan las categorías ya asignadas; si se envía una lista de categorías (incluida una vacía), entonces se reemplaza por completo el conjunto de categorías asignadas | Media |
| SUPP-D-004 | Derivación | Si se crea, edita o elimina un proveedor, entonces la operación se registra automáticamente en la bitácora del sistema | Media |
| SUPP-EDGE-001 | Caso de Borde | Si una categoría de servicio está marcada como categoría por defecto del sistema, entonces está disponible para todos los usuarios administradores sin importar quién la haya creado | Media |
| SUPP-EDGE-002 | Caso de Borde | Si dos proveedores del mismo usuario administrador tienen el mismo nombre, entonces el sistema permite el registro de ambos sin restricción de unicidad | Baja |
| SUPP-EDGE-003 | Caso de Borde | Si un proveedor se registra sin asignarle ninguna categoría de servicio, entonces el sistema lo permite y las categorías pueden asignarse posteriormente | Baja |

---
