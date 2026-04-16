---
name: momentum-prompt-gen
description: Genera prompts optimizados para cada agente del chatbot siguiendo la metodologia Momentum AI. Usa cuando necesitas crear prompts para agente principal, classifier, agentes especializados, agente de objeciones LAARC, o cuando el usuario dice "generar prompts", "crear prompt", "escribir prompt", "prompt para el agente".
---

# Momentum Prompt Generator — Generacion de Prompts Optimizados

## Evaluacion Inicial

Antes de generar:
- **Lee** `outputs/architectures/{cliente}.md` — si no existe, sugerir `/momentum-architect` primero
- **Lee** `inputs/{cliente}/discovery.json` — datos del discovery
- **Lee** `memory/metodologia-core.md` — reglas de prompting (limites chars, formato, tono)
- **Consulta** los templates en `assets/` segun el tipo de agente

## Principios Core

1. **Cada prompt DEBE ser <5k chars** (principal) o <2k chars (especializado) — SIEMPRE reportar conteo
2. **Framework segun tipo de agente** — CO-STAR para principal, TIDD-EC para classifier, RTF para especialistas
3. **Copiable directo a n8n** — sin variables sin resolver, sin placeholders, valores reales
4. **Una regla anti-repeticion** — arriba del prompt, una sola vez

## Proceso

### Paso 1: Identificar Agentes a Generar

Segun la arquitectura, listar cada agente que necesita prompt:
- [ ] Agente principal
- [ ] Classifier (Code Node o LLM)
- [ ] Agente(s) especializado(s)
- [ ] Agente de objeciones (si aplica)
- [ ] Response formatter (Code Node — JavaScript, no prompt)

### Paso 2: Seleccionar Framework por Agente

| Tipo de Agente | Framework | Template | Chars Target |
|---------------|-----------|----------|-------------|
| Principal | CO-STAR | `assets/template-principal.md` | 3,000-5,000 |
| Classifier LLM | TIDD-EC | `assets/template-classifier-llm.md` | 1,500-3,000 |
| Classifier Code Node | JavaScript | `assets/template-classifier-code.js` | N/A |
| Especializado | RTF | `assets/template-especialista.md` | 800-1,500 |
| Objeciones LAARC | Custom | `assets/template-objeciones.md` | 1,000-2,000 |

Leer el framework correspondiente en `references/` para entender como aplicarlo:
- `references/co-star-framework.md` — para agente principal
- `references/tidd-ec-framework.md` — para classifier y guardrails
- `references/prompt-rules.md` — reglas universales

### Paso 3: Generar Cada Prompt

Para cada agente:

1. Leer el template correspondiente de `assets/`
2. Rellenar con datos del discovery y la arquitectura
3. Verificar que cumple las reglas de `references/prompt-rules.md`
4. Contar caracteres — si excede el limite, recortar sin perder funcionalidad
5. Verificar formato (sin bold, sin bullets, max 3-4 lineas por mensaje en ejemplos)

### Paso 4: Generar Code Nodes

Para classifier Code Node y response formatter, generar JavaScript funcional basado en los patrones de `knowledge/04_PATRONES_TECNICOS_N8N.md`.

### Paso 5: Guardar y Reportar

Guardar cada prompt en `outputs/prompts/{cliente}/`:
- `agente-principal.md`
- `classifier.md` (o `classifier.js` si Code Node)
- `agente-{nombre}.md` (por cada especializado)
- `agente-objeciones.md` (si aplica)
- `response-formatter.js`

Reportar tabla resumen:

```
| Agente | Tipo | Framework | Chars | Modelo |
|--------|------|-----------|-------|--------|
| [nombre] | Principal | CO-STAR | 4,200 | GPT-4o |
| [nombre] | Classifier | TIDD-EC | 2,100 | GPT-4o-mini |
| ...
```

## Edge Cases

- **Prompt excede limite:** Buscar redundancias, eliminar edge cases "por si acaso", consolidar instrucciones
- **Negocio multiidioma:** Agregar deteccion de idioma en classifier, agente principal responde en el idioma detectado
- **Sin objeciones definidas:** Usar las 4 genericas: precio, timing, product fit, desconfianza
- **Tono no costarricense:** Adaptar el template de tono. No forzar "vos" si el cliente es de otro pais

## Errores Comunes

- **Problema:** Prompt con instrucciones repetidas 3-4 veces
  **Solucion:** Cada regla UNA sola vez. Anti-repeticion en las primeras 500 chars.

- **Problema:** Variables sin resolver ({{ nombre }}, [EMPRESA])
  **Solucion:** Todo debe estar resuelto con datos reales del discovery. Cero placeholders.

- **Problema:** Ejemplos con datos ficticios que el modelo generaliza
  **Solucion:** Usar datos reales del cliente o no poner ejemplos con datos especificos.

- **Problema:** Prompt dice "te voy a pasar X" sin dar X en el mismo mensaje
  **Solucion:** En n8n cada agente responde directamente. Si va a dar un link/contacto, darlo EN EL MISMO mensaje.

## Skills Relacionados

- `/momentum-architect` — paso anterior (define que agentes necesitan prompts)
- `/momentum-prompt-optimizer` — para mejorar prompts existentes
- `/momentum-n8n-builder` — siguiente paso (usa los prompts en el workflow)
