# ADR-0002 · Monolito modular como arquitectura de backend

**Estado:** aceptado
**Fecha:** 2026-06-28

---

## Contexto y fuerza

El piloto tiene alcance acotado: 4 épicas, 12 historias, 5 roles con dominios
claramente delimitados (solicitudes, observaciones, máquina de estados, evidencia,
tablero). El MVP Canvas establece que el equipo es pequeño y el objetivo es
demostrar valor en 4 semanas (`mvp-canvas#métrica-de-éxito`).

La pregunta es si este dominio justifica la inversión operacional de una
arquitectura de microservicios: despliegue independiente por servicio, service
discovery, comunicación entre procesos (REST o mensajería), distributed tracing
y gestión de contratos entre servicios.

La fuerza dominante no es la pureza arquitectónica; es la velocidad de entrega
con trazabilidad de dominio mantenida.

---

## Decisión

Se adopta un **monolito modular**: un único proceso desplegable con módulos
internos desacoplados por responsabilidad de dominio.

Los módulos internos son: Solicitudes, Observaciones, Máquina de Estados,
Evidencia y Tablero. Cada módulo tiene su propio conjunto de rutas, lógica de
negocio y acceso a la BD; no acceden directamente a las tablas de otro módulo
(la interdependencia se resuelve mediante llamadas a funciones/servicios internos,
no mediante queries cruzadas directas).

El Gateway HTTP con middleware de autenticación (JWT + RBAC) es la capa de
entrada única; no es un módulo de dominio sino infraestructura transversal.

Los límites de módulo están diseñados para que, si el sistema crece y requiere
escalar un componente de forma independiente (por ejemplo, el módulo de Evidencia
o el Tablero), la extracción a servicio separado sea posible sin reescritura de
la lógica de negocio.

---

## Alternativas consideradas

- **Microservicios desde el inicio** — Máximo aislamiento y escalado independiente
  por dominio. Descartada: el overhead operacional (orquestación, service mesh,
  contratos entre servicios, observabilidad distribuida) es desproporcionado para
  un piloto de equipo pequeño y 4 semanas. El riesgo de retrasar la entrega
  supera cualquier beneficio de escalado que el piloto no va a necesitar.

- **Monolito sin estructura de módulos (big ball of mud)** — La opción más rápida
  de arrancar. Descartada: el dominio tiene transacciones que cruzan conceptos
  (una observación dispara una transición de estado que actualiza la evidencia y
  el tablero); sin límites de módulo explícitos, esta lógica se mezcla y la
  trazabilidad de dominio se pierde, haciendo difícil mantener R-06 y R-18 de
  forma robusta.

- **Backend-for-Frontend (BFF) separado por rol** — Un BFF para móvil y otro para
  desktop. Descartada: el dominio de datos es el mismo para todos los roles; la
  diferenciación es de presentación, no de lógica. La PWA única hace irrelevante
  esta separación en el piloto.

---

## Consecuencias

**Ganamos:**
- Un único proceso para desplegar, depurar y monitorear; apropiado para el tamaño
  del piloto.
- Transacciones de base de datos sin coordinación distribuida: la máquina de estados
  puede escribir en `state_transitions` en la misma transacción que actualiza el
  estado de la solicitud, garantizando consistencia para R-06.
- Límites de módulo que facilitan la extracción futura a microservicios si el sistema
  escala después del piloto.

**Costo que aceptamos:**
- El despliegue es todo-o-nada: un fallo en el módulo de Tablero afecta al mismo
  proceso que sirve la máquina de estados. Mitigación: health checks y restart
  automático en el entorno de hosting; es riesgo aceptable para un piloto.
- Si dos módulos crecen a ritmos muy distintos en la vida post-piloto, la extracción
  requiere trabajo de separación. El riesgo se controla manteniendo los límites de
  módulo disciplinados desde el inicio.
