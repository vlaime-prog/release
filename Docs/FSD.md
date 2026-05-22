A continuación, presento el contenido para el **Functional Specification Document (FSD) ligero**, diseñado para ser guardado en la ruta **`docs/FSD.md`**. Este documento extiende las historias de usuario semilla y cumple con la estructura formal de casos de uso (UC) y bloques Given/When/Then.

***

# Functional Specification Document (FSD) - FTGO Platform

## 1. Introducción
El propósito de este FSD es detallar el comportamiento funcional de la plataforma FTGO para guiar la migración del monolito a microservicios. Se enfoca en los flujos críticos de valor para los stakeholders, asegurando que cada funcionalidad esté alineada con las capacidades de negocio definidas en el PRD.

## 2. Tabla de Casos de Uso (UCs)
| ID | Título | Actor Primario | Capacidad PRD | Origen |
| :--- | :--- | :--- | :--- | :--- |
| **UC-01** | Realizar Pedido | Consumidor | Order Taking | US-01 |
| **UC-02** | Gestionar Ticket de Cocina | Restaurante | Order Fulfillment | US-02 |
| **UC-03** | Gestionar Disponibilidad y Asignación | Courier | Delivery | US-03 |
| **UC-04** | Procesar Pago de Pedido | Sistema | Billing & Accounting | Libro Cap. 3 |
| **UC-05** | Tracking de Pedido en Tiempo Real | Consumidor | Delivery | NFR-02/03 |

---

## 3. Detalle de Casos de Uso

### UC-01: Realizar Pedido
*   **Actor primario:** Consumidor.
*   **Capacidad PRD:** Order Taking.
*   **Origen:** US-01 (Brief §A.5).
*   **Precondiciones:** El consumidor ha seleccionado un restaurante y tiene ítems en el carrito.
*   **Flujo principal:**
    1. El consumidor confirma la dirección de entrega.
    2. El sistema valida la disponibilidad del restaurante.
    3. El sistema calcula el total incluyendo impuestos y envío.
    4. El sistema crea el pedido en estado `PENDING_CHECKOUT`.
*   **Postcondiciones:** Pedido registrado y listo para proceso de pago.
*   **Given/When/Then:**
    *   **Given:** El consumidor tiene un carrito con ítems y el restaurante está abierto.
    *   **When:** El consumidor presiona "Confirmar Pedido".
    *   **Then:** El sistema genera un número único de pedido y solicita el procesamiento del pago.

### UC-02: Gestionar Ticket de Cocina
*   **Actor primario:** Restaurante.
*   **Capacidad PRD:** Order Fulfillment / Kitchen.
*   **Origen:** US-02 (Brief §A.5).
*   **Precondiciones:** El pago del pedido ha sido autorizado.
*   **Flujo principal:**
    1. El restaurante recibe una notificación de nuevo ticket.
    2. El operador de cocina revisa los detalles del pedido.
    3. El restaurante acepta el pedido indicando un tiempo estimado de preparación.
*   **Flujos alternativos:** Si el restaurante está saturado, rechaza el ticket con un motivo; el sistema cancela el pedido y notifica al consumidor.
*   **Given/When/Then:**
    *   **Given:** Un nuevo ticket llega al dashboard del restaurante con estado `PAID`.
    *   **When:** El restaurante acepta el ticket con un tiempo de "30 minutos".
    *   **Then:** El estado del pedido cambia a `PREPARING` y se notifica al consumidor.

### UC-03: Gestionar Disponibilidad y Asignación
*   **Actor primario:** Courier.
*   **Capacidad PRD:** Delivery.
*   **Origen:** US-03 (Brief §A.5).
*   **Precondiciones:** El courier ha iniciado sesión en la app móvil.
*   **Flujo principal:**
    1. El courier se marca como "Disponible" en la app.
    2. El sistema identifica pedidos listos para recoger cerca de su ubicación.
    3. El sistema ofrece la asignación al courier.
    4. El courier acepta la asignación antes del timeout de 30 segundos.
*   **Given/When/Then:**
    *   **Given:** Un courier está disponible y a menos de 2km del restaurante.
    *   **When:** El sistema le ofrece un pedido y el courier acepta.
    *   **Then:** El sistema muestra la ruta optimizada hacia el restaurante y el destino final.

### UC-04: Procesar Pago de Pedido (Derivado)
*   **Actor primario:** Sistema (Automático).
*   **Capacidad PRD:** Billing & Accounting.
*   **Origen:** Derivado de restricciones de cumplimiento (PCI-DSS) y flujo de checkout.
*   **Precondiciones:** El pedido ha sido creado en estado `PENDING_CHECKOUT`.
*   **Flujo principal:**
    1. El sistema envía los datos de cobro a la pasarela Stripe.
    2. El sistema recibe la confirmación del cargo exitoso.
    3. El sistema actualiza el estado del pedido a `PAID`.
*   **Flujos alternativos:** Si Stripe está caído, el sistema encola la transacción para reintento y permite que el flujo continúe según la política de tolerancia a fallos.
*   **Given/When/Then:**
    *   **Given:** Un pedido pendiente de pago por $50.00.
    *   **When:** La pasarela Stripe confirma el cargo a la tarjeta del consumidor.
    *   **Then:** El sistema emite un recibo digital y dispara la notificación al servicio de cocina.

### UC-05: Tracking de Pedido en Tiempo Real (Derivado)
*   **Actor primario:** Consumidor.
*   **Capacidad PRD:** Delivery / Notifications.
*   **Origen:** Derivado de NFR-02 (Latencia UX) y NFR-03 (Disponibilidad).
*   **Precondiciones:** El courier ha recogido el pedido en el restaurante.
*   **Flujo principal:**
    1. El sistema recibe coordenadas GPS frecuentes del courier.
    2. El sistema publica la ubicación en el mapa del consumidor.
    3. El sistema estima el tiempo de llegada basado en el tráfico.
*   **Given/When/Then:**
    *   **Given:** El pedido está en estado `PICKED_UP`.
    *   **When:** El courier se desplaza hacia la dirección de entrega.
    *   **Then:** El consumidor ve el movimiento del courier en el mapa con una latencia perceptible < 200ms p95.
