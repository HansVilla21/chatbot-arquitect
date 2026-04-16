# Chatbot Arquitect

Sistema automatizado de creacion de chatbots de ventas usando la metodologia Momentum AI.

## Que hace

Dado un negocio nuevo, el sistema guia todo el proceso de creacion de un chatbot:

1. **Discovery** — Recopila informacion del negocio en 15 minutos
2. **Arquitectura** — Diseña la estructura modular (agentes, modelos, canales)
3. **Prompts** — Genera prompts optimizados para cada agente (<5k chars)
4. **Workflow n8n** — Genera la estructura del flujo de automatizacion
5. **Entrega** — Crea documento de entrega sin jerga tecnica

## Setup

1. Clona el repositorio
2. Copia `.env.example` a `.env` y llena tus API keys:
   ```bash
   cp .env.example .env
   ```
3. Abre la carpeta en Claude Code

## Uso

### Pipeline completo (recomendado)
```
/momentum-pipeline
```
Ejecuta todo el proceso de principio a fin con checkpoints entre cada paso.

### Skills individuales
```
/momentum-discovery         # Discovery con cliente nuevo
/momentum-architect         # Diseñar arquitectura
/momentum-prompt-gen        # Generar prompts
/momentum-n8n-builder       # Generar workflow n8n
/momentum-delivery          # Generar documento de entrega
/momentum-prompt-optimizer  # Optimizar prompt existente
```

### Agents de soporte
```
@n8n-analyzer       # Analizar workflows JSON importados
@prompt-reviewer     # Revisar prompts contra metodologia
```

## Agregar workflows de n8n

Para que el sistema aprenda de tus mejores flujos:

1. Exporta el workflow desde n8n (Menu → Download)
2. Copia el JSON a `workflows/`
3. Usa `@n8n-analyzer` para extraer patrones

## Estructura

```
knowledge/       # Base de conocimiento (7 docs de Momentum AI)
memory/          # Reglas criticas destiladas
.claude/skills/  # Skills del pipeline
.claude/agents/  # Agents de soporte
inputs/          # Data de discovery por cliente
outputs/         # Resultados generados
workflows/       # JSONs de n8n importados
```

## Metodologia

Basado en Momentum AI — 18+ proyectos reales de chatbots de ventas en Costa Rica. Principios clave:

- Arquitectura modular (3-5 agentes, nunca mega-prompt)
- Prompts <5k caracteres
- BANT conversacional (nunca interrogatorio)
- Valor primero, datos despues
- Cambios quirurgicos, nunca reescrituras
