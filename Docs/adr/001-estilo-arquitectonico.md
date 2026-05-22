ADR 1: Selección del Estilo Arquitectónico para la Plataforma FTGO
Archivo: docs/adr/0001-estilo-arquitectonico.md Estado: Accepted (Aceptado)
1. Contexto
La aplicación actual de FTGO es un monolito Java (WAR) que presenta síntomas de "infierno monolítico": ciclos de entrega lentos, falta de aislamiento de fallos y dificultades para escalar módulos específicos
. La dirección requiere una arquitectura que soporte un crecimiento de 5x en carga y permita una migración sin detener la operación
.
2. Opciones Consideradas
Opción 1: Mantener y Optimizar el Monolito Existente
Descripción: Continuar con la arquitectura WAR actual, optimizando el código y escalando mediante la replicación de toda la instancia (X-axis scaling).
Pros: Menor complejidad operativa inicial; no requiere cambios en el modelo de datos.
Contras: No resuelve el bloqueo del equipo de desarrollo
; el escalado es ineficiente (se escala todo el sistema por un solo módulo pesado)
.
Impacto en NFRs: Falla en NFR-05 (escalabilidad independiente) y dificulta el cumplimiento de NFR-01 (tráfico pico 5x) de forma eficiente.
Opción 2: Rediseño Total Inmediato (Big Bang Rewrite)
Descripción: Desarrollar una nueva plataforma de microservicios desde cero y reemplazar el monolito una vez terminada.
Pros: Permite usar tecnologías modernas sin deuda técnica acumulada.
Contras: Riesgo extremadamente alto de fracaso; no entrega valor de negocio durante el desarrollo (meses/años)
.
Impacto en NFRs: Viola la restricción de Migración Incremental
.
Opción 3: Migración Incremental mediante Microservicios (Strangler Fig)
Descripción: Extraer gradualmente las 7 capacidades de negocio identificadas (Consumer, Order, Kitchen, etc.) hacia microservicios independientes mientras el monolito sigue operando
.
Pros: Entrega de valor continua; permite escalabilidad independiente por servicio (Y-axis scaling)
; aislamiento de fallos entre dominios.
Contras: Mayor complejidad de red y necesidad de observabilidad distribuida; requiere gestionar la consistencia eventual
.
Impacto en NFRs: Cumple con NFR-01 (escalado 5x) y NFR-05 (escalabilidad horizontal independiente).
3. Decisión
Se elige la Opción 3: Migración Incremental (Strangler Fig). Esta decisión se fundamenta en la necesidad de modernizar el sistema sin interrumpir el negocio, alineándose con el Capítulo 1 de Richardson y las restricciones de migración del brief
.
4. Consecuencias
Positivas: Los equipos pueden desplegar servicios de forma independiente; se mejora la tolerancia a fallos mediante el aislamiento de procesos.
Negativas: Incremento en la complejidad de infraestructura (necesidad de API Gateway y Service Mesh); esfuerzo adicional para mantener la interoperabilidad entre el monolito y los nuevos servicios.
