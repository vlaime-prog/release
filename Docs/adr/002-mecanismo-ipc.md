ADR 2: Mecanismo de Comunicación entre Servicios (IPC)
Archivo: docs/adr/0002-mecanismo-ipc.md Estado: Accepted (Aceptado)
1. Contexto
Al descomponer el monolito en servicios, es necesario definir cómo interactuarán las capacidades (ej. Order Taking con Billing). Se requiere un balance entre la latencia de usuario (< 200ms) y la resiliencia ante fallos externos (como caídas de Stripe)
.
2. Opciones Consideradas
Opción 1: Comunicación Puramente Síncrona (REST/HTTP)
Descripción: Todos los servicios se comunican mediante llamadas REST síncronas.
Pros: Modelo de programación sencillo; fácil de depurar.
Contras: Acoplamiento temporal severo; si un servicio falla, la cadena de llamadas se rompe (falla en cascada)
.
Impacto en NFRs: No cumple con NFR-04 (Tolerancia a fallos externos) si la pasarela de pagos está caída.
Opción 2: Comunicación Puramente Asíncrona (Messaging/Kafka)
Descripción: Todas las interacciones se realizan mediante el intercambio de mensajes en un broker (ej. RabbitMQ o Kafka).
Pros: Máximo desacoplamiento; alta resiliencia y soporte nativo para reintentos
.
Contras: Complejidad alta para flujos de consulta síncronos (Request/Response sobre mensajería); posible impacto en la latencia percibida.
Impacto en NFRs: Excelente para NFR-04, pero puede complicar el cumplimiento de NFR-02 (Latencia UX < 200ms) en consultas directas.
Opción 3: Estrategia Híbrida (REST para Consultas / Mensajería para Transacciones)
Descripción: Usar REST/gRPC para operaciones de lectura o validaciones inmediatas y Mensajería Asíncrona para confirmar pedidos y procesos de pago [Richardson Cap 3].
Pros: Optimiza la latencia para el usuario (NFR-02) mientras garantiza que los procesos críticos (Pagos/Cocina) se completen mediante colas de retry
.
Contras: Requiere mantener dos pilas de protocolos y gestionar la complejidad de la consistencia eventual.
Impacto en NFRs: Cumple simultáneamente con NFR-02 (Latencia UX) y NFR-04 (Tolerancia a fallos mediante colas de retry).
3. Decisión
Se elige la Opción 3: Estrategia Híbrida. Se utilizarán APIs síncronas para la navegación y toma de pedidos inicial, pero la confirmación final y el procesamiento de pagos se gestionarán de forma asíncrona para cumplir con la tolerancia a fallos exigida en el brief
.
4. Consecuencias
Positivas: El sistema puede seguir aceptando pedidos aunque sistemas externos estén lentos o caídos; se mantiene la agilidad de la interfaz de usuario.
Negativas: Los desarrolladores deben implementar lógica para manejar estados intermedios (ej. "Pedido en procesamiento") y compensar transacciones fallidas (Sagas).
