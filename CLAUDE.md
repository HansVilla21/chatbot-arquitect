# Chatbot Arquitect

## Que es este proyecto

Sistema automatizado de creacion de chatbots de ventas usando la metodologia Momentum AI. Dado un negocio nuevo, el sistema guia todo el proceso: discovery con el cliente, diseño de arquitectura modular, generacion de prompts optimizados, estructura de workflow n8n, y documento de entrega sin jerga tecnica.

**Pipeline completo:**
`/momentum-discovery` → `/momentum-architect` → `/momentum-prompt-gen` → `/momentum-n8n-builder` → `/momentum-delivery`

**Pipeline en un solo paso:**
`/momentum-pipeline` — ejecuta todo el proceso de principio a fin con checkpoints entre cada paso.

## Para quien

Hans (Momentum AI) — construye chatbots de ventas para negocios en Costa Rica y LATAM. Stack: n8n (self-hosted), Evolution API/YCloud (WhatsApp), ManyChat (Instagram), GPT-4o/4o-mini, Supabase/PostgreSQL.

## El Pipeline

| Paso | Skill | Que hace | Output |
|------|-------|----------|--------|
| 1 | `/momentum-discovery` | Guia discovery de 15 min con cliente nuevo | `inputs/{cliente}/discovery.json` |
| 2 | `/momentum-architect` | Diseña arquitectura modular del chatbot | `outputs/architectures/{cliente}.md` |
| 3 | `/momentum-prompt-gen` | Genera prompts para cada agente | `outputs/prompts/{cliente}/` |
| 4 | `/momentum-n8n-builder` | Genera estructura del workflow n8n | `outputs/workflows/{cliente}/` |
| 5 | `/momentum-delivery` | Genera documento de entrega al cliente | `outputs/deliveries/{cliente}.md` |

**Skills de soporte:**
- `/momentum-prompt-optimizer` — optimiza prompts existentes con cambios quirurgicos
- `@n8n-analyzer` — analiza workflows JSON importados en `workflows/`
- `@prompt-reviewer` — revisa prompts contra la metodologia

---

## Metodologia Momentum AI — Reglas NO NEGOCIABLES

Estas reglas estan comprobadas con 18+ proyectos reales. Son absolutas.

### Arquitectura
1. **SIEMPRE arquitectura modular** — nunca mega-prompt. 3-5 agentes especializados.
2. **Classifier como Code Node** cuando sea posible — 0 LLM calls, <50ms, $0, sin alucinaciones.
3. **Response Formatter como Code Node** — nunca LLM para formatear.
4. **Un solo proposito por agente especializado** — si hace dos cosas, dividir.

### Prompts
5. **Agente principal: 3,000-5,000 chars max** — sweet spot comprobado.
6. **Agentes especializados: 1,000-2,000 chars max** — una sola funcion.
7. **Classifiers: 1,500-3,000 chars max** — output JSON puro.
8. **Cambios QUIRURGICOS, nunca reescrituras** — si funciona al 70%, arreglar lo que falla.
9. **Una regla anti-repeticion, arriba del prompt** — nunca repetir la misma instruccion.
10. **Si no sabe, no inventa** — "Deja verifico eso" en vez de inventar datos.

### Ventas
11. **Valor PRIMERO, datos despues** — nunca pedir email/tel antes de demostrar valor.
12. **BANT conversacional, nunca interrogatorio** — preguntas abiertas que extraen Budget, Authority, Need, Timeline naturalmente.
13. **NUNCA compromisos vinculantes** — no confirmar precios, disponibilidad, ni promesas que no puede cumplir.
14. **Handoff = bot DEJA DE RESPONDER** — sin mensaje de "te paso con alguien". Notificar equipo por Discord.

### Formato (WhatsApp/Instagram)
15. **Maximo 3-4 lineas por mensaje** — corto = natural = engagement.
16. **UNA pregunta por mensaje** — nunca dos preguntas juntas.
17. **NO bold, NO bullets, NO emojis excesivos** — no renderizan en WhatsApp.
18. **Tono semi-formal costarricense por default** — "vos", "queres", "tenes". Sin "mae" ni "pura vida" en contexto profesional.
19. **SIEMPRE nombre propio del bot** — nunca generico. Personalidad consistente.

### Modelos LLM
20. **GPT-4o-mini para prompts <3k chars y tareas simples** — classifiers, agentes ultra-simples.
21. **GPT-4o para prompts >3k chars y conversaciones complejas** — agente principal complejo, multi-turno.
22. **Si el bot "se pierde" o inventa → cambiar de mini a 4o** — resuelve ~80% de los problemas.

---

## Estructura del Proyecto

```
Chatbot Arquitect/
├── CLAUDE.md                    # Este archivo — instrucciones del proyecto
├── README.md                    # Setup y uso
├── .env.example                 # Template de variables de entorno
│
├── knowledge/                   # Base de conocimiento (7 docs de Momentum AI)
│   ├── 01_METODOLOGIA...       # Principios, frameworks, metricas
│   ├── 02_CASOS_CLIENTES...    # 7 clientes reales con arquitecturas
│   ├── 03_TEMPLATES...         # Plantillas, checklists, curriculum
│   ├── 04_PATRONES_TECNICOS... # Code snippets, DB schemas, integraciones
│   ├── 05_TROUBLESHOOTING...   # Debugging, pre-deploy, optimizacion
│   └── 07_REPOSITORIOS...      # Repos de GitHub recomendados
│
├── .claude/
│   ├── skills/                  # Skills del pipeline (formato oficial Anthropic)
│   │   ├── momentum-discovery/
│   │   ├── momentum-architect/
│   │   ├── momentum-prompt-gen/
│   │   ├── momentum-prompt-optimizer/
│   │   ├── momentum-n8n-builder/
│   │   ├── momentum-delivery/
│   │   └── momentum-pipeline/
│   └── agents/                  # Agents para trabajo aislado
│       ├── n8n-analyzer.md
│       └── prompt-reviewer.md
│
├── memory/                      # Conocimiento destilado para acceso rapido
│   ├── proyecto.md
│   ├── metodologia-core.md
│   ├── client-patterns.md
│   ├── learnings.md
│   └── decisions.md
│
├── workflows/                   # JSONs de n8n importados (Hans agrega)
├── inputs/                      # Data de discovery por cliente
└── outputs/                     # Resultados generados
    ├── architectures/
    ├── prompts/
    ├── workflows/
    └── deliveries/
```

## Convenciones

- **Idioma del proyecto:** Espanol
- **Outputs de prompts:** En el idioma del cliente (ES por default, multiidioma si aplica)
- **Nombres de archivos:** kebab-case para skills/agents, nombre del cliente para outputs
- **Conteo de caracteres:** SIEMPRE reportar el conteo de chars de cada prompt generado
- **Knowledge como fuente de verdad:** Los 7 docs en `knowledge/` contienen todo el conocimiento. Los skills los leen bajo demanda via `references/`. `memory/` contiene solo las reglas criticas destiladas para acceso rapido.

## Reglas del Proyecto

1. Todo output de prompt DEBE incluir conteo de caracteres
2. Todo output de arquitectura DEBE incluir diagrama de flujo de nodos
3. Documentos de entrega NUNCA mencionan: n8n, API, LLM, GPT, prompt, webhook, node, token
4. Antes de generar prompts, SIEMPRE leer `memory/metodologia-core.md`
5. Antes de diseñar arquitectura, SIEMPRE leer `memory/client-patterns.md`
6. Los prompts generados DEBEN ser copiables directamente a n8n (sin necesidad de editar)
