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

## Variar los mensajes repetidos — NO usar templates palabra por palabra (CRITICO)

Cuando el bot tiene que enviar algo mas de una vez (link de Calendly, link a comunidad, respuesta a objecion recurrente), NUNCA debe usar el mismo mensaje textual. Repetir literal el mismo texto es la señal #2 que delata al bot (despues de la puntuacion formal).

**Solucion en el prompt:**
- NO dar UN template unico para acciones repetibles
- DAR 3-5 ejemplos variados y decirle "usa esto como inspiracion, NO como template"
- Instruccion explicita: "Cada vez que envies X, redacta el mensaje como si fuera la primera vez, tomando en cuenta lo que acabamos de hablar"
- Si el lead pide la misma cosa de nuevo, referenciar el contexto anterior ("aca te lo dejo de nuevo...", "ahi lo tenes otra vez...")

**Donde aplica especialmente:**
- Envio de Calendly (cada vez que se manda el link)
- Envio de links educativos / comunidades
- Respuestas a preguntas repetidas (precios, seguridad, rendimientos)
- Cierres de conversacion

## NUNCA prometer lo que el bot no puede entregar (CRITICO)

El bot solo puede enviar:
- Links (Calendly, web, comunidades, formularios)
- Texto conversacional

NO puede enviar (y por lo tanto NO debe prometer):
- PDFs, brochures, catalogos
- Videos grabados
- Imagenes
- Material educativo generico
- Audios

Si el cliente tiene material real que se puede compartir via link → ese link se hardcodea en el prompt con la frase exacta. Si NO hay material disponible, NO prometer nada — es peor decir "te mando material" y nunca enviarlo que solo cerrar cordialmente.

**Regla:** verificar en el discovery que SOLO material que tenga un LINK concreto puede estar en el prompt. Todo lo demas es promesa vacia.

## Information Extractor — PROHIBIDO usar llaves en el systemPromptTemplate

**Regla critica:** El campo `systemPromptTemplate` del Information Extractor (y de cualquier nodo que interpole expresiones de n8n) NO puede contener los simbolos `{` ni `}` sueltos. n8n los interpreta como sintaxis de expresion `{{ ... }}` y rompe el nodo.

**Solucion:** describir el formato del JSON de output usando notacion YAML (sin llaves) dentro del prompt. Aclarar explicitamente:
- "El output debe ser JSON, NO YAML"
- "YAML es solo para visualizar la estructura"
- "Devuelve el equivalente JSON con los mismos nombres de campos"

**Donde SI se pueden usar llaves:**
- Campo `inputSchema` del Information Extractor — es texto plano que no se interpola
- Campo `text` del Information Extractor — solo dentro de expresiones validas `{{ }}`

**Donde NO se pueden usar llaves:**
- systemPromptTemplate (del Information Extractor)
- systemMessage (de AI Agent)
- Cualquier campo que n8n marque como "expression" con el boton `fx`

## Information Extractor — el schema NO es contrato (CRITICO)

El campo `inputSchema` del nodo Information Extractor es solo una SUGERENCIA para el LLM, no un contrato estricto. El LLM puede renombrar campos espontaneamente segun el contexto del prompt. Ej: `agente` → `agente_asignado` → `decision` → `agente_destino`.

**Solucion obligatoria:** Meter el FORMATO EXACTO del JSON de output DENTRO del systemPromptTemplate del Information Extractor, al inicio del prompt, con:
1. El JSON completo con todos los campos
2. Una seccion explicita listando nombres PROHIBIDOS ("no uses `agente`, `decision`, etc.")
3. Repetir el nombre del campo principal al menos 3 veces en el prompt

**Patron comprobado:** los workflows reales de Dr. Carlos y El Canal usan `destino` como nombre del campo principal — palabra corta, neutra, que el LLM no tiende a renombrar.

**Si aun asi el LLM renombra:** el Switch debe leer el nombre que realmente se genera (inspeccionar el output real del nodo anterior, NO asumir por el schema).

## Puntuacion (CRITICO — evita delatar al bot)

La puntuacion formal es la señal #1 que delata que un mensaje viene de un bot. La gente real en WhatsApp/Instagram NO escribe con puntuacion academica.

**NO usar NUNCA:**
- **Dos puntos ( : )** — casi nadie los usa en chat, menos dentro de una pregunta
- **Punto y coma ( ; )** — nadie los usa en WhatsApp, se ve extremadamente formal
- **Signo de pregunta de apertura ( ¿ )** — casi nadie lo usa, solo signo al final

**Minimizar:**
- **Punto y seguido** — se percibe formal. Preferir saltos de linea o frases cortas separadas.
- **Punto y aparte** — preferir saltos de linea sencillos

**SI usar:**
- Signo de interrogacion SOLO al final: "Que te parece?"
- Comas naturales para pausa
- Saltos de linea para separar ideas
- Tildes correctamente (eso si es natural)

**Ejemplo comparativo:**

❌ BOT (delata): "Es totalmente valido, Hans, esa preocupacion es comun. ¿Que es lo que mas te preocupa: perder todo o que no rinda lo suficiente? La seguridad del capital es nuestra prioridad; todos los productos que asesoramos cuentan con proteccion y garantias."

✅ HUMANO: "Es totalmente valido, Hans, esa preocupacion es super comun. Que es lo que mas te preocupa, perder todo o que no rinda lo suficiente? La seguridad del capital es nuestra prioridad. Todos los productos que asesoramos cuentan con proteccion y garantias."

**Regla de oro:** Si al leerlo en voz alta suena a articulo de periodico o ensayo, es bot. Si suena a como le escribirias a un amigo por WhatsApp, es humano.

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
