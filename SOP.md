# SOP: Creacion de Chatbot — Momentum AI

Guia paso a paso para crear un chatbot de ventas desde cero. Imposible perderse.

---

## Paso 0: Crear carpeta del cliente

```
clients/{nombre-en-kebab-case}/
  docs/
  prompts/
  workflow/
  README.md
```

Copiar la estructura de `clients/.template/` o crearla manualmente.

**Output:** Carpeta lista para recibir documentos.

---

## Paso 1: Recopilar documentos del cliente

Meter en `clients/{cliente}/docs/` TODO lo que el cliente te pase:

**Lo que necesitas (minimo):**
- [ ] Descripcion del negocio / que venden / a quien
- [ ] Precios o rangos de precios
- [ ] Preguntas frecuentes (Top 5)
- [ ] Objeciones comunes (Top 3)
- [ ] Diferenciador vs competencia
- [ ] Que accion quieren que el bot genere (agendar, vender, redirigir)

**Lo que suma mucho:**
- [ ] Brochures, catalogos, PDFs del negocio
- [ ] Transcripciones de reuniones / llamadas de venta
- [ ] Capturas de chats reales con clientes
- [ ] Guiones de venta / scripts de seguimiento
- [ ] Material educativo que usan con clientes

**Lo operativo:**
- [ ] Quien recibe leads calificados (nombre, contacto)
- [ ] Horario de atencion humana
- [ ] Canal: WhatsApp / Instagram / ambos
- [ ] CRM actual (Notion, Airtable, Sheets, etc.)
- [ ] Sistema de citas (Calendly, etc.)
- [ ] Nombre y personalidad del bot

**Output:** Carpeta `docs/` con todos los archivos del cliente.

---

## Paso 2: Leer y analizar documentos

Leer TODOS los archivos en `docs/`. Para cada documento, extraer:
- Informacion del negocio
- Datos de precios, productos, servicios
- Tono y estilo de comunicacion del cliente
- Proceso de venta actual
- Objeciones y como las manejan
- Criterios de calificacion de leads

**Output:** Entendimiento completo del negocio. No se genera archivo todavia.

---

## Paso 3: Generar discovery.json

Condensar TODA la informacion de los docs + reuniones en un JSON estructurado.

Este archivo es el "contrato" — todo el resto del pipeline lo lee como input.

```json
{
  "cliente": "nombre del negocio",
  "fecha_discovery": "YYYY-MM-DD",
  "negocio": {
    "descripcion": "",
    "producto_servicio": "",
    "cliente_ideal": "",
    "ticket_promedio": "",
    "rango_precios": "",
    "ciclo_venta": "",
    "diferenciador": "",
    "volumen_consultas": ""
  },
  "ventas": {
    "origen_leads": [],
    "preguntas_frecuentes": [],
    "objeciones_top3": [],
    "trigger_compra": "",
    "metodo_cierre": "",
    "proceso_seguimiento": ""
  },
  "tecnico": {
    "canal": "",
    "handoff_persona": "",
    "handoff_contacto": "",
    "handoff_metodo": "",
    "horario_atencion": "",
    "crm_actual": "",
    "sistema_citas": ""
  },
  "bot": {
    "nombre": "",
    "tono": "",
    "identidad": "humano | bot | no_revelado",
    "idiomas": ["es"]
  },
  "calificacion": {
    "criterios_lead_bueno": [],
    "criterios_descalificacion": [],
    "metodo_asignacion_vendedor": ""
  },
  "contenido": {
    "material_educativo": [],
    "links_importantes": [],
    "documentos_referencia": []
  },
  "notas": ""
}
```

**Output:** `clients/{cliente}/discovery.json`

---

## Paso 4: Diseñar arquitectura

Basado en el discovery.json, decidir:

### 4.1 Cuantos agentes?
- 1 agente: negocio ultra-simple (formulario, 1 CTA)
- 2 agentes: calificacion + objeciones (clinicas, consulting)
- 3 agentes: calificacion + inventario/catalogo + derivacion (real estate, e-commerce)

### 4.2 Que hace cada agente?
Para cada uno definir: nombre, proposito UNICO, modelo, tools, chars target.

### 4.3 Que extrae el router?
Campos a extraer del historial, destinos del Switch, condiciones de handoff.

### 4.4 Componentes opcionales?
- Filtro inicial (si hay conversaciones previas en el canal)
- Detector de descalificacion post-agente
- Deteccion de links (Calendly, wa.me) post-agente
- Asignacion de vendedores (round-robin)
- Sub-workflow para actualizar CRM

### 4.5 Stack
Canal, CRM, tools (RAG, Google Sheets), notificaciones.

**Output:** `clients/{cliente}/architecture.md`

---

## Paso 5: Generar prompts

Para cada componente de la arquitectura, generar el prompt usando los templates de referencia.

### Orden de generacion:
1. **Router/Classifier** (Information Extractor) — EL MAS CRITICO. Si rutea mal, todo falla.
2. **Agente principal** — El que maneja 70-80% del trafico.
3. **Agentes especializados** — Uno por uno, si aplican.
4. **Agente objeciones** — Si aplica.
5. **Filtro inicial** — Si aplica.
6. **Formateador** — Copiar sin modificar (es universal).
7. **Detector descalificacion** — Si aplica.

### Para cada prompt:
- Basarse en los templates de `.claude/skills/momentum-prompt-gen/assets/`
- Consultar prompts reales similares en `workflows/prompts-referencia/`
- Rellenar con datos REALES del discovery.json — cero placeholders
- Contar caracteres y verificar que esta dentro del limite
- Verificar formato WhatsApp (sin bold, max 3-4 lineas, 1 pregunta)

### Limites de caracteres:
| Componente | Min | Sweet Spot | Max |
|-----------|-----|-----------|-----|
| Agente principal | 2,000 | 3,000-5,000 | 6,500 |
| Agente especializado | 500 | 1,000-2,000 | 2,000 |
| Router/Classifier | 800 | 1,500-3,500 | 4,000 |
| Agente objeciones | 800 | 1,000-2,000 | 2,000 |

**Output:** Archivos individuales en `clients/{cliente}/prompts/`

---

## Paso 6: Configurar workflow n8n

No se crea un workflow desde cero. Se duplica el template base y se configura.

### Que cambiar en el template:
1. **Airtable** — credenciales, bases, tablas, campos del cliente
2. **Information Extractor (Router)** — pegar prompt del router + schema
3. **Switch** — ajustar rutas segun agentes de la arquitectura
4. **AI Agents** — pegar prompt de cada agente + configurar LLM + memory + tools
5. **Post-processing** — activar/desactivar segun arquitectura
6. **ManyChat** — credenciales, field IDs, flow IDs del cliente

### Config estandar para todos los nodos AI:
```
Router: gpt-4.1-mini | temp 0.1 | max 300-400 tokens | response_format json_object
Agentes: gpt-4.1-mini | temp 0.4 | max 400 tokens
Memory: Postgres Chat Memory | context window 15
Formateador: gpt-4o-mini
```

**Outputs:**
- `clients/{cliente}/workflow/chatbot-{cliente}.json` — JSON importable en n8n (produccion)
- `clients/{cliente}/workflow/chatbot-{cliente}-TEST.json` — Version simplificada para chat interno de n8n
- `clients/{cliente}/workflow/chatbot-{cliente}-TELEGRAM.json` — Version Telegram para pruebas con cliente
- `clients/{cliente}/workflow/workflow-config.md` — Guia de configuracion + checklist

### Las 3 versiones del workflow:

**Produccion (~65 nodos):** ManyChat + Airtable + Redis batching + formateador + todo el post-processing. Deploy final.

**TEST (~15 nodos):** Chat Trigger de n8n, sin Airtable/Redis/ManyChat. Para pruebas rapidas del equipo tecnico.

**TELEGRAM (~30 nodos):** Telegram Trigger + Telegram Send + REINICIAR + formateador con loop. Para pruebas con cliente/lead real.

**Generacion:** Usar `/momentum-workflow-variants` para crear las versiones TEST y TELEGRAM a partir del JSON de produccion. Los prompts deben ser byte-por-byte identicos en las 3 versiones (verificar con hash MD5).

### Verificaciones obligatorias antes de entregar cualquier JSON:

1. **Prompts sincronizados** — hash MD5 de cada prompt debe coincidir entre las 3 versiones
2. **Cero llaves en systemPromptTemplate** del Information Extractor (`{` y `}` rompen n8n)
3. **Expresiones con `.first()`** despues de Code/Agent/Loop (no `.item`)
4. **Switch lee campo correcto** (`$json.output.destino`, verificado contra el output real del IE)
5. **Postgres delete usa `operation: "deleteTable"` + `deleteCommand: "delete"`** (no `"delete"` solo)
6. **Telegram Send con `appendAttribution: false`** (si aplica)
7. **Prompt principal sigue reglas:** cierre en 2 pasos, puntuacion informal, variaciones, no prometer material inexistente
8. **Router prompt sigue reglas:** formato YAML embebido, nombres prohibidos listados, PREGUNTA vs OBJECION vs CORRECCION

### Si el MCP de n8n esta conectado:
Se puede deployar el workflow directo a la instancia con `n8n_create_workflow`, modificar nodos con `n8n_update_partial_workflow`, y testear con `n8n_test_workflow`.

---

## Paso 7: Documento de entrega

Generar documento para el cliente SIN jerga tecnica.

**Palabras prohibidas:** n8n, API, LLM, GPT, prompt, webhook, node, token, classifier, workflow, Redis, Postgres, Supabase

**Estructura:**
1. Que hace el bot (2-3 lineas simples)
2. Como funciona (sin tecnologia)
3. Que puede hacer (lista)
4. Que NO puede hacer (expectativas)
5. Cuando te notifica
6. Resultados esperados
7. Soporte

**Output:** `clients/{cliente}/entrega.md`

---

## Checklist Final

- [ ] Paso 0: Carpeta creada
- [ ] Paso 1: Docs cargados
- [ ] Paso 2: Docs leidos y analizados
- [ ] Paso 3: discovery.json generado
- [ ] Paso 4: architecture.md generado
- [ ] Paso 5: Prompts generados en prompts/
- [ ] Paso 6: JSON de workflow generado + version TEST
- [ ] Paso 6b: workflow-config.md generado (guia de configuracion)
- [ ] Paso 7: entrega.md generado
- [ ] JSON importado en n8n y credenciales configuradas
- [ ] Testeado con version TEST (chat interno de n8n)
- [ ] Testeado con 5+ conversaciones simuladas en produccion
- [ ] Cliente aprueba documento de entrega

---

*Este SOP se actualiza conforme se trabajan nuevos clientes. Ultima actualizacion: 2026-04-16*
