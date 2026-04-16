# Chatbot Arquitect

## Que es este proyecto

Sistema automatizado de creacion de chatbots de ventas usando la metodologia Momentum AI. Dado un negocio nuevo, el sistema guia todo el proceso: discovery con el cliente, diseño de arquitectura modular, generacion de prompts optimizados, configuracion del workflow n8n, y documento de entrega sin jerga tecnica.

## Para quien

Hans (Momentum AI) — construye chatbots de ventas para negocios en Costa Rica y LATAM. Stack: n8n (self-hosted), Evolution API/YCloud (WhatsApp), ManyChat (Instagram), GPT-4.1-mini, Supabase/PostgreSQL.

## Como funciona: El template base

Hans tiene un template de workflow n8n que se **duplica por cada cliente nuevo**. La estructura del template nunca cambia. Lo que cambia por cliente son:

1. **Prompts** — del router, agente(s), objeciones, filtro
2. **Numero de agentes** — 1 a 3, segun complejidad
3. **Tools** — RAG Supabase, Google Sheets, o ninguno
4. **Post-processing** — deteccion de links, descalificacion, vendedores
5. **Credenciales** — Airtable, ManyChat, OpenAI, etc.

**El objetivo de este proyecto** es generar las piezas que cambian (prompts, arquitectura, config) de la forma mas rapida y precisa posible.

---

## Proceso: Cliente Nuevo

### Paso 1: Crear carpeta del cliente
```
clients/{nombre-cliente}/
  docs/           ← Cargar aqui todo lo que el cliente me pasa
  prompts/        ← Prompts individuales (para revision facil)
  workflow/       ← Config del workflow n8n
```
Copiar la estructura de `clients/.template/` o dejar que el pipeline la cree.

### Paso 2: Cargar documentos del cliente
Meter en `clients/{cliente}/docs/` todo lo que el cliente me pasa:
- Brochures, catalogos, PDFs
- Lista de precios
- FAQs
- Politicas
- Cualquier informacion para entrenar el bot

### Paso 3: Discovery
`/momentum-discovery`
- Guia interactiva de 15 min
- Output: `clients/{cliente}/discovery.json`

### Paso 4: Arquitectura
`/momentum-architect`
- Lee el discovery + docs del cliente
- Define: agentes, router, tools, post-processing
- Output: `clients/{cliente}/architecture.md`

### Paso 5: Prompts
`/momentum-prompt-gen`
- Genera prompts basados en la arquitectura
- Output: archivos individuales en `clients/{cliente}/prompts/`
- Cada prompt listo para copiar-pegar en n8n

### Paso 6: Workflow n8n
`/momentum-n8n-builder`
- Guia nodo-por-nodo de que cambiar en el template
- Output: `clients/{cliente}/workflow/workflow-config.md`

### Paso 7: Entrega
`/momentum-delivery`
- Documento de entrega sin jerga tecnica
- Output: `clients/{cliente}/entrega.md`

**Pipeline completo en un solo paso:** `/momentum-pipeline`

---

## Skills y Agents

| Skill | Que hace |
|-------|----------|
| `/momentum-discovery` | Guia discovery de 15 min con cliente nuevo |
| `/momentum-architect` | Diseña arquitectura (que cambia en el template) |
| `/momentum-prompt-gen` | Genera prompts para cada componente del chatbot |
| `/momentum-n8n-builder` | Guia de configuracion nodo-por-nodo del template |
| `/momentum-delivery` | Documento de entrega sin jerga tecnica |
| `/momentum-prompt-optimizer` | Optimiza prompts existentes con cambios quirurgicos |
| `/momentum-pipeline` | Ejecuta todo el pipeline con checkpoints |

| Agent | Que hace |
|-------|----------|
| `@n8n-analyzer` | Analiza workflows JSON importados en `workflows/` |
| `@prompt-reviewer` | Revisa prompts contra la metodologia |

---

## Metodologia Momentum AI — Reglas NO NEGOCIABLES

Estas reglas estan comprobadas con 18+ proyectos reales. Son absolutas.

### Arquitectura
1. **SIEMPRE arquitectura modular** — nunca mega-prompt. 1-3 agentes especializados.
2. **Information Extractor como router LLM** — clasifica + extrae datos en un solo nodo.
3. **Formateador como LLM (Basic LLM Chain)** — divide respuesta en bloques de max 3 lineas.
4. **Un solo proposito por agente especializado** — si hace dos cosas, dividir.
5. **Template base se duplica** — nunca crear workflow desde cero.

### Prompts
6. **Agente principal: 3,000-5,000 chars max** (hasta 6,500 con gpt-4.1-mini).
7. **Agentes especializados: 1,000-2,000 chars max**.
8. **Router/Classifier: 1,500-3,500 chars max** — output JSON puro.
9. **Cambios QUIRURGICOS, nunca reescrituras** — si funciona al 70%, arreglar lo que falla.
10. **Si no sabe, no inventa** — "Deja verifico eso" en vez de inventar datos.

### Ventas
11. **Valor PRIMERO, datos despues** — nunca pedir email/tel antes de demostrar valor.
12. **BANT conversacional, nunca interrogatorio**.
13. **NUNCA compromisos vinculantes** — no confirmar precios, disponibilidad.
14. **Handoff = bot se apaga para ese lead** — Airtable "Chatbot Activado = Apagado".

### Formato (WhatsApp/Instagram)
15. **Maximo 3-4 lineas por mensaje** — el formateador se encarga.
16. **UNA pregunta por mensaje**.
17. **NO bold, NO bullets pegados, emojis moderados**.
18. **Tono semi-formal costarricense por default** — "vos", "queres", "tenes".
19. **SIEMPRE nombre propio del bot** — personalidad consistente.

### Modelos LLM
20. **gpt-4.1-mini para todo** — router (temp 0.1), agentes (temp 0.4), formateador usa gpt-4o-mini.
21. **Max tokens: 300-400** para router, 400 para agentes.
22. **Memory: Postgres Chat Memory, context window 15**.

---

## Estructura del Proyecto

```
Chatbot Arquitect/
├── CLAUDE.md                    # Este archivo
├── README.md                    # Setup y uso
├── .env.example                 # Template de variables
│
├── clients/                     # UN FOLDER POR CLIENTE
│   ├── .template/               # Estructura base (copiar para nuevo cliente)
│   └── {nombre-cliente}/
│       ├── docs/                # Lo que el cliente me pasa (PDFs, precios, etc.)
│       ├── discovery.json       # Output del discovery
│       ├── architecture.md      # Diseño de arquitectura
│       ├── prompts/             # Prompts individuales (para revision)
│       │   ├── router-classifier.md
│       │   ├── agente-principal.md
│       │   └── ...
│       ├── workflow/            # Config del workflow n8n
│       │   └── workflow-config.md
│       └── entrega.md           # Documento de entrega
│
├── knowledge/                   # Base de conocimiento (7 docs Momentum AI)
├── workflows/                   # Template JSON + workflows reales analizados
│   ├── chatbot-manychat.json    # Template base
│   ├── prompts-referencia/      # Prompts extraidos de workflows reales
│   └── *-analysis.md            # Analisis de cada workflow
│
├── .claude/
│   ├── skills/                  # Skills del pipeline
│   └── agents/                  # Agents de soporte
│
└── memory/                      # Conocimiento destilado
```

## Convenciones

- **Idioma del proyecto:** Espanol
- **Carpeta por cliente:** `clients/{nombre-en-kebab-case}/`
- **Prompts copiables:** Cada prompt listo para pegar en n8n, sin placeholders
- **Conteo de caracteres:** SIEMPRE reportar chars de cada prompt
- **Prompts de referencia:** `workflows/prompts-referencia/` contiene prompts reales de produccion

## Reglas del Proyecto

1. Todo prompt DEBE incluir conteo de caracteres
2. Todo architecture.md DEBE incluir diagrama de flujo y tabla de agentes
3. Documentos de entrega NUNCA mencionan: n8n, API, LLM, GPT, prompt, webhook, node, token
4. Antes de generar prompts, SIEMPRE leer `memory/metodologia-core.md`
5. Antes de diseñar arquitectura, SIEMPRE leer `memory/client-patterns.md`
6. Los prompts generados van en `clients/{cliente}/prompts/` como archivos individuales
7. El workflow-config.md incluye los prompts dentro de la config de cada nodo
