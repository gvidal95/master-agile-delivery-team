# ADR-0003 · Procesar recordatorios por correo mediante cola de tareas asincrona

**Estado:** aceptado
**Fecha:** 2026-06-20

## Contexto y fuerza

US-03 (req:R-05) establece que el sistema debe enviar un recordatorio automático al paciente 24 horas antes de su cita. El criterio de aceptación exige que el recordatorio se dispare en el momento exacto T-24 h sin intervención manual y que la recepcionista pueda verificar el estado "recordatorio enviado" con marca de tiempo.

Dos restricciones no funcionales condicionan cómo se implementa este envío:

- **R-19 (disponibilidad):** El sistema no debe interrumpirse durante el horario de atención. Si el proveedor de correo electrónico no está disponible en el momento del envío, la operación de confirmación de cita no debe fallar ni degradar la experiencia del usuario.
- **R-19 combinado con la naturaleza del envío:** El envío de correo es una operación con latencia variable e impredecible (depende de un proveedor externo). Ejecutarlo en el mismo hilo del request HTTP que confirma la cita añadiría latencia y crearía una dependencia de disponibilidad del proveedor SMTP.

El inbox no especifica el canal de comunicación con el paciente (OQ-01 en epics.md). Se adopta correo electrónico como canal por ser el más estándar y de menor costo operativo; si se confirma otro canal (SMS, WhatsApp) antes del sprint, la arquitectura de cola lo admite sin cambio estructural.

## Decisión

Se introduce un **worker asíncrono con cola de tareas** (ej. Celery + Redis, o equivalente) separado del proceso principal del backend. El flujo es:

1. Cuando la recepcionista confirma una cita, el bounded context Agenda **encola** una tarea programada con los datos de la cita y el instante de ejecución T-24 h.
2. El worker, en el instante T-24 h, extrae la tarea, genera el correo y lo envía al proveedor SMTP configurable.
3. El worker actualiza el estado de la tarea en la cola; el bounded context Agenda refleja el estado "recordatorio enviado" en la vista de la cita (CA2 de US-03).
4. Si el envío falla, el worker reintenta un número configurable de veces antes de marcar la tarea como fallida y notificar internamente.

El proveedor SMTP queda desacoplado: se configura por variable de entorno (SMTP_HOST, SMTP_PORT, SMTP_USER, SMTP_PASS). En el MVP se usará un proveedor SMTP estándar; en iteraciones futuras puede sustituirse por otro canal sin cambiar la lógica de Agenda.

## Alternativas consideradas

- **Cron job simple que recorre la base de datos** — un proceso programado que cada N minutos busca citas en T-24 h y envía el correo. Descartado: genera carga de lectura constante sobre la base de datos; difícil de reintentar ante fallos de envío sin un registro de estado; no escala si el volumen de citas crece; el intervalo del cron introduce un margen de error en la hora exacta de envío.
- **Envío sincrono en el request HTTP de confirmación de cita** — el controller llama directamente al proveedor SMTP en el momento de confirmar. Descartado: añade la latencia del SMTP al tiempo de respuesta del usuario; si el proveedor falla, la confirmación de cita falla (viola R-19); no permite programar el envío para T-24 h (habría que calcular una espera síncrona, lo cual es inviable).
- **Servicio de mensajeria externo gestionado (ej. AWS SQS + Lambda)** — externaliza la cola y el worker. Descartado en el MVP: añade dependencia de infraestructura externa y costo variable; el inbox no menciona infraestructura cloud y la clínica es pequeña. Puede evaluarse si la clínica crece.

## Consecuencias

**Ganamos:**
- El flujo de confirmación de cita es independiente del proveedor SMTP: R-19 se cumple incluso si el correo falla.
- El worker gestiona reintentos automáticamente, lo que aumenta la fiabilidad de entrega del recordatorio.
- El desacoplamiento del canal (SMTP configurable) permite cambiar o añadir canales (SMS, WhatsApp) sin modificar el bounded context Agenda.
- El estado de la tarea queda persistido, lo que habilita el CA2 de US-03 (estado "recordatorio enviado" con marca de tiempo).

**Costo que aceptamos:**
- Se introduce un segundo proceso (worker) que debe desplegarse y monitorearse junto al backend. Añade complejidad operativa mínima pero real: si el worker no está activo, los recordatorios no se envían.
- Se requiere un broker de mensajería (Redis o equivalente) como componente adicional de infraestructura.
- La entrega del recordatorio no es estrictamente instantánea: existe un margen de minutos según la frecuencia de polling del worker. Aceptable para un recordatorio de 24 h.
