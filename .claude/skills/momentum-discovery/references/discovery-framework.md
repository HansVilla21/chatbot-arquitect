# Discovery Framework Detallado

Fuente: `knowledge/03_TEMPLATES_Y_RECURSOS.md` seccion 2

## Fase 1: Entender el Negocio (5 min)

Objetivo: Dimensionar la complejidad del proyecto.

| Pregunta | Que captura | Por que importa |
|----------|-------------|-----------------|
| Que vende y a quien | Producto + cliente ideal | Define el tono y nivel del bot |
| Ticket promedio | Complejidad de venta | Determina modelo LLM (mini vs 4o) |
| Ciclo de venta | Dias/semanas/meses | Define si necesita follow-up/nurturing |
| Diferenciador | Propuesta de valor | Lo que el bot debe destacar |
| Volumen de consultas | Dimension | Sizing del stack |

## Fase 2: Mapear Proceso de Ventas (5 min)

Objetivo: Entender como venden hoy para replicarlo/mejorarlo.

| Pregunta | Que captura | Por que importa |
|----------|-------------|-----------------|
| De donde vienen leads | Canales de adquisicion | Define triggers y canales del bot |
| Que preguntan primero | FAQs reales | Contenido del agente principal |
| Objeciones comunes | Resistencia | Contenido del agente LAARC |
| Que los convence | Trigger de compra | Estrategia de cierre del bot |
| Como cierran | Metodo actual | Define el CTA final del bot |

## Fase 3: Decisiones Tecnicas (5 min)

Objetivo: Definir stack y operacion.

Basado en las respuestas de Fase 1 y 2, determinar:

| Si... | Entonces... |
|-------|-------------|
| Ticket <$500, flujo simple | GPT-4o-mini, 3 agentes |
| Ticket >$500, conversacion compleja | GPT-4o, 4 agentes |
| Solo WhatsApp | Evolution API (self-hosted) o YCloud (oficial) |
| Solo Instagram | ManyChat → n8n |
| Multi-canal | ManyChat (IG) + Evolution/YCloud (WA) |
| Sin CRM | Google Sheets (simple) o Airtable (medio) |
| Necesita proactivo/broadcast | YCloud (templates Meta) |
| Volumen alto (100+/dia) | Supabase/PostgreSQL + Redis |
| Volumen bajo (<20/dia) | Google Sheets suficiente |
