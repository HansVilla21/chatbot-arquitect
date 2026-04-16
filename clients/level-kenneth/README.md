# Level — Kenneth (LEO)

## Estado: En proceso

## Negocio
Asesoria financiera / inversiones. Kenneth Varela es el asesor principal.

## Proceso

- [x] **1. Docs del cliente** — Cargar archivos en `docs/`
- [x] **2. Discovery** — `discovery.json` generado desde docs + reunion
- [ ] **3. Arquitectura** — Correr `/momentum-architect` → genera `architecture.md`
- [ ] **4. Prompts** — Correr `/momentum-prompt-gen` → genera archivos en `prompts/`
- [ ] **5. Workflow** — Correr `/momentum-n8n-builder` → genera `workflow/workflow-config.md`
- [ ] **6. Entrega** — Correr `/momentum-delivery` → genera `entrega.md`

## Archivos

```
docs/                   # Lo que Kenneth me pasa
prompts/                # Prompts individuales (para revision)
workflow/               # Config del workflow n8n
discovery.json          # Output del discovery
architecture.md         # Diseño de arquitectura
entrega.md              # Documento de entrega
```
