Para completar esta tarea, he generado el contenido del **PRD ligero** para FTGO, integrando el prompt semilla B.1 con la información específica del Brief (Anexo A) y las capacidades de negocio definidas por Richardson.
***

# Product Requirements Document (PRD) - FTGO Platform

## 1. Contexto y Objetivos
FTGO es una plataforma de marketplace de delivery de comida que actualmente opera bajo una arquitectura monolítica Java (WAR). Debido a problemas de escalabilidad, ciclos de entrega lentos y falta de aislamiento de fallos, la dirección ha decidido migrar hacia una **arquitectura de microservicios**.

El objetivo de este documento es definir los requisitos para la arquitectura objetivo que permita una **migración incremental mediante el patrón Strangler Fig**, asegurando que el sistema pueda escalar horizontalmente y mantener la disponibilidad durante el proceso de transición.

## 2. Stakeholders
Basado en el análisis del dominio, los actores clave y sus intereses son:
*   **Consumidor:** Usuario final que busca una UX rápida, transparencia en el estado del pedido y tracking en tiempo real.
*   **Restaurante:** Negocio asociado que requiere gestionar tickets de cocina y controlar su carga de trabajo.
*   **Courier:** Repartidor independiente interesado en rutas optimizadas, asignaciones cercanas y pagos confiables.
*   **Empleado FTGO (Back office):** Personal interno de soporte y finanzas que requiere visibilidad y reportes de la operación.
*   **Arquitecto de Software:** Responsable de la mantenibilidad y la trazabilidad de la nueva arquitectura.
*   **Sistemas Externos:** Stripe (pagos), Google Maps (mapas) y SendGrid/Twilio (notificaciones) que requieren integraciones estables.

## 3. Capacidades de Negocio
Se han identificado las 7 capacidades de negocio fundamentales del Capítulo 2 de Richardson, que servirán como base para la descomposición:
1.  **Consumer Management:** Gestión del ciclo de vida del consumidor, incluyendo perfiles, direcciones de entrega y preferencias.
2.  **Restaurant Management:** Gestión de la información de restaurantes, catálogos de menús, horarios de operación y disponibilidad.
3.  **Order Taking:** Proceso central de toma de pedidos, validación de reglas de negocio, cálculo de totales y confirmación inicial.
4.  **Order Fulfillment (Kitchen):** Gestión de los tickets de cocina enviados a los restaurantes y seguimiento del estado de preparación.
5.  **Delivery:** Gestión logística que incluye la asignación de couriers, optimización de rutas y tracking geográfico en tiempo real.
6.  **Billing & Accounting:** Gestión financiera de cobros a consumidores, cálculo de comisiones y pagos (payouts) a socios.
7.  **Notifications:** Gestión de comunicaciones omnicanal (Email, SMS, Push) para alertas de estado y recibos.

## 4. Requisitos No Funcionales (NFRs)
Para garantizar la calidad arquitectónica, se definen los siguientes requisitos con trazabilidad explícita:

*   **NFR-01: Escalabilidad en Carga Crítica**
    *   **Métrica:** Soportar tráfico pico de **5x** sobre el promedio en horarios 12:00-14:00 y 19:00-22:00.
    *   **Origen:** [Brief §A.4 Carga].
*   **NFR-02: Latencia de Experiencia de Usuario**
    *   **Métrica:** Tiempo de respuesta percibido **< 200 ms (p95)** para acciones del consumidor en la app.
    *   **Origen:** [Brief §A.4 Latencia UX].
*   **NFR-03: Disponibilidad Crítica**
    *   **Métrica:** **99.9% mensual** para el flujo de toma de pedidos; el tracking puede degradar a 99.5%.
    *   **Origen:** [Brief §A.4 Disponibilidad].
*   **NFR-04: Resiliencia ante Fallos de Terceros**
    *   **Métrica:** El sistema debe permitir la toma de pedidos incluso con la pasarela de pagos caída mediante **colas de retry**.
    *   **Origen:** [Brief §A.4 Tolerancia a fallos externos].
*   **NFR-05: Trazabilidad Distribuida**
    *   **Métrica:** Cada acción debe incluir un **Correlation ID** que permita el rastreo end-to-end entre servicios.
    *   **Origen:** [Brief §A.4 Trazabilidad].
*   **NFR-06: Cumplimiento de Datos**
    *   **Métrica:** Delegación total de datos de pago sensibles a Stripe (**PCI-DSS**) y cumplimiento de normativas locales de privacidad.
    *   **Origen:** [Brief §A.4 Cumplimiento].

## 5. Alcance
*   **In-Scope (Dentro):** Definición de la arquitectura objetivo basada en las 7 capacidades de negocio, diseño de la estrategia de descomposición y definición de mecanismos de integración para la migración incremental.
*   **Out-of-Scope (Fuera):** Reemplazo total inmediato ("Big Bang") del monolito, implementación de código fuente completo de todos los servicios, y gestión de infraestructura física de servidores.
*   **Estrategia de Migración:** Se utilizará exclusivamente el patrón **Strangler Fig**, moviendo funcionalidades del monolito Java actual a los nuevos microservicios de forma gradual durante un periodo de 18-24 meses.
