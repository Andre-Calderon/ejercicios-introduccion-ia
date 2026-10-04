# Catálogo de Reglas de Negocio — Partes del Alquiler

Extracto temático de [`corpus_catalog_2026-10-04.md`](../corpus_catalog_2026-10-04.md) (copia íntegra del corpus). Este archivo agrupa los módulos 3, 4, 5 (29 reglas) como documento independiente, para satisfacer el criterio de ≥5 documentos si se exige literalmente ≥5 archivos separados en vez de un solo archivo con módulos internos.

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
