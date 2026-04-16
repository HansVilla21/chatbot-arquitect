# Chatbot Arquitect — Contexto del Proyecto

## Que es
Sistema que automatiza la creacion de chatbots de ventas usando la metodologia Momentum AI. El pipeline completo va desde discovery con el cliente hasta documento de entrega.

## Quien lo usa
Hans (Momentum AI) — construye chatbots para negocios en Costa Rica y LATAM. 18+ proyectos reales documentados.

## Stack tecnico de los chatbots que se crean
- **Automatizacion:** n8n (self-hosted)
- **WhatsApp:** Evolution API (self-hosted) o YCloud (oficial)
- **Instagram:** ManyChat
- **CRM:** Airtable, Google Sheets, Notion
- **Backend:** Supabase/PostgreSQL
- **Cache:** Redis
- **LLMs:** GPT-4o (principal), GPT-4o-mini (classifiers/simple)
- **Scheduling:** Calendly
- **Notificaciones:** Discord
- **Chat Management:** Chatwoot

## Pipeline del sistema
1. `/momentum-discovery` — Discovery de 15 min con cliente
2. `/momentum-architect` — Diseño de arquitectura modular
3. `/momentum-prompt-gen` — Generacion de prompts optimizados
4. `/momentum-n8n-builder` — Estructura del workflow n8n
5. `/momentum-delivery` — Documento de entrega

## Estado actual
- Base de conocimiento: 7 documentos completos en knowledge/
- Skills: En construccion
- Workflows de referencia: Pendiente (Hans proveera JSONs)

## Objetivo
Reducir el tiempo de "cliente nuevo a bot deployado" de 1 semana a 1-2 dias.
