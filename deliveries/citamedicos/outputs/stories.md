# Historias refinadas — citamedicos

> Fecha: 2026-06-20 | Gate: DoR/INVEST ✓

---

## E-01 · Agenda Unificada

### US-02 · Bloqueo automático de conflictos de horario   ·   épica E-01   ·   3 pts
**Como** recepcionista, **quiero** que el sistema rechace automáticamente el registro de una cita si el horario o el consultorio ya está ocupado, **para** eliminar citas dobles sin depender de mi verificación manual.

Criterios de aceptación (Gherkin):
- Dado que un horario está ocupado, cuando la recepcionista intenta registrar otra cita en ese mismo horario y consultorio, entonces el sistema bloquea la operación y muestra un mensaje indicando el conflicto.
- Dado que la cita se registra correctamente, cuando se confirma la reserva, entonces el horario queda bloqueado de inmediato para el resto de usuarios.

Origen: us:US-02 · req:R-02

---

### US-01 · Calendario unificado en tiempo real   ·   épica E-01   ·   5 pts
**Como** recepcionista, **quiero** ver en un único calendario la disponibilidad de todos los médicos y consultorios en tiempo real, **para** agendar citas sin revisar varias fuentes ni llamar a colegas.

Criterios de aceptación (Gherkin):
- Dado que la recepcionista abre el módulo de agenda, cuando selecciona un médico y una fecha, entonces el sistema muestra todos los horarios disponibles y ocupados de ese médico para ese día.
- Dado que hay más de un médico activo, cuando la recepcionista filtra por especialidad, entonces el calendario muestra únicamente los médicos de esa especialidad.

Origen: us:US-01 · req:R-01 · req:R-03

---

### US-03 · Recordatorio automático al paciente 24 h antes   ·   épica E-01   ·   3 pts
**Como** recepcionista, **quiero** que el sistema envíe un recordatorio automático al paciente 24 horas antes de su cita por correo electrónico, **para** reducir inasistencias sin tener que llamarles manualmente.

Criterios de aceptación (Gherkin):
- Dado que una cita está confirmada, cuando faltan 24 horas para la cita, entonces el sistema envía automáticamente un correo electrónico al paciente con fecha, hora y nombre del médico.
- Dado que el recordatorio se envió, cuando la recepcionista revisa la cita, entonces el sistema muestra el estado "recordatorio enviado" con marca de tiempo.

Origen: us:US-03 · req:R-05

---

## E-02 · Historia Clínica Centralizada

### US-04 · Acceso rápido a antecedentes, alergias y medicamentos   ·   épica E-02   ·   5 pts
**Como** médico especialista, **quiero** acceder a los antecedentes, alergias y medicamentos actuales del paciente al iniciar la consulta, **para** no depender de documentos físicos dispersos ni tener que preguntar datos ya registrados.

Criterios de aceptación (Gherkin):
- Dado que el médico inicia una consulta, cuando busca al paciente por nombre o número de historia, entonces el sistema muestra en menos de 3 segundos sus antecedentes, alergias activas y medicamentos actuales.
- Dado que el paciente tiene alergias registradas, cuando el médico accede a su ficha, entonces las alergias se muestran destacadas visualmente antes del resto de la información.

Origen: us:US-04 · req:R-07 · req:R-08 · req:R-17

---

### US-05 · Registro estructurado y cronológico de la evolución de consulta   ·   épica E-02   ·   3 pts
**Como** médico especialista, **quiero** registrar los hallazgos y diagnóstico de cada consulta de forma estructurada, **para** que queden disponibles como historial cronológico en visitas futuras y accesibles desde cualquier sede.

Criterios de aceptación (Gherkin):
- Dado que el médico terminó la consulta, cuando guarda la nota de evolución, entonces queda asociada al paciente en orden cronológico y visible para cualquier médico autorizado de la clínica.
- Dado que el médico busca consultas anteriores, cuando abre el historial, entonces las entradas aparecen ordenadas por fecha con médico tratante y diagnóstico principal.

Origen: us:US-05 · req:R-09 · req:R-18

---

## E-03 · Facturación Automática

### US-06 · Traspaso automático de servicios al módulo de facturación   ·   épica E-03   ·   5 pts
**Como** responsable de facturación, **quiero** que los servicios realizados en cada visita lleguen automáticamente al módulo de facturación, **para** eliminar la transcripción manual y la duplicación de trabajo entre áreas.

Criterios de aceptación (Gherkin):
- Dado que el médico cierra una consulta y registra los procedimientos realizados, cuando el responsable de facturación abre el expediente de esa visita, entonces los procedimientos aparecen con código, descripción y precio sin ingreso manual.
- Dado que ocurre un error de traspaso, cuando el sistema no puede transferir un servicio, entonces notifica al responsable de facturación con el detalle del problema.

Origen: us:US-06 · req:R-12

---

### US-07 · Consolidación de servicios y generación de factura   ·   épica E-03   ·   3 pts
**Como** responsable de facturación, **quiero** que el sistema consolide todos los servicios de una misma visita y genere la factura correspondiente, **para** no armar manualmente la cuenta de pacientes que recibieron varias atenciones.

Criterios de aceptación (Gherkin):
- Dado que un paciente recibió más de un servicio en la misma visita, cuando el responsable de facturación solicita generar la factura, entonces el sistema muestra un resumen con todos los servicios consolidados y el total antes de confirmar.
- Dado que la factura se confirma, cuando el sistema la registra, entonces el estado del pago queda visible para seguimiento posterior.

Origen: us:US-07 · req:R-13 · req:R-15

---

## Decisiones de refinamiento

**OQ-01 — Canal de recordatorio (US-03).**
El inbox (req:R-05, us:US-03) no especificaba el canal de notificación. Decisión de MVP: canal = correo electrónico. Motivo: es el canal de menor complejidad de integración; no requiere proveedor de SMS ni aprobación de WhatsApp Business API. El `want` y los criterios de aceptación de US-03 quedan actualizados con esta especificación. Las restricciones de canal adicionales (SMS, WhatsApp) se difieren a una iteración posterior.

**OQ-02 — SLA de carga del calendario (US-01).**
R-17 define un tiempo de respuesta máximo de 3 segundos exclusivamente para la historia clínica. El inbox no establece ningún SLA equivalente para la vista de agenda. La pregunta no era bloqueante para US-01, ya que la historia no promete rendimiento específico del calendario. La definición de un SLA de carga para la agenda queda diferida a la segunda iteración; si se establece, requerirá añadir un criterio de aceptación medible a US-01.

**OQ-03 — Permisos granulares por rol sobre la HC (US-04, US-05).**
R-18 menciona "control de acceso por rol" sin detallar permisos por subtipo de médico. Decisión de MVP: cualquier usuario con rol "médico" y sesión activa en la clínica puede leer la historia clínica. Las restricciones granulares (médico tratante vs. médico de guardia vs. otra especialidad) se difieren a la segunda iteración. Los criterios de aceptación de US-04 y US-05 expresan "médico autorizado de la clínica", que es la definición suficiente para el MVP.
