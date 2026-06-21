# ADR-0002 · Usar PostgreSQL relacional con schemas separados por bounded context

**Estado:** aceptado
**Fecha:** 2026-06-20

## Contexto y fuerza

Tres fuerzas del Discovery obligan a decidir la estrategia de persistencia:

1. **R-17 (rendimiento):** La historia clínica debe cargarse en menos de 3 segundos en el 90 % de los accesos. Este SLA requiere un motor de base de datos con soporte maduro de índices y capacidad de optimización de consultas.
2. **R-18 (seguridad):** Se exige confidencialidad de HC y datos de facturación con control de acceso por rol. El esquema de datos debe permitir aislar los datos sensibles y aplicar permisos a nivel de schema.
3. **US-02 / req:R-02 (integridad transaccional):** El bloqueo automático de conflictos de cita requiere que la reserva de un horario sea atómica; si dos recepcionistas intentan registrar la misma cita simultáneamente, solo una debe tener éxito. Esto exige soporte de transacciones ACID y, opcionalmente, bloqueo pesimista a nivel de fila.

Los datos del sistema tienen relaciones fuertes y bien definidas: un paciente tiene múltiples citas; una cita está asociada a un médico, un consultorio, una nota de evolución y un ítem de facturación. Estas relaciones son naturales para el modelo relacional.

## Decisión

Se utiliza **PostgreSQL** como única base de datos del MVP, con **schemas separados por bounded context** (`agenda`, `historia_clinica`, `facturacion`, `auth`) dentro de una misma instancia. La separación por schema proporciona aislamiento lógico sin la complejidad operativa de múltiples instancias. Los índices sobre campos de búsqueda frecuente (número de historia clínica, fecha de cita, id de paciente) son obligatorios para cumplir R-17.

## Alternativas consideradas

- **Base de datos NoSQL (ej. MongoDB)** — esquema flexible, escalado horizontal sencillo. Descartada: la flexibilidad de esquema no aporta valor cuando las entidades están bien definidas; las relaciones fuertes entre paciente, cita y nota clínica requieren joins que son más costosos y menos naturales en documentos. La consistencia transaccional ACID es más difícil de garantizar para el bloqueo de citas dobles (US-02).
- **Una base de datos por bounded context (instancias separadas)** — máximo aislamiento, permite escalar módulos de forma independiente. Descartada en el MVP: añade overhead operativo (múltiples instancias, backups separados, conexiones separadas) sin beneficio justificado por el volumen de la clínica. Genera complejidad en el join entre visita (agenda) y nota clínica (historia_clinica) necesario para el traspaso a facturación (US-06). Puede evaluarse en iteraciones futuras si el equipo crece.
- **SQLite** — sin servidor, cero configuración. Descartada: no soporta concurrencia de escrituras simultáneas necesaria para múltiples recepcionistas agendando citas al mismo tiempo; no es adecuada para un sistema multiusuario con R-19 (disponibilidad continua).

## Consecuencias

**Ganamos:**
- Transacciones ACID nativas: el bloqueo de citas (US-02) y el traspaso automático a facturación (US-06) son atómicos.
- Índices B-tree y GIN de PostgreSQL permiten cumplir el SLA de 3 s de R-17 con la carga de datos esperada de una clínica pequeña.
- La separación por schema facilita la auditoría de acceso y el cumplimiento de R-18 mediante permisos de schema en PostgreSQL.
- Una sola instancia simplifica el backup y el mantenimiento operativo en el MVP.

**Costo que aceptamos:**
- La escalabilidad horizontal de escrituras requiere réplicas de PostgreSQL (no incluido en el MVP); aceptable dado el volumen de la clínica.
- Si en el futuro se separa un bounded context a su propio servicio, será necesario migrar su schema a una instancia independiente. El aislamiento lógico por schema facilita esa migración.
