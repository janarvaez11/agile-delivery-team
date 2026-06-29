# agile-delivery-team
An Agile Delivery team Power by AI

# Reflexión. 

# ¿Qué cambió en tu plan cuando separaste el trabajo en cuatro roles en vez de pensarlo "todo junto"?
```text
Al separar el trabajo en cuatro roles, el plan dejó de sentirse como una lista grande de tareas y empezó a verse como un flujo de entrega más ordenado. El Product Owner obligó a priorizar por valor y a decidir qué épicas atacaban primero el dolor central del discovery: liberar obra con evidencia completa, trazabilidad y menos dependencia de WhatsApp o correos. El Developer aterrizó ese valor en historias INVEST, cuidando que cada historia fuera pequeña, testeable y con criterios de aceptación verificables. El Architect convirtió esas necesidades en decisiones técnicas explícitas, como PWA, monolito modular, object storage para fotos y una tabla append-only para auditoría. El Scrum Master cerró el ciclo con un Sprint Goal y una capacidad realista de 20 puntos, evitando comprometer todo el backlog.

El mayor cambio fue que dejé de pensar “quiero construir todo el sistema” y empecé a pensar “qué incremento de valor puede entregar el equipo en un sprint sin romper la trazabilidad ni sobrecomprometerse”. Esa separación también hizo visibles las tensiones: por ejemplo, el Product Owner quería maximizar valor, el Architect cuidaba las restricciones técnicas, el Developer afinaba las historias y el Scrum Master protegía la capacidad del sprint.
```
# ¿Qué historia te costó más dejar lista según INVEST, y por qué?
```text
La historia que más me costó dejar lista según INVEST fue E-02-S02 · Registrar aprobación formal con identidad y timestamp. Al inicio parecía una historia simple: que el fiscalizador apruebe una liberación dentro del sistema. Pero al refinarla aparecieron varias ambigüedades: ¿la aprobación digital tiene valor documental suficiente?, ¿debe ser una firma legal o solo un registro autenticado para el piloto?, ¿qué significa que el estado “liberado” sea inmutable?, ¿qué debe pasar si alguien intenta modificarlo después?

Me costó especialmente por la parte de Valuable y Testable de INVEST. Era valiosa porque cerraba el ciclo del MVP y permitía medir si el fiscalizador podía aprobar en el primer intento, pero solo era testeable si la historia quedaba aterrizada en comportamientos concretos: registrar identidad autenticada, fecha/hora exacta, cambiar el estado a “liberado”, bloquear retrocesos manuales y mostrar el historial como evidencia. También tuve que cuidar que no creciera demasiado: una firma electrónica completa podía convertirla en una historia grande y dependiente de un proveedor externo, así que quedó acotada al registro formal suficiente para el piloto, dejando el riesgo documental como una pregunta abierta.

```
# ¿Para qué te serviría un gate de Definition of Ready en tu equipo real?
```text
Un gate de Definition of Ready me serviría para evitar que el equipo empiece a construir trabajo incompleto, ambiguo o sin evidencia. En proyectos reales, muchas veces se inicia una tarea porque “parece urgente”, pero después aparecen vacíos: falta responsable, no hay criterio de aceptación, no está claro quién aprueba, no existe evidencia del usuario o la historia depende de algo que nadie resolvió. El gate ayuda a detener ese problema antes de comprometer capacidad del equipo.

En mi trabajo, lo aplicaría como una forma de gobernanza ejecutable para proyectos internos y operativos. Por ejemplo, una historia no debería pasar al sprint si no tiene rol usuario, valor esperado, criterios de aceptación verificables, trazabilidad al discovery o requisito, tamaño razonable y dependencias claras. En un contexto como SEDEMI, esto podría ayudar mucho en iniciativas de digitalización, control documental, calidad, liberaciones de obra o automatizaciones, porque obligaría a que cada mejora entre al equipo con suficiente claridad para ejecutarse y probarse. No reemplaza el criterio del equipo, pero sí protege la calidad del backlog y evita que el sprint se llene de tareas mal definidas.
```