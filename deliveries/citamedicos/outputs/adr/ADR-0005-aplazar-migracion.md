# ADR-0005 · Aplazar la migracion de datos historicos fuera del MVP

**Estado:** aceptado
**Fecha:** 2026-06-20

## Contexto y fuerza

El mvp-canvas.md identifica explícitamente como **supuesto de riesgo alto** el siguiente enunciado: "Los datos históricos de pacientes se pueden migrar o ingresar sin un esfuerzo prohibitivo." El nivel de riesgo es "alto" porque el impacto de una migración incorrecta sobre datos de salud (antecedentes, alergias, medicamentos) puede provocar errores clínicos durante la consulta.

En el backlog actual (backlog.json, epics.md) no existe ninguna historia de usuario ni historia técnica que defina el proceso de migración: origen de datos, formato, volumen, herramienta de extracción, validación, estrategia de rollback. Esta ausencia fue registrada como pregunta abierta OQ-04 en epics.md.

La arquitectura del bounded context Historia Clínica define el schema de la base de datos a partir de las historias del MVP (US-04, US-05); ese schema no ha sido validado en producción. Ejecutar una migración masiva de datos históricos sobre un schema no validado combina dos riesgos independientes: el riesgo del schema cambia con el riesgo de la calidad de los datos de origen.

## Decisión

La **migración de datos históricos de pacientes no forma parte del MVP**. El sistema arranca con datos vacíos; el personal registra los datos de pacientes nuevos directamente en el sistema desde el primer día de operación. Los registros históricos de pacientes existentes se mantienen en el sistema actual (hoja de cálculo u otro) durante el período de validación del MVP. La migración se planifica como historia técnica explícita en la iteración 2, una vez que:

1. El schema de Historia Clínica ha sido validado en producción con datos reales.
2. Se han definido el origen de datos, el volumen y el formato de los registros históricos.
3. Se ha estimado y aprobado el esfuerzo de migración con el cliente.

El equipo debe comunicar este supuesto al cliente antes del inicio del primer sprint.

## Alternativas consideradas

- **Incluir la migracion en el MVP como historia tecnica** — diseñar e implementar la carga de datos históricos como parte del sprint inicial. Descartada: no existe historia técnica en el backlog; el esfuerzo no está estimado; el riesgo de datos incorrectos sobre registros de salud es alto (mvp-canvas.md); bloquearía el sprint si la migración resulta más compleja de lo previsto. El principio de valor antes que solución indica que la clínica obtiene valor del sistema con datos nuevos desde el primer día.
- **Migración parcial (solo pacientes activos)** — importar un subconjunto de registros. Descartada en el MVP: requiere definir los criterios de "paciente activo", validar la calidad de los datos de origen y construir la herramienta de importación. Ninguna de estas actividades está respaldada por evidencia en el inbox ni tiene historia en el backlog. Se puede considerar como alcance de la iteración 2 si el cliente lo prioriza.

## Consecuencias

**Ganamos:**
- El sprint no se bloquea por una tarea de esfuerzo desconocido y riesgo alto.
- El schema de Historia Clínica se valida con datos reales antes de recibir una carga masiva de datos históricos.
- Se elimina el riesgo de corrupción de datos de salud durante el MVP.

**Costo que aceptamos:**
- Durante el periodo del MVP, el médico no tendrá acceso a los antecedentes históricos de pacientes ya existentes en el sistema actual. El personal deberá consultar el sistema anterior de forma paralela para pacientes con historial previo. Este costo operativo temporal es menor que el riesgo de una migración mal ejecutada.
- El cliente debe ser informado explícitamente de esta decisión antes del sprint para alinear expectativas. Si el cliente considera la migración una precondición del MVP, debe abrirse una historia técnica estimada antes de comprometer el plan de sprint.
