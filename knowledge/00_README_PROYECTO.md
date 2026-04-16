# MOMENTUM AI - PROYECTO CLAUDE CODE
## Índice y guía de uso de documentación

---

# ESTRUCTURA DE ARCHIVOS

```
/proyecto-momentum-ai/
│
├── README.md                              ← ESTE ARCHIVO (empieza aquí)
│
├── 01_METODOLOGIA_MOMENTUM_AI.md          ← Principios, arquitectura, frameworks
│   - Reglas fundamentales (modular >5k, modelo, quirúrgico vs reescritura)
│   - Patrones de arquitectura n8n
│   - BANT, SPIN, LAARC implementados
│   - Formato y tono (WhatsApp, Costa Rica)
│   - Métricas y benchmarks
│   - Errores fatales comprobados
│
├── 02_CASOS_CLIENTES_COMPLETOS.md         ← Todos los clientes con arquitecturas y decisiones
│   - Jacó Dream Rentals (Liliana) — villas de lujo, agente único
│   - Desarrollos Ecológicos / El Canal (Eva) — real estate, multi-agente
│   - SmartCheck Costa Rica — inspecciones vehiculares
│   - Dr. Carlos Hernández — consultorio médico, Instagram DM
│   - Microcréditos Grandit (Alexa) — microfinanzas, ultra-simple
│   - Level (LEO) — asesoría de inversiones
│   - 97 Display (Ray) — referencia de n8n community
│
├── 03_TEMPLATES_Y_RECURSOS.md             ← Plantillas reutilizables
│   - Checklist onboarding cliente nuevo
│   - Discovery framework (15 min)
│   - Template prompt agente principal
│   - Template prompt classifier
│   - Template prompt objeciones LAARC
│   - Template prompt agente especialista
│   - Momentum AI Academy curriculum (8 clases)
│   - 4 GPTs especificaciones
│   - Template documento entrega (no técnico)
│   - Template propuesta comercial
│
├── 04_PATRONES_TECNICOS_N8N.md            ← Código y configuraciones
│   - Estructura base de workflow
│   - Triggers por canal (Evolution, YCloud, ManyChat)
│   - Code snippets: classifier, round-robin, formatter, notificaciones
│   - Conversión de moneda (CRC ↔ USD)
│   - Extracción de datos
│   - PostgreSQL schema
│   - Google Sheets como CRM
│   - Matriz de stack por tipo de negocio
│   - Configuración de herramientas externas
│
├── 05_TROUBLESHOOTING_Y_OPTIMIZACION.md   ← Debugging y mejora continua
│   - Diagnóstico rápido (síntoma → causa → solución)
│   - Checklist pre-deploy
│   - Playbook de optimización post-launch
│   - SQL queries para dashboard de métricas
│   - Metodología de debugging de prompts
│   - Patrones anti-repetición consolidados
│
├── 06_INVESTIGACION_PROMPTS_VENTAS.md     ← Documento de research original
│   - Datos de 400+ empresas
│   - Casos reales con métricas >30% conversión
│   - Frameworks BANT, SPIN, LAARC con implementaciones
│   - Longitud óptima de prompts
│   - Anti-patterns que destruyen conversiones
│   - Métricas por industria
│   - Implementaciones n8n documentadas
│   - Arquitecturas multi-agent
│   - RAG optimization
│   - A/B testing methodology
│
└── /workflows/                            ← PENDIENTE: JSONs de n8n exportados
    ├── jaco_dream_rentals.json
    ├── smartcheck.json
    ├── el_canal.json
    ├── dr_carlos.json
    └── base_template.json
```

---

# CÓMO USAR ESTE PROYECTO

## Para crear un chatbot nuevo desde cero:
1. Leer `01_METODOLOGIA` → principios y reglas
2. Buscar caso similar en `02_CASOS_CLIENTES` → referencia de arquitectura
3. Usar templates de `03_TEMPLATES` → discovery, prompts, entrega
4. Implementar con snippets de `04_PATRONES_TECNICOS` → código n8n
5. Verificar con `05_TROUBLESHOOTING` → checklist pre-deploy

## Para optimizar un chatbot existente:
1. Diagnosticar con `05_TROUBLESHOOTING` → síntoma → causa → solución
2. Aplicar cambios quirúrgicos basados en `01_METODOLOGIA` principios
3. Comparar con `02_CASOS_CLIENTES` para ver si otro proyecto tuvo el mismo issue

## Para la Academia:
1. Curriculum completo en `03_TEMPLATES` sección 7
2. Specs de GPTs en sección 7.1-7.4
3. Proyecto build-along (clínica dental) documentado

---

# PENDIENTES PARA COMPLETAR EL PROYECTO

## Archivos que Hans debe agregar manualmente:
- [ ] Exportar workflows JSON de n8n de cada cliente → carpeta /workflows/
- [ ] Copiar el documento de research original como 06_INVESTIGACION_PROMPTS_VENTAS.md
- [ ] Agregar prompts COMPLETOS actuales de cada cliente (los que están en producción HOY)
- [ ] Crear skills personalizadas basadas en esta documentación

## Skills sugeridas para crear en Claude Code:
1. **chatbot-architect**: Dado un negocio, diseña arquitectura modular
2. **prompt-generator**: Genera prompts <5k chars para cada tipo de agente
3. **n8n-workflow-builder**: Genera JSON de workflow basado en arquitectura
4. **prompt-optimizer**: Analiza prompt existente y sugiere mejoras quirúrgicas
5. **client-delivery**: Genera documentación de entrega sin jerga técnica

---

# STACK TÉCNICO DE REFERENCIA

```yaml
Automatización: n8n (self-hosted)
WhatsApp: Evolution API (self-hosted) o YCloud (oficial)
Instagram: ManyChat
CRM: Airtable, Google Sheets, Notion
Backend: Supabase/PostgreSQL
Cache: Redis
LLMs: GPT-4o (principal), GPT-4o-mini (classifiers)
Scheduling: Calendly
Notificaciones: Discord
Chat Management: Chatwoot
Imágenes: Freepik
```
