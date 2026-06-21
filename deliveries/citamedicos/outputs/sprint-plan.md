# Sprint 1 — Al final del Sprint 1, la recepcionista agenda citas sin conflictos desde un calendario unificado, el médico accede a la ficha clínica del paciente en menos de 3 segundos y los pacientes reciben recordatorios automáticos por correo — eliminando las fuentes manuales de error más frecuentes.

**Capacidad:** 20 pts · **Comprometido:** 19 pts · **Buffer:** 1 pt

## Historias comprometidas

| Historia | Pts | Épica | Prioridad | Depende de |
|----------|-----|-------|-----------|------------|
| US-01 · Calendario unificado en tiempo real | 5 | E-01 | 2 | — |
| US-02 · Bloqueo automático de conflictos | 3 | E-01 | 1 | US-01 |
| US-03 · Recordatorio automático 24 h (email) | 3 | E-01 | 7 | US-01 |
| US-04 · Acceso HC < 3 s | 5 | E-02 | 3 | — |
| US-05 · Registro de evolución cronológico | 3 | E-02 | 4 | US-04 |

## Historias diferidas al Sprint 2

| Historia | Pts | Razón |
|----------|-----|-------|
| US-06 · Traspaso automático a facturación | 5 | Depende de US-05; incluirla junto con US-07 excedería la capacidad (19 + 5 = 24 pts) |
| US-07 · Factura consolidada | 3 | Depende de US-06, que queda fuera del sprint |

## Justificación de la selección

Las cinco historias comprometidas se seleccionaron en orden de prioridad global respetando las reglas de dependencia:

1. US-02 tiene prioridad 1 pero depende de US-01. Ambas entran juntas (8 pts) para cerrar la épica de mayor valor (E-01 Agenda Unificada) con su funcionalidad central.
2. US-04 (prioridad 3) es independiente; entra directamente. US-05 (prioridad 4) depende de US-04; ambas entran juntas (8 pts) cerrando el núcleo de E-02 Historia Clínica Centralizada.
3. Acumulado US-01 + US-02 + US-04 + US-05 = 16 pts. Quedan 4 pts de margen.
4. US-03 (prioridad 7, 3 pts) depende de US-01 que ya está en el sprint. Su costo marginal es bajo: el pipeline de notificaciones por correo queda validado desde el Sprint 1 y reduce las inasistencias sin trabajo manual. Total final: 19 pts (1 pt de buffer).
5. US-06 (5 pts) no cabe: depende de US-05 (ya dentro) pero agregarla llevaría el sprint a 24 pts, superando la capacidad. US-07 depende de US-06 y queda igualmente diferida.

## Orden de implementación sugerido

1. US-01 — habilita US-02 y US-03; es la base de la épica de mayor valor.
2. US-02 — bloqueo de conflictos; depende de US-01 y tiene la mayor prioridad global.
3. US-04 — acceso a la HC; es independiente de la agenda y puede iniciarse en paralelo con US-01/US-02.
4. US-05 — registro de evolución; depende de US-04.
5. US-03 — recordatorio por correo; depende de US-01 (disponible desde el paso 1); puede paralelizarse con US-04/US-05.

## Riesgos del Sprint

- Setup del worker asíncrono para el envío de correos (ADR-0003) puede consumir tiempo no estimado en US-03. Se recomienda validar la integración con el proveedor de correo en los primeros días del sprint.
- La tabla de pacientes debe existir y tener datos de prueba antes de que US-04 y US-05 puedan testearse; si no hay fixtures, se bloquea la validación de los criterios de aceptación.
- OQ-04 (migración de datos históricos): el sistema arranca con datos vacíos en Sprint 1 (ADR-0005). Este supuesto de riesgo alto debe comunicarse al cliente antes del inicio del sprint para alinear expectativas sobre el estado inicial del sistema.
