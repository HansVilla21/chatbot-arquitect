---
name: momentum-architect
description: Diseña la arquitectura modular de un chatbot dado el discovery de un cliente. Usa cuando necesitas decidir cuantos agentes, que modelo LLM, que canal, que CRM, como estructurar el flujo de n8n, o cuando el usuario dice "diseñar arquitectura", "cuantos agentes necesito", "que stack usar".
---

# Momentum Architect — Diseño de Arquitectura de Chatbot

## Evaluacion Inicial

Antes de diseñar:
- **Lee** `inputs/{cliente}/discovery.json` — si no existe, sugerir correr `/momentum-discovery` primero
- **Lee** `memory/metodologia-core.md` — reglas NO NEGOCIABLES
- **Lee** `memory/client-patterns.md` — patrones de los 7 clientes reales
- **Consulta** `references/stack-matrix.md` — matriz de stack por tipo de negocio
- **Consulta** `references/architecture-patterns.md` — patrones comprobados

## Principios Core

1. **SIEMPRE modular** — 3-5 agentes especializados, nunca mega-prompt
2. **Classifier primero** — Code Node si solo necesita routing, LLM si necesita extraccion de datos
3. **Agente principal como DEFAULT** — maneja 70-80% del trafico, es el fallback seguro
4. **Menos es mas** — empezar con minimo de agentes, agregar solo si hay necesidad clara

## Proceso

### Paso 1: Clasificar el Tipo de Negocio

Segun el discovery, determinar que patron aplica. Lee `references/stack-matrix.md` para la matriz completa.

| Tipo | Agentes | Modelo | Canal tipico |
|------|---------|--------|-------------|
| Servicios locales (clinicas, salones) | 2-3 | mini si <3k, 4o si >3k | WA o IG |
| Real estate / alquileres | 3-4 | GPT-4o siempre | WA + IG |
| E-commerce / productos | 3 | mini | WA o IG |
| Microfinanzas / formularios | 1 | mini | WA |
| Asesoria / consulting | 2 | GPT-4o | WA (YCloud) |
| B2B / SaaS | 4 | GPT-4o | WA + Web |

### Paso 2: Definir los Agentes

Para cada agente, definir:
- **Nombre** — descriptivo del rol
- **Proposito** — UNA sola funcion
- **Modelo** — GPT-4o o GPT-4o-mini (segun reglas en metodologia-core)
- **Chars target** — dentro de los limites
- **Tools** — que herramientas necesita (Google Sheets, Supabase, etc.)
- **% trafico** — estimado

Estructura minima (siempre presente):
```
1. CLASSIFIER (Code Node o LLM)
2. AGENTE PRINCIPAL (70-80% trafico)
3. RESPONSE FORMATTER (Code Node)
```

Agregar si necesario:
```
4. AGENTE PRECIOS/INVENTARIO (si hay catalogo o precios dinamicos)
5. AGENTE OBJECIONES LAARC (si el ciclo de venta es largo o ticket alto)
6. AGENTE CITAS (si hay agendamiento — aunque Calendly link suele ser suficiente)
```

### Paso 3: Definir el Routing

Para el classifier, definir:
- **Tipo:** Code Node (keywords) o LLM (extraccion de datos)
- **Destinos:** Maximo 3-4 rutas
- **Keywords por ruta** (si Code Node)
- **Campos a extraer** (si LLM)
- **Default:** SIEMPRE el agente principal

Regla: si solo hay 2-3 agentes y el routing es por keywords obvios → Code Node. Si necesitas extraer datos del historial (nombre, email, presupuesto) → LLM.

### Paso 4: Definir el Stack

| Componente | Opciones | Decision basada en |
|-----------|---------|-------------------|
| Canal WA | Evolution API / YCloud | Evolution si self-hosted. YCloud si necesita broadcasts/templates Meta |
| Canal IG | ManyChat → n8n | Siempre ManyChat para IG |
| CRM | Google Sheets / Airtable / Supabase | Sheets si simple. Airtable si visual. Supabase si complejo |
| Memory | Window Buffer 10 msgs | Siempre 10, subir a 15 si conversaciones largas |
| Cache | Redis | Solo si volumen alto o respuestas frecuentes |
| Citas | Calendly link hardcoded | No conectar API, el link ya muestra disponibilidad |
| Notificaciones | Discord | String detection en output del agente |
| Chat management | Chatwoot | Si hay equipo humano que necesita ver conversaciones |

### Paso 5: Dibujar el Flujo

Generar diagrama del workflow n8n:

```
[Webhook/Canal Trigger]
    ↓
[Code Node: Message Classifier]
    ↓
[Switch Node: Router]
    ↓ ←──── ↓ ←──── ↓
[AI Agent    [AI Agent    [AI Agent
 Principal]   Esp. 1]      Esp. 2]
    ↓             ↓             ↓
[Merge/Set Node]
    ↓
[Code Node: Response Formatter]
    ↓
[Canal Response Node]
    ↓
[DB: Save State]
    ↓ (conditional)
[Discord: Notification]
```

Adaptar segun la arquitectura definida (quitar/agregar nodos).

### Paso 6: Documentar y Guardar

Guardar en `outputs/architectures/{cliente}.md` con:
- Resumen del negocio (1-2 lineas)
- Tabla de agentes (nombre, proposito, modelo, chars target, % trafico)
- Tipo de classifier y routing
- Stack completo
- Diagrama de flujo
- Reglas de negocio criticas (que NO puede hacer el bot)
- Criterios de handoff
- Metricas target

## Edge Cases

- **Negocio ultra-simple** (formulario, 1 accion): Agente unico sin classifier. Ver patron Grandit.
- **Negocio complejo** (multi-producto, multi-canal): Maximo 5 agentes. Si necesita mas, dividir en workflows separados por canal.
- **Sin API de precios/inventario**: El bot NO confirma precios/disponibilidad. Redirige a links o al vendedor.
- **Modo proactivo + reactivo**: Dos flujos separados. El proactivo usa templates de Meta (YCloud). Ver patron Level/LEO.

## Errores Comunes

- **Problema:** Crear demasiados agentes por "por si acaso"
  **Solucion:** Empezar con 3. Agregar solo cuando un agente existente falla consistentemente.

- **Problema:** Classifier con 5+ destinos
  **Solucion:** Maximo 3-4. El default siempre es agente principal.

- **Problema:** Conectar API de Calendly para verificar disponibilidad
  **Solucion:** No. El link de Calendly YA muestra disponibilidad. Hardcodear el link en el prompt.

- **Problema:** Elegir GPT-4o-mini para ahorrar y despues el bot se pierde
  **Solucion:** Si ticket >$500 o prompt >3k chars → GPT-4o desde el inicio.

## Skills Relacionados

- `/momentum-discovery` — paso anterior (input para este skill)
- `/momentum-prompt-gen` — siguiente paso (genera prompts basados en esta arquitectura)
- `/momentum-pipeline` — ejecuta todo el pipeline
