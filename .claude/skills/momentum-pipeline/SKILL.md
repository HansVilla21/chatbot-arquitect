---
name: momentum-pipeline
description: Ejecuta el pipeline completo de creacion de chatbot de principio a fin — discovery, arquitectura, prompts, workflow n8n, y documento de entrega. Usa cuando quieres crear un chatbot nuevo desde cero, cuando el usuario dice "chatbot nuevo", "crear chatbot", "proyecto nuevo de chatbot", "pipeline completo", o cuando quieres ejecutar todo el proceso.
---

# Momentum Pipeline — Creacion Completa de Chatbot

## Evaluacion Inicial

- **Lee** `memory/proyecto.md` — estado actual del sistema
- **Lee** `memory/metodologia-core.md` — reglas criticas
- **Pregunta:** Nombre del cliente/negocio para crear las carpetas

## Principios Core

1. **Checkpoint entre cada paso** — confirmar con el usuario antes de avanzar
2. **No saltar pasos** — el pipeline es secuencial por una razon
3. **Cada paso genera output** — el siguiente paso lo lee como input
4. **Documentar todo** — outputs en la carpeta del cliente

## Pipeline

### Paso 1: Discovery
Invocar `/momentum-discovery`
- Guia al usuario por el discovery de 15 min
- Output: `inputs/{cliente}/discovery.json`
- **CHECKPOINT:** "Discovery completo. Quieres revisar antes de diseñar la arquitectura?"

### Paso 2: Arquitectura
Invocar `/momentum-architect`
- Lee el discovery y diseña la arquitectura modular
- Output: `outputs/architectures/{cliente}.md`
- **CHECKPOINT:** "Arquitectura diseñada. Quieres revisar antes de generar los prompts?"

### Paso 3: Generacion de Prompts
Invocar `/momentum-prompt-gen`
- Lee la arquitectura y genera prompts para cada agente
- Output: `outputs/prompts/{cliente}/` (un archivo por agente)
- **CHECKPOINT:** "Prompts generados. Quieres revisar antes de diseñar el workflow?"

### Paso 4: Workflow n8n
Invocar `/momentum-n8n-builder`
- Lee la arquitectura y prompts, genera estructura del workflow
- Output: `outputs/workflows/{cliente}/workflow-spec.md`
- **CHECKPOINT:** "Workflow diseñado. Quieres revisar antes de generar el documento de entrega?"

### Paso 5: Documento de Entrega
Invocar `/momentum-delivery`
- Genera documento de entrega sin jerga tecnica
- Output: `outputs/deliveries/{cliente}-entrega.md`
- **FINAL:** "Pipeline completo. Todos los archivos estan en la carpeta del cliente."

## Resumen de Outputs

Al finalizar, el usuario tiene:
```
inputs/{cliente}/
  discovery.json              # Datos del discovery

outputs/architectures/
  {cliente}.md                # Arquitectura del chatbot

outputs/prompts/{cliente}/
  agente-principal.md         # Prompt del agente principal
  classifier.md (o .js)      # Classifier
  agente-*.md                 # Agentes especializados
  response-formatter.js      # Formatter

outputs/workflows/{cliente}/
  workflow-spec.md            # Especificacion del workflow n8n

outputs/deliveries/
  {cliente}-entrega.md        # Documento para el cliente
```

## Edge Cases

- **Usuario quiere saltar un paso:** Permitir, pero advertir que el output puede ser incompleto
- **Discovery ya existe:** Preguntar si quiere usar el existente o re-hacer
- **Solo necesita prompts:** Redirigir a `/momentum-prompt-gen` directamente

## Skills Relacionados

- `/momentum-discovery` — Paso 1
- `/momentum-architect` — Paso 2
- `/momentum-prompt-gen` — Paso 3
- `/momentum-n8n-builder` — Paso 4
- `/momentum-delivery` — Paso 5
