---
name: momentum-pipeline
description: Ejecuta el pipeline completo de creacion de chatbot de principio a fin — discovery, arquitectura, prompts, workflow n8n, y documento de entrega. Usa cuando quieres crear un chatbot nuevo desde cero, cuando el usuario dice "chatbot nuevo", "crear chatbot", "proyecto nuevo de chatbot", "pipeline completo", o cuando quieres ejecutar todo el proceso.
---

# Momentum Pipeline — Creacion Completa de Chatbot

## Evaluacion Inicial

- **Lee** `memory/proyecto.md` — estado actual del sistema
- **Lee** `memory/metodologia-core.md` — reglas criticas
- **Pregunta:** Nombre del cliente/negocio para crear la carpeta

## Principios Core

1. **Checkpoint entre cada paso** — confirmar con el usuario antes de avanzar
2. **No saltar pasos** — el pipeline es secuencial por una razon
3. **Todo en una carpeta** — `clients/{cliente}/` contiene todo del proyecto
4. **Documentar todo** — cada paso genera un archivo visible

## Paso 0: Crear Carpeta del Cliente

Crear la estructura en `clients/{nombre-en-kebab-case}/`:
```
clients/{cliente}/
  docs/
  prompts/
  workflow/
  README.md (copiar de clients/.template/README.md y personalizar)
```

Si el usuario ya cargo docs, verificar que esten en `clients/{cliente}/docs/`.

## Pipeline

### Paso 1: Discovery
Invocar `/momentum-discovery`
- Si hay docs en `clients/{cliente}/docs/` → leerlos primero para contexto
- Guia al usuario por el discovery de 15 min
- Output: `clients/{cliente}/discovery.json`
- **CHECKPOINT:** "Discovery completo. Quieres revisar antes de diseñar la arquitectura?"

### Paso 2: Arquitectura
Invocar `/momentum-architect`
- Lee el discovery + docs del cliente
- Define que cambia en el template base
- Output: `clients/{cliente}/architecture.md`
- **CHECKPOINT:** "Arquitectura diseñada. Quieres revisar antes de generar los prompts?"

### Paso 3: Generacion de Prompts
Invocar `/momentum-prompt-gen`
- Lee la arquitectura y genera prompts para cada componente
- Output: archivos en `clients/{cliente}/prompts/` (uno por componente)
- Cada prompt listo para copiar-pegar al nodo de n8n correspondiente
- **CHECKPOINT:** "Prompts generados. Quieres revisar antes de configurar el workflow?"

### Paso 4: Workflow n8n
Invocar `/momentum-n8n-builder`
- Genera guia nodo-por-nodo de que cambiar en el template duplicado
- Incluye los prompts embebidos en la config de cada nodo
- Output: `clients/{cliente}/workflow/workflow-config.md`
- **CHECKPOINT:** "Workflow configurado. Quieres revisar antes del documento de entrega?"

### Paso 5: Documento de Entrega
Invocar `/momentum-delivery`
- Genera documento de entrega sin jerga tecnica
- Output: `clients/{cliente}/entrega.md`
- **FINAL:** "Pipeline completo."

## Resumen de Outputs

Al finalizar, la carpeta del cliente tiene:
```
clients/{cliente}/
  docs/                     # Lo que el cliente paso
  discovery.json            # Datos del discovery
  architecture.md           # Diseño de arquitectura
  prompts/                  # Prompts individuales (para revision)
    router-classifier.md
    agente-principal.md
    agente-*.md
    formateador.md
  workflow/
    workflow-config.md      # Config nodo-por-nodo con prompts embebidos
  entrega.md                # Documento para el cliente
  README.md                 # Estado y checklist del proyecto
```

## Edge Cases

- **Usuario quiere saltar un paso:** Permitir, advertir que el output puede ser incompleto
- **Discovery ya existe:** Preguntar si quiere usar el existente o re-hacer
- **Solo necesita prompts:** Redirigir a `/momentum-prompt-gen` directamente
- **Docs del cliente aun no estan:** Crear la carpeta y pedirle que los cargue antes de continuar

## Skills Relacionados

- `/momentum-discovery` — Paso 1
- `/momentum-architect` — Paso 2
- `/momentum-prompt-gen` — Paso 3
- `/momentum-n8n-builder` — Paso 4
- `/momentum-delivery` — Paso 5
