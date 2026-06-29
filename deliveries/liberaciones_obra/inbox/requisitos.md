# Requisitos Candidatos — Liberaciones de Obra

Generado el 2026-06-21. Fuente: 5 entrevistas en `interviews/`.
Todos los requisitos están respaldados por evidencia directa; ninguno es inventado.

---

## Funcionales

- **[R-01]** El sistema debe permitir registrar una solicitud de liberación por actividad o frente, con campos mínimos: código de actividad/frente, responsable, fecha, estado, documentos adjuntos y observaciones abiertas.
  - Tipo: funcional
  - Origen: `coordinador_documental.md` · Coordinadora Documental / `jefe_proyecto.md` · Jefe de Proyecto

- **[R-02]** El sistema debe permitir registrar una observación asociada a una solicitud de liberación, incluyendo descripción, foto, responsable de corrección, fecha compromiso y estado.
  - Tipo: funcional
  - Origen: `inspector_calidad.md` · Inspector de Calidad / `residente_obra.md` · Residente de Obra

- **[R-03]** El flujo de estados de una liberación debe seguir la secuencia: solicitado → observado → corregido → liberado. Los estados deben ser visibles por todos los actores.
  - Tipo: funcional
  - Origen: `jefe_proyecto.md` · Jefe de Proyecto / `residente_obra.md` · Residente de Obra

- **[R-04]** Solo los usuarios con rol de calidad o de cliente (fiscalizador) deben poder cerrar una observación o cambiar el estado a "liberado".
  - Tipo: funcional
  - Origen: `inspector_calidad.md` · Inspector de Calidad / `fiscalizador_cliente.md` · Fiscalizador del Cliente

- **[R-05]** El sistema debe permitir adjuntar fotos directamente a una observación o punto de liberación desde el dispositivo móvil, en el momento de la inspección.
  - Tipo: funcional
  - Origen: `inspector_calidad.md` · Inspector de Calidad / `fiscalizador_cliente.md` · Fiscalizador del Cliente

- **[R-06]** Todo cambio de estado debe quedar registrado con la identidad del usuario que lo realizó y la marca de tiempo (trazabilidad de cambios).
  - Tipo: funcional
  - Origen: `coordinador_documental.md` · Coordinadora Documental / `fiscalizador_cliente.md` · Fiscalizador del Cliente

- **[R-07]** El sistema debe mostrar una vista por frente o actividad con el estado actual de cada punto (pendiente, observado, corregido, liberado) y el responsable en turno.
  - Tipo: funcional
  - Origen: `residente_obra.md` · Residente de Obra / `fiscalizador_cliente.md` · Fiscalizador del Cliente

- **[R-08]** El sistema debe presentar un tablero de control con las liberaciones agrupadas por estado: solicitadas, observadas, corregidas, liberadas y vencidas.
  - Tipo: funcional
  - Origen: `jefe_proyecto.md` · Jefe de Proyecto

- **[R-09]** El tablero debe incluir un ranking por frente y por responsable, y el indicador de días promedio para cerrar observaciones.
  - Tipo: funcional
  - Origen: `jefe_proyecto.md` · Jefe de Proyecto

- **[R-10]** El sistema debe permitir exportar un reporte por frente o hito en formato PDF o Excel, para entrega directa al cliente sin que este deba acceder al sistema.
  - Tipo: funcional
  - Origen: `coordinador_documental.md` · Coordinadora Documental

- **[R-11]** Las observaciones deben registrarse y gestionarse en un canal único dentro del sistema, reemplazando la dispersión por WhatsApp, correo y minutas.
  - Tipo: funcional
  - Origen: `fiscalizador_cliente.md` · Fiscalizador del Cliente / `residente_obra.md` · Residente de Obra

- **[R-12]** La aprobación del fiscalizador del cliente dentro del sistema debe quedar vinculada a la evidencia correspondiente (foto antes / foto después), con fecha y firma digital.
  - Tipo: funcional
  - Origen: `fiscalizador_cliente.md` · Fiscalizador del Cliente

- **[R-13]** El sistema debe permitir clasificar observaciones por criticidad y filtrar por ese criterio.
  - Tipo: funcional
  - Origen: `fiscalizador_cliente.md` · Fiscalizador del Cliente / `jefe_proyecto.md` · Jefe de Proyecto

- **[R-14]** El sistema debe generar un indicador diario de liberaciones vencidas y observaciones críticas abiertas, visible al inicio de la jornada.
  - Tipo: funcional
  - Origen: `residente_obra.md` · Residente de Obra

- **[R-15]** El sistema debe imponer una nomenclatura consistente para frentes y actividades, de modo que el mismo código aparezca en todos los registros (solicitudes, fotos, protocolos, reportes).
  - Tipo: funcional
  - Origen: `coordinador_documental.md` · Coordinadora Documental

---

## No funcionales

- **[R-16]** El sistema debe funcionar en modo offline en campo y sincronizar automáticamente al recuperar conectividad, sin pérdida de datos ingresados.
  - Tipo: no funcional
  - Origen: `inspector_calidad.md` · Inspector de Calidad

- **[R-17]** El flujo de registro de una observación desde el móvil no debe superar un minuto bajo condiciones normales de uso.
  - Tipo: no funcional (usabilidad)
  - Origen: `inspector_calidad.md` · Inspector de Calidad

- **[R-18]** Los registros aprobados no deben ser editables sin dejar un rastro de auditoría inmutable (quién cambió qué y cuándo).
  - Tipo: no funcional (integridad / auditoría)
  - Origen: `fiscalizador_cliente.md` · Fiscalizador del Cliente / `coordinador_documental.md` · Coordinadora Documental

- **[R-19]** El fiscalizador del cliente debe poder descargar toda la evidencia de un punto (fotos de observación y de cierre) directamente, sin depender del contratista para obtener capturas.
  - Tipo: no funcional (autonomía / acceso)
  - Origen: `fiscalizador_cliente.md` · Fiscalizador del Cliente
