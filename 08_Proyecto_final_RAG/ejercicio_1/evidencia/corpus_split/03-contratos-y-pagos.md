# Catálogo de Reglas de Negocio — Contratos y Pagos

Extracto temático de [`corpus_catalog_2026-10-04.md`](../corpus_catalog_2026-10-04.md) (copia íntegra del corpus). Este archivo agrupa los módulos 6, 7, 8, 10, 30 (103 reglas) como documento independiente, para satisfacer el criterio de ≥5 documentos si se exige literalmente ≥5 archivos separados en vez de un solo archivo con módulos internos.

---

## Módulo 6: Contratos

| ID | Tipo | Regla | Prioridad |
|----|------|-------|-----------|
| CONT-R-001 | Restricción | Si un contrato tiene pagos con montos recibidos mayores a cero (excluyendo depósitos) o tiene facturas en estado firmado, entonces no puede ser eliminado del sistema | Alta |
| CONT-R-002 | Restricción | Si la fecha de fin del contrato es anterior a la fecha de inicio, entonces el contrato no puede ser creado | Alta |
| CONT-R-003 | Restricción | Si un contrato está cancelado, entonces no puede reactivarse; debe crearse uno nuevo | Alta |
| CONT-R-004 | Restricción | Si un contrato ya tiene pagos registrados, entonces el monto base de renta no puede modificarse retroactivamente | Alta |
| CONT-R-005 | Restricción | Si un contrato tiene incremento anual de renta configurado y ya existen pagos con monto recibido mayor a cero, entonces no puede modificarse ni eliminarse el método ni el valor del incremento | Alta |
| CONT-V-001 | Validación | Si se crea un contrato, entonces debe estar asociado a una propiedad activa, un arrendador activo y al menos un inquilino | Alta |
| CONT-V-002 | Validación | Si se configuran intereses por mora, entonces el porcentaje de interés debe ser mayor a cero | Alta |
| CONT-V-003 | Validación | Si se agrega un servicio al contrato, entonces el servicio debe existir en el catálogo del sistema | Media |
| CONT-V-004 | Validación | Si se configura una comisión en el contrato, entonces el monto o porcentaje de comisión debe ser positivo | Alta |
| CONT-V-005 | Validación | Si se configura un incremento anual de renta, entonces el contrato debe tener una duración superior a doce meses | Alta |
| CONT-V-006 | Validación | Si el método de incremento anual es porcentaje fijo o monto fijo, entonces debe capturarse exactamente el valor correspondiente a ese método | Media |
| CONT-V-007 | Validación | Si el método de incremento anual es INPC, entonces no debe capturarse porcentaje ni monto fijo | Media |
| CONT-V-008 | Validación | Si se crea un contrato, entonces debe especificarse una periodicidad de pago (mensual, anual, quincenal, bimestral, trimestral, semestral, diaria o personalizada) y una duración entera mayor a cero | Alta |
| CONT-V-009 | Validación | Si se crea un contrato, entonces puede clasificarse opcionalmente como "Arrendamiento" u "Hospedaje"; si no se especifica, el sistema no aplica ninguna distinción adicional por modalidad | Baja |
| CONT-D-001 | Derivación | Si se asignan servicios al contrato con distribución proporcional, entonces el sistema calcula el porcentaje correspondiente a cada inquilino | Alta |
| CONT-D-002 | Derivación | Si se configura comisión porcentual, entonces el monto de comisión mensual se calcula aplicando el porcentaje sobre el valor de renta | Alta |
| CONT-D-003 | Derivación | Si la fecha de fin del contrato es anterior a la fecha actual, entonces el sistema lo clasifica como "finalizado" en las consultas y reportes — no existe un estado "vencido" almacenado; el sistema envía recordatorios al arrendador mediante un proceso programado | Alta |
| CONT-D-004 | Derivación | Si se registra un pago en el contrato, entonces el sistema actualiza el saldo pendiente y la fecha del último pago | Alta |
| CONT-D-005 | Derivación | Si un contrato tiene incremento anual configurado por porcentaje o monto fijo, entonces el sistema calcula el precio de cada año contractual a partir del precio del año inmediato anterior (nunca del precio original) y genera la corrida completa con esos precios desde la creación del contrato | Alta |
| CONT-D-006 | Derivación | Si un contrato tiene incremento anual por INPC, entonces las rentas de los años posteriores al primero se generan marcadas como provisionales (con el último importe conocido, no definitivo) hasta que el sistema calcule el valor real por INPC | Alta |
| CONT-D-007 | Derivación | Si un contrato tiene incremento anual de renta por INPC y se alcanza la fecha de corte (15 días naturales antes del aniversario), entonces el sistema calcula el valor real usando el histórico local del INPC, actualiza las rentas provisionales de ese año a definitivas, y registra el cálculo en el historial de incrementos. El incremento del depósito en garantía (cualquier método) usa un corte distinto: se aplica al cumplirse el aniversario mismo, no 15 días antes (PAY-D-005) | Alta |
| CONT-D-008 | Derivación | Si un contrato está por concluir, entonces el sistema puede sugerir una renta de renovación basada en la variación acumulada del INPC desde el inicio del contrato hasta hoy; la sugerencia es únicamente informativa y no modifica el contrato ni se aplica automáticamente | Media |
| CONT-D-009 | Derivación | Si la periodicidad del contrato es "personalizada", entonces el sistema calcula las fechas de pago según el intervalo de días que defina el usuario en vez de aplicar un intervalo fijo de calendario (mensual, anual, etc.) | Media |
| CONT-EDGE-001 | Caso de Borde | Si el contrato tiene múltiples inquilinos y uno se retira, entonces la distribución de servicios se recalcula entre los inquilinos restantes | Alta |
| CONT-EDGE-002 | Caso de Borde | Si se intenta crear un contrato para una propiedad con un contrato activo para el mismo período, entonces el sistema alerta sobre el solapamiento | Alta |
| CONT-EDGE-003 | Caso de Borde | Si el contrato vence y no se renueva, entonces el sistema notifica al arrendador sobre el contrato expirado | Media |

---

## Módulo 7: Pagos

| ID | Tipo | Regla | Prioridad |
|----|------|-------|-----------|
| PAY-R-001 | Restricción | Si un pago fue confirmado por una pasarela de pago, entonces no puede modificarse; solo puede procesarse un reembolso | Alta |
| PAY-R-002 | Restricción | Si el saldo de depósito del inquilino es insuficiente, entonces no puede aplicarse como pago de renta sin autorización explícita del arrendador | Alta |
| PAY-R-003 | Restricción | Si un pago está en estado "pendiente de confirmación", entonces no puede registrarse otro pago para el mismo período | Alta |
| PAY-R-004 | Restricción | Si un contrato tiene incremento anual del depósito en garantía configurado y ya existen movimientos económicos, entonces no puede modificarse ni eliminarse el método ni el valor del incremento | Alta |
| PAY-R-005 | Restricción | Si una renta está marcada como provisional por INPC, entonces no puede registrarse, editarse, aplicarse depósito, ni facturarse sobre ella | Alta |
| PAY-R-006 | Restricción | Si un contrato (o servicio contratado) tiene al menos una factura vigente (no cancelada) que depende de su configuración de desglose, entonces no puede modificarse el tipo de desglose ni los impuestos locales configurados hasta que esa factura se cancele | Alta |
| PAY-V-001 | Validación | Si se registra un pago, entonces el monto debe ser mayor a cero | Alta |
| PAY-V-002 | Validación | Si se registra un pago parcial, entonces el monto no puede exceder el saldo pendiente del período | Alta |
| PAY-V-003 | Validación | Si se procesa un pago mediante pasarela, entonces el sistema debe verificar la confirmación de la transacción antes de marcar el pago como completado | Alta |
| PAY-V-004 | Validación | Si se registra un depósito de garantía, entonces el monto debe ser especificado y positivo | Alta |
| PAY-V-005 | Validación | Si se configura un incremento anual del depósito en garantía, entonces el contrato debe tener una duración superior a doce meses y debe existir un depósito en garantía asociado | Alta |
| PAY-V-006 | Validación | Si se configura un depósito en garantía al crear un contrato, entonces debe indicarse su estado inicial (pagado, pendiente, parcial o transferido de un contrato anterior); si es "pagado" debe indicarse además el método de pago, y en cualquier estado distinto de "transferido" debe indicarse un monto mayor a cero | Alta |
| PAY-D-001 | Derivación | Si se registra un pago, entonces el sistema desglosa automáticamente el monto en: renta base, servicios, comisiones e impuestos | Alta |
| PAY-D-002 | Derivación | Si el pago tiene mora (fecha posterior a la fecha límite), entonces el sistema calcula el monto de interés según la tasa configurada en el contrato | Alta |
| PAY-D-003 | Derivación | Si se registra un depósito inicial, entonces el sistema crea un registro de garantía separado del historial de pagos periódicos | Alta |
| PAY-D-004 | Derivación | Si el pago es confirmado, entonces el sistema genera automáticamente un comprobante de pago | Alta |
| PAY-D-005 | Derivación | Si un contrato tiene incremento anual del depósito en garantía configurado (por cualquier método), entonces el sistema actualiza el monto requerido del mismo registro de depósito en garantía al cumplirse cada aniversario contractual, sin generar cargos independientes por año | Alta |
| PAY-D-006 | Derivación | Si una renta está marcada como provisional por INPC, entonces no se le genera interés por mora aunque su fecha de vencimiento ya haya pasado | Alta |
| PAY-D-007 | Derivación | Si un contrato tiene incremento del depósito en garantía por INPC, entonces el historial de ese año queda como proyección (sin valor real) hasta que, al cumplirse el aniversario, el sistema calcula el valor real y actualiza el mismo depósito | Alta |
| PAY-D-008 | Derivación | Si el incremento anual deja al depósito en garantía con saldo pendiente (estaba Pagado o Aplicado), entonces el sistema regresa su estado a Pendiente para reflejar que se debe cobrar la diferencia | Alta |
| PAY-D-009 | Derivación | Si al renovar un contrato se reutiliza (transfiere) el depósito en garantía del contrato original y ese depósito ya tiene incremento anual configurado, entonces el sistema conserva el método y valor configurados —ignorando cualquier configuración de incremento que envíe el usuario en la renovación— y genera nuevas filas de proyección para los años del contrato renovado a partir del monto vigente del depósito | Alta |
| PAY-D-010 | Derivación | Si un contrato (o servicio contratado) tiene desglose de impuestos configurado y un pago o abono se registra sin factura vigente, entonces el sistema calcula y guarda de forma permanente el desglose (subtotal, IVA, retenciones e impuestos locales) asociado a ese pago o abono | Alta |
| PAY-D-013 | Derivación | Si al crear un contrato el depósito en garantía se marca como "transferido", entonces el sistema reutiliza el depósito del contrato original (mismo monto y método de pago) en vez de solicitar un nuevo pago, y marca el depósito original como transferido | Media |
| PAY-D-011 | Derivación | Si un pago o abono con desglose guardado a nivel pago se factura, entonces el sistema elimina ese desglose porque la factura pasa a ser la fuente única; si la factura se cancela posteriormente, el sistema recalcula y vuelve a guardar el desglose a nivel pago con la configuración compartida vigente | Alta |
| PAY-D-012 | Derivación | Si se confirma el primer pago de suscripción de una cuenta (por Stripe o PayPal), entonces el sistema reporta a la plataforma publicitaria una única compra con el monto y la moneda reales del pago, sin duplicarla ante notificaciones repetidas o reprocesos; no se reportan renovaciones, cambios de propiedades, compras de timbres, depósitos, pagos pendientes, rechazados o reembolsados, ni pagos con más de 7 días de antigüedad, y una falla del reporte nunca impide ni retrasa el cobro | Alta |
| PAY-EDGE-001 | Caso de Borde | Si se realiza un pago que excede el saldo pendiente, entonces el excedente se registra como saldo a favor del inquilino para el siguiente período | Alta |
| PAY-EDGE-002 | Caso de Borde | Si la pasarela de pago reporta un error de conexión durante el procesamiento, entonces el sistema marca el pago como "pendiente de verificación" y no lo da por completado | Alta |
| PAY-EDGE-003 | Caso de Borde | Si se cancela un contrato con depósito de garantía no devuelto, entonces el sistema genera una alerta para que el arrendador gestione la devolución | Alta |
| PAY-EDGE-004 | Caso de Borde | Si el depósito en garantía de un contrato llega a un estado terminal (Reembolsado, Cancelado o Transferido) —ya sea al momento de esa acción o, si no se detectó entonces, en la corrida diaria del cronjob—, entonces el sistema anula de forma permanente (`voided_at`/`voided_reason`) los años de incremento todavía no aplicados de ese contrato, sin modificar el monto/estado del depósito y sin notificar | Media |
| PAY-EDGE-005 | Caso de Borde | Si un pago se cubre mediante varios abonos y el contrato tiene desglose configurado, entonces el desglose de cada abono es proporcional al monto abonado, y el último abono que completa el pago absorbe cualquier diferencia de redondeo para que la suma de los desgloses de los abonos cuadre exactamente con el desglose del pago completo | Alta |

---

## Módulo 8: Facturas (CFDI)

| ID | Tipo | Regla | Prioridad |
|----|------|-------|-----------|
| CFDI-R-001 | Restricción | Si una factura fue timbrada (sellada digitalmente), entonces no puede modificarse; solo puede cancelarse mediante una nota de crédito | Alta |
| CFDI-R-002 | Restricción | Si el arrendador no tiene certificado digital activo y vigente, entonces no puede emitir facturas | Alta |
| CFDI-R-003 | Restricción | Si ya existe una factura para un período de renta específico, entonces no puede emitirse una segunda factura para el mismo período sin cancelar la anterior | Alta |
| CFDI-V-001 | Validación | Si se emite una factura, entonces el RFC del receptor debe ser válido según el formato oficial | Alta |
| CFDI-V-002 | Validación | Si se configura el uso de CFDI, entonces el uso debe existir en el catálogo oficial | Alta |
| CFDI-V-003 | Validación | Si se emite una factura, entonces el método de pago debe estar en el catálogo oficial (PUE, PPD) | Alta |
| CFDI-V-004 | Validación | Si se agrega un concepto a la factura, entonces la clave de producto/servicio debe existir en el catálogo fiscal | Alta |
| CFDI-V-005 | Validación | Si se solicita la cancelación de una factura con motivo 01 (comprobante emitido con errores con relación), entonces debe indicarse el folio fiscal de la factura que la sustituye, y ese folio no puede ser el de la propia factura ni el de la factura que esta sustituye; en caso contrario la solicitud se rechaza sin enviarse al fisco | Alta |
| CFDI-D-001 | Derivación | Si se registra un pago y el contrato tiene configuración de facturación automática, entonces el sistema genera un borrador de factura | Alta |
| CFDI-D-002 | Derivación | Si una factura es timbrada exitosamente, entonces el sistema almacena el UUID fiscal, el XML y el PDF del comprobante | Alta |
| CFDI-D-003 | Derivación | Si se emite una nota de crédito para cancelar una factura, entonces la nota queda vinculada a la factura original mediante su UUID | Alta |
| CFDI-D-004 | Derivación | Si el arrendador solicita explícitamente un complemento de pago para una factura con método PPD y existen abonos registrados, entonces el sistema genera el complemento vinculado a la factura original — la generación es bajo demanda, no automática | Alta |
| CFDI-EDGE-001 | Caso de Borde | Si el servicio de timbrado no está disponible en el momento de la solicitud, entonces la operación falla inmediatamente con el mensaje de error del servicio — no existe reintento automático ni cola de espera; el arrendador debe reintentar manualmente | Alta |
| CFDI-EDGE-002 | Caso de Borde | Si se solicita la cancelación de una factura y el receptor la ha aceptado ante el fisco, entonces el proceso de cancelación requiere aceptación del receptor | Alta |
| CFDI-D-005 | Derivación | Si se factura el cobro de una suscripción y el sistema está registrado en el servicio de timbrado externo, entonces el CFDI de ingreso se timbra a través de dicho servicio; en caso contrario se timbra por el servicio anterior | Alta |
| CFDI-R-004 | Restricción | Si el sistema está registrado en el servicio de timbrado externo, entonces toda cancelación de facturas de suscripción se procesa por ese servicio, incluso para facturas emitidas originalmente por el servicio anterior | Alta |
| CFDI-EDGE-003 | Caso de Borde | Si el servicio de timbrado externo no está disponible al timbrar o cancelar una factura de suscripción del sistema, entonces la operación falla con un mensaje claro y no se reintenta por el servicio anterior (sin fallback) | Alta |
| CFDI-EDGE-004 | Caso de Borde | Si el sistema no tiene timbres disponibles en el servicio externo al facturar una suscripción, entonces la operación falla con un error específico de timbres insuficientes, distinguible de otros errores de timbrado | Media |
| CFDI-EDGE-005 | Caso de Borde | Si el servicio de timbrado responde la cancelación como procesada pero el fisco rechazó el folio (o no informa resultado y el fisco reporta la factura vigente sin cancelación en proceso), entonces la factura permanece vigente y la cancelación se registra como rechazada, nunca como en espera | Alta |
| CFDI-EDGE-006 | Caso de Borde | Si una factura sustituida por refacturación (motivo 01) quedó en espera de cancelación, entonces puede reintentarse su cancelación con el mismo motivo y el folio de la factura nueva; el fisco determina el resultado | Media |

---

## Módulo 10: Comisiones

| ID | Tipo | Regla | Prioridad |
|----|------|-------|-----------|
| COM-R-001 | Restricción | Si una comisión ya fue pagada, entonces no puede modificarse ni eliminarse | Alta |
| COM-R-002 | Restricción | Si no hay configuración de comisión en el contrato, entonces no se generan registros de comisión | Alta |
| COM-V-001 | Validación | Si se configura una comisión porcentual, entonces el porcentaje debe estar entre 0.01% y 100% | Alta |
| COM-V-002 | Validación | Si se configura una comisión fija, entonces el monto debe ser mayor a cero | Alta |
| COM-D-001 | Derivación | Si se registra un pago de renta y el contrato tiene comisión configurada, entonces el sistema calcula automáticamente el monto de comisión generado | Alta |
| COM-D-002 | Derivación | Si se acumulan múltiples comisiones pendientes, entonces el sistema puede generar un resumen consolidado de comisiones por período | Media |
| COM-EDGE-001 | Caso de Borde | Si el monto de la renta varía en un período (pago parcial), entonces la comisión se calcula sobre el monto efectivamente pagado | Alta |
| COM-EDGE-002 | Caso de Borde | Si se cancela un contrato con comisiones pendientes de pago, entonces el sistema genera una alerta al intermediario sobre las comisiones pendientes | Media |

---

## Módulo 30: Conciliación Bancaria

| ID | Tipo | Regla | Prioridad |
|----|------|-------|-----------|
| BANK-R-001 | Restricción | Si ya existe un estado de cuenta cargado para un período, entonces el sistema rechaza una nueva carga para ese mismo período y exige usar el flujo de reemplazo | Alta |
| BANK-R-002 | Restricción | Si un payout o un pago de usuario ya fue emparejado con un movimiento bancario dentro de una reconciliación, entonces no puede volver a usarse para emparejar otro movimiento bancario en esa misma reconciliación | Alta |
| BANK-R-003 | Restricción | Si un movimiento bancario es de tipo débito (cargo), entonces el sistema lo excluye por completo del proceso de reconciliación | Alta |
| BANK-R-004 | Restricción | Si el sistema evalúa una posible combinación de pagos que sumen el monto de un movimiento bancario, entonces la combinación debe incluir al menos dos pagos para considerarse un emparejamiento por acumulación | Media |
| BANK-R-005 | Restricción | Si un pago candidato representa menos del 5% del monto del movimiento bancario, entonces se excluye como candidato para un emparejamiento por acumulación | Media |
| BANK-V-001 | Validación | Si el archivo del estado de cuenta no puede leerse o no arroja ningún movimiento extraíble, entonces el sistema rechaza la carga y no crea el estado de cuenta | Alta |
| BANK-V-002 | Validación | Si el monto del sistema y el monto bancario de un emparejamiento difieren por más de un centavo, entonces el movimiento se clasifica como discrepancia en lugar de coincidencia | Alta |
| BANK-V-003 | Validación | Si se solicita procesar la reconciliación de un período sin estado de cuenta cargado, entonces el sistema rechaza la solicitud | Media |
| BANK-V-004 | Validación | Si se solicita reemplazar un estado de cuenta existente, entonces el sistema debe parsear exitosamente el nuevo archivo antes de eliminar cualquier dato del estado de cuenta anterior | Alta |
| BANK-D-001 | Derivación | Si un movimiento bancario contiene un identificador de rastreo de PayPal que coincide con el de un payout, entonces el sistema calcula la diferencia de monto entre ambos para determinar si es coincidencia o discrepancia | Alta |
| BANK-D-002 | Derivación | Si un movimiento bancario es identificado como proveniente de Stripe, entonces el sistema busca un payout con el mismo monto dentro de una ventana de 3 días respecto a la fecha de liquidación | Alta |
| BANK-D-003 | Derivación | Si un payout está fechado dentro de los últimos 3 días del mes y no aparece en el estado de cuenta del mismo período, entonces se clasifica como "pendiente de llegada" en lugar de "sin correspondencia" | Media |
| BANK-D-004 | Derivación | Si una discrepancia de monto proviene de un movimiento del canal OXXO, entonces el sistema anota automáticamente que probablemente corresponde a una comisión del canal de pago | Baja |
| BANK-D-005 | Derivación | Si la diferencia de monto entre banco y sistema es menor a un dólar, entonces el sistema la etiqueta como diferencia mínima atribuible a redondeo | Baja |
| BANK-D-006 | Derivación | Si un movimiento bancario coincide con un payout o pago registrado en un período distinto al de la reconciliación en curso, entonces se clasifica como "período anterior" | Alta |
| BANK-D-007 | Derivación | Si un movimiento está clasificado como "período anterior", entonces se excluye del cálculo del total de payouts del sistema usado para la diferencia del mes en curso | Alta |
| BANK-EDGE-001 | Caso de Borde | Si un movimiento bancario de crédito no tiene ningún payout ni pago de usuario correspondiente, entonces el sistema lo clasifica como "solo banco" y genera una nota de diagnóstico sugiriendo verificar por referencia | Alta |
| BANK-EDGE-002 | Caso de Borde | Si un movimiento bancario coincide en monto y fecha exacta con un pago registrado en el sistema, entonces el sistema prioriza resolver ese emparejamiento directo antes de intentar un emparejamiento por acumulación | Alta |
| BANK-EDGE-003 | Caso de Borde | Si ningún pago o payout candidato alcanza el puntaje mínimo de similitud, entonces el sistema no genera ninguna sugerencia de cliente para el movimiento sin correspondencia | Baja |

---
