---
name: momentum-n8n-builder
description: Genera la estructura de un workflow de n8n para un chatbot, incluyendo nodos, conexiones, configuracion, y code snippets. Usa cuando necesitas diseñar el flujo de n8n, configurar triggers, classifiers, agentes, formatters, notificaciones, o cuando el usuario dice "workflow n8n", "crear flujo", "armar el workflow", "nodos de n8n".
---

# Momentum N8N Builder — Estructura de Workflow

## Evaluacion Inicial

- **Lee** `outputs/architectures/{cliente}.md` — arquitectura definida
- **Lee** `outputs/prompts/{cliente}/` — prompts generados
- **Consulta** `references/workflow-patterns.md` — patrones de nodos
- **Consulta** `references/code-snippets.md` — snippets reutilizables

## Principios Core

1. **Seguir la estructura universal** — Trigger → Classifier → Switch → Agents → Formatter → Response → DB
2. **Code Nodes para logica deterministica** — classifier por keywords, formatter, notificaciones, conversiones
3. **AI Agent Nodes para conversacion** — solo donde se necesita LLM
4. **Cada nodo con proposito claro** — si no sabes por que esta, no lo pongas

## Proceso

### Paso 1: Definir Nodos del Workflow

Basado en la arquitectura, listar cada nodo necesario:

**Nodos obligatorios:**
1. **Trigger** — Webhook (Evolution), Webhook (ManyChat), o trigger nativo (YCloud)
2. **Message Classifier** — Code Node o AI Agent segun arquitectura
3. **Switch/Router** — Basado en output del classifier
4. **AI Agent(s)** — Uno por cada agente definido en arquitectura
5. **Response Formatter** — Code Node que limpia formato
6. **Canal Response** — Envia respuesta al canal
7. **Save State** — PostgreSQL/Supabase o Google Sheets

**Nodos opcionales:**
8. **Discord Notification** — String detection condicional
9. **Round-Robin** — Asignacion de vendedores
10. **Currency Converter** — Si maneja colones/dolares

### Paso 2: Configurar Cada Nodo

Para cada nodo, especificar:
- **Tipo de nodo** en n8n
- **Configuracion clave** (model, temperature, max_tokens, etc.)
- **Input esperado** — que recibe del nodo anterior
- **Output esperado** — que envia al nodo siguiente
- **Codigo** (si es Code Node) — de `references/code-snippets.md`

### Paso 3: Definir Conexiones

Diagrama de conexiones entre nodos:
```
Trigger → Classifier → Switch
  Switch → Agent Principal (default)
  Switch → Agent Especialista 1
  Switch → Agent Especialista 2
Agent * → Formatter → Response → Save State
Formatter → (conditional) Discord Notification
```

### Paso 4: Configuracion de AI Agents

Para cada AI Agent node:
```yaml
Model: OpenAI Chat Model
  Model Name: gpt-4o / gpt-4o-mini
  Temperature: 0.3-0.5
  Max Tokens: 500-1000
Memory: Window Buffer Memory
  Context Window: 10
System Message: [prompt del agente — de outputs/prompts/{cliente}/]
Tools: [segun arquitectura]
```

### Paso 5: Generar Documento

Guardar en `outputs/workflows/{cliente}/workflow-spec.md` con:
- Lista de nodos con configuracion
- Diagrama de conexiones
- Code snippets para Code Nodes
- Configuracion de AI Agents
- Variables de entorno necesarias
- Instrucciones de setup en n8n

## Edge Cases

- **Sin workflows de referencia:** Generar solo la especificacion textual, no JSON
- **Multi-canal:** Workflows separados por canal, comparten agentes via sub-workflows
- **Follow-up automations:** Workflow SEPARADO del bot principal

## Errores Comunes

- **Problema:** Poner toda la logica en un solo AI Agent sin classifier
  **Solucion:** Siempre separar: classifier → routing → agentes especializados

- **Problema:** Formatter como AI Agent en vez de Code Node
  **Solucion:** Formateo es determinista — Code Node siempre

## Skills Relacionados

- `/momentum-prompt-gen` — paso anterior (genera los prompts que van en los agents)
- `/momentum-delivery` — siguiente paso (documento de entrega)
- `@n8n-analyzer` — analiza workflows existentes para aprender patrones
