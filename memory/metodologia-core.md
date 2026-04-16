# Metodologia Momentum AI — Reglas Criticas

Estas reglas estan comprobadas con 18+ proyectos reales. Son la fuente de verdad para todo lo que genera el sistema.

Fuente completa: `knowledge/01_METODOLOGIA_MOMENTUM_AI.md`

---

## Arquitectura

- **SIEMPRE modular** — 3-5 agentes especializados, nunca un mega-prompt
- **Classifier como Code Node** — keywords/patterns, 0 LLM calls, <50ms, $0
- **Classifier como LLM** — solo cuando necesitas extraer datos estructurados del historial
- **Response Formatter como Code Node** — formateo sin LLM
- **Un proposito por agente especializado** — si hace dos cosas, dividir
- **Agente principal = DEFAULT** — cuando hay duda en routing, va al principal
- **Maximo 3-4 destinos en routing** — demasiadas rutas = confusion

### Estructura universal
```
1. MESSAGE CLASSIFIER (Code Node, 0 LLM): Clasifica + enruta
2. AGENTE PRINCIPAL (70-80% trafico): Info general + calificacion BANT
3. AGENTES ESPECIALIZADOS (1-3, 10-30%): Un solo proposito cada uno
4. RESPONSE FORMATTER (Code Node, 0 LLM): Formato consistente
```

## Prompts — Limites de Caracteres

| Tipo | Sweet Spot | Maximo |
|------|-----------|--------|
| Agente principal | 3,000-5,000 chars | 5,000 |
| Agente especializado | 1,000-2,000 chars | 2,000 |
| Classifier | 1,500-3,000 chars | 3,000 |
| Agente objeciones | 1,000-2,000 chars | 2,000 |

- 500 → 2,000 chars: +40% mejora
- 5,000 → 10,000 chars: solo +5% mejora pero 3x costo
- >10k chars solo si incluye few-shot examples

## Prompts — Reglas

- **Cambios quirurgicos** — si funciona al 70%, arreglar lo que falla. NUNCA reescribir.
- **Una regla anti-repeticion** — arriba del prompt, en las primeras 500 chars. No repetirla.
- **No inventar** — "Deja verifico eso" en vez de inventar datos
- **Contar chars despues de cada cambio** — los prompts crecen con el tiempo
- **No instrucciones repetidas** — cada regla una sola vez

## Ventas

- **Valor PRIMERO, datos despues** — nunca pedir email/tel antes de dar valor. Cambio comprobado: 70% abandono → 30%+ conversion.
- **BANT conversacional** — preguntas abiertas que extraen Budget, Authority, Need, Timeline naturalmente. Nunca "cual es tu presupuesto?"
- **SPIN simplificado** — Situacion (1-2 preguntas) → Problema → Implicacion → Necesidad
- **LAARC para objeciones** — Listen → Acknowledge → Assess → Respond → Confirm
- **NUNCA descuento inmediato** — explorar primero "que presupuesto tenias?"
- **NUNCA compromisos vinculantes** — no confirmar precios exactos ni disponibilidad

## Formato WhatsApp/Instagram

- Max 3-4 lineas por mensaje
- UNA pregunta por mensaje (nunca dos)
- NO bold (**texto**) — no funciona en WhatsApp
- NO bullets — se siente formulario
- Emojis estrategicos y moderados (no excesivos)
- Mensajes cortos = natural = engagement

## Tono

- Semi-formal costarricense por default: "vos", "queres", "tenes"
- NO "mae" ni "pura vida" en contexto profesional
- SI: "perfecto", "con mucho gusto", "claro que si"
- SIEMPRE nombre propio del bot, personalidad consistente
- NUNCA revelar que es bot/IA (excepto si es parte de la estrategia)
- Adaptar nivel de formalidad al cliente

## Modelos LLM

| Uso | Modelo | Cuando |
|-----|--------|--------|
| Classifiers | GPT-4o-mini | Siempre |
| Agente principal <3k chars | GPT-4o-mini | Tickets <$500, volumen alto |
| Agente principal >3k chars | GPT-4o | Siempre |
| Conversaciones multi-turno complejas | GPT-4o | Siempre |
| Agentes especializados simples | GPT-4o-mini | Prompts <2k chars |

**Si el bot "se pierde" o inventa → cambiar de mini a 4o (resuelve ~80%)**

## Handoff a Humano

- Usuario pide hablar con humano → handoff
- 3+ mensajes de frustracion consecutivos → handoff
- Objecion que requiere negociacion real → handoff
- Lead altamente calificado listo para cerrar → handoff
- **Handoff = bot DEJA DE RESPONDER** — sin mensaje de "te paso con alguien"
- Notificar equipo por Discord/WhatsApp

## Metricas Target

```
Conversion: 30-40% (industria promedia 10-15%)
Coherencia: >95%
Latencia: <3 segundos
Abandono: <20%
Calificacion BANT: 60%+ con 3/4 criterios
Costo por chat: <$0.10
Sweet spot conversacion: 10-15 mensajes
```

## Errores Fatales (Comprobados)

1. Mega-prompt con GPT-4o-mini → olvida instrucciones, inventa
2. Pedir email/tel antes de dar valor → 70% abandono
3. BANT como interrogatorio → usuario se siente encuestado
4. Sin nombre ni personalidad → desenganche en 8 segundos
5. Bot confirma disponibilidad que no sabe → liability legal
6. Bot da precios exactos sin verificar → compromisos incumplibles
7. Instrucciones repetidas 3-4 veces → desperdicio + confusion
8. Demasiados edge cases "por si acaso" → complejidad innecesaria
9. "Te voy a pasar X" sin darlo en el mismo mensaje → UX roto en n8n
10. Formato bold/bullets en WhatsApp → no renderiza
