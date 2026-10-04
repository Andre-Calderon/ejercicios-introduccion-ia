# Feature Specification: Business Rules Catalog — MISRENTAS

**Feature Branch**: `ft/andre/001-business-rules-catalog`
**Created**: 2026-02-27
**Status**: Draft
**Input**: Actúa como un Business Analyst Senior y Arquitecto de Software. Genera un listado exhaustivo de Reglas de Negocio (Business Rules) categorizadas por módulo, clasificadas en Derivación, Restricción y Validación, con prioridad (Alta, Media, Baja) y casos de borde.

---

## Índice de Módulos (Navegación Rápida)

> Usa **Ctrl+F** con el nombre del módulo o busca directamente el ID de regla (e.g., `AUTH-R-001`)

| # | Módulo | Prefijo | Ir a sección |
|---|--------|---------|-------------|
| 1 | Autenticación & Seguridad | `AUTH` | [→ Módulo 1](#módulo-1-autenticación--seguridad) |
| 2 | Usuarios | `USR` | [→ Módulo 2](#módulo-2-usuarios) |
| 3 | Arrendadores (Propietarios) | `LESS` | [→ Módulo 3](#módulo-3-arrendadores-propietarios) |
| 4 | Inquilinos | `TEN` | [→ Módulo 4](#módulo-4-inquilinos) |
| 5 | Propiedades | `PROP` | [→ Módulo 5](#módulo-5-propiedades) |
| 6 | Contratos | `CONT` | [→ Módulo 6](#módulo-6-contratos) |
| 7 | Pagos | `PAY` | [→ Módulo 7](#módulo-7-pagos) |
| 8 | Facturas (CFDI) | `CFDI` | [→ Módulo 8](#módulo-8-facturas-cfdi) |
| 9 | Gastos | `EXP` | [→ Módulo 9](#módulo-9-gastos) |
| 10 | Comisiones | `COM` | [→ Módulo 10](#módulo-10-comisiones) |
| 11 | Servicios | `SRV` | [→ Módulo 11](#módulo-11-servicios) |
| 12 | Incidencias | `INC` | [→ Módulo 12](#módulo-12-incidencias) |
| 13 | Inventario | `INV2` | [→ Módulo 13](#módulo-13-inventario) |
| 14 | Archivos | `FILE` | [→ Módulo 14](#módulo-14-archivos) |
| 15 | Notificaciones | `NOTIF` | [→ Módulo 15](#módulo-15-notificaciones) |
| 16 | Bitácora (Binnacle) | `BIN` | [→ Módulo 16](#módulo-16-bitácora-binnacle) |
| 17 | Paquetes & Suscripciones | `PKG` | [→ Módulo 17](#módulo-17-paquetes--suscripciones) |
| 18 | Plataforma (Multi-tenant) | `PLAT` | [→ Módulo 18](#módulo-18-plataforma-multi-tenant) |
| 19 | Reportes & Exportaciones | `RPT` | [→ Módulo 19](#módulo-19-reportes--exportaciones) |
| 20 | Catálogos Generales | `CAT` | [→ Módulo 20](#módulo-20-catálogos-generales) |
| 21 | Catálogos Fiscales (CFDI) | `CFDICAT` | [→ Módulo 21](#módulo-21-catálogos-fiscales-cfdi) |
| 22 | Formatos de Documentos | `FMT` | [→ Módulo 22](#módulo-22-formatos-de-documentos) |
| 23 | Sellos Digitales (Stamps) | `STAMP` | [→ Módulo 23](#módulo-23-sellos-digitales-stamps) |
| 24 | Seguridad del Sistema | `SEC` | [→ Módulo 24](#módulo-24-seguridad-del-sistema) |
| 25 | Configuración del Sistema | `SYSCFG` | [→ Módulo 25](#módulo-25-configuración-del-sistema) |
| 26 | Mi Renta (MyRent) | `MR` | [→ Módulo 26](#módulo-26-mi-renta-myrent) |
| 27 | Roles & Permisos (RBAC) | `RBAC` | [→ Módulo 27](#módulo-27-roles--permisos-rbac) |
| 28 | API de Integración | `INTAPI` | [→ Módulo 28](#módulo-28-api-de-integración) |
| 29 | Gestión de Proveedores | `SUPP` | [→ Módulo 29](#módulo-29-gestión-de-proveedores) |
| 30 | Conciliación Bancaria | `BANK` | [→ Módulo 30](#módulo-30-conciliación-bancaria) |

**Estadísticas del catálogo (actualizado 2026-10-04)**: 358 reglas · 30 módulos · Módulo 6 (Contratos) y Módulo 7 (Pagos) — cobertura de las modalidades de creación de un contrato que faltaban: periodicidad de pago, clasificación Arrendamiento/Hospedaje y configuración inicial del depósito en garantía (CONT-V-008, CONT-V-009, CONT-D-009, PAY-V-006, PAY-D-013); terminología corregida de "lessor"/"tenant" a "arrendador"/"inquilino" en todo el catálogo · Módulo 29 (Gestión de Proveedores) y Módulo 30 (Conciliación Bancaria) agregados vía auditoría de cobertura contra `specs/009-supplier-management` y `specs/004-bank-reconciliation` — ambas features no tenían ninguna regla documentada desde su implementación original · Módulo 6 (Contratos) — incremento anual de renta por porcentaje fijo, monto fijo o INPC (con resolución automática en el aniversario y sugerencia de renta por INPC en renovación) en contratos multianuales, ahora también soportado en `POST /api/contracts/renew` con el mismo comportamiento que creación (CONT-R-005, CONT-V-005, CONT-V-006, CONT-V-007, CONT-D-005, CONT-D-006, CONT-D-007, CONT-D-008) · Módulo 7 (Pagos) — incremento anual del depósito en garantía (cualquier método) corregido a "mismo registro actualizado en el aniversario" en vez de cargos independientes por año, con anulación inmediata del historial pendiente al cancelar/reembolsar/transferir el depósito y herencia de la configuración de incremento al reutilizar el depósito en una renovación (PAY-R-004, PAY-R-005, PAY-V-005, PAY-D-005, PAY-D-006, PAY-D-007, PAY-D-008, PAY-D-009, PAY-EDGE-004) · desglose de impuestos nivel pago/abono sin factura vigente, con interruptor de recibo independiente y guard contra cambio de configuración si hay factura vigente (PAY-R-006, PAY-D-010, PAY-D-011, PAY-EDGE-005) · Módulo 23 (Sellos) — asignación en renovación (STAMP-D-003), distinción de origen (STAMP-D-004) y fórmulas de sellos por concepto sin alterar la suscripción/pasarela (STAMP-D-005) · Módulo 1/7 — medición de conversiones con Meta (Pixel + Conversions API): registro con periodo de prueba activado (AUTH-D-010), primer pago de suscripción (PAY-D-012) y restricción por configuración/consentimiento con datos protegidos (AUTH-R-009) · Módulo 8 (Facturas) — cancelación con motivo 01: folio de sustitución obligatorio y no puede ser la propia factura ni la que esta sustituye (CFDI-V-005), un rechazo del fisco ya no deja la factura en espera de cancelación (CFDI-EDGE-005), reintento de la cancelación motivo 01 de una factura en espera (CFDI-EDGE-006)

---

## User Scenarios & Testing *(mandatory)*

### User Story 1 — Business Analyst Consulta Reglas por Módulo (Priority: P1)

Como Business Analyst o Arquitecto de Software, necesito consultar el catálogo de reglas de negocio por módulo para entender las restricciones, validaciones y derivaciones que rigen el comportamiento del sistema, sin necesidad de leer el código fuente.

**Why this priority**: Es la razón de ser del catálogo. Si el BA no puede navegar y entender las reglas por módulo, todo lo demás pierde valor.

**Independent Test**: Se puede probar tomando el documento y respondiendo la pregunta "¿Qué pasa si un usuario falla 5 veces el login?" en menos de 30 segundos — el catálogo debe responder directamente.

**Acceptance Scenarios**:

1. **Given** el catálogo está disponible, **When** el BA busca el módulo de Autenticación, **Then** encuentra una tabla con todas las reglas clasificadas por tipo y prioridad
2. **Given** el BA consulta una regla, **When** lee AUTH-R-001, **Then** comprende la condición y la acción resultante sin ambigüedad
3. **Given** el BA busca casos de borde, **When** revisa el módulo de Pagos, **Then** encuentra reglas específicas para situaciones excepcionales

---

### User Story 2 — Desarrollador Valida Implementación vs Reglas (Priority: P2)

Como desarrollador, necesito consultar el catálogo antes de implementar una funcionalidad para asegurar que mi código cumple con las reglas de negocio establecidas.

**Why this priority**: Previene errores de implementación y reduce el retrabajo al tener una referencia normativa clara.

**Independent Test**: Un desarrollador puede tomar una regla (e.g., CONT-V-001) e implementar directamente una validación sin necesidad de consultar al BA.

**Acceptance Scenarios**:

1. **Given** el desarrollador va a implementar validación de contrato, **When** consulta el módulo de Contratos, **Then** encuentra las reglas de validación con el formato "Si [condición], entonces [resultado]"
2. **Given** el desarrollador encuentra una regla de derivación, **When** la lee, **Then** entiende exactamente cómo calcular o generar el dato derivado

---

### User Story 3 — QA Genera Casos de Prueba desde las Reglas (Priority: P3)

Como QA Engineer, necesito usar el catálogo como base para generar casos de prueba que cubran todas las reglas de negocio del sistema.

**Why this priority**: Garantiza que las pruebas estén alineadas con las reglas de negocio y no solo con la implementación técnica.

**Independent Test**: El equipo de QA puede generar al menos 1 caso de prueba por cada regla catalogada sin requerir información adicional.

**Acceptance Scenarios**:

1. **Given** una regla de restricción (e.g., AUTH-R-001), **When** el QA la lee, **Then** puede derivar un caso positivo y uno negativo directamente
2. **Given** una regla de caso de borde, **When** el QA la identifica, **Then** puede crear un escenario de prueba para la condición excepcional

---

### Edge Cases

- ¿Qué pasa si una regla de un módulo entra en conflicto con la regla de otro módulo?
- ¿Cómo se comporta el sistema cuando múltiples reglas de validación aplican simultáneamente a la misma operación?
- ¿Qué ocurre cuando las fechas de contratos solapan con períodos de facturación ya cerrados?
- ¿Cómo maneja el sistema reglas que dependen de configuraciones externas (e.g., tasas de cambio no actualizadas)?

---

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: El catálogo DEBE cubrir los 28 módulos del sistema MISRENTAS
- **FR-002**: Cada regla DEBE seguir la estructura: "Si [Condición/Evento], entonces [Resultado/Acción/Restricción]"
- **FR-003**: Cada regla DEBE clasificarse como: Derivación | Restricción | Validación
- **FR-004**: Cada regla DEBE tener una prioridad asignada: Alta | Media | Baja
- **FR-005**: Cada módulo DEBE incluir al menos una regla de caso de borde
- **FR-006**: Las reglas DEBEN presentarse en tablas por módulo
- **FR-007**: Las reglas DEBEN ser atómicas (una sola condición y un solo resultado por regla)
- **FR-008**: El catálogo DEBE ser tecnológicamente agnóstico (sin referencias a frameworks, bases de datos o lenguajes de programación)
- **FR-009**: Cada regla DEBE tener un identificador único con formato: [MÓDULO]-[D|R|V]-[###]

### Key Entities

- **BusinessRule**: Identificador único, módulo, tipo (Derivación/Restricción/Validación), texto en formato Si/Entonces, nivel de prioridad, indicador de caso de borde
- **Module**: Nombre, categoría de negocio, entidades relacionadas, conjunto de reglas
- **Priority**: Nivel de criticidad — Alta (integridad/seguridad/cumplimiento legal), Media (flujos de negocio), Baja (experiencia/comodidad)

---

## Business Rules Catalog

> **Convención de IDs**: `[MÓDULO]-[D|R|V]-[###]`
> - **D** = Derivación (calcula o crea nuevos datos)
> - **R** = Restricción (limitaciones que el sistema impone)
> - **V** = Validación (criterios para que una operación sea exitosa)
> - Reglas con sufijo **EDGE** corresponden a casos de borde

---

## Módulo 1: Autenticación & Seguridad

| ID | Tipo | Regla | Prioridad |
|----|------|-------|-----------|
| AUTH-R-001 | Restricción | Si el usuario ingresa credenciales incorrectas 5 veces consecutivas dentro de una ventana de 30 minutos, entonces el sistema aplica un bloqueo temporal de 15 minutos a ese IP+endpoint. Si esto ocurre más de 3 veces (bloqueos temporales acumulados), el IP se agrega permanentemente a la lista negra | Alta |
| AUTH-R-002 | Restricción | Si el token de sesión ha expirado, entonces el sistema rechaza la solicitud y requiere nuevo inicio de sesión | Alta |
| AUTH-R-003 | Restricción | Si la dirección IP del solicitante está en la lista negra, entonces el acceso se deniega independientemente de las credenciales | Alta |
| AUTH-R-004 | Restricción | Si el usuario no ha verificado su correo electrónico y el período de gracia (7 días) ha expirado, entonces el acceso queda restringido | Alta |
| AUTH-R-005 | Restricción | Si el usuario tiene 2FA activado y no proporciona un código TOTP válido, entonces el acceso se deniega aunque las credenciales sean correctas | Alta |
| AUTH-R-006 | Restricción | Si se detectan 3 intentos de registro incorrectos desde la misma IP dentro de una ventana de 30 minutos, entonces el sistema aplica un bloqueo temporal de 15 minutos a ese IP+endpoint. Si esto ocurre más de 3 veces (bloqueos temporales acumulados), el IP se agrega permanentemente a la lista negra | Alta |
| AUTH-R-007 | Restricción | Si el usuario tiene un método biométrico (huella digital) registrado y lo elige como segundo factor, entonces el sistema exige verificación del usuario en el autenticador y rechaza respuestas que no confirmen esa verificación | Alta |
| AUTH-R-008 | Restricción | Si el usuario no tiene ningún método biométrico registrado, entonces el sistema no ofrece la opción de huella digital como segundo factor y solo permite TOTP o código de recuperación | Media |
| AUTH-R-009 | Restricción | Si la medición publicitaria está deshabilitada, o el usuario no ha otorgado su consentimiento de seguimiento, entonces el sistema no reporta ninguna conversión a la plataforma publicitaria por ningún canal; y cuando sí reporta, los datos personales del usuario (correo, teléfono, nombre) se envían únicamente de forma protegida e irreversible, nunca en texto legible | Alta |
| AUTH-V-001 | Validación | Si el usuario intenta iniciar sesión, entonces el sistema debe verificar que el correo y contraseña corresponden a una cuenta activa | Alta |
| AUTH-V-002 | Validación | Si el usuario activa 2FA, entonces el sistema debe validar que el código TOTP sea vigente y no haya sido usado anteriormente | Alta |
| AUTH-V-003 | Validación | Si el sistema recibe una solicitud de restablecimiento de contraseña, entonces el enlace enviado debe ser válido por un tiempo limitado y de un solo uso | Alta |
| AUTH-V-004 | Validación | Si el registro requiere CAPTCHA, entonces el sistema debe verificar la respuesta del CAPTCHA antes de procesar el registro | Alta |
| AUTH-V-005 | Validación | Si el usuario intenta verificar una respuesta biométrica, entonces el sistema debe validar la firma contra la llave pública almacenada, el origen (dominio) de la solicitud, y que el contador de firmas del autenticador sea mayor al último valor registrado, antes de conceder acceso | Alta |
| AUTH-V-006 | Validación | Si el usuario intenta registrar un nuevo autenticador biométrico, entonces el sistema solo acepta autenticadores integrados al dispositivo (huella digital, reconocimiento facial), rechazando llaves de seguridad externas USB/NFC | Alta |
| AUTH-D-001 | Derivación | Si el inicio de sesión es exitoso, entonces el sistema genera un token de sesión firmado que contiene el identificador del usuario, su tipo y sus roles | Alta |
| AUTH-D-002 | Derivación | Si el usuario activa 2FA, entonces el sistema genera un conjunto de códigos de recuperación de un solo uso como respaldo | Alta |
| AUTH-D-003 | Derivación | Si el usuario se registra exitosamente, entonces el sistema envía automáticamente un correo de verificación con un enlace único | Media |
| AUTH-D-004 | Derivación | Si un bloqueo temporal expira sin que se reciba un nuevo intento, entonces el contador de intentos se reinicia para una nueva ventana, conservando el historial de bloqueos | Alta |
| AUTH-D-005 | Derivación | Si un inicio de sesión es exitoso, entonces el sistema elimina por completo el registro de intentos, incluyendo el contador de bloqueos temporales | Alta |
| AUTH-D-006 | Derivación | Si un administrador remueve manualmente el bloqueo de intentos de una IP (por solicitud de un cliente), entonces el sistema elimina el registro de intentos correspondiente, liberando el bloqueo temporal y reiniciando intentos y strikes | Media |
| AUTH-D-007 | Derivación | Si un administrador consulta la lista de bloqueos temporales activos, entonces el sistema devuelve un listado paginado de IP+endpoint actualmente bloqueados, filtrable por dirección IP o por el correo electrónico utilizado en los intentos | Media |
| AUTH-D-008 | Derivación | Si el usuario registra exitosamente un autenticador biométrico, entonces el sistema lo agrega como método adicional de segundo factor sin afectar ni reemplazar la configuración TOTP existente | Alta |
| AUTH-D-009 | Derivación | Si el usuario elimina un autenticador biométrico registrado, entonces el sistema lo remueve sin requerir verificación adicional, dado que el segundo factor obligatorio (TOTP) permanece activo | Media |
| AUTH-D-010 | Derivación | Si un arrendador completa su registro (por correo, Google o Facebook) y el sistema le activa automáticamente el periodo de prueba, entonces el sistema reporta a la plataforma publicitaria una única conversión de "registro completado" identificada por el usuario, sin duplicarla aunque el reporte se reintente; no se reporta si el periodo de prueba no se activa, si el registro es de un inquilino o si el registro no llega a confirmarse, y una falla del reporte nunca impide el registro | Alta |
| AUTH-EDGE-001 | Caso de Borde | Si el usuario usa un código de recuperación de 2FA, entonces el código es invalidado inmediatamente tras su uso para evitar reutilización | Alta |
| AUTH-EDGE-002 | Caso de Borde | Si el token de sesión es válido pero el usuario ha sido desactivado por un administrador, entonces el sistema revoca el acceso en la siguiente solicitud | Alta |
| AUTH-EDGE-003 | Caso de Borde | Si un IP+endpoint está bajo bloqueo temporal activo, entonces las solicitudes a ese endpoint se rechazan con código 429 informando el tiempo de espera restante y la hora de liberación, sin llegar al controlador | Alta |
| AUTH-EDGE-004 | Caso de Borde | Si el sistema detecta que el contador de firmas de un autenticador biométrico no aumentó respecto al último uso registrado, entonces el sistema rechaza la respuesta por posible clonación del autenticador | Alta |

---

## Módulo 2: Usuarios

| ID | Tipo | Regla | Prioridad |
|----|------|-------|-----------|
| USR-R-001 | Restricción | Si un usuario no tiene un rol asignado, entonces no puede acceder a ningún recurso protegido del sistema | Alta |
| USR-R-002 | Restricción | Si un administrador intenta asignar un permiso que no existe en el catálogo, entonces la operación se rechaza | Alta |
| USR-R-003 | Restricción | Si un usuario intenta modificar los datos de otro usuario sin el permiso adecuado, entonces la operación se deniega | Alta |
| USR-R-004 | Restricción | Si un usuario es desactivado, entonces todas sus sesiones activas son invalidadas inmediatamente | Alta |
| USR-V-001 | Validación | Si se crea un nuevo usuario, entonces el correo electrónico debe ser único en el sistema | Alta |
| USR-V-002 | Validación | Si se actualiza la contraseña, entonces la nueva contraseña debe cumplir la política de complejidad mínima definida | Alta |
| USR-V-003 | Validación | Si se asigna un rol a un usuario, entonces el rol debe existir previamente en el catálogo de roles | Alta |
| USR-D-001 | Derivación | Si se crea un usuario nuevo, entonces el sistema registra la fecha de creación y el estado inicial como "pendiente de verificación" | Alta |
| USR-D-002 | Derivación | Si un usuario cambia su correo electrónico, entonces el sistema invalida la verificación anterior y genera una nueva solicitud de verificación | Alta |
| USR-D-003 | Derivación | Si se asignan múltiples roles a un usuario, entonces el sistema combina los permisos de todos los roles asignados (unión de permisos) | Media |
| USR-EDGE-001 | Caso de Borde | Si el último administrador activo intenta ser desactivado, entonces el sistema DEBERÍA rechazar la operación — **BACKLOG: regla de negocio deseable, no implementada actualmente** | Alta |
| USR-EDGE-002 | Caso de Borde | Si un usuario tiene historial de pagos o contratos activos y se intenta eliminar, entonces el sistema impide la eliminación física y marca el registro como inactivo | Alta |

---

## Módulo 3: Arrendadores (Propietarios)

| ID | Tipo | Regla | Prioridad |
|----|------|-------|-----------|
| LESS-R-001 | Restricción | Si un arrendador no tiene configuración de facturación (RFC, régimen fiscal), entonces no puede generar facturas CFDI | Alta |
| LESS-R-002 | Restricción | Si un arrendador no tiene un certificado digital activo, entonces no puede emitir facturas con sello digital | Alta |
| LESS-R-003 | Restricción | Si un arrendador tiene contratos activos o facturas pendientes, entonces no puede ser eliminado del sistema | Alta |
| LESS-V-001 | Validación | Si se registra un arrendador, entonces su RFC debe seguir el formato oficial mexicano (13 caracteres persona física, 12 persona moral) | Alta |
| LESS-V-002 | Validación | Si se carga un certificado digital (CSD), entonces el sistema debe verificar que el certificado no ha expirado y corresponde al RFC del arrendador | Alta |
| LESS-V-003 | Validación | Si se configura una cuenta bancaria para el arrendador, entonces el número de cuenta y CLABE deben seguir el formato requerido | Media |
| LESS-D-001 | Derivación | Si se registra un arrendador, entonces el sistema crea automáticamente un perfil de configuración de facturación vacío y un perfil de configuración de recibos | Alta |
| LESS-D-002 | Derivación | Si el certificado digital de un arrendador está próximo a vencer (menos de 30 días), entonces el sistema genera una notificación de alerta | Media |
| LESS-EDGE-001 | Caso de Borde | Si el certificado digital de un arrendador vence durante un proceso de facturación en curso, entonces el proceso se completa con el certificado vigente al momento de inicio | Alta |
| LESS-EDGE-002 | Caso de Borde | Si un arrendador opera con múltiples RFC (persona física y moral), entonces cada configuración de facturación queda asociada al RFC correspondiente de forma independiente | Media |

---

## Módulo 4: Inquilinos

| ID | Tipo | Regla | Prioridad |
|----|------|-------|-----------|
| TEN-R-001 | Restricción | Si un inquilino no ha aceptado una invitación al contrato, entonces no puede acceder al portal del inquilino | Alta |
| TEN-R-002 | Restricción | Si se intenta eliminar a un inquilino de un contrato activo y es el único inquilino, entonces la operación es rechazada | Alta |
| TEN-V-001 | Validación | Si se invita a un inquilino a un contrato, entonces el correo electrónico de invitación debe ser único por contrato | Alta |
| TEN-V-002 | Validación | Si un inquilino acepta una invitación, entonces el sistema debe verificar que la invitación no ha expirado | Alta |
| TEN-V-003 | Validación | Si se asocia un inquilino a un contrato, entonces el inquilino debe tener perfil completo (nombre, correo) | Alta |
| TEN-D-001 | Derivación | Si un inquilino es invitado a un contrato, entonces el sistema genera un enlace de invitación único con tiempo de expiración | Alta |
| TEN-D-002 | Derivación | Si un inquilino acepta la invitación y crea su cuenta, entonces el sistema genera automáticamente acceso al portal del inquilino | Alta |
| TEN-D-003 | Derivación | Si se registran múltiples inquilinos en un contrato, entonces el sistema asigna uno como inquilino principal | Media |
| TEN-EDGE-001 | Caso de Borde | Si la invitación expira antes de ser aceptada, entonces el arrendador debe generar una nueva invitación; la anterior no puede ser reutilizada | Media |
| TEN-EDGE-002 | Caso de Borde | Si un inquilino está en múltiples contratos simultáneos (diferentes propiedades), entonces el portal muestra cada contrato de forma independiente | Media |

---

## Módulo 5: Propiedades

| ID | Tipo | Regla | Prioridad |
|----|------|-------|-----------|
| PROP-R-001 | Restricción | Si una propiedad tiene contratos activos, entonces no puede ser eliminada del sistema | Alta |
| PROP-R-002 | Restricción | Si una propiedad está inactiva, entonces no pueden crearse nuevos contratos asociados a ella | Alta |
| PROP-V-001 | Validación | Si se registra una propiedad, entonces debe asociarse a un arrendador existente y activo | Alta |
| PROP-V-002 | Validación | Si se asigna un tipo de propiedad, entonces el tipo debe existir en el catálogo de tipos de propiedad | Media |
| PROP-V-003 | Validación | Si se registran características numéricas de la propiedad (recámaras, baños, etc.), entonces los valores deben ser positivos | Baja |
| PROP-D-001 | Derivación | Si se crea una propiedad, entonces el sistema genera un identificador único y registra la fecha de registro | Alta |
| PROP-D-002 | Derivación | Si se asocian servicios a una propiedad, entonces esos servicios están disponibles para ser incluidos en contratos de esa propiedad | Media |
| PROP-EDGE-001 | Caso de Borde | Si una propiedad tiene múltiples unidades independientes, entonces cada unidad se gestiona como una propiedad separada | Media |
| PROP-EDGE-002 | Caso de Borde | Si un arrendador transfiere una propiedad a otro arrendador, entonces los contratos activos mantienen su configuración original hasta su vencimiento | Alta |

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

## Módulo 24: Seguridad del Sistema

| ID | Tipo | Regla | Prioridad |
|----|------|-------|-----------|
| SEC-R-001 | Restricción | Si una IP es agregada a la lista negra, entonces ningún usuario puede acceder al sistema desde esa IP | Alta |
| SEC-R-002 | Restricción | Si un correo electrónico es agregado a la lista de bloqueados, entonces no pueden enviarse correos a esa dirección | Alta |
| SEC-R-003 | Restricción | Si una IP está en la lista negra, entonces el bloqueo aplica a todos los usuarios, no solo al que causó el bloqueo | Alta |
| SEC-R-004 | Restricción | Si un correo electrónico está registrado en la lista de desuscritos para una lista específica, entonces el sistema omite el envío de correos de esa lista a esa dirección | Alta |
| SEC-R-005 | Restricción | Si el administrador consulta la lista negra de emails con filtro de tipo, entonces el sistema retorna únicamente los registros correspondientes al tipo indicado | Media |
| SEC-V-001 | Validación | Si se agrega una IP a la lista negra, entonces el formato debe ser una dirección IP o rango CIDR válido | Alta |
| SEC-V-002 | Validación | Si se configura una regla de seguridad, entonces debe tener un motivo de bloqueo registrado | Media |
| SEC-V-003 | Validación | Si se consulta la lista negra de emails con parámetro de tipo, entonces el valor debe ser uno de los permitidos: all, blocked, unsubscribed | Media |
| SEC-D-001 | Derivación | Si se detectan múltiples intentos fallidos desde una IP dentro de una ventana de 30 minutos, entonces el sistema aplica un bloqueo temporal de 15 minutos al IP+endpoint, registrando el correo electrónico utilizado como referencia. Si el IP acumula más de 3 bloqueos temporales, se agrega permanentemente a la lista negra | Alta |
| SEC-D-002 | Derivación | Si un email en la lista unificada tiene origen bloqueado, entonces el sistema expone el motivo de bloqueo registrado por el administrador | Baja |
| SEC-D-003 | Derivación | Si un email en la lista unificada tiene origen desuscrito, entonces el sistema expone la lista específica de la que se desuscribió con su identificador numérico y nombre legible | Baja |
| SEC-EDGE-001 | Caso de Borde | Si la IP del administrador que gestiona la lista negra coincide con una IP a bloquear, entonces el sistema requiere confirmación adicional | Alta |
| SEC-EDGE-002 | Caso de Borde | Si una IP a bloquear pertenece a un rango compartido, entonces el sistema alerta al administrador sobre el impacto potencial en múltiples usuarios | Alta |
| SEC-EDGE-003 | Caso de Borde | Si un correo electrónico figura simultáneamente en emails bloqueados y en desuscritos, entonces aparece como entradas independientes en la lista unificada, una por cada origen | Media |

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

## Módulo 27: Roles & Permisos (RBAC)

| ID | Tipo | Regla | Prioridad |
|----|------|-------|-----------|
| RBAC-R-001 | Restricción | Si un rol tiene usuarios asignados, entonces no puede eliminarse sin reasignar primero los usuarios | Alta |
| RBAC-R-002 | Restricción | Si un rol es predefinido por el sistema, entonces sus permisos base no pueden ser removidos; solo pueden agregarse permisos adicionales | Alta |
| RBAC-R-003 | Restricción | Si un usuario intenta realizar una acción para la cual no tiene permiso, entonces el sistema deniega la operación con un mensaje claro | Alta |
| RBAC-V-001 | Validación | Si se crea un rol, entonces el nombre debe ser único en el sistema | Alta |
| RBAC-V-002 | Validación | Si se asigna un permiso a un rol, entonces el permiso debe existir en el catálogo de permisos del sistema | Alta |
| RBAC-D-001 | Derivación | Si un usuario tiene múltiples roles, entonces el sistema une los permisos de todos sus roles para determinar el acceso efectivo | Alta |
| RBAC-D-002 | Derivación | Si se crea un nuevo permiso en el sistema, entonces está disponible para ser asignado a roles pero no se asigna automáticamente a ninguno | Alta |
| RBAC-D-003 | Derivación | Si se revoca un permiso de un rol, entonces el cambio aplica inmediatamente a todos los usuarios con ese rol en sus próximas solicitudes | Alta |
| RBAC-EDGE-001 | Caso de Borde | Si se crea un rol sin permisos asignados, entonces los usuarios con ese rol no pueden realizar ninguna acción en el sistema | Alta |
| RBAC-EDGE-002 | Caso de Borde | Si un usuario tiene múltiples roles con permisos contradictorios entre sí, entonces aplica el principio de mayor permiso (unión de permisos, no intersección) | Media |
| RBAC-D-004 | Derivación | Si un rol tiene permiso de acceso total a un módulo (`{módulo}.*`), entonces ese permiso cubre automáticamente cualquier acción granular del mismo módulo (`{módulo}.crear`, `{módulo}.leer`, etc.) sin necesidad de asignación explícita | Alta |
| RBAC-D-005 | Derivación | Si se agregan permisos granulares (crear, leer, actualizar, eliminar) al catálogo de un módulo, entonces los permisos no se asignan automáticamente a ningún rol — la asignación es explícita y voluntaria por parte del arrendador | Alta |

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

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: Los 30 módulos del sistema tienen al menos una tabla de reglas documentada — cobertura del 100%
- **SC-002**: El 100% de las reglas sigue el formato atómico "Si [Condición], entonces [Resultado]"
- **SC-003**: El catálogo contiene al menos 200 reglas de negocio distribuidas entre los 30 módulos
- **SC-004**: El 100% de los módulos tiene al menos una regla de caso de borde documentada
- **SC-005**: Un Business Analyst puede localizar las reglas de cualquier módulo en menos de 30 segundos
- **SC-006**: El equipo de QA puede derivar al menos un caso de prueba por cada regla sin requerir información adicional
- **SC-007**: El catálogo no contiene ninguna referencia tecnológica específica (frameworks, bases de datos, lenguajes) — 0 referencias técnicas

---

## Assumptions

- Las reglas documentadas reflejan el comportamiento del sistema basado en el análisis del código fuente y la configuración de la versión actual
- Los valores específicos de configuración (e.g., 7 días de gracia, 5 intentos de login) son los valores predeterminados y pueden ser configurables por administradores
- Las reglas de prioridad Alta tienen impacto directo en la integridad de datos, seguridad del sistema o cumplimiento legal
- El catálogo es un documento vivo que debe actualizarse cuando se implementen cambios en las reglas de negocio
- Las reglas de CFDI están orientadas al cumplimiento fiscal mexicano (SAT) en la versión vigente del estándar de comprobante fiscal digital

---

## Historial de Cambios

| Versión | Fecha | Descripción | Reglas | Módulos |
|---------|-------|-------------|--------|---------|
| 1.0.0 | 2026-02-27 | Versión inicial del catálogo — documentación exhaustiva de reglas de negocio del sistema MISRENTAS | 247 | 27 |
| 1.1.0 | 2026-02-27 | Validación del catálogo contra código fuente — correcciones aplicadas a 5 reglas (CONT-R-001, CONT-D-003, CFDI-D-004, CFDI-EDGE-001) y USR-EDGE-001 marcada como BACKLOG | 247 | 27 |
| 1.2.0 | 2026-02-27 | Agregada tabla de índice rápido de módulos para navegación; verificada cobertura mínima de 5 reglas por módulo | 247 | 27 |
| 1.3.0 | 2026-03-11 | Agregadas SYSCFG-D-003 y SYSCFG-EDGE-002 — estándar de serialización de fechas en UTC para respuestas de la API | 249 | 27 |
| 1.4.0 | 2026-07-01 | Agregado Módulo 28 (API de Integración, prefijo `INTAPI`) — 8 reglas nuevas para el servicio externo de solo lectura: aislamiento de cuenta, prohibición de escritura, credenciales inválidas/expiradas, secreto de un solo uso, revocación inmediata, listas vacías y recursos de otra cuenta como no encontrados (INTAPI-R-001, INTAPI-R-002, INTAPI-V-001, INTAPI-V-002, INTAPI-D-001, INTAPI-D-002, INTAPI-EDGE-001, INTAPI-EDGE-002) | 264 | 28 |
| 1.5.0 | 2026-07-01 | Módulo 28 (API de Integración) — agregado control granular de acceso por recurso (scopes) por credencial: rechazo de recursos no habilitados y default de todos los recursos habilitados al crear (INTAPI-R-003, INTAPI-D-003) | 266 | 28 |
| 1.6.0 | 2026-07-02 | Módulo 28 (API de Integración) — agregado modo prueba/en vivo por credencial (estilo Stripe: prefijo intapi_test_/intapi_live_ en el API Key), default en vivo si no se especifica (INTAPI-D-004) | 267 | 28 |
| 1.7.0 | 2026-07-20 | Módulo 6 (Contratos) — incremento anual de renta por porcentaje fijo o monto fijo en contratos multianuales (>12 meses): inmutable una vez que existen pagos, cálculo compuesto/aditivo año sobre año desde la renta vigente, corrida completa generada desde la creación (CONT-R-005, CONT-V-005, CONT-V-006, CONT-D-005). No incluye método INPC, incremento del depósito en garantía, ni sugerencia de renta en renovación — fases futuras | 271 | 28 |
| 1.8.0 | 2026-07-20 | Módulo 7 (Pagos) — incremento anual del depósito en garantía por porcentaje fijo o monto fijo en contratos multianuales, totalmente independiente del incremento de renta: genera desde la creación del contrato un cargo por año contractual con el diferencial requerido, inmutable una vez que existen movimientos económicos (PAY-R-004, PAY-V-005, PAY-D-005). Solo aplica a contratos nuevos. No incluye método INPC ni reconciliación contra lo efectivamente cobrado — fases futuras | 274 | 28 |
| 1.9.0 | 2026-07-20 | Módulo 6 (Contratos) y Módulo 7 (Pagos) — método INPC habilitado como opción de incremento anual de renta: las rentas de años posteriores al primero nacen marcadas como provisionales (sin valor real calculado todavía) y quedan bloqueadas para pago, edición, aplicación de depósito y facturación hasta que un futuro job las resuelva (CONT-V-007, CONT-D-006, PAY-R-005). El histórico de incrementos (`contract_price_increase_history`) no se escribe para INPC hasta que exista un cálculo real. Incremento del depósito en garantía por INPC sigue fuera de alcance | 277 | 28 |
| 2.0.0 | 2026-07-20 | Cierre del feature de incremento anual por INPC: job diario `InpcAnniversaryResolverJob` que resuelve renta y depósito provisionales 15 días antes de cada aniversario usando el histórico local del INPC (CONT-D-007); notificación al arrendador/inquilino cuando se resuelve un incremento; incremento del depósito en garantía por INPC habilitado (cargos provisionales resueltos igual que la renta, PAY-D-007); mora nunca se genera sobre rentas provisionales (PAY-D-006); sugerencia de renta por INPC en renovación de contratos, informativa y de solo lectura (CONT-D-008). Fuera de alcance: notificación de incrementos %/fijo por aniversario, y prorrateo cuando un periodo de cobro no anual cruza el aniversario (spec.md §9.6, sigue abierto) | 281 | 28 |
| 2.1.0 | 2026-07-28 | Corrección del modelo de incremento anual del depósito en garantía: se reemplaza "un Payment nuevo por año" por "el mismo depósito (Payment + PaymentDeposit) actualizado en cada aniversario", para cualquier método (no solo INPC) — nuevo job diario `DepositAnniversaryIncreaseJob`, historial de incrementos ahora distingue proyección de aplicado (`applied_at`), el depósito regresa a Pendiente si el incremento deja saldo por cobrar (PAY-D-005 reescrita, PAY-D-007 reescrita, PAY-D-008, PAY-EDGE-004; CONT-D-007 acotada a renta). Corrige de paso: recibo del depósito, corrida de pagos, KPIs del dashboard y colisión de facturación que el modelo anterior rompía | 283 | 28 |
| 2.2.0 | 2026-07-29 | Anulación permanente (`voided_at`/`voided_reason`) del historial de incremento del depósito no aplicado cuando el depósito llega a un estado terminal, aplicada tanto por el cronjob diario como al momento en los endpoints de cancelar/reembolsar/transferir depósito (PAY-EDGE-004 ampliada). Soporte de incremento anual (renta y depósito) en `POST /api/contracts/renew`, con el mismo comportamiento, validaciones y shape que `POST /api/contracts` (CONT-V-005, CONT-V-006, CONT-V-007, CONT-D-005, CONT-D-006, PAY-R-004, PAY-V-005, PAY-D-005, PAY-D-007 extendidas a `ContractRenewRequestHandler`). Nueva regla: si la renovación reutiliza (transfiere) un depósito que ya tiene incremento configurado, se conserva esa configuración y se generan nuevas proyecciones para los años del contrato renovado (PAY-D-009) | 284 | 28 |
| 2.3.0 | 2026-08-02 | Módulo 7 (Pagos) — desglose de impuestos a nivel pago/abono cuando no hay factura vigente, con interruptor de recibo independiente de la facturación: persistencia del snapshot (PAY-D-010), limpieza al facturar y recálculo al cancelar (PAY-D-011), guard contra cambio de configuración de desglose si hay factura vigente (PAY-R-006), y prorrateo de abonos con ajuste de redondeo en el último abono (PAY-EDGE-005) | 288 | 28 |
| 2.4.0 | 2026-08-07 | Módulo 23 (Sellos) — la renovación de suscripción (Stripe `subscription_cycle` y PayPal sale recurrente) asigna sellos del periodo igual que la contratación inicial (STAMP-D-003) | 289 | 28 |
| 2.5.0 | 2026-08-08 | Módulo 23 (Sellos) — el historial distingue origen: asignación manual, compra, remoción, contratación y renovación (STAMP-D-004) | 290 | 28 |
| 2.6.0 | 2026-08-09 | Módulo 23 (Sellos) — fórmulas de asignación por concepto aisladas de la lógica de suscripción/pasarela: aumento = diferencial × meses restantes, disminución = 0, suscripción/renovación = props × frecuencia (STAMP-D-005) | 291 | 28 |
| 2.7.0 | 2026-09-18 | Auditoría de cobertura contra `specs/`: agregado Módulo 29 (Gestión de Proveedores, prefijo `SUPP`, 18 reglas) documentando `specs/009-supplier-management` — directorio de proveedores, categorías de servicio, restricciones de eliminación, derivación de nombre mostrar — y Módulo 30 (Conciliación Bancaria, prefijo `BANK`, 19 reglas) documentando `specs/004-bank-reconciliation` | 328 | 30 |
| 2.8.0 | 2026-09-21 | Medición de conversiones con Meta (Pixel + Conversions API): registro con periodo de prueba activado (AUTH-D-010), primer pago de suscripción (PAY-D-012) y restricción por configuración/consentimiento con datos protegidos (AUTH-R-009) | 331 | 30 |
| 2.9.0 | 2026-10-02 | Módulo 8 (Facturas) — cancelación con motivo 01: el folio de sustitución es obligatorio y no puede ser la propia factura ni la que esta sustituye (CFDI-V-005); un rechazo del fisco ya no deja la factura en espera de cancelación (CFDI-EDGE-005); reintento de la cancelación motivo 01 de una factura en espera (CFDI-EDGE-006) | 353 | 30 |
| 2.10.0 | 2026-10-04 | Auditoría de cobertura contra `ContractCreateRequest`/`FrequencyType`/`ContractModality`: Módulo 6 (Contratos) no documentaba las modalidades de creación de un contrato — periodicidad de pago (mensual, anual, quincenal, bimestral, trimestral, semestral, diaria o personalizada) y clasificación opcional Arrendamiento/Hospedaje (CONT-V-008, CONT-V-009, CONT-D-009) — ni la configuración del depósito en garantía al momento de crear el contrato, distinta de su incremento anual ya documentado (PAY-V-006, PAY-D-013). Se corrige además la terminología: el catálogo usaba el anglicismo "lessor"/"tenant" en vez de "arrendador"/"inquilino", que es el término real usado en la aplicación | 358 | 30 |

> Para agregar una entrada: indicar versión (semver), fecha, descripción del cambio, y conteo actualizado de reglas/módulos.
