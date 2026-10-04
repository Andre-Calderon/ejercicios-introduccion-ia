# Catálogo de Reglas de Negocio — Identidad y Acceso

Extracto temático de [`corpus_catalog_2026-10-04.md`](../corpus_catalog_2026-10-04.md) (copia íntegra del corpus). Este archivo agrupa los módulos 1, 2, 24, 27 (67 reglas) como documento independiente, para satisfacer el criterio de ≥5 documentos si se exige literalmente ≥5 archivos separados en vez de un solo archivo con módulos internos.

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
