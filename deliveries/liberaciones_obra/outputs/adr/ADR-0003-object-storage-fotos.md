# ADR-0003 · Object storage S3-compatible para almacenamiento de fotos

**Estado:** aceptado
**Fecha:** 2026-06-28

---

## Contexto y fuerza

Las fotos son el activo que cierra el ciclo de valor del sistema: el Fiscalizador
del Cliente solo puede aprobar en el primer intento si ve la foto de la observación
original y la foto de la corrección juntas (E-02-S01; `mvp-canvas#resultado-esperado`).
Sin fotos fiables, la métrica de éxito del piloto (tasa de aprobación ≥ 70 % en
semana 4) es inalcanzable.

Los requisitos que determinan la decisión de almacenamiento son:
- R-05: adjuntar fotos desde el dispositivo móvil en el momento de la inspección.
- R-19: el fiscalizador puede descargar toda la evidencia directamente, sin depender
  del contratista.
- E-04-S02: los registros aprobados con su evidencia son inmutables.

El Inspector de Calidad captura fotos directamente en campo desde el celular; cada
foto puede pesar entre 2 y 8 MB dependiendo del dispositivo. Si el sistema acumula
decenas de solicitudes de liberación por proyecto (cada una con foto de observación
+ foto de corrección), el volumen crece rápidamente.

La pregunta es: ¿dónde se persiste el binario de la foto y cómo se vincula a
la observación correspondiente?

---

## Decisión

Las fotos se almacenan en un **servicio de object storage S3-compatible**
(AWS S3, Cloudflare R2, MinIO u equivalente). La base de datos relacional
solo almacena la referencia: clave del objeto (S3 key), observación a la que
pertenece, timestamp de upload y usuario que la adjuntó.

El flujo de upload es:
1. El cliente PWA solicita al Módulo Evidencia una URL pre-firmada para escribir
   (tiempo de expiración: 15 minutos).
2. El cliente sube la foto directamente al object storage usando esa URL. El
   binario no pasa por el servidor backend.
3. El cliente confirma al Módulo Evidencia que el upload terminó; el módulo
   persiste la referencia en la BD y la vincula a la observación.

El acceso de lectura (visualización y descarga por el fiscalizador, R-19) se
sirve también mediante URLs pre-firmadas de lectura con expiración configurable.

---

## Alternativas consideradas

- **Almacenar fotos en la base de datos relacional (columna BYTEA/BLOB)** —
  La opción más simple de implementar: un solo sistema de persistencia. Descartada:
  el rendimiento de PostgreSQL se degrada con datos binarios grandes; los backups
  y las migraciones se vuelven lentos; el costo de almacenamiento es más alto que
  un object storage; y la consulta de metadata (¿qué observación tiene qué foto?)
  se mezcla con la transferencia de bytes pesados, complicando la carga de la vista
  de evidencia (E-02-S01).

- **Sistema de archivos del servidor (filesystem local)** — Sin dependencia de
  servicio externo. Descartada: si el servidor se remplaza o escala horizontalmente,
  las fotos quedan en una sola instancia; la durabilidad depende de la política de
  backup del servidor, no de un sistema diseñado para eso. Incompatible con
  cualquier estrategia de hosting gestionado en cloud.

- **Servicio de CDN/hosting de imágenes de terceros (Cloudinary, Imgix)** —
  Funcionalidades avanzadas (redimensionamiento, compresión automática). Descartada
  para el piloto: introduce un proveedor especializado con costo adicional y
  complejidad de integración; las fotos de campo no requieren transformaciones en
  este alcance. Puede considerarse en iteraciones posteriores si el rendimiento
  de descarga en mobile se convierte en problema.

---

## Consecuencias

**Ganamos:**
- Durabilidad y disponibilidad garantizadas por el proveedor de storage
  (AWS S3 ofrece 99.999999999 % de durabilidad por diseño).
- El binario nunca pasa por el servidor backend: el ancho de banda del API no se
  consume en transferencias de archivos; el servidor solo gestiona metadata.
- El acceso de descarga directa para el Fiscalizador (R-19) se resuelve con una
  URL pre-firmada: no requiere endpoint dedicado en el backend.
- La inmutabilidad de evidencia aprobada (R-18, E-04-S02) puede reforzarse a nivel
  de bucket (Object Lock / políticas de retención) si OQ-01 se resuelve requiriendo
  mayor rigor documental.

**Costo que aceptamos:**
- El sistema tiene dependencia de un servicio externo de almacenamiento. Si el
  proveedor tiene una interrupción, las fotos no son accesibles aunque el backend
  funcione. Mitigación: la metadata en BD siempre refleja el estado correcto; las
  fotos son accesibles en cuanto el servicio se restablece.
- El flujo de upload en dos pasos (solicitar URL → subir directo → confirmar al
  backend) es ligeramente más complejo de implementar en la PWA que un formulario
  multipart simple. El Módulo Evidencia debe manejar el estado de uploads pendientes
  de confirmación.
