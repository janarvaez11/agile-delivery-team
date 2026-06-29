# Sprint 1 — El equipo de obra registra la primera solicitud de campo con evidencia fotográfica y el Fiscalizador la aprueba formalmente dentro del sistema, sin necesitar WhatsApp ni correos

**Capacidad:** 20 pts · **Comprometido:** 20 pts

## Historias comprometidas

| Historia | Título | Pts | Épica | Prioridad |
|---|---|---|---|---|
| E-01-S01 | Registrar solicitud de liberación | 3 | E-01 | 1 |
| E-01-S02 | Registrar observación con foto desde móvil | 3 | E-01 | 2 |
| E-01-S04 | Control de roles para cierre de observaciones y aprobación | 3 | E-01 | 3 |
| E-01-S05 | Avance automático de estados del flujo de liberación | 3 | E-01 | 4 |
| E-02-S01 | Revisar evidencia completa para decidir aprobación | 3 | E-02 | 5 |
| E-02-S02 | Registrar aprobación formal con identidad y timestamp | 3 | E-02 | 6 |
| E-04-S03 | Asociación del código de frente en todos los registros derivados | 2 | E-04 | 12 |

**Suma:** 3 + 3 + 3 + 3 + 3 + 3 + 2 = **20 pts**

### Cadena de valor del sprint

E-01-S01 establece la solicitud con código de frente → E-01-S02 registra la observación con foto desde el celular → E-01-S04 asegura que solo los roles autorizados cierran y aprueban → E-01-S05 avanza los estados automáticamente con identidad y timestamp en cada transición → E-02-S01 reúne toda la evidencia en una sola vista para el Fiscalizador → E-02-S02 registra la aprobación formal e inmutable. E-04-S03 propaga el código de frente a todos los registros derivados, cimentando el ciclo documental del Sprint 2.

### Justificación de E-04-S03 sobre E-02-S03

Ambas tienen 2 pts y sus dependencias están satisfechas dentro del sprint. Se elige E-04-S03 (prioridad 12) en lugar de E-02-S03 (prioridad 13) por dos razones: (1) E-04-S03 asienta la propagación del código de frente que E-04-S01 y E-04-S02 necesitan en Sprint 2; sin ella, el dossier del Sprint 2 empieza sin cimientos. (2) E-02-S03 tiene OQ-04 abierta (el equipo no confirmó si la descarga de evidencia entra en el piloto o en la siguiente iteración), lo que la hace menos segura para comprometer en este sprint.

## Historias diferidas (próximo sprint)

| Historia | Título | Pts | Razón de diferimiento |
|---|---|---|---|
| E-03-S01 | Ver estado actual y responsable en turno por frente | 3 | Capacidad insuficiente en Sprint 1; agrega valor de planificación al Residente pero no es condición del ciclo de aprobación piloto |
| E-03-S02 | Indicador visual de observaciones con fecha compromiso vencida | 2 | Depende de E-03-S01, que no entra en Sprint 1; diferida junto a su dependencia |
| E-03-S03 | Tablero de liberaciones por estado en tiempo real | 3 | Capacidad insuficiente; requiere E-01-S05 (en Sprint 1) como base; entra en Sprint 2 con cimientos listos |
| E-04-S01 | Historial cronológico completo de una solicitud | 3 | Capacidad insuficiente; el registro de auditoría lo construye E-01-S05 en este sprint; la vista consultable entra en Sprint 2 |
| E-04-S02 | Inmutabilidad de registros aprobados con rastro de auditoría | 3 | Depende de E-02-S02 (en Sprint 1); entra en Sprint 2 una vez que la aprobación formal esté operativa |
| E-02-S03 | Descarga autónoma de evidencia por el Fiscalizador | 2 | Prioridad 13; OQ-04 sin confirmar si entra en el piloto; postergada en favor de E-04-S03 |

## Riesgos activos para este sprint

- **OQ-01** (valor documental de la aprobación digital): Si el Fiscalizador del Cliente no acepta el registro digital con identidad y timestamp como suficiente para sus auditorías internas, E-02-S02 no cierra el ciclo de valor del piloto y la métrica de tasa de aprobación en primer intento no puede medirse al finalizar el sprint. Acción requerida antes de la demo: confirmar con el Fiscalizador que la aprobación dentro del sistema es aceptable. Origen: `mvp-canvas#riesgos-supuestos`, `evidence-map#sin-valor-documental`.

- **OQ-02** (conectividad en los frentes del piloto): Si los frentes seleccionados no tienen conectividad estable, los inspectores no podrán usar la app en el momento de la inspección y volverán al papel, invalidando la adopción de E-01-S02 y bloqueando el ciclo completo. Acción requerida antes del inicio de construcción: verificar cobertura de red en los frentes del piloto. Origen: `mvp-canvas#riesgos-supuestos`, `evidence-map#sin-modo-offline`.
