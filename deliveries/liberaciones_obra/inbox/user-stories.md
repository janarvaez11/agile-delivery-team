# User Stories — Liberaciones de Obra

Generado el 2026-06-21. Fuentes: 5 entrevistas en `interviews/`.
Cubre únicamente el alcance del MVP; las historias fuera de alcance quedan
documentadas en `mvp-canvas.md`.

---

## Núcleo: solicitud y flujo de liberación

- **[US-01]** Como Residente de Obra, quiero registrar una solicitud de liberación
  para una actividad completada —con código de frente, responsable y documentos
  adjuntos— para que calidad pueda procesarla sin necesitar mensajes por WhatsApp.
  - Criterios de aceptación:
    - Dado que soy residente autenticado, cuando completo el formulario con código
      de frente, responsable y al menos un adjunto, entonces el sistema crea la
      solicitud en estado "solicitado" y queda visible para todos los roles.
    - Dado que la solicitud fue creada, cuando el inspector la abre, entonces puede
      registrar una observación o aprobar la liberación directamente desde ahí, sin
      buscar el contexto en otro canal.
  - Fuente: `residente_obra.md`

- **[US-02]** Como Inspector de Calidad, quiero registrar una observación con
  descripción, foto y responsable de corrección directamente desde el celular en
  el momento de la inspección, para no tener que ordenar fotos al final del día.
  - Criterios de aceptación:
    - Dado que estoy en campo ante una solicitud, cuando registro la observación con
      foto, responsable y fecha compromiso, entonces la solicitud pasa a "observado"
      y el residente recibe notificación.
    - Dado que adjunto la foto, cuando queda guardada, entonces aparece vinculada a
      esa observación específica (no a la galería general del dispositivo).
  - Fuente: `inspector_calidad.md`

- **[US-03]** Como Inspector de Calidad o Fiscalizador del Cliente, quiero ser el
  único rol autorizado para cerrar una observación o cambiar el estado a "liberado",
  para que no se cierre sin revisión correcta.
  - Criterios de aceptación:
    - Dado que un usuario con rol "producción" intenta cerrar una observación,
      entonces el sistema bloquea la acción y muestra un mensaje de rol insuficiente.
    - Dado que un usuario con rol "calidad" o "cliente" cierra la observación,
      entonces el sistema registra su identidad, la fecha y avanza al estado siguiente.
  - Fuente: `inspector_calidad.md`, `fiscalizador_cliente.md`

- **[US-04]** Como Fiscalizador del Cliente, quiero revisar y aprobar una liberación
  con toda la evidencia (foto de la observación y foto de la corrección) disponible
  en un solo lugar, para poder aprobar en el primer intento sin pedir respaldo
  adicional.
  - Criterios de aceptación:
    - Dado que una solicitud está en estado "corregido", cuando la abro, entonces
      veo la foto de la observación original, la foto de la corrección, el responsable
      de cada cambio de estado y la marca de tiempo correspondiente.
    - Dado que apruebo la liberación, entonces el sistema registra mi identidad y la
      fecha como cierre formal, y el estado pasa a "liberado" de forma inmutable.
  - Fuente: `fiscalizador_cliente.md`

---

## Visibilidad de estado

- **[US-05]** Como Residente de Obra, quiero ver el estado actual de cada
  actividad/frente (solicitado, observado, corregido, liberado) y el responsable en
  turno, para planificar el día siguiente sin descubrir bloqueos en el último
  momento.
  - Criterios de aceptación:
    - Dado que accedo a la vista de frentes, cuando la consulto al cierre de la
      jornada, entonces veo para cada actividad su estado actual y qué rol tiene la
      responsabilidad ahora (calidad, producción o cliente).
    - Dado que una actividad tiene una observación cuya fecha compromiso ya venció,
      entonces aparece diferenciada visualmente del resto de actividades.
  - Fuente: `residente_obra.md`

- **[US-06]** Como Jefe de Proyecto, quiero ver un tablero con las liberaciones
  agrupadas por estado para identificar qué frentes bloquean la facturación sin
  armar el resumen manualmente.
  - Criterios de aceptación:
    - Dado que accedo al tablero, cuando lo consulto, entonces veo el conteo de
      liberaciones por estado (solicitadas, observadas, corregidas, liberadas) en
      tiempo real.
    - Dado que existen observaciones cuya fecha compromiso ya venció, entonces
      aparecen agrupadas en una categoría "vencidas" separada, con el responsable
      identificado y los días de retraso.
  - Fuente: `jefe_proyecto.md`

---

## Trazabilidad documental

- **[US-07]** Como Coordinadora Documental, quiero que cada solicitud, observación,
  corrección y aprobación quede registrada con código de frente, identidad del
  responsable y marca de tiempo, para armar el dossier sin perseguir archivos de
  cada área.
  - Criterios de aceptación:
    - Dado que consulto el historial de una solicitud, cuando la abro, entonces veo
      la cadena completa de cambios de estado en orden cronológico: quién hizo qué
      y cuándo.
    - Dado que un registro fue aprobado por el fiscalizador, entonces no puede
      editarse sin que el sistema genere un rastro de auditoría inmutable (quién
      intentó modificarlo y cuándo).
  - Fuente: `coordinador_documental.md`

---

## Resumen de cobertura

| Historia | Persona principal | Requisitos cubiertos |
|---|---|---|
| US-01 | Residente de Obra | R-01, R-11 |
| US-02 | Inspector de Calidad | R-02, R-05, R-17 |
| US-03 | Inspector de Calidad / Fiscalizador | R-04 |
| US-04 | Fiscalizador del Cliente | R-05, R-06, R-12 |
| US-05 | Residente de Obra | R-07, R-14 |
| US-06 | Jefe de Proyecto | R-08 |
| US-07 | Coordinadora Documental | R-06, R-15, R-18 |
