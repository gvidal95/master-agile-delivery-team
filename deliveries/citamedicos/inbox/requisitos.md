# Requisitos Candidatos — citamedicos

> Artefacto generado desde las entrevistas de `discoveries/citamedicos/interviews/`.
> Fecha: 2026-06-18

---

## Funcionales

- **[R-01]** El sistema debe mostrar en tiempo real la disponibilidad de médicos y consultorios.
  - Tipo: funcional
  - Origen: recepcionista.md · Recepcionista
  - Dolor: `conflictos-horario`, `verificacion-manual-disponibilidad`

- **[R-02]** El sistema debe validar y bloquear automáticamente la asignación de una cita si el horario o el consultorio ya están ocupados.
  - Tipo: funcional
  - Origen: recepcionista.md · Recepcionista
  - Dolor: `conflictos-horario`

- **[R-03]** El sistema debe unificar en un único calendario compartido las agendas de todos los médicos y consultorios, independientemente de la especialidad.
  - Tipo: funcional
  - Origen: recepcionista.md · Recepcionista
  - Dolor: `sistemas-fragmentados`

- **[R-04]** El sistema debe notificar automáticamente a los pacientes afectados cuando un médico modifica su horario y permitir reagendar sin intervención manual masiva.
  - Tipo: funcional
  - Origen: recepcionista.md · Recepcionista
  - Dolor: `reagendamiento-manual`

- **[R-05]** El sistema debe enviar recordatorios automáticos de cita a los pacientes.
  - Tipo: funcional
  - Origen: recepcionista.md · Recepcionista
  - Dolor: `sin-recordatorios`

- **[R-06]** El sistema debe mantener una lista de espera activa por médico/especialidad y ofrecer el hueco disponible cuando se produzca una cancelación.
  - Tipo: funcional
  - Origen: recepcionista.md · Recepcionista
  - Dolor: `sin-lista-espera`

- **[R-07]** El sistema debe mantener una historia clínica centralizada por paciente, accesible desde cualquier sede.
  - Tipo: funcional
  - Origen: medico.md · Médico Especialista
  - Dolor: `informacion-dispersa`, `busqueda-lenta-hc`

- **[R-08]** La historia clínica debe incluir antecedentes, alergias, medicamentos actuales, resultados de laboratorio y diagnósticos anteriores.
  - Tipo: funcional
  - Origen: medico.md · Médico Especialista
  - Dolor: `informacion-dispersa`, `riesgo-omision-datos`

- **[R-09]** El sistema debe permitir al médico registrar la evolución de la consulta de forma estructurada y cronológica.
  - Tipo: funcional
  - Origen: medico.md · Médico Especialista
  - Dolor: `informacion-dispersa`

- **[R-10]** El sistema debe permitir cargar y visualizar resultados de exámenes e imágenes diagnósticas asociados al paciente.
  - Tipo: funcional
  - Origen: medico.md · Médico Especialista
  - Dolor: `informacion-dispersa`, `busqueda-lenta-hc`

- **[R-11]** El sistema debe soportar prescripción electrónica emitida desde la consulta.
  - Tipo: funcional
  - Origen: medico.md · Médico Especialista
  - Dolor: `informacion-dispersa`

- **[R-12]** El sistema debe registrar automáticamente los servicios y procedimientos realizados en cada visita y trasladarlos al módulo de facturación sin intervención manual.
  - Tipo: funcional
  - Origen: responsable_facturacion.md · Responsable de Facturación
  - Dolor: `procedimientos-mal-registrados`, `duplicacion-trabajo`

- **[R-13]** El sistema debe consolidar automáticamente todos los servicios de una misma visita y generar la factura correspondiente.
  - Tipo: funcional
  - Origen: responsable_facturacion.md · Responsable de Facturación
  - Dolor: `consolidacion-manual-servicios`

- **[R-14]** El sistema debe integrarse con aseguradoras para validar coberturas y generar la documentación requerida por cada una.
  - Tipo: funcional
  - Origen: responsable_facturacion.md · Responsable de Facturación
  - Dolor: `validacion-aseguradoras-lenta`

- **[R-15]** El sistema debe controlar y reportar el estado de pagos pendientes por paciente.
  - Tipo: funcional
  - Origen: responsable_facturacion.md · Responsable de Facturación
  - Dolor: `procedimientos-mal-registrados`

- **[R-16]** El sistema debe generar informes financieros automáticos para la administración.
  - Tipo: funcional
  - Origen: responsable_facturacion.md · Responsable de Facturación
  - Dolor: `duplicacion-trabajo`

---

## No funcionales

- **[R-17]** La historia clínica del paciente debe cargarse en menos de 3 segundos al iniciar una consulta.
  - Tipo: no funcional (rendimiento)
  - Origen: medico.md · Médico Especialista
  - Dolor: `busqueda-lenta-hc`

- **[R-18]** El sistema debe garantizar confidencialidad de la historia clínica y datos de facturación con control de acceso por rol.
  - Tipo: no funcional (seguridad)
  - Origen: medico.md, responsable_facturacion.md
  - Dolor: `riesgo-omision-datos`, `procedimientos-mal-registrados`

- **[R-19]** El sistema debe estar disponible sin interrupciones durante el horario de atención de la clínica.
  - Tipo: no funcional (disponibilidad)
  - Origen: recepcionista.md, medico.md
  - Dolor: `coordinacion-consume-tiempo`, `busqueda-lenta-hc`
