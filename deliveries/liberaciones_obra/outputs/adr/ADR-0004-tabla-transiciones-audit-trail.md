# ADR-0004 · Tabla de transiciones append-only como implementación del audit trail

**Estado:** aceptado
**Fecha:** 2026-06-28

---

## Contexto y fuerza

R-03 define la secuencia de estados: `solicitado → observado → corregido → liberado`.
R-06 exige que todo cambio de estado quede registrado con la identidad del usuario
que lo realizó y el timestamp exacto. R-18 exige que los registros aprobados sean
inmutables y que cualquier intento de modificación genere un rastro irrefutable.

Estas tres reglas no son capas decorativas: son el contrato que decide si el
Fiscalizador del Cliente confía en el sistema como sustituto de sus procesos en
papel (`personas#fiscalizador_cliente`, pain: `sin-valor-documental`; OQ-01).
La Coordinadora Documental depende de este rastro para armar dossiers sin perseguir
archivos dispersos (E-04-S01, E-04-S02).

La pregunta es cómo implementar la máquina de estados de forma que el audit trail
sea estructuralmente confiable, no una capa separada que puede quedar desincronizada
con el estado real del sistema.

---

## Decisión

La máquina de estados se implementa sobre una tabla `state_transitions` en
PostgreSQL con las siguientes propiedades:

1. **Append-only por restricción de base de datos**: la tabla no tiene operación
   UPDATE ni DELETE habilitada para la aplicación (se usa un usuario de BD con
   permisos solo de INSERT y SELECT sobre esta tabla). Ninguna capa de aplicación
   puede sobrescribir o borrar una transición registrada.

2. **El estado actual es derivado**: no existe una columna `estado_actual` en la
   tabla de solicitudes que se actualiza; el estado actual se deriva de la última
   fila de `state_transitions` correspondiente a esa solicitud. Esto elimina la
   posibilidad de que el estado "visible" y el historial queden inconsistentes.

3. **Cada fila contiene**: `id`, `solicitud_id`, `from_state`, `to_state`,
   `actor_id` (referencia a `users`), `actor_name` (desnormalizado para legibilidad
   del historial), `timestamp` (con timezone, UTC), `metadata` opcional (e.g., razón
   del rechazo o intento de edición bloqueado).

4. **Inmutabilidad post-`liberado`**: el Módulo Máquina de Estados verifica en
   la aplicación si el último estado es `liberado` antes de aceptar cualquier
   transición o edición. Si el intento es bloqueado, se escribe igualmente una
   fila en `state_transitions` con `from_state = liberado` y `to_state = intento_bloqueado`,
   registrando el actor y el timestamp del intento (E-04-S02, R-18).

5. **Transición atómica**: la escritura en `state_transitions` y cualquier
   efecto secundario (e.g., vincular una referencia de foto a la observación) se
   ejecutan en la misma transacción de BD. Si algo falla, nada se persiste.

---

## Alternativas consideradas

- **Columna `estado` mutable en la tabla `solicitudes` más log separado** —
  El estado actual se actualiza en la fila de la solicitud; el log escribe en una
  tabla separada en un segundo paso. Descartada: los dos pasos pueden quedar
  inconsistentes si la transacción falla entre ellos; la tabla de log puede ser
  omitida por descuido en el código. El audit trail deja de ser la fuente de
  verdad y se convierte en una copia que puede derivar.

- **Event sourcing completo (solo eventos, sin estado derivado materializado)** —
  La arquitectura más pura para audit trail: el estado se reconstruye siempre
  desde el stream de eventos. Descartada para el piloto: requiere infraestructura
  adicional (event store o proyecciones materializadas), complejidad de depuración
  y curva de aprendizaje que no está justificada por el alcance del MVP. La tabla
  append-only captura los mismos beneficios de integridad con menor complejidad.

- **Log de auditoría en tabla separada gestionada por triggers de BD** —
  Triggers de PostgreSQL capturan cambios automáticamente sin depender del código
  de aplicación. Parcialmente considerada: los triggers garantizan que ningún
  UPDATE pase sin registrarse. Descartada como mecanismo único porque el trigger
  opera a nivel de fila de BD, no de acción de usuario con contexto (quien es el
  actor autenticado en ese momento). Combinar triggers con la tabla de transiciones
  podría añadirse como capa de seguridad adicional en una iteración posterior, no
  como diseño primario del piloto.

---

## Consecuencias

**Ganamos:**
- La fuente de verdad del estado del sistema y el audit trail son el mismo
  artefacto: `state_transitions`. No pueden divergir.
- R-06 se cumple de forma estructural: no existe la opción de cambiar un estado
  sin que el actor y el timestamp queden registrados.
- R-18 se cumple de dos formas: el Módulo de Aplicación bloquea y registra el
  intento; la restricción de permisos de BD impide que cualquier otro vector de
  acceso altere la tabla.
- E-04-S01 (historial cronológico) es una consulta `SELECT ... ORDER BY timestamp`
  sobre `state_transitions`; no requiere lógica adicional.

**Costo que aceptamos:**
- Las consultas que necesitan el estado actual (por ejemplo, el tablero de E-03-S03)
  requieren un `SELECT ... ORDER BY timestamp DESC LIMIT 1` o una vista materializada.
  Para el volumen del piloto (decenas a centenas de solicitudes) el costo es
  insignificante. Si el sistema escala a miles de solicitudes, puede añadirse un
  índice sobre `(solicitud_id, timestamp DESC)` o una columna `estado_actual`
  en `solicitudes` mantenida por la aplicación como caché de lectura, con la tabla
  `state_transitions` como fuente de verdad.
- El diseño requiere disciplina en el equipo: todo cambio de estado debe pasar
  por el Módulo Máquina de Estados, nunca por un UPDATE directo sobre la tabla
  de solicitudes. Esta regla debe estar documentada en el onboarding del proyecto.
