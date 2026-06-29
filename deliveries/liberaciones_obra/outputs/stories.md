# Historias de usuario refinadas — liberaciones_obra

## E-01 · Flujo de solicitud y observación en campo

---

### E-01-S01 · Registrar solicitud de liberación   ·   épica E-01   ·   3 pts
**Como** Residente de Obra, **quiero** registrar una solicitud de liberación con código de frente, responsable, documentos adjuntos y estado inicial 'solicitado', **para** que calidad pueda procesarla sin necesitar coordinar por WhatsApp ni buscar contexto en canales externos.

Criterios de aceptación (Gherkin):
- Dado que el Residente está autenticado, cuando crea una nueva solicitud completando código de frente, responsable y fecha, entonces el sistema guarda la solicitud con estado inicial 'solicitado' y la hace visible para los usuarios con rol de calidad.
- Dado que el Residente intenta guardar una solicitud sin código de frente o sin responsable, cuando confirma la acción, entonces el sistema muestra un error indicando el campo obligatorio faltante y no crea la solicitud.
- Dado que la solicitud fue creada, cuando cualquier usuario autenticado la consulta, entonces puede ver el código de frente, el responsable, la fecha de creación, el estado 'solicitado' y los documentos adjuntos en caso de haberlos.

**Origen:** user-stories#US-01, requisitos#R-01, requisitos#R-11
**Dependencias:** ninguna

---

### E-01-S02 · Registrar observación con foto desde móvil   ·   épica E-01   ·   3 pts
**Como** Inspector de Calidad, **quiero** registrar una observación con descripción, foto vinculada y responsable de corrección directamente desde el celular en el momento de la inspección, **para** que la evidencia quede asociada al punto exacto sin necesidad de ordenar fotos al final del día.

Criterios de aceptación (Gherkin):
- Dado que el Inspector está autenticado y tiene una solicitud abierta en estado 'solicitado', cuando registra una observación con descripción, foto tomada desde el celular, responsable de corrección y fecha compromiso, entonces el sistema guarda la observación vinculada a esa solicitud específica (no a la galería del dispositivo) y cambia el estado de la solicitud a 'observado'.
- Dado que la foto fue adjuntada a la observación, cuando cualquier usuario autenticado abre esa observación, entonces la foto aparece visible como parte de la observación con referencia explícita a la solicitud, no como imagen suelta.
- Dado que el Inspector completa todos los campos de la observación (descripción, foto, responsable, fecha compromiso) y los confirma desde el celular en condiciones normales de conectividad, cuando envía el registro, entonces el sistema confirma el guardado en no más de 60 segundos desde que el Inspector inicia el registro hasta que recibe la confirmación en pantalla.
- Dado que el campo 'responsable de corrección' está vacío, cuando el Inspector intenta guardar la observación, entonces el sistema muestra un mensaje de error indicando el campo obligatorio y no registra la observación.

**Origen:** user-stories#US-02, requisitos#R-02, requisitos#R-05, requisitos#R-17
**Dependencias:** E-01-S01

---

### E-01-S04 · Control de roles para cierre de observaciones y aprobación   ·   épica E-01   ·   3 pts
**Como** Inspector de Calidad, **quiero** que solo los usuarios con rol de calidad o de cliente (fiscalizador) puedan cerrar una observación o cambiar el estado a 'liberado', **para** que ningún cambio de estado crítico quede sin la revisión técnica del responsable correcto.

Criterios de aceptación (Gherkin):
- Dado que un usuario con rol 'producción' intenta cerrar una observación, cuando confirma la acción, entonces el sistema bloquea la operación y muestra un mensaje indicando que el rol no tiene autorización para cerrar observaciones.
- Dado que un usuario con rol 'calidad' cierra una observación, cuando confirma la acción, entonces el sistema registra la identidad del usuario y el timestamp exacto, avanza el estado de la solicitud al siguiente según el flujo definido, y la acción queda en el historial de auditoría.
- Dado que un usuario con rol 'cliente' (fiscalizador) intenta cerrar una observación o aprobar una liberación, cuando confirma la acción, entonces el sistema permite la operación y registra la identidad del usuario y el timestamp correspondiente.
- Dado que cualquier usuario intenta cambiar el estado a 'liberado' sin tener rol de calidad o de cliente, cuando confirma la acción, entonces el sistema bloquea la operación y muestra un mensaje de rol insuficiente.

**Origen:** user-stories#US-03, requisitos#R-04, requisitos#R-06
**Dependencias:** E-01-S01

---

### E-01-S05 · Avance automático de estados del flujo de liberación   ·   épica E-01   ·   3 pts
**Como** cualquier usuario autenticado, **quiero** que el estado de la solicitud avance automáticamente a través del flujo (solicitado → observado → corregido → liberado) según las acciones registradas, con la identidad del actor y el timestamp de cada cambio, **para** que el estado visible siempre refleje la realidad del proceso sin depender de actualizaciones manuales en canales externos.

Criterios de aceptación (Gherkin):
- Dado que el Inspector registra una observación sobre una solicitud en estado 'solicitado', cuando la observación es guardada, entonces el estado de la solicitud cambia automáticamente a 'observado' y el sistema registra la identidad del Inspector y el timestamp del cambio.
- Dado que el Residente marca una solicitud como 'corregida', cuando la acción es confirmada por un usuario con rol autorizado, entonces el estado cambia automáticamente a 'corregido' y el sistema registra la identidad del actor y el timestamp.
- Dado que el Fiscalizador aprueba una solicitud en estado 'corregido', cuando la acción es confirmada, entonces el estado cambia a 'liberado' y el sistema registra la identidad del Fiscalizador y el timestamp, sin posibilidad de retroceder el estado sin rastro de auditoría.
- Dado que cualquier usuario autenticado consulta una solicitud, cuando la visualiza, entonces puede ver el estado actual y en el historial la cadena completa de cambios con identidad del actor y timestamp de cada transición, en orden cronológico.

**Origen:** requisitos#R-03, requisitos#R-06, mvp-canvas#funcionalidades-mínimas
**Dependencias:** E-01-S01, E-01-S02

---

## E-02 · Aprobación del fiscalizador con evidencia completa

---

### E-02-S01 · Revisar evidencia completa para decidir aprobación   ·   épica E-02   ·   3 pts
**Como** Fiscalizador del Cliente, **quiero** revisar una solicitud en estado 'corregido' y ver toda la evidencia (foto de la observación original, foto de la corrección, responsable y timestamp de cada cambio de estado) reunida en un solo lugar, **para** decidir la aprobación sin necesitar pedir respaldo adicional al contratista.

Criterios de aceptación (Gherkin):
- Dado que una solicitud está en estado 'corregido', cuando el Fiscalizador la abre, entonces ve en una sola pantalla: la foto de la observación original, la foto de la corrección, el nombre del responsable de cada cambio de estado y el timestamp correspondiente a cada transición.
- Dado que el Fiscalizador está revisando la evidencia de una solicitud, cuando la visualiza, entonces ningún elemento de la evidencia (fotos, responsables, timestamps) requiere salir del sistema o solicitar archivos al contratista para verificarse.
- Dado que una solicitud en estado 'corregido' no tiene foto de corrección adjuntada, cuando el Fiscalizador la abre, entonces el sistema indica claramente que la evidencia de corrección está incompleta, sin ocultar el campo ni el estado.

**Origen:** user-stories#US-04, requisitos#R-05, requisitos#R-06
**Dependencias:** E-01-S02, E-01-S05

---

### E-02-S02 · Registrar aprobación formal con identidad y timestamp   ·   épica E-02   ·   3 pts
**Como** Fiscalizador del Cliente, **quiero** registrar mi aprobación formal con mi identidad autenticada y la fecha/hora exacta del acto de aprobación, **para** que el estado 'liberado' quede como cierre formal e inmutable que pueda presentarse ante auditorías internas del cliente.

Criterios de aceptación (Gherkin):
- Dado que el Fiscalizador revisó la evidencia de una solicitud en estado 'corregido', cuando confirma la aprobación, entonces el sistema registra automáticamente su identidad autenticada y la fecha/hora exacta del momento de la aprobación, y cambia el estado a 'liberado'.
- Dado que una solicitud está en estado 'liberado', cuando cualquier usuario intenta cambiar el estado manualmente a uno anterior, entonces el sistema bloquea la acción y muestra un mensaje indicando que el estado 'liberado' es inmutable.
- Dado que una solicitud está en estado 'liberado', cuando cualquier usuario consulta su historial, entonces ve la identidad del Fiscalizador que aprobó, la fecha/hora exacta de la aprobación y el estado final, como registro irrefutable del cierre.

**Origen:** user-stories#US-04, requisitos#R-06, requisitos#R-12
**Dependencias:** E-02-S01, E-01-S04

---

## E-03 · Visibilidad del estado para la planificación diaria

---

### E-03-S01 · Ver estado actual y responsable en turno por frente   ·   épica E-03   ·   3 pts
**Como** Residente de Obra, **quiero** consultar el estado actual de cada actividad o frente (solicitado, observado, corregido, liberado) y el rol que tiene la responsabilidad en turno en una sola vista, **para** planificar el día siguiente sin descubrir bloqueos en el último momento ni perder horas-hombre de cuadrilla.

Criterios de aceptación (Gherkin):
- Dado que el Residente accede a la vista de frentes, cuando la consulta, entonces ve para cada actividad registrada: su estado actual (solicitado, observado, corregido o liberado) y el rol responsable en turno (calidad, producción o cliente), en una sola pantalla sin necesidad de abrir cada actividad individualmente.
- Dado que el estado de una solicitud cambia por acción de cualquier rol, cuando el Residente actualiza la vista de frentes, entonces el nuevo estado y el responsable en turno se reflejan sin requerir un recargo manual de la aplicación.
- Dado que existen actividades en distintos estados, cuando el Residente consulta la vista de frentes, entonces puede distinguir visualmente qué actividades están bloqueadas (observado) de las que avanzan normalmente (solicitado, corregido, liberado).

**Origen:** user-stories#US-05, requisitos#R-07
**Dependencias:** E-01-S05

---

### E-03-S02 · Indicador visual de observaciones con fecha compromiso vencida   ·   épica E-03   ·   2 pts
**Como** Residente de Obra, **quiero** ver en la vista de frentes un indicador visual que diferencie las actividades con observaciones cuya fecha compromiso ya venció del resto, **para** priorizar las correcciones urgentes al inicio de la jornada sin revisar manualmente cada registro uno a uno.

Criterios de aceptación (Gherkin):
- Dado que una observación tiene una fecha compromiso registrada y esa fecha ya pasó sin que la solicitud avance de estado 'observado', cuando el Residente consulta la vista de frentes, entonces la actividad correspondiente aparece con un indicador visual diferenciado (etiqueta 'vencida' o color de alerta) del resto de actividades.
- Dado que el Residente consulta la vista de frentes con actividades vencidas y no vencidas, cuando las visualiza, entonces las actividades vencidas se distinguen sin necesidad de abrir cada registro individual.
- Dado que una observación vencida es corregida y la solicitud avanza al estado 'corregido', cuando el Residente actualiza la vista de frentes, entonces esa actividad deja de mostrarse con el indicador de vencida.

**Origen:** requisitos#R-14, user-stories#US-05
**Dependencias:** E-03-S01, E-01-S02

---

### E-03-S03 · Tablero de liberaciones por estado en tiempo real   ·   épica E-03   ·   3 pts
**Como** Jefe de Proyecto, **quiero** ver un tablero con el conteo de liberaciones agrupadas por estado (solicitadas, observadas, corregidas, liberadas) en tiempo real, incluyendo una categoría 'vencidas' con el responsable identificado y los días de retraso, **para** identificar qué frentes bloquean la facturación y poder escalar sin tener que armar el resumen manualmente.

Criterios de aceptación (Gherkin):
- Dado que el Jefe de Proyecto accede al tablero de control, cuando lo consulta, entonces ve el conteo de liberaciones agrupadas por estado: solicitadas, observadas, corregidas y liberadas, actualizados en tiempo real (los conteos reflejan cambios en menos de 60 segundos desde la última acción en el sistema).
- Dado que existen observaciones cuya fecha compromiso ya venció, cuando el Jefe de Proyecto consulta el tablero, entonces aparece una categoría 'vencidas' separada que muestra el responsable identificado y los días de retraso de cada una.
- Dado que no existen observaciones vencidas, cuando el Jefe de Proyecto consulta el tablero, entonces la categoría 'vencidas' muestra un conteo de cero o no aparece, sin ocultar los demás estados.

**Origen:** user-stories#US-06, requisitos#R-08
**Dependencias:** E-01-S05

---

## E-04 · Trazabilidad documental e integridad de registros

---

### E-04-S01 · Historial cronológico completo de una solicitud   ·   épica E-04   ·   3 pts
**Como** Coordinadora Documental, **quiero** consultar el historial completo de una solicitud con todos sus cambios de estado en orden cronológico, incluyendo quién realizó cada acción y cuándo, **para** armar el dossier sin tener que perseguir archivos o depender de que cada rol recuerde lo que hizo.

Criterios de aceptación (Gherkin):
- Dado que la Coordinadora Documental abre una solicitud desde el sistema, cuando consulta el historial, entonces ve la cadena completa de cambios de estado en orden cronológico: el estado de origen, el estado de destino, la identidad del usuario que realizó el cambio y el timestamp exacto de cada transición.
- Dado que una solicitud tiene observaciones con fotos adjuntas, cuando la Coordinadora consulta el historial, entonces las fotos vinculadas a cada observación aparecen accesibles desde el mismo historial, sin necesidad de buscarlas en otro lugar del sistema.
- Dado que la Coordinadora consulta el historial de una solicitud cuyo ciclo no ha terminado (por ejemplo en estado 'observado'), cuando lo visualiza, entonces el historial muestra las transiciones ocurridas hasta ese momento sin ocultar el estado parcial.

**Origen:** user-stories#US-07, requisitos#R-06
**Dependencias:** E-01-S05

---

### E-04-S02 · Inmutabilidad de registros aprobados con rastro de auditoría   ·   épica E-04   ·   3 pts
**Como** Coordinadora Documental, **quiero** que los registros aprobados por el Fiscalizador no puedan editarse sin que el sistema genere un rastro de auditoría inmutable con la identidad de quien intentó modificarlo y el timestamp, **para** garantizar la integridad documental del dossier frente a auditorías internas y externas del cliente.

Criterios de aceptación (Gherkin):
- Dado que una solicitud está en estado 'liberado' (aprobada por el Fiscalizador), cuando cualquier usuario intenta editar cualquier campo de esa solicitud, entonces el sistema bloquea la edición y registra en el rastro de auditoría: la identidad del usuario que intentó la modificación, el campo que intentó cambiar y el timestamp del intento.
- Dado que un administrador del sistema intenta editar un registro aprobado, cuando realiza el intento, entonces el sistema registra la acción en el rastro de auditoría inmutable y el intento de modificación queda visible en el historial de la solicitud.
- Dado que la Coordinadora consulta el rastro de auditoría de una solicitud aprobada, cuando lo revisa, entonces ve todos los intentos de modificación registrados con identidad del actor, campo afectado y timestamp, en orden cronológico.

**Origen:** user-stories#US-07, requisitos#R-18
**Dependencias:** E-02-S02

---

### E-04-S03 · Asociación del código de frente en todos los registros derivados   ·   épica E-04   ·   2 pts
**Como** Coordinadora Documental, **quiero** que el código de frente ingresado en la solicitud original quede asociado y visible en todas las observaciones, correcciones y aprobaciones derivadas de esa solicitud, **para** organizar el dossier por frente sin reconciliación manual de nomenclaturas entre Excel, plano y sistema.

Criterios de aceptación (Gherkin):
- Dado que una solicitud fue creada con un código de frente, cuando la Coordinadora consulta cualquier observación, corrección o aprobación derivada de esa solicitud, entonces el código de frente aparece asociado a cada registro sin necesidad de ingresarlo nuevamente.
- Dado que la Coordinadora filtra los registros del sistema por código de frente, cuando aplica el filtro, entonces el sistema muestra todas las solicitudes, observaciones y aprobaciones que comparten ese código de frente en una lista unificada.

**Origen:** user-stories#US-07, requisitos#R-01, personas#coordinador_documental
**Dependencias:** E-01-S01

---

### E-02-S03 · Descarga autónoma de evidencia por el Fiscalizador   ·   épica E-02   ·   2 pts
**Como** Fiscalizador del Cliente, **quiero** descargar toda la evidencia (fotos de observación y de corrección) de un punto de liberación directamente desde el sistema, **para** obtener los respaldos para mis auditorías internas sin depender del contratista para entregar capturas.

Criterios de aceptación (Gherkin):
- Dado que el Fiscalizador está revisando la evidencia de una solicitud, cuando selecciona la opción de descarga de evidencia, entonces el sistema genera un archivo descargable que contiene las fotos de observación y de corrección vinculadas a ese punto, sin requerir intervención del contratista.
- Dado que el Fiscalizador descargó la evidencia de un punto, cuando abre el archivo descargado, entonces las fotos están identificadas con el código de frente y el identificador de la solicitud correspondiente, no con nombres genéricos de archivo.

**Origen:** requisitos#R-19, personas#fiscalizador_cliente
**Dependencias:** E-02-S01
