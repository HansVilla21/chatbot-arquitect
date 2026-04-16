# Decisiones del Proyecto

Registro de decisiones arquitectonicas y de diseño tomadas en el proyecto.

---

## 2026-04-15: Estructura Skills vs Agents

**Decision:** Usar skills (`.claude/skills/`) para el pipeline interactivo y agents (`.claude/agents/`) solo para trabajo aislado pesado.

**Por que:** La investigacion de anthropics/skills, louisblythe/Sales-Skills, y la documentacion oficial de Claude Code mostro que:
- Skills corren inline en la conversacion — el usuario ve todo e interactua
- Agents corren en contexto aislado — ideales para analisis pesado
- `.claude/commands/` esta deprecado a favor de `.claude/skills/`
- `.agent/skills/` no es el formato oficial — el correcto es `.claude/skills/`

**Alternativa descartada:** Todo como agents en `.claude/agents/`. Problema: el usuario no veria el proceso inline.

## 2026-04-15: Frameworks de Prompting

**Decision:** Integrar CO-STAR, TIDD-EC, y RTF como frameworks de generacion de prompts.

**Por que:** ckelsoe/prompt-architect documenta 27 frameworks con evidencia. Los 3 seleccionados cubren los casos del proyecto:
- CO-STAR: personalidad del chatbot (Context, Objective, Style, Tone, Audience, Response)
- TIDD-EC: guardrails del chatbot (Task, Instructions, Do, Don't, Examples, Context)
- RTF: agentes simples/especializados (Role, Task, Format)

## 2026-04-15: Documentos existentes a knowledge/

**Decision:** Mover los 7 docs existentes a `knowledge/` sin modificarlos. Destilar las reglas criticas a `memory/`.

**Por que:** Los docs son la fuente de verdad completa. `memory/` es para acceso rapido a reglas criticas. Los skills leen `knowledge/` via `references/` cuando necesitan profundidad.
