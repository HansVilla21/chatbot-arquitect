# {NOMBRE DEL CLIENTE}

## Estado: Pendiente

## Negocio
{descripcion}

## Proceso

- [ ] **1. Docs del cliente** — Cargar archivos en `docs/`
- [ ] **2. Discovery** — Correr `/momentum-discovery` → genera `discovery.json`
- [ ] **3. Arquitectura** — Correr `/momentum-architect` → genera `architecture.md`
- [ ] **4. Prompts** — Correr `/momentum-prompt-gen` → genera archivos en `prompts/`
- [ ] **5. Workflow** — Correr `/momentum-n8n-builder` → genera `workflow/workflow-config.md`
- [ ] **6. Entrega** — Correr `/momentum-delivery` → genera `entrega.md`

## Archivos

```
docs/                   # Lo que el cliente me pasa
  brochure.pdf
  precios.xlsx
  faqs.txt
  ...

discovery.json          # Output del discovery

architecture.md         # Diseño de arquitectura

prompts/                # Prompts individuales (para revision)
  router-classifier.md
  agente-principal.md
  agente-*.md
  formateador.md

workflow/               # Config del workflow n8n
  workflow-config.md    # Guia nodo-por-nodo
  
entrega.md              # Documento de entrega al cliente
```
