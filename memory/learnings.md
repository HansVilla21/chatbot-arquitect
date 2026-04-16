# Aprendizajes Acumulados

Lecciones aprendidas de proyectos reales. Se agrega aqui cada vez que se descubre algo nuevo.

---

## De Proyectos Reales (18+)

- En n8n, cada agente responde directamente al usuario — NO hay chaining de agentes en un solo turno
- "Te voy a pasar el contacto" + esperar = UX ROTO. Dar el contacto EN EL MISMO mensaje
- Classifier con demasiadas rutas = confusion. Maximo 2-3 destinos
- Token limit del classifier demasiado bajo = JSON vacio/cortado (minimo 500 tokens)
- Keywords de emergencia deben ser exhaustivos (se paso un caso en testing)
- Contenido educativo solo para awareness bajo (1-2), no para todos
- Closing pitch ANTES del link — no solo tirar el link
- Follow-up automations van en workflow SEPARADO, no en el bot principal
- Si el usuario pausa para consultar/coordinar → cerrar cordialmente, NO continuar el flujo
- A veces la optimizacion mas poderosa es QUITAR pasos, no agregar
- URLs con variables no resueltas en produccion = error fatal
- Inconsistencia entre "mision" y "flujo" del prompt = bot confundido
- Links de propiedades = experiencia positiva si se presentan bien
- El bot debe EDUCAR el proceso (usuarios no saben como funciona el sitio web)

## De Casos Legales (Advertencias)

- **Air Canada:** Bot prometio descuento por duelo que no existia → lawsuit. LECCION: el bot NUNCA hace compromisos vinculantes.
- **Chevy Dealership:** Bot confirmo compra de Tahoe por $1 sin guardrails de precio. LECCION: SIEMPRE validar rangos de precio.
