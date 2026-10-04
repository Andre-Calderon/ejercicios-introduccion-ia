# Catálogo de Reglas de Negocio — Catálogos y Configuración

Extracto temático de [`corpus_catalog_2026-10-04.md`](../corpus_catalog_2026-10-04.md) (copia íntegra del corpus). Este archivo agrupa los módulos 17, 18, 19, 20, 21, 22, 23, 25, 26 (74 reglas) como documento independiente, para satisfacer el criterio de ≥5 documentos si se exige literalmente ≥5 archivos separados en vez de un solo archivo con módulos internos.

---

## Módulo 17: Paquetes & Suscripciones

| ID | Tipo | Regla | Prioridad |
|----|------|-------|-----------|
| PKG-R-001 | Restricción | Si un usuario tiene una suscripción activa, entonces no puede contratar otra suscripción del mismo tipo simultáneamente | Alta |
| PKG-R-002 | Restricción | Si un paquete está deprecado, entonces no puede asignarse a nuevos usuarios aunque permanezca visible para usuarios existentes | Media |
| PKG-V-001 | Validación | Si se asigna un paquete a un usuario, entonces el paquete debe existir y estar activo en el catálogo | Alta |
| PKG-V-002 | Validación | Si se configura la tarifa de un paquete, entonces el precio debe ser mayor o igual a cero | Alta |
| PKG-D-001 | Derivación | Si una suscripción llega a su fecha de renovación, entonces el sistema envía un recordatorio al usuario con 5 días de anticipación | Alta |
| PKG-D-002 | Derivación | Si una suscripción es cancelada, entonces el sistema calcula el período de gracia y la fecha efectiva de fin del acceso | Alta |
| PKG-D-003 | Derivación | Si el pago de renovación falla, entonces el sistema cambia el estado de la suscripción a "pago pendiente" y notifica al usuario | Alta |
| PKG-EDGE-001 | Caso de Borde | Si el usuario cancela la suscripción durante el período pagado, entonces conserva el acceso hasta el final del período pagado | Media |
| PKG-EDGE-002 | Caso de Borde | Si el paquete contratado tiene límite de propiedades y el usuario intenta agregar más del límite, entonces la operación se rechaza indicando el límite alcanzado | Alta |
| PKG-EDGE-003 | Caso de Borde | Si el usuario actualiza la cantidad de propiedades de su suscripción faltando menos de 2 días para el corte del periodo, entonces se cobra de inmediato el periodo completo del plan nuevo (sin prorrateo), el nuevo periodo inicia ese día, el acceso anterior finaliza el día previo y el pago se registra como pago de suscripción | Alta |

---

## Módulo 18: Plataforma (Multi-tenant)

| ID | Tipo | Regla | Prioridad |
|----|------|-------|-----------|
| PLAT-R-001 | Restricción | Si una plataforma es desactivada, entonces todos los usuarios asociados pierden acceso al sistema | Alta |
| PLAT-R-002 | Restricción | Si un plan de plataforma tiene usuarios activos, entonces no puede eliminarse | Alta |
| PLAT-V-001 | Validación | Si se configura una plataforma, entonces debe tener un nombre único en el sistema | Alta |
| PLAT-V-002 | Validación | Si se asigna un plan a una plataforma, entonces el plan debe existir en el catálogo de planes | Alta |
| PLAT-D-001 | Derivación | Si se crea una plataforma nueva, entonces el sistema genera la configuración base con valores por defecto | Media |
| PLAT-D-002 | Derivación | Si un usuario se asocia a una plataforma, entonces hereda los permisos base definidos en el plan de la plataforma | Alta |
| PLAT-EDGE-001 | Caso de Borde | Si un usuario intenta acceder a recursos de una plataforma diferente a la suya, entonces el acceso es denegado independientemente de sus roles | Alta |

---

## Módulo 19: Reportes & Exportaciones

| ID | Tipo | Regla | Prioridad |
|----|------|-------|-----------|
| RPT-R-001 | Restricción | Si el usuario no tiene permisos para ver datos financieros de un arrendador, entonces los reportes de ese arrendador no son accesibles | Alta |
| RPT-R-002 | Restricción | Si el rango de fechas solicitado excede el máximo permitido por el sistema, entonces la solicitud es rechazada | Media |
| RPT-R-003 | Restricción | Si el usuario no tiene rol administrativo, entonces no puede solicitar la exportación de los indicadores financieros | Alta |
| RPT-V-001 | Validación | Si se solicita un reporte, entonces la fecha de inicio debe ser anterior o igual a la fecha de fin | Alta |
| RPT-V-002 | Validación | Si se solicita una exportación, entonces el formato solicitado debe ser compatible (PDF, XLSX, CSV) | Media |
| RPT-D-001 | Derivación | Si se genera un reporte de pagos, entonces el sistema consolida todos los pagos del período con sus desgloses (renta, servicios, comisiones, mora) | Alta |
| RPT-D-002 | Derivación | Si se exporta un reporte, entonces el sistema genera el archivo en el formato solicitado y proporciona un enlace de descarga temporal | Media |
| RPT-D-003 | Derivación | Si se solicita la exportación anual de indicadores financieros, entonces el sistema genera un archivo con los once indicadores anuales, sus comparativas contra el año anterior y la conciliación del ingreso recurrente anualizado, y omite los desgloses por cliente que solo aplican al periodo mensual | Media |
| RPT-D-004 | Derivación | Si se exporta el periodo anual del año en curso, entonces el archivo señala que los datos son parciales con su fecha de corte y advierte que el ingreso recurrente anualizado y los ingresos acumulados a la fecha no son comparables hasta el cierre del año | Media |
| RPT-D-005 | Derivación | Si se genera el reporte de ingresos con desglose fiscal, entonces por cada pago o abono se muestra subtotal, IVA, retenciones de IVA/ISR e impuestos locales con la misma prioridad usada en el recibo (factura vigente sobre el desglose calculado del pago), y si el desglose de recibo está desactivado para el contrato o servicio, sólo se muestra el total | Media |
| RPT-EDGE-001 | Caso de Borde | Si el reporte contiene un volumen muy alto de datos, entonces el sistema procesa la exportación en segundo plano y notifica al usuario cuando esté listo | Media |

---

## Módulo 20: Catálogos Generales

| ID | Tipo | Regla | Prioridad |
|----|------|-------|-----------|
| CAT-R-001 | Restricción | Si un elemento de catálogo está siendo usado por registros activos, entonces no puede eliminarse | Alta |
| CAT-R-002 | Restricción | Si un catálogo del sistema es de solo lectura (catálogos del SAT), entonces los usuarios no pueden modificar sus elementos | Alta |
| CAT-V-001 | Validación | Si se agrega un estado al catálogo, entonces debe estar asociado a un país existente | Alta |
| CAT-V-002 | Validación | Si se agrega un municipio, entonces debe estar asociado a un estado existente en el catálogo | Alta |
| CAT-D-001 | Derivación | Si se selecciona un país en un formulario, entonces el sistema filtra automáticamente los estados disponibles para ese país | Media |
| CAT-D-002 | Derivación | Si se selecciona un estado, entonces el sistema filtra automáticamente los municipios disponibles para ese estado | Media |
| CAT-EDGE-001 | Caso de Borde | Si un catálogo del SAT es actualizado (nuevos códigos, deprecación de claves), entonces el sistema importa la nueva versión sin eliminar los registros históricos | Alta |

---

## Módulo 21: Catálogos Fiscales (CFDI)

| ID | Tipo | Regla | Prioridad |
|----|------|-------|-----------|
| CFDICAT-R-001 | Restricción | Si una clave del catálogo fiscal está deprecada, entonces no puede usarse en nuevas facturas pero permanece en el historial de facturas previas | Alta |
| CFDICAT-V-001 | Validación | Si se selecciona un uso de CFDI, entonces debe corresponder a un uso válido para el tipo de persona del receptor (física/moral) | Alta |
| CFDICAT-V-002 | Validación | Si se selecciona un régimen fiscal, entonces debe ser válido para el tipo de persona del emisor | Alta |
| CFDICAT-V-003 | Validación | Si se utiliza una clave de producto o servicio, entonces debe existir en el catálogo vigente | Alta |
| CFDICAT-D-001 | Derivación | Si se selecciona el uso de CFDI correspondiente a arrendamiento, entonces el sistema pre-selecciona automáticamente las claves fiscales recomendadas para el sector inmobiliario | Media |
| CFDICAT-EDGE-001 | Caso de Borde | Si el fisco actualiza el catálogo y depreca un uso activo, entonces las facturas existentes no son afectadas pero el sistema bloquea el uso deprecado en nuevas emisiones | Alta |

---

## Módulo 22: Formatos de Documentos

| ID | Tipo | Regla | Prioridad |
|----|------|-------|-----------|
| FMT-R-001 | Restricción | Si un formato está asignado a contratos activos, entonces no puede eliminarse | Media |
| FMT-R-002 | Restricción | Si un formato es marcado como "oficial", entonces solo administradores pueden modificarlo | Alta |
| FMT-V-001 | Validación | Si se crea un formato de documento, entonces debe tener un nombre único y un tipo definido (contrato, recibo, carta) | Media |
| FMT-D-001 | Derivación | Si se genera un documento basado en un formato, entonces el sistema sustituye automáticamente las variables del formato con los datos del contrato o propiedad correspondiente | Alta |
| FMT-EDGE-001 | Caso de Borde | Si una variable del formato no tiene valor disponible en el contexto del contrato, entonces el sistema sustituye la variable con un espacio en blanco o valor por defecto configurado | Media |

---

## Módulo 23: Sellos Digitales (Stamps)

| ID | Tipo | Regla | Prioridad |
|----|------|-------|-----------|
| STAMP-R-001 | Restricción | Si un sello fue aplicado a un documento, entonces el documento no puede modificarse sin invalidar el sello | Alta |
| STAMP-R-002 | Restricción | Si el usuario no tiene un paquete activo que incluya sellos, entonces no puede aplicar sellos a documentos | Alta |
| STAMP-V-001 | Validación | Si se aplica un sello, entonces el usuario debe autenticarse con su contraseña o código TOTP para confirmar la acción | Alta |
| STAMP-D-001 | Derivación | Si se aplica un sello, entonces el sistema registra en el historial: quién selló, cuándo, qué documento y desde qué origen | Alta |
| STAMP-D-002 | Derivación | Si se consume un sello del paquete, entonces el sistema actualiza el contador de sellos disponibles del usuario | Alta |
| STAMP-D-003 | Derivación | Si se confirma el pago de renovación de una suscripción, entonces el sistema asigna al usuario los sellos gratuitos del periodo contratado según propiedades y frecuencia, igual que en la contratación inicial | Alta |
| STAMP-D-004 | Derivación | Si se registra un movimiento de sellos en el historial, entonces el sistema distingue el origen como asignación manual, compra, remoción, asignación por contratación o asignación por renovación | Alta |
| STAMP-D-005 | Derivación | Si se registra o edita un pago de suscripción, entonces la asignación de sellos depende solo del concepto de sello: en aumento se asignan sellos del diferencial de propiedades por los meses restantes del periodo vigente actual; en disminución no se asignan sellos; en suscripción nueva, renovación o adelanto se asignan sellos de todas las propiedades por la frecuencia del periodo registrado. Esta regla no altera el concepto de pago ni la suscripción en la pasarela | Alta |
| STAMP-EDGE-001 | Caso de Borde | Si el usuario intenta sellar un documento cuando su contador de sellos es cero, entonces el sistema sugiere adquirir más sellos o un plan con mayor capacidad | Media |

---

## Módulo 25: Configuración del Sistema

| ID | Tipo | Regla | Prioridad |
|----|------|-------|-----------|
| SYSCFG-R-001 | Restricción | Si la configuración de facturación del sistema no está completa, entonces el sistema no puede operar en modo de producción con CFDI | Alta |
| SYSCFG-R-002 | Restricción | Si se modifica la configuración financiera del sistema, entonces el cambio aplica solo a operaciones futuras, no retroactivamente | Alta |
| SYSCFG-V-001 | Validación | Si se configura la tasa de cambio, entonces el valor debe ser positivo y mayor a cero | Alta |
| SYSCFG-V-002 | Validación | Si se configura el período de gracia para verificación de correo, entonces el valor debe ser un número positivo de días | Alta |
| SYSCFG-D-001 | Derivación | Si se actualiza la tasa de cambio del sistema, entonces el sistema registra el historial de tasas para auditoría | Media |
| SYSCFG-D-002 | Derivación | Si se cambia la configuración de un módulo del sistema, entonces el sistema registra en bitácora quién realizó el cambio, qué cambió y cuándo | Alta |
| SYSCFG-D-003 | Derivación | Si el sistema genera una respuesta con campos de fecha y hora, entonces los valores se expresan en UTC con formato ISO 8601 (AAAA-MM-DDTHH:mm:ss.ffffffZ) | Alta |
| SYSCFG-EDGE-001 | Caso de Borde | Si la configuración del sistema es importada desde un archivo externo, entonces el sistema valida todos los campos antes de aplicar; si alguno falla, se rechaza toda la importación | Alta |
| SYSCFG-EDGE-002 | Caso de Borde | Si un campo de fecha-hora en la respuesta tiene valor nulo, entonces el sistema retorna null sin intentar conversión de zona horaria | Media |
| SYSCFG-R-003 | Restricción | Si se guarda la configuración de facturación del sistema con certificado válido, entonces el sistema se registra (primera vez) o se actualiza (si ya estaba registrado) como cliente en el servicio de timbrado externo, sin crear duplicados | Alta |
| SYSCFG-R-004 | Restricción | Si la comunicación con el servicio de timbrado externo falla al guardar la configuración del sistema, entonces la configuración local se conserva y el registro queda pendiente para reintento automático, sin bloquear el guardado | Alta |
| SYSCFG-D-004 | Derivación | Si el registro del sistema en el servicio externo queda pendiente o fallido, entonces el sistema lo reintenta automáticamente usando el certificado y la contraseña ya almacenados, sin requerir que el administrador vuelva a subirlos | Media |

---

## Módulo 26: Mi Renta (MyRent)

| ID | Tipo | Regla | Prioridad |
|----|------|-------|-----------|
| MR-R-001 | Restricción | Si un usuario no tiene contratos activos como inquilino, entonces no tiene acceso al módulo Mi Renta | Alta |
| MR-R-002 | Restricción | Si el arrendador no ha habilitado el acceso al portal para un inquilino, entonces ese inquilino no puede acceder a Mi Renta | Alta |
| MR-V-001 | Validación | Si el inquilino accede a Mi Renta, entonces debe autenticarse con sus credenciales de portal (distintas de las del arrendador) | Alta |
| MR-D-001 | Derivación | Si el inquilino accede a Mi Renta, entonces el sistema muestra automáticamente todos los contratos activos asociados a ese inquilino | Alta |
| MR-D-002 | Derivación | Si el inquilino realiza un pago desde Mi Renta, entonces el sistema refleja el pago en el registro del contrato en tiempo real | Alta |
| MR-EDGE-001 | Caso de Borde | Si un inquilino tiene contratos en múltiples propiedades de diferentes arrendadores, entonces Mi Renta muestra todos los contratos consolidados en una sola vista | Media |
| MR-EDGE-002 | Caso de Borde | Si el contrato del inquilino vence y no es renovado, entonces Mi Renta muestra el contrato como "finalizado" pero el inquilino puede consultar el historial por un período configurable | Media |

---
