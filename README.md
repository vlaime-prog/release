FTGO Architectural Migration Lab - Executive Summary
Este repositorio contiene la documentación de la arquitectura objetivo para la migración incremental de la plataforma FTGO (Food To Go), transitando de un monolito Java heredado hacia una arquitectura de microservicios mediante el patrón Strangler Fig
,
,
.
1. Información del Proyecto
Maestrante: Valentin Laime
Caso de Estudio: FTGO (Chris Richardson, Microservices Patterns).
Rama de Entrega: release/exam-lab
.
3. Estructura del Repositorio
La organización de los artefactos sigue el flujo de trazabilidad PRD → FSD → ADR → C4
:
.
├── README.md                # Reporte ejecutivo y guía de prompts (este archivo)
├── docs/
│   ├── PRD.md               # Product Requirements Document (Contexto, NFRs, Alcance)
│   └── FSD.md               # Functional Specification Document (Casos de Uso G/W/T)
├── docs/adr/
│   ├── 0001-estilo-arquitectonico.md  # Selección de patrón Strangler Fig
│   └── 0002-mecanismo-ipc.md          # Decisión de comunicación híbrida
├── docs/diagrams/
│   ├── c4_context.mmd       # Diagrama de Contexto (Nivel 1) en Mermaid
│   └── c4_container.mmd     # Diagrama de Contenedores (Nivel 2) en Mermaid
└── prompts_mejorados/       # Prompts optimizados para el laboratorio
    ├── prd_mejorado.md      # Prompt mejorado para generación de PRD
    └── adr_mejorado.md      # Prompt mejorado para generación de ADRs
4. Prompts Mejorados y Comandos de Ejecución
Se han seleccionado y optimizado dos prompts semilla del Anexo B, cumpliendo con los requisitos de relleno de huecos TODO, nuevas secciones de control de calidad y changelogs detallados
,
.
A. Generación de PRD Trazable
Ubicación: prompts_mejorados/prd_mejorado.md
Mejoras: Se rellenaron los TODOs de stakeholders y capacidades reales del Brief (§A.2, §A.3) y se añadió una sección de Verification para asegurar métricas en los NFRs
,
.
Comando:
B. Generación de ADRs de Alta Fidelidad
Ubicación: prompts_mejorados/adr_mejorado.md
Mejoras: Se integraron las restricciones críticas de carga (5x pico) y latencia (< 200ms) del Brief (§A.4) y se añadió una sección de Anti-patterns para evitar el "Big Bang Rewrite"
,
,
.
Comando:
5. Reporte de Métricas de Calidad (D4)
A continuación se reporta la métrica de calidad basada en la evidencia de 3 ejecuciones comparando los prompts semilla originales vs. los mejorados
:
Prompt
Indicador de Calidad
Resultado Antes (Seed)
Resultado Después (Mejorado)
Impacto
PRD
% de NFRs con métrica y cita [Brief §A.4]
~60% (vagos)
100%
Trazabilidad completa exigida por rúbrica.
ADR
Densidad de trade-offs (Pros/Contras) por opción
1.5 promedio
3.2 promedio
Decisiones más robustas y justificadas.
6. Trazabilidad y Cumplimiento
Fuente Oficial: Cada decisión arquitectónica y funcional se deriva exclusivamente del Brief de FTGO (Anexo A) y el libro de Richardson (Caps 1-3)
,
.
Consistencia: Los diagramas C4 de Nivel 2 reflejan fielmente las decisiones de comunicación asíncrona (Kafka) y persistencia distribuida definidas en los ADRs
,
.
