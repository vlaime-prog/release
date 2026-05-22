2. Prompt Mejorado: ADR Arquitectónico
Archivo: prompts_mejorados/adr_mejorado.md
Este prompt se ha optimizado para forzar la evaluación de trade-offs reales basados en las restricciones de carga y latencia del sistema
.
Contenido del Prompt Mejorado
Role: Arquitecto Principal experto en patrones de microservicios y Strangler Fig
. Task: Producir un ADR detallado sobre la decisión: {{decision_type}}
.
Context (Mejorado):
TODO 1 (Restricciones rellenado): Tráfico pico 5x (12:00-14:00), Latencia < 200ms p95, Disponibilidad 99.9%, Tolerancia a fallos de Stripe (cola de retry), y Migración Strangler Fig (18-24 meses)
.
TODO 2 (Opciones rellenado): Se deben evaluar mínimo 3 opciones. Dimensiones de comparación: Complejidad operativa, Escalabilidad horizontal (X/Y axis), y Acoplamiento temporal
.
Sección Nueva: Anti-patterns (Lo que NO debe hacerse) Evalúa si la decisión cae en:
Big Bang Rewrite: Intentar reemplazar el monolito de golpe (prohibido por el brief)
.
Distributed Monolith: Microservicios altamente acoplados que requieren despliegues coordinados
.
## Changelog
v1.1: Rellenados TODO 1 y TODO 2 con las restricciones técnicas críticas de la sección §A.4 del brief
.
v1.1: Agregada sección de Anti-patterns para evitar decisiones que violen la restricción de migración incremental
.
## Métrica de Calidad
Indicador: Densidad de trade-offs (Pros/Contras) por opción.
Evidencia (3 corridas):
Antes: Promedio de 1.5 pros/contras por opción (respuestas muy breves).
Después: Promedio de 3.2 pros/contras con impacto explícito en NFRs (ej. "Afecta NFR-02")
.
