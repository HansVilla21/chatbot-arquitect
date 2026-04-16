# Configuracion del Workflow: Level (LEO)

Guia nodo-por-nodo de que modificar en el template base duplicado.

---

## 1. Airtable — Credenciales y Tablas

### Tabla ON/OFF (nodo "Search records1")
```yaml
Base: [CRM Level]
Tabla: "Chatbot ON/OFF"
Campos: Estado (ON/OFF)
Credencial: [Airtable Level]
```

### Tabla Leads (nodos "Buscar Lead", "Crear Lead", "GET Lead", "Update Timestamp")
```yaml
Base: [CRM Level]
Tabla: "Leads LEO"
Campos:
  - ID Manychat (texto) — identificador unico
  - Nombre (texto)
  - Objetivo Financiero (texto)
  - Capital Mencionado (texto)
  - Estado (opciones): Nuevo | Seguimiento | Frio | Tibio | Caliente | Llamada Agendada | Level | Descartado
  - Chatbot Activado (opciones): Encendido | Apagado
  - Ultimo contacto (fecha)
  - Notas (texto largo)
Credencial: [Airtable Level]
```

### Buscar Lead
```yaml
Operacion: search
Formula: ={ID Manychat} = "{{ $json.ID }}"
```

### Crear Lead
```yaml
Operacion: create
Valores:
  ID Manychat: {{ $('ID y Mensaje').item.json.ID }}
  Nombre: {{ $('Webhook').item.json.body.name }}
  Chatbot Activado: "Encendido"
  Ultimo contacto: {{ $now }}
  Estado: "Nuevo"
```

---

## 2. Information Extractor — Router (NODO MAS CRITICO)

```yaml
Nodo: Information Extractor
Nombre: "Clasificador"

Campo "text":
  "# Historial de conversacion\n{{ $json['Historial de conversación'] }}\n\n# Mensaje actual del usuario\n{{ $json['Mensaje actual del usuario'] }}"

Campo "schemaType": manual

Campo "inputSchema":
  → Copiar el Output Schema de: prompts/router-classifier.md

Campo "systemPromptTemplate":
  → Copiar el System Prompt de: prompts/router-classifier.md

Sub-nodo LLM (OpenAI Chat Model):
  model: gpt-4.1-mini
  temperature: 0.1
  maxTokens: 300
  responseFormat: json_object
  Credencial: [OpenAI Level]
```

---

## 3. Switch — Rutas

```yaml
Nodo: Switch1
Nombre: "Router"

Output 0 — "LEO_PRINCIPAL":
  Condicion: {{ $json.output.agente_destino }} equals "LEO_PRINCIPAL"
  Conecta a: AI Agent - Principal

Output 1 — "AGENTE_OBJECIONES":
  Condicion: {{ $json.output.agente_destino }} equals "AGENTE_OBJECIONES"
  Conecta a: AI Agent - Objeciones

Output 2 — "HANDOFF_HUMANO":
  Condicion: {{ $json.output.agente_destino }} equals "HANDOFF_HUMANO"
  Conecta a: Update record (Airtable: Chatbot Activado = "Apagado")

Output 3 — "BACKUP":
  Condicion: {{ $json.output.agente_destino }} notExists
  Conecta a: AI Agent - Principal (fallback)
```

---

## 4. AI Agent — LEO Principal

```yaml
Nodo: AI Agent
Nombre: "AI Agent - Principal"

promptType: define

Campo "text" (user message):
  "# Mensaje del usuario\n{{ $('Unificación de Variables').item.json['Mensaje actual del usuario'] }}"

Campo "systemMessage":
  → Copiar el System Prompt de: prompts/agente-principal.md
  (todo lo que esta dentro del bloque ```)

Sub-nodo LLM (OpenAI Chat Model):
  model: gpt-4.1-mini
  temperature: 0.4
  maxTokens: 400
  Credencial: [OpenAI Level]

Sub-nodo Memory (Postgres Chat Memory):
  sessionKey: {{ $('Unificación de Variables').item.json.Telefono }}
  contextWindowLength: 15
  Credencial: [Postgres Level]

Sub-nodo Tools: ninguno

Conecta a (en paralelo):
  → Redis (delete) — cleanup
  → Basic LLM Chain (formateador)
  → Detector descalificacion
  → Deteccion Calendly
```

---

## 5. AI Agent — Objeciones

```yaml
Nodo: AI Agent
Nombre: "AI Agent - Objeciones"

promptType: define

Campo "text" (user message):
  "# Mensaje del usuario\n{{ $('Unificación de Variables').item.json['Mensaje actual del usuario'] }}"

Campo "systemMessage":
  → Copiar el System Prompt de: prompts/agente-objeciones.md

Sub-nodo LLM (OpenAI Chat Model):
  model: gpt-4.1-mini
  temperature: 0.4
  maxTokens: 400
  Credencial: [OpenAI Level]

Sub-nodo Memory (Postgres Chat Memory):
  sessionKey: {{ $('Unificación de Variables').item.json.Telefono }}
  contextWindowLength: 15
  Credencial: [Postgres Level]

Sub-nodo Tools: ninguno

Conecta a: mismos 4 nodos paralelos que el Principal
```

---

## 6. Post-Processing

### 6a. Detector de Descalificacion

```yaml
Nodo: Information Extractor
Nombre: "Descalificado?"

Recibe output de: AMBOS agentes (Principal y Objeciones)

Campo "text":
  "Respuesta del agente: {{ $json.output }}"

Campo "inputSchema":
  → Copiar Output Schema de: prompts/detector-descalificacion.md

Campo "systemPromptTemplate":
  → Copiar System Prompt de: prompts/detector-descalificacion.md

Sub-nodo LLM:
  model: gpt-4.1-mini
  temperature: 0.1
  maxTokens: 400

Despues:
  If: {{ $json.output.es_descalificacion }} == true
    → Airtable Update: Chatbot Activado = "Apagado", Estado = "Descartado"
```

### 6b. Deteccion de Calendly

```yaml
Nodo: If
Nombre: "Calendly Enviado?"

Condicion: {{ $json.output }} contains "calendly.com"

Si TRUE:
  → Airtable Update: Chatbot Activado = "Apagado", Estado = "Llamada Agendada"
  → (Opcional) Notificar a Valeria via Notion o WhatsApp
```

### 6c. Redis Cleanup

```yaml
Nodo: Redis (delete)
Key: {{ $('Unificación de Variables').item.json.Telefono }}
```

---

## 7. Formateador

```yaml
Nodo: Basic LLM Chain
Nombre: "Formateador"

Campo "text" (user message):
  "Respuesta a formatear: {{ $json.output }}"

Campo "messages" (system message):
  → Copiar prompt completo de: .claude/skills/momentum-prompt-gen/assets/template-formateador.md

Sub-nodo LLM:
  model: gpt-4o-mini
  Credencial: [OpenAI Level]

Sub-nodo Output Parser:
  Auto-fixing Output Parser → Structured Output Parser
  Schema: { "output": { "MENSAJE 1": "texto", "MENSAJE 2": "texto" } }
```

---

## 8. ManyChat — Envio de Mensajes

```yaml
Nodo: HTTP Request — "Set Respuesta Chatbot"
  Method: POST
  URL: https://api.manychat.com/fb/subscriber/setCustomField
  Auth: Bearer [Token ManyChat Level]
  Body:
    subscriber_id: {{ $('Unificación de Variables').item.json.Telefono }}
    field_id: [ID del campo "Respuesta Chatbot" en ManyChat]
    field_value: {{ $json.output }}

Nodo: HTTP Request — "Send Respuesta"
  Method: POST
  URL: https://api.manychat.com/fb/sending/sendFlow
  Auth: Bearer [Token ManyChat Level]
  Body:
    subscriber_id: {{ $('Unificación de Variables').item.json.Telefono }}
    flow_ns: [ID del flujo de envio en ManyChat]
```

---

## 9. Credenciales Necesarias

| Credencial | Donde conseguir | Nodos que la usan |
|-----------|----------------|-------------------|
| Airtable API Token | airtable.com/account | Todos los nodos Airtable |
| OpenAI API Key | platform.openai.com/api-keys | Todos los LLM + Formateador |
| PostgreSQL | Conexion a la DB de n8n | Chat Memory + Conversation |
| Redis | Conexion al Redis de n8n | Message batching + cleanup |
| ManyChat Bearer Token | manychat.com → Settings → API | HTTP Requests de envio |
| (Futuro) YCloud | Cuando este el codigo de invitacion | Reemplaza ManyChat |

---

## Checklist de Configuracion

- [ ] Airtable: base creada con tabla "Chatbot ON/OFF" + tabla "Leads LEO"
- [ ] Airtable: credenciales conectadas en n8n
- [ ] Template n8n duplicado y renombrado "Chatbot Level - LEO"
- [ ] Router: prompt pegado en Information Extractor + schema configurado
- [ ] Switch: 4 rutas configuradas (LEO, Objeciones, Handoff, Backup)
- [ ] AI Agent Principal: prompt pegado + LLM + Memory configurados
- [ ] AI Agent Objeciones: prompt pegado + LLM + Memory configurados
- [ ] Detector descalificacion: prompt pegado + If node conectado
- [ ] Deteccion Calendly: If node configurado + Airtable update conectado
- [ ] Formateador: prompt pegado + Output Parsers configurados
- [ ] ManyChat: Bearer token + field_id + flow_ns configurados
- [ ] Redis: credenciales verificadas
- [ ] PostgreSQL: credenciales verificadas + tabla n8n_chat_histories existe
- [ ] Webhook URL actualizada en ManyChat
- [ ] Testeado con "REINICIAR" para limpiar estado
- [ ] Testeado con 5+ conversaciones simuladas:
  - [ ] Saludo basico → flujo normal
  - [ ] Lead calificado → Calendly
  - [ ] Lead sin capital → descalificacion elegante
  - [ ] Objecion de precio → LAARC
  - [ ] Pide hablar con humano → handoff
