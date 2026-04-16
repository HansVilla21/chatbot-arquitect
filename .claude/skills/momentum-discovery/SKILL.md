---
name: momentum-discovery
description: Guia el proceso de discovery con un cliente nuevo de chatbot en 15 minutos. Usa cuando un cliente nuevo necesita onboarding, discovery, recopilacion de requerimientos, cuando empiezas un proyecto de chatbot desde cero, o cuando el usuario dice "tengo un cliente nuevo", "nuevo proyecto", "nuevo chatbot".
---

# Momentum Discovery — Recopilacion de Requerimientos

## Evaluacion Inicial

Antes de empezar, verificar:
- **Contexto:** Lee `memory/proyecto.md` para entender el stack disponible
- **Estado:** Pregunta el nombre del cliente/negocio para crear la carpeta en `inputs/`
- **Objetivo:** Recopilar toda la informacion necesaria para diseñar la arquitectura del chatbot

## Principios Core

1. **Discovery conversacional, no formulario** — preguntar de forma natural, no como encuesta
2. **15 minutos maximo** — 3 fases de 5 minutos cada una
3. **Capturar lo que importa** — diferenciador, objeciones, handoffs. No perder tiempo en nice-to-haves
4. **Output estructurado** — todo se guarda como JSON en `inputs/{cliente}/discovery.json`

## Proceso

### Fase 1: Entender el Negocio (5 min)

Pregunta UNA a la vez, en este orden. Si el usuario da multiples datos en una respuesta, extrae todo sin re-preguntar.

1. "Contame sobre el negocio — que vende/ofrece [empresa] y a quien?"
   → Captura: producto/servicio, cliente ideal
2. "Cual es el ticket promedio o rango de precios?"
   → Captura: ticket_promedio, rango_precios
3. "Cual es el ciclo de venta tipico — dias, semanas, meses?"
   → Captura: ciclo_venta
4. "Que los diferencia de la competencia? Cual es su ventaja principal?"
   → Captura: diferenciador
5. "Cuantas consultas reciben por dia/semana aproximadamente?"
   → Captura: volumen_consultas

### Fase 2: Mapear Proceso de Ventas (5 min)

6. "De donde vienen los leads — ads, organico, referidos, redes?"
   → Captura: origen_leads
7. "Que es lo primero que preguntan los clientes?"
   → Captura: preguntas_frecuentes (top 5)
8. "Cuales son las objeciones mas comunes — las 3 principales?"
   → Captura: objeciones (top 3)
9. "Que los convence de comprar/agendar — demo, precio, social proof?"
   → Captura: trigger_compra
10. "Como cierran actualmente — call, email, visita, compra directa?"
    → Captura: metodo_cierre

### Fase 3: Decisiones Tecnicas (5 min)

11. "Por que canal quieren el bot — WhatsApp, Instagram, ambos?"
    → Captura: canal
12. "Quien recibe los leads calificados? Nombre y como contactarlo."
    → Captura: handoff_persona, handoff_contacto
13. "Cual es el horario de atencion humana?"
    → Captura: horario_atencion
14. "Que nombre y personalidad quieren para el bot?"
    → Captura: bot_nombre, bot_tono
15. "El bot se identifica como bot o se hace pasar por humano?"
    → Captura: bot_identidad

### Checklist Post-Discovery

Despues de las 15 preguntas, verificar estos items que suelen escaparse:

- [ ] Accesos al canal (numero de WhatsApp, cuenta de IG)
- [ ] Definicion clara del handoff (quien recibe, como, cuando)
- [ ] Templates de WhatsApp aprobados por Meta (si usan API oficial)
- [ ] Que puede prometer el bot vs que NO puede prometer
- [ ] Tiene CRM actual o necesita uno nuevo?
- [ ] Tiene sistema de citas (Calendly, etc)?

Si falta algo critico, preguntar. Si es nice-to-have, anotar como pendiente.

## Output Esperado

Guardar en `inputs/{cliente}/discovery.json` con esta estructura:

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
    "metodo_cierre": ""
  },
  "tecnico": {
    "canal": "",
    "handoff_persona": "",
    "handoff_contacto": "",
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
  "pendientes": [],
  "notas": ""
}
```

## Edge Cases

- **Cliente no sabe que quiere:** Usar los patrones de `memory/client-patterns.md` para sugerir basado en negocios similares
- **Multiples canales:** Documentar todos, recomendar empezar con uno
- **Sin CRM:** Recomendar Google Sheets (simple) o Airtable (medio)
- **Sin sistema de citas:** Recomendar Calendly (gratis para empezar)
- **Cliente quiere TODO de una vez:** Priorizar — que es lo minimo viable que genera valor?

## Errores Comunes

- **Problema:** Preguntar las 15 preguntas como formulario sin escuchar las respuestas
  **Solucion:** Adaptar las preguntas segun lo que el cliente ya dijo. Si ya menciono el precio, no preguntar de nuevo.

- **Problema:** No capturar las objeciones — son criticas para el agente LAARC
  **Solucion:** Insistir en las top 3 objeciones. Sin ellas el bot no puede manejar resistencia.

- **Problema:** No definir el handoff claramente
  **Solucion:** Preguntar: "Cuando el bot identifica un lead calificado, exactamente que pasa? Quien lo recibe, por donde, en cuanto tiempo?"

## Skills Relacionados

- `/momentum-architect` — siguiente paso despues del discovery
- `/momentum-pipeline` — ejecuta todo el pipeline incluyendo discovery
