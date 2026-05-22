1. Prompt Mejorado: PRD Ligero
Archivo: prompts_mejorados/prd_mejorado.md
Este prompt ha sido mejorado rellenando los huecos de stakeholders y capacidades, añadiendo una sección de verificación para asegurar la trazabilidad de los NFRs
.
Contenido del Prompt Mejorado
Role: Arquitecto de Software Senior experto en FTGO y DDD
. Task: Generar un PRD ligero para FTGO en Markdown
.
Context (Mejorado):
TODO 1 (Stakeholders rellenado): Consumidor (UX rápida), Restaurante (Gestión de tickets), Courier (Rutas óptimas), Empleado FTGO (Reportes), y Sistemas Externos (Stripe, Google Maps, SendGrid/Twilio)
.
TODO 2 (Capacidades rellenado): 1. Consumer Management, 2. Restaurant Management, 3. Order Taking, 4. Order Fulfillment/Kitchen, 5. Delivery, 6. Billing & Accounting, 7. Notifications
.
Sección Nueva: Verification (Calidad) Antes de entregar el output, verifica:
¿Cada NFR tiene una métrica numérica (ej. < 200ms)?
.
¿Cada NFR cita su origen como [Brief §A.4]?
.
¿Se incluyeron las 7 capacidades de negocio de Richardson?
.
## Changelog
v1.1: Rellenados TODO 1 y TODO 2 con datos reales del Brief §A.2 y §A.3 para evitar alucinaciones de dominio
.
v1.1: Agregada sección de Verification para forzar el cumplimiento de la rúbrica de trazabilidad
.
## Métrica de Calidad
Indicador: % de NFRs con métrica y cita correcta.
Evidencia (3 corridas):
Antes: 60% (algunos NFRs eran genéricos sin origen).
Después: 100% (el modelo valida cada NFR contra la sección de verificación).
