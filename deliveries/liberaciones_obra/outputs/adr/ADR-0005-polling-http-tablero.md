# ADR-0005 · Polling HTTP de 30 segundos para actualización del tablero en tiempo real

**Estado:** aceptado
**Fecha:** 2026-06-28

---

## Contexto y fuerza

E-03-S03 exige que el tablero del Jefe de Proyecto refleje cambios de estado en
menos de 60 segundos desde la última acción registrada en el sistema (R-08;
`user-stories#US-06`). El criterio de aceptación explícito es: "los conteos reflejan
cambios en menos de 60 segundos desde la última acción".

La pregunta es con qué mecanismo de actualización se implementa este comportamiento
en el piloto, considerando que el equipo es pequeño y el tablero no exige
interactividad bidireccional ni latencia inferior al segundo.

Las opciones principales son:
- **Polling HTTP periódico**: el cliente llama al endpoint del Módulo Tablero cada N
  segundos.
- **Server-Sent Events (SSE)**: el servidor mantiene una conexión HTTP de larga duración
  y empuja eventos al cliente.
- **WebSockets**: conexión bidireccional persistente entre cliente y servidor.

---

## Decisión

Se implementa **polling HTTP con intervalo de 30 segundos** como mecanismo de
actualización del tablero para el piloto.

El cliente PWA ejecuta un `setInterval` de 30 s que llama al endpoint existente
del Módulo Tablero (`GET /api/tablero`). El servidor responde con los conteos actuales.
No se requiere infraestructura adicional ni gestión de conexiones persistentes.

Un intervalo de 30 s cumple el requisito de < 60 s con margen de seguridad
(peor caso: cambio ocurre 1 segundo después del último poll → el tablero lo refleja
en 31 s, dentro del límite). Si el criterio se endurece a menos de 30 s en una
iteración posterior, SSE es la ruta de upgrade natural (ADR actualizable).

---

## Alternativas consideradas

- **Server-Sent Events (SSE)** — El servidor empuja eventos al cliente en cuanto
  cambia el estado; menor latencia que polling. Considerada seriamente: SSE es
  simple de implementar (HTTP estándar, sin protocolo de handshake propio), y la
  mayoría de frameworks modernos lo soportan de forma nativa. Descartada para el
  piloto porque requiere gestionar conexiones persistentes abiertas en el servidor
  (una por cliente activo), lo que añade complejidad de manejo de reconexión y de
  liberación de recursos. El piloto tiene pocos usuarios concurrentes (el Jefe de
  Proyecto y posiblemente el Residente); el beneficio de latencia no justifica la
  complejidad operacional adicional en este momento. SSE está documentado como
  upgrade preferido si el polling resulta insuficiente.

- **WebSockets** — Comunicación bidireccional en tiempo real, latencia mínima.
  Descartada: el tablero es de lectura unidireccional; la bidireccionalidad de
  WebSockets no aporta valor aquí. Además requiere infraestructura de servidor que
  soporte conexiones WebSocket persistentes (no todos los hostings gestionados
  económicos lo hacen con simplicidad). El overhead de protocolo, autenticación de
  socket y reconexión es desproporcionado para el caso de uso del piloto.

- **Long polling** — El servidor retiene la solicitud HTTP hasta que hay un cambio
  y entonces responde. Descartada: más complejo de implementar que el polling simple
  (requiere manejo de timeouts, colas internas y respuestas diferidas en el servidor)
  sin ventaja material para el número de usuarios del piloto.

---

## Consecuencias

**Ganamos:**
- Implementación trivial: `setInterval(fetchTablero, 30000)` en el cliente;
  el Módulo Tablero expone un endpoint GET ya necesario para la carga inicial
  de la página. Sin infraestructura adicional.
- Compatible con cualquier proveedor de hosting; no requiere soporte especial de
  WebSockets ni SSE en el servidor.
- El endpoint de tablero es reutilizable para otros consumidores futuros
  (por ejemplo, un comando de reporte) sin cambios.

**Costo que aceptamos:**
- Cada cliente activo genera una solicitud HTTP cada 30 s aunque no haya cambios
  ("chatty" en idle). Para el piloto con pocos usuarios concurrentes el impacto en
  carga del servidor es despreciable.
- La latencia mínima real es 0 segundos (si el cambio ocurre justo antes del poll)
  y la máxima es 30 segundos. Si algún rol necesita notificaciones inmediatas
  (por ejemplo, alertas push cuando una solicitud queda bloqueada), el polling no
  es el mecanismo adecuado. Ese caso de uso no está en el alcance del piloto.
- Si el volumen de usuarios crece post-piloto y el polling genera carga
  significativa en el servidor, SSE es la evolución directa: el endpoint de
  agregación del Módulo Tablero se convierte en un stream de eventos sin cambiar
  la lógica de dominio ni el cliente de forma sustancial.
