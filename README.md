# Chatbot Arquitect

Sistema automatizado de creacion de chatbots de ventas usando la metodologia Momentum AI. Basado en 18+ proyectos reales en produccion.

## Que hace

Dado un negocio nuevo, el sistema guia todo el proceso de creacion de un chatbot:

1. **Discovery** — Recopila informacion del negocio en 15 minutos
2. **Arquitectura** — Diseña la estructura modular (agentes, modelos, canales)
3. **Prompts** — Genera prompts optimizados siguiendo los patrones probados
4. **Workflow n8n** — Genera 3 versiones del workflow (produccion + TEST + TELEGRAM)
5. **Entrega** — Crea documento para el cliente sin jerga tecnica

Pipeline completo en un solo comando: `/momentum-pipeline`

## Setup

1. Clona el repositorio
2. Copia `.env.example` a `.env` y llena tus API keys:
   ```bash
   cp .env.example .env
   ```
3. Abre la carpeta en Claude Code

## Skills disponibles

```
/momentum-pipeline           # Pipeline completo de principio a fin
/momentum-discovery          # Discovery con cliente nuevo (15 min)
/momentum-architect          # Diseñar arquitectura modular
/momentum-prompt-gen         # Generar prompts optimizados
/momentum-n8n-builder        # Guia de configuracion del workflow
/momentum-workflow-variants  # Generar versiones TEST y TELEGRAM
/momentum-delivery           # Documento de entrega sin jerga tecnica
/momentum-prompt-optimizer   # Optimizar prompts existentes
```

Agents de soporte:
```
@n8n-analyzer      # Analizar workflows JSON importados
@prompt-reviewer    # Revisar prompts contra metodologia
```

## Estructura del Proyecto

```
.claude/
├── skills/                      # Skills del pipeline (formato oficial)
│   ├── momentum-discovery/
│   ├── momentum-architect/
│   ├── momentum-prompt-gen/
│   ├── momentum-prompt-optimizer/
│   ├── momentum-n8n-builder/
│   ├── momentum-workflow-variants/
│   ├── momentum-delivery/
│   └── momentum-pipeline/
└── agents/                      # Agents para trabajo aislado
    ├── n8n-analyzer.md
    └── prompt-reviewer.md

knowledge/                       # Conocimiento y templates publicos
├── 00_README_PROYECTO.md
├── 01_METODOLOGIA_MOMENTUM_AI.md
├── 02_CASOS_CLIENTES_COMPLETOS.md
├── 03_TEMPLATES_Y_RECURSOS.md
├── 04_PATRONES_TECNICOS_N8N.md
├── 05_TROUBLESHOOTING_Y_OPTIMIZACION.md
├── 07_REPOSITORIOS_GITHUB_RECOMENDADOS.md
├── 08_LECCIONES_LEVEL_KENNETH.md
├── workflows-reference/         # 3 workflows reales como referencia
│   ├── template-base/           # Template original (villas de lujo)
│   ├── dr-carlos/               # Ejemplo: clinica medica (2 agentes)
│   └── el-canal/                # Ejemplo: real estate (3 agentes)
└── workflow-variants-templates/ # Templates anonimizados
    ├── TEST-template.json       # Para chat interno de n8n
    └── TELEGRAM-template.json   # Para bot de Telegram

memory/                          # Reglas criticas destiladas
├── metodologia-core.md          # 20+ reglas no negociables
├── client-patterns.md           # Patrones por tipo de negocio
├── learnings.md                 # Aprendizajes acumulados
└── decisions.md                 # Decisiones arquitectonicas

clients/                         # Clientes reales (NO en repo publico)
├── .template/                   # Estructura base (publica)
└── {nombre}/                    # Un folder por cliente (local)
    ├── docs/
    ├── discovery.json
    ├── architecture.md
    ├── prompts/
    ├── workflow/
    └── entrega.md

SOP.md                           # Procedimiento operativo estandar
CLAUDE.md                        # Instrucciones del proyecto
```

## Como Usarlo con un Cliente Nuevo

### Opcion A: Pipeline completo
```
/momentum-pipeline
```
Ejecuta los 7 pasos con checkpoints entre cada uno.

### Opcion B: Paso a paso

1. **Crear carpeta del cliente:**
   ```
   clients/{nombre-cliente}/
   ```
2. **Cargar documentos** del cliente en `docs/` (brochures, FAQs, precios, transcripts de reuniones)
3. **Generar discovery:** `/momentum-discovery`
4. **Diseñar arquitectura:** `/momentum-architect`
5. **Generar prompts:** `/momentum-prompt-gen`
6. **Generar workflow:** `/momentum-n8n-builder` (produccion)
7. **Generar variantes:** `/momentum-workflow-variants` (TEST + TELEGRAM)
8. **Documento de entrega:** `/momentum-delivery`

## Metodologia Momentum AI

Basado en 18+ proyectos reales de chatbots de ventas en Costa Rica y LATAM.

### Principios No Negociables

- **Arquitectura modular** — 3-5 agentes especializados, nunca mega-prompt
- **BANT conversacional** — preguntas abiertas, nunca interrogatorio
- **Valor primero, datos despues** — jamas pedir email/tel antes de dar valor
- **Cambios quirurgicos, nunca reescrituras** — si funciona al 70%, arreglar el 30%
- **Cierre en 2 pasos** — proponer → esperar confirmacion → enviar link
- **Handoff = bot deja de responder** — sin mensaje de transicion

### Stack Tipico

- n8n (self-hosted) — automatizacion
- Evolution API / YCloud — WhatsApp
- ManyChat — Instagram
- GPT-4.1-mini — agentes y router
- gpt-4o-mini — formateador
- Supabase/PostgreSQL — DB
- Redis — message batching

## Workflows de Referencia

Incluidos en `knowledge/workflows-reference/`:

- **Template base** — 1 agente + RAG (villas de lujo)
- **Dr. Carlos** — 2 agentes + objeciones LAARC (clinica medica)
- **El Canal** — 3 agentes + inventario + vendedores (real estate)

Cada uno con workflow JSON completo, analisis detallado y prompts de produccion como referencia.

## Templates para Testing

- **TEST-template.json** — Chat interno de n8n (15 nodos) — pruebas tecnicas rapidas
- **TELEGRAM-template.json** — Bot de Telegram (30 nodos) — pruebas con cliente real

Ambos anonimizados y listos para adaptar a cualquier cliente.

## Integracion con n8n MCP

El proyecto incluye configuracion para el MCP server de n8n-mcp (czlonkowski/n8n-mcp) que permite:
- Crear/modificar workflows directamente desde Claude Code
- Validar nodos contra la documentacion oficial
- Buscar entre 1,505 nodos y 2,709 templates
- Auditar seguridad de la instancia

Setup en `.mcp.json` — solo necesitas agregar `N8N_URL` y `N8N_API_KEY` en `.env`.

## Contribuir

Este es el repo publico. Para agregar mejoras:

1. Actualiza skills en `.claude/skills/`
2. Agrega templates nuevos en `knowledge/workflow-variants-templates/`
3. Documenta lecciones aprendidas en `knowledge/08_LECCIONES_*.md`
4. Nunca incluyas datos de clientes reales (estan en `clients/*` que esta gitignoreado)

## Licencia

MIT
