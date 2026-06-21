# ADR-0004 · Autenticacion propia con JWT y tres roles fijos para el MVP

**Estado:** aceptado
**Fecha:** 2026-06-20

## Contexto y fuerza

R-18 exige que el sistema garantice la confidencialidad de la historia clínica y los datos de facturación mediante **control de acceso por rol**. El inbox (personas.md) define exactamente tres roles de usuario: Recepcionista, Médico Especialista y Responsable de Facturación. El MVP define el alcance mínimo de acceso del rol médico (mvp-canvas.md: "rol médico con sesión activa"), con permisos granulares por subtipo de médico diferidos a la iteración 2 (open_question en epics.md).

El inbox no contiene ninguna evidencia de que la clínica disponga de un directorio corporativo (LDAP, Active Directory) ni de un proveedor de identidad externo (SSO, OAuth corporativo). Inventar esa infraestructura violaría la regla de cero invención.

La aplicación es una web interna usada por el personal de la clínica, no un portal de acceso público. La superficie de ataque es acotada.

## Decisión

Se implementa **autenticación propia del sistema** con emisión de **tokens JWT** (JSON Web Tokens) con tiempo de expiración configurable. El módulo de autenticación (`schema: auth`) gestiona usuarios, contraseñas (almacenadas con hash bcrypt) y la asignación de rol. Se definen exactamente tres roles para el MVP:

- `recepcionista`: acceso de lectura y escritura al bounded context Agenda. Sin acceso a Historia Clínica ni Facturación.
- `medico`: acceso de lectura al bounded context Agenda (su propia agenda). Acceso completo de lectura y escritura al bounded context Historia Clínica. Sin acceso a Facturación.
- `facturacion`: acceso de lectura al bounded context Agenda (para verificar visitas). Sin acceso a Historia Clínica. Acceso completo al bounded context Facturación.

Cada endpoint del backend verifica el rol del token antes de procesar la solicitud. Los datos de HC solo son accesibles para el rol `medico`; los de facturación solo para `facturacion`.

## Alternativas consideradas

- **SSO / LDAP / Active Directory** — integración con un directorio corporativo existente. Descartada: no hay evidencia en el inbox de que la clínica disponga de esta infraestructura. Añadir un proveedor de identidad externo sin respaldo en el Discovery es invención; la complejidad de integración no está justificada para el MVP de una clínica pequeña.
- **OAuth 2.0 con proveedor externo (Google, Microsoft)** — autenticación federada. Descartada: introduce dependencia de un proveedor externo para la disponibilidad del sistema (R-19); si el proveedor cae, el personal no puede iniciar sesión durante el horario de atención. Sin evidencia en el inbox de que el personal use cuentas corporativas de estos proveedores.
- **Sin autenticación (acceso abierto)** — inviable por R-18: la ausencia de control de acceso expone la historia clínica a cualquier usuario, lo que viola directamente el requisito de confidencialidad.
- **Sesion de servidor (cookies de sesión)** — alternativa válida a JWT. No se descarta por razones técnicas fuertes; JWT se prefiere porque facilita la verificación stateless en el backend sin consulta a base de datos en cada request, lo que contribuye al SLA de R-17.

## Consecuencias

**Ganamos:**
- R-18 se cumple con una implementación directa y auditable: cada request lleva el rol en el token y el backend lo verifica sin estado compartido.
- El módulo de autenticación es interno y no introduce dependencias externas que afecten a R-19.
- Los tres roles cubren exactamente los actores definidos en el Discovery; no se inventan roles adicionales.
- La estructura de roles está diseñada para extenderse (añadir `medico_guardia`, `medico_especialidad_X`) en la iteración 2 sin cambio arquitectural.

**Costo que aceptamos:**
- La gestión de usuarios (altas, bajas, cambios de contraseña) debe implementarse en el MVP o delegarse a un administrador con acceso directo a la base de datos. No hay historia de usuario que cubra la administración de usuarios en el backlog actual; es una pregunta abierta para aclarar antes del sprint.
- La renovación de tokens (refresh token) debe diseñarse para no interrumpir la sesión del médico durante una consulta larga; este detalle de implementación queda al criterio del Developer en el refinamiento.
- Si en el futuro la clínica adquiere un directorio corporativo, la migración a SSO requerirá reemplazar este módulo. El impacto es acotado porque el módulo de autenticación es el único punto de cambio.
