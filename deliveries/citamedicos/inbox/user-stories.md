# User Stories — citamedicos

> Historias generadas desde `personas.md` y `requisitos.md` de `discoveries/citamedicos/outputs/`.
> Fecha: 2026-06-18

Alcance del MVP: las historias cubren el **núcleo de valor** — calendario unificado
con bloqueo de conflictos, ficha clínica básica centralizada y traspaso automático
de servicios a facturación. Las funcionalidades fuera de alcance se documentan en
`mvp-canvas.md`.

---

## Módulo de agenda

- **[US-01]** Como recepcionista, quiero ver en un único calendario la disponibilidad
  de todos los médicos y consultorios en tiempo real para agendar citas sin revisar
  varias fuentes ni llamar a colegas.
  - Criterios de aceptación:
    - Dado que la recepcionista abre el módulo de agenda, cuando selecciona un médico
      y una fecha, entonces el sistema muestra todos los horarios disponibles y
      ocupados de ese médico para ese día.
    - Dado que hay más de un médico activo, cuando la recepcionista filtra por
      especialidad, entonces el calendario muestra únicamente los médicos de esa
      especialidad.
  - Fuente: recepcionista.md
  - Requisitos: R-01, R-03

- **[US-02]** Como recepcionista, quiero que el sistema rechace automáticamente el
  registro de una cita si el horario o el consultorio ya está ocupado para eliminar
  citas dobles sin depender de mi verificación manual.
  - Criterios de aceptación:
    - Dado que un horario está ocupado, cuando la recepcionista intenta registrar otra
      cita en ese mismo horario y consultorio, entonces el sistema bloquea la operación
      y muestra un mensaje indicando el conflicto.
    - Dado que la cita se registra correctamente, cuando se confirma la reserva,
      entonces el horario queda bloqueado de inmediato para el resto de usuarios.
  - Fuente: recepcionista.md
  - Requisitos: R-02

- **[US-03]** Como recepcionista, quiero que el sistema envíe un recordatorio
  automático al paciente 24 horas antes de su cita para reducir inasistencias sin
  tener que llamarles manualmente.
  - Criterios de aceptación:
    - Dado que una cita está confirmada, cuando faltan 24 horas para la cita,
      entonces el sistema envía automáticamente un mensaje al paciente con fecha,
      hora y nombre del médico.
    - Dado que el recordatorio se envió, cuando la recepcionista revisa la cita,
      entonces el sistema muestra el estado "recordatorio enviado" con marca de tiempo.
  - Fuente: recepcionista.md
  - Requisitos: R-05

---

## Módulo de historia clínica

- **[US-04]** Como médico especialista, quiero acceder a los antecedentes, alergias
  y medicamentos actuales del paciente al iniciar la consulta para no depender de
  documentos físicos dispersos ni tener que preguntar datos ya registrados.
  - Criterios de aceptación:
    - Dado que el médico inicia una consulta, cuando busca al paciente por nombre o
      número de historia, entonces el sistema muestra en menos de 3 segundos sus
      antecedentes, alergias activas y medicamentos actuales.
    - Dado que el paciente tiene alergias registradas, cuando el médico accede a su
      ficha, entonces las alergias se muestran destacadas visualmente antes del resto
      de la información.
  - Fuente: medico.md
  - Requisitos: R-07, R-08, R-17

- **[US-05]** Como médico especialista, quiero registrar los hallazgos y diagnóstico
  de cada consulta de forma estructurada para que queden disponibles como historial
  cronológico en visitas futuras y accesibles desde cualquier sede.
  - Criterios de aceptación:
    - Dado que el médico terminó la consulta, cuando guarda la nota de evolución,
      entonces queda asociada al paciente en orden cronológico y visible para
      cualquier médico autorizado de la clínica.
    - Dado que el médico busca consultas anteriores, cuando abre el historial,
      entonces las entradas aparecen ordenadas por fecha con médico tratante y
      diagnóstico principal.
  - Fuente: medico.md
  - Requisitos: R-09, R-18

---

## Módulo de facturación

- **[US-06]** Como responsable de facturación, quiero que los servicios realizados
  en cada visita lleguen automáticamente al módulo de facturación para eliminar la
  transcripción manual y la duplicación de trabajo entre áreas.
  - Criterios de aceptación:
    - Dado que el médico cierra una consulta y registra los procedimientos realizados,
      cuando el responsable de facturación abre el expediente de esa visita, entonces
      los procedimientos aparecen con código, descripción y precio sin ingreso manual.
    - Dado que ocurre un error de traspaso, cuando el sistema no puede transferir un
      servicio, entonces notifica al responsable de facturación con el detalle del
      problema.
  - Fuente: responsable_facturacion.md
  - Requisitos: R-12

- **[US-07]** Como responsable de facturación, quiero que el sistema consolide todos
  los servicios de una misma visita y genere la factura correspondiente para no armar
  manualmente la cuenta de pacientes que recibieron varias atenciones.
  - Criterios de aceptación:
    - Dado que un paciente recibió más de un servicio en la misma visita, cuando el
      responsable de facturación solicita generar la factura, entonces el sistema
      muestra un resumen con todos los servicios consolidados y el total antes de
      confirmar.
    - Dado que la factura se confirma, cuando el sistema la registra, entonces el
      estado del pago queda visible para seguimiento posterior.
  - Fuente: responsable_facturacion.md
  - Requisitos: R-13, R-15
