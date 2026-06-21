# ADR-0001 · Adoptar monolito modular con bounded contexts en lugar de microservicios

**Estado:** aceptado
**Fecha:** 2026-06-20

## Contexto y fuerza

El MVP comprende tres módulos de negocio (Agenda, Historia Clínica, Facturación) que comparten entidades centrales: paciente y visita. El traspaso automático de servicios al módulo de facturación (US-06, req:R-12) requiere que el cierre de una consulta en Historia Clínica desencadene inmediatamente la creación de los ítems de facturación; esta operación debe ser transaccional para garantizar la métrica de traspaso ≥ 95 % (mvp-canvas.md — Métrica 3). Asimismo, el bloqueo automático de conflictos de agenda (US-02, req:R-02) requiere transaccionalidad dentro del bounded context Agenda. El inbox no describe un equipo de desarrollo grande ni una carga de tráfico que justifique distribución; la clínica es una organización pequeña con tres roles de usuario (personas.md).

## Decisión

Se adopta una arquitectura de **monolito modular**: un único proceso de backend desplegado como API REST, con separación interna clara de responsabilidades en tres bounded contexts (Agenda, Historia Clínica, Facturación) más un módulo transversal de autenticación. Cada bounded context expone una interfaz interna bien definida; la comunicación entre contexts ocurre mediante llamadas directas en proceso, no mediante red.

## Alternativas consideradas

- **Microservicios desde el inicio** — cada bounded context como servicio independiente con su propia base de datos y comunicación por red (REST o mensajería). Descartado: añade overhead operativo desproporcionado (gestión de red, descubrimiento de servicios, transacciones distribuidas) para el volumen de una clínica pequeña; la transaccionalidad entre Agenda y HC necesaria para el bloqueo de citas dobles (US-02) se vuelve compleja sin un coordinador de transacciones. No hay evidencia en el inbox que justifique esta complejidad en el MVP.
- **Monolito no modular (big ball of mud)** — código sin separación interna de dominios. Descartado: dificulta la evolución y el aislamiento de acceso por rol requerido por R-18; impide extraer bounded contexts a futuro si el sistema crece.

## Consecuencias

**Ganamos:**
- Transaccionalidad local sin coordinadores distribuidos: el bloqueo de citas (US-02) y el traspaso automático a facturación (US-06) se implementan con una sola transacción de base de datos.
- Despliegue y operación simples: un único proceso, un único punto de monitoreo.
- La separación interna por bounded context permite migrar a microservicios en iteraciones futuras si el equipo o el volumen lo justifican.

**Costo que aceptamos:**
- El despliegue del sistema completo se hace como unidad: una falla en el módulo de facturación afecta al proceso completo. Se mitiga con tests de módulo y feature flags.
- El escalado horizontal aplica al monolito completo, no a módulos individuales. Aceptable para el MVP dado el tamaño de la clínica.
