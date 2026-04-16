---
name: momentum-delivery
description: Genera documentos de entrega para el cliente sin jerga tecnica, o propuestas comerciales para clientes nuevos. Usa cuando el chatbot esta listo y necesitas crear el documento de entrega, cuando necesitas una propuesta comercial, o cuando el usuario dice "documento de entrega", "entrega al cliente", "propuesta comercial".
---

# Momentum Delivery — Documentos para el Cliente

## Evaluacion Inicial

- **Contexto:** Lee `clients/{cliente}/architecture.md` y `clients/{cliente}/discovery.json`
- **Modo:** Entrega (post-build) o Propuesta (pre-build)?
- **Objetivo:** Documento que el cliente entienda sin ser tecnico

## Principios Core

1. **NUNCA jerga tecnica** — no mencionar: n8n, API, LLM, GPT, prompt, webhook, node, token, classifier, workflow
2. **Lenguaje de beneficios** — no "usamos GPT-4o", si "asistente inteligente que entiende conversaciones"
3. **Expectativas claras** — que puede hacer Y que NO puede hacer
4. **Accionable** — el cliente sabe exactamente que pasa despues de leer

## Proceso

### Modo Entrega (Post-Build)

Usar template `assets/template-entrega.md`. Generar documento con:

1. **Que hace [Nombre del Bot]** — 2-3 lineas en lenguaje simple
2. **Como funciona** — sin mencionar tecnologia. "Cuando alguien te escribe por WhatsApp, [Nombre] responde automaticamente..."
3. **Que puede hacer** — lista de capacidades
4. **Que NO puede hacer** — limitaciones importantes para expectativas
5. **Cuando te notifica** — triggers de notificacion
6. **Resultados esperados** — metricas target del primer mes
7. **Soporte y mantenimiento** — que incluye post-entrega

### Modo Propuesta (Pre-Build)

Usar template `assets/template-propuesta.md`. Generar documento con:

1. **Diagnostico** — situacion actual y oportunidad
2. **Solucion propuesta** — que se va a construir, en lenguaje de negocio
3. **Fases de implementacion** — Semana 1: Setup, Semana 2: Desarrollo, Semana 3: Lanzamiento
4. **Inversion** — precio y estructura de pago
5. **Resultados esperados** — metricas del primer mes
6. **Siguientes pasos** — 1-2 decisiones que el cliente necesita tomar

### Guardar

Output: `clients/{cliente}/entrega.md` o `clients/{cliente}/propuesta.md`

## Edge Cases

- **Cliente tecnico:** Puede incluir seccion opcional "Detalles tecnicos" al final
- **Sin metricas definidas:** Usar benchmarks de industria de `memory/metodologia-core.md`

## Errores Comunes

- **Problema:** Mencionar "n8n" o "GPT" en el documento
  **Solucion:** Buscar y reemplazar toda jerga antes de entregar

- **Problema:** Prometer resultados sin caveats
  **Solucion:** Siempre incluir "resultados tipicos basados en proyectos similares"

## Skills Relacionados

- `/momentum-architect` — fuente de la arquitectura para el documento
- `/momentum-pipeline` — ultimo paso del pipeline
