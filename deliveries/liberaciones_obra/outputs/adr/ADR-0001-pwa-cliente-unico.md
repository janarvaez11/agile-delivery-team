# ADR-0001 · PWA como cliente único para acceso móvil y desktop

**Estado:** aceptado
**Fecha:** 2026-06-28

---

## Contexto y fuerza

El Inspector de Calidad opera en campo con el celular y necesita tomar fotos
en el momento de la inspección, vinculadas al punto exacto, sin pasos adicionales
(R-17: flujo de registro < 1 minuto; R-05: adjuntar fotos desde móvil al instante;
E-01-S02: historia crítica para la adopción). Es el usuario con mayor riesgo de
abandono: si el sistema le exige pasos extra, vuelve al papel (`personas#inspector_calidad`,
pain: `fotos-sin-referencia`, `observaciones-sin-registro-formal`).

Al mismo tiempo, el Fiscalizador del Cliente, el Jefe de Proyecto y la Coordinadora
Documental operan desde desktop y tablet en oficina. El MVP Canvas declara
explícitamente que el equipo del piloto es pequeño y el horizonte es de 4 semanas;
no hay presupuesto técnico ni de tiempo para mantener dos bases de código nativas
separadas (iOS + Android) junto con una interfaz de desktop.

La fuerza determinante es la combinación: acceso a cámara desde móvil + un solo
cliente para todos los dispositivos + piloto de velocidad máxima.

---

## Decisión

Se construye una **PWA (Progressive Web App)** como cliente único que atiende
a todos los roles y dispositivos.

La PWA accede a la cámara del dispositivo mediante las APIs del navegador
(`MediaDevices.getUserMedia` o `<input type="file" accept="image/*" capture>`),
evitando la necesidad de una app nativa. Funciona en Chrome/Android y Safari/iOS
sin instalación desde tiendas. Un Service Worker provee cacheo de assets estáticos
para carga rápida; no implementa sync offline en el piloto (OQ-02).

---

## Alternativas consideradas

- **App nativa iOS + Android por separado** — Acceso completo a la cámara y al
  sistema de archivos, sin restricciones del navegador. Descartada: requiere dos
  bases de código, tiempo de publicación en tiendas y conocimiento nativo;
  inviable para un piloto de 4 semanas con equipo pequeño.

- **App nativa multiplataforma (React Native / Flutter)** — Una sola base de código
  con acceso nativo a cámara. Descartada: requiere conocimiento específico del
  framework, proceso de build y distribución ad-hoc para el piloto; añade complejidad
  de toolchain innecesaria cuando las APIs de cámara del navegador son suficientes
  para el caso de uso.

- **Web responsiva sin Service Worker** — La opción más simple. Descartada a favor
  de PWA porque el Service Worker permite cacheo de assets estáticos y abre la
  puerta a offline en futuras iteraciones (OQ-02) sin reescribir el cliente; el
  costo incremental es mínimo.

---

## Consecuencias

**Ganamos:**
- Una sola base de código para los 5 roles en todos los dispositivos.
- Sin proceso de aprobación en tiendas; el piloto se instala vía URL.
- Acceso a cámara con las APIs estándar del navegador (suficiente para R-05 y R-17).
- Ruta de evolución hacia offline (R-16) mediante Background Sync sin cambiar la
  arquitectura de backend.

**Costo que aceptamos:**
- Las APIs de cámara del navegador en iOS Safari tienen restricciones históricas
  (e.g., no hay acceso continuo a la cámara trasera sin interacción del usuario).
  El flujo de captura debe diseñarse con `<input capture>` como fallback, no
  solo con `getUserMedia`. Esto debe validarse con el equipo antes de codificar
  la historia E-01-S02.
- Si OQ-02 se resuelve negativamente (sin conectividad en campo), la estrategia
  offline de una PWA es más limitada que la de una app nativa. El riesgo está
  documentado en `architecture.md`.
