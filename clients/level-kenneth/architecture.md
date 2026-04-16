# Arquitectura: Level (LEO)

## Resumen
Asesoria financiera e inversiones. Bot "LEO" califica leads, educa sobre inversiones, maneja objeciones, y cierra a Calendly o venta directa. Canal: WhatsApp.

## Patron Base
**Dr. Carlos** — mismo tipo de negocio (servicio profesional, calificacion + objeciones + CTA a Calendly). 2 agentes + router.

---

## Agentes

| Agente | Proposito | Modelo | Tools | Memory | Chars Target |
|--------|-----------|--------|-------|--------|-------------|
| Router/Classifier | Clasifica destino + extrae datos del lead | gpt-4.1-mini (temp 0.1, 300 tokens) | - | - | 2,000-3,000 |
| LEO Principal | Calificacion conversacional + educacion financiera + cierre a Calendly/venta | gpt-4.1-mini (temp 0.4, 400 tokens) | - | Postgres 15 msgs | 4,000-5,000 |
| Agente Objeciones | LAARC para las 3 objeciones (pensarlo, miedo, caro) | gpt-4.1-mini (temp 0.4, 400 tokens) | - | Postgres 15 msgs | 1,200-1,800 |

**Por que 2 agentes y no 1:**
- El flujo de Kenneth es complejo: diagnostico → educacion → calificacion → cierre. Eso ya es un prompt largo (~4,500 chars).
- Las objeciones tienen su propio framework (LAARC) con 3 tipos especificos. Meterlo en el principal lo haria exceder el limite.
- Patron comprobado con Dr. Carlos: principal + objeciones funciona bien para servicios profesionales.

**Por que no 3 agentes:**
- No hay inventario/catalogo dinamico (no necesita agente de inventario con Google Sheets)
- No hay round-robin de vendedores (todo va a Ken o Valeria)
- El agente principal puede compartir el Calendly directamente sin necesidad de agente de derivacion

---

## Router (Information Extractor)

### Destinos
| Destino | Cuando | % Trafico |
|---------|--------|-----------|
| LEO_PRINCIPAL | Default. Todo lo que no es objecion ni handoff | ~75% |
| AGENTE_OBJECIONES | Primera objecion detectada (pensarlo, miedo, caro, otra) | ~15% |
| HANDOFF_HUMANO | Crisis, legal, 2da objecion, pide humano, frustrado | ~10% |
| BACKUP | Output vacio del router → LEO_PRINCIPAL | Fallback |

### Campos a Extraer
```json
{
  "nombre": null,
  "objetivo_financiero": null,
  "capital_mencionado": null,
  "capital_calificado": false,
  "tipo_inversion_interes": null,
  "nivel_conocimiento": "principiante | intermedio | avanzado",
  "fase_conversacion": "saludo | discovery | educacion | calificacion | cierre",
  "objeciones_count": 0,
  "ultima_objecion": null,
  "calendly_enviado": false
}
```

### Condiciones de Handoff
- Lead pide hablar con humano explicitamente
- 2da objecion (ya hubo una manejada por agente objeciones)
- Consulta legal/regulatoria sobre inversiones
- Lead frustrado o agresivo
- Caso ultra-especifico que requiere Kenneth (ej: inversion >$100K, situacion fiscal compleja)

---

## Flujo del Agente Principal (LEO)

```
1. Bienvenida + nombre
2. Pregunta abierta: "que tipo de inversion te interesa?" / "que objetivo financiero tenes?"
3. Discovery conversacional (como un doctor):
   - Que busca (ingresos pasivos, crecimiento, jubilacion, educacion)
   - Capital disponible (sin preguntar directo — inferir del contexto)
   - Que ha intentado antes
   - Nivel de conocimiento financiero
4. Educacion financiera adaptada al interes del lead:
   - Si busca seguridad → hablar de proteccion de capital, garantias
   - Si busca crecimiento → hablar de bolsa, rentabilidades historicas
   - Si busca ingresos pasivos → hablar de renta fija, dividendos
5. Calificacion:
   - Capital CRC 5M+ → lead calificado
   - Capital < CRC 5M → redirigir a comunidad educativa Skool
   - Sin capital pero interesado → contenido educativo
6. Cierre:
   - Lead calificado + interesado → Calendly directo
   - Lead tibio → ofrecer "15 min gratis con Kenneth para explorar opciones"
   - Lead frio → contenido de valor (video, PDF, blog)
```

---

## Componentes Opcionales

- [x] **Filtro inicial:** NO — es un numero de WhatsApp nuevo, no hay conversaciones previas
- [x] **Detector descalificacion:** SI — si LEO descalifica elegantemente (sin capital, busca prestamos), auto-apagar chatbot para ese lead
- [x] **Deteccion de links post-agente:** SI — Calendly detectado → notificar equipo + apagar chatbot
- [ ] **Asignacion vendedores:** NO — todo va a Ken o Valeria, no hay round-robin
- [ ] **Sub-workflow CRM:** NO por ahora — Notion no tiene API nativa facil de integrar desde n8n

---

## Post-Processing

Despues de TODOS los agentes, en paralelo al formateador:

```
Agent output → detectar links:
  Si contiene "calendly.com" → Notion: mover a "Llamada Agendada" + apagar chatbot
  Si contiene "wa.me/" → Notion: mover a handoff + apagar chatbot

Agent output → detector descalificacion:
  Si es_descalificacion = true → apagar chatbot
```

**Handoff = apagar chatbot en Airtable** (como en los otros workflows). Kenneth dijo que usa Notion pero para el chatbot se puede usar Airtable como CRM auxiliar del bot.

---

## Stack

| Componente | Seleccion | Razon |
|-----------|-----------|-------|
| Canal | ManyChat (WhatsApp) — YCloud cuando este listo | Kenneth esperando codigo de YCloud, ManyChat como backup |
| CRM bot | Airtable (auxiliar) | Notion no tiene API facil. Airtable es el estandar del template |
| CRM equipo | Notion (existente) | Kenneth y Valeria ya lo usan |
| DB historial | PostgreSQL | Estandar del template |
| Cache | Redis | Estandar del template (message batching) |
| Citas | Calendly: calendly.com/kenvarela/asesoria | Link hardcoded en prompt |
| Notificaciones | Por definir (Notion o WhatsApp a Valeria) | Kenneth no usa Discord |
| RAG | No necesario | Info del negocio cabe en el prompt, no hay catalogo extenso |

---

## Diagrama

```
Webhook (ManyChat/YCloud)
  → Airtable ON/OFF → Audio? → ID y Mensaje → Reiniciar?
  → Buscar Lead → Existe?
     ├─ SI → Apagado? → Update → GET Lead → continuar
     └─ NO → Crear Lead → GET Lead → continuar
  
  → Redis batching (45s) → ultimo? → Juntar
  → Postgres historial → Code formatear → Unificacion Variables
  
  → Information Extractor (ROUTER)
  → Switch:
     ├─ LEO_PRINCIPAL → AI Agent LEO
     ├─ AGENTE_OBJECIONES → AI Agent Objeciones (LAARC)
     ├─ HANDOFF_HUMANO → Apagar chatbot + notificar equipo
     └─ BACKUP → AI Agent LEO
  
  Agentes → [paralelo]:
     ├─ Redis delete (cleanup)
     ├─ Formateador LLM → Loop ManyChat send
     ├─ Detector descalificacion → apagar si descalifico
     └─ Deteccion Calendly → apagar + notificar si agendo
```

---

## Reglas de Negocio Criticas

1. **NUNCA garantizar rendimientos especificos** — "los rendimientos varian segun el producto"
2. **NUNCA dar asesoria financiera concreta** — solo educar y derivar a Kenneth
3. **NUNCA mencionar fees/comisiones** sobre productos de inversion — solo el precio del servicio
4. **Capital minimo CRC 5M** — descalificar elegantemente si menor
5. **Enfoque doctor:** preguntar para diagnosticar, no para vender
6. **Seguimientos con valor** — NUNCA pushy. PDFs, videos, educacion.
7. **Precio del servicio:** CRC 200-250K — no dar descuentos, explicar valor
8. **Calendly es el CTA principal:** calendly.com/kenvarela/asesoria
