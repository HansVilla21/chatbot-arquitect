# Formateador de Mensajes — Universal
# Nodo: Basic LLM Chain
# Modelo: gpt-4o-mini
# NOTA: Este prompt es universal — NO se modifica por cliente.
# Copiar tal cual al nodo Basic LLM Chain del template.

## Ver prompt completo en:
`.claude/skills/momentum-prompt-gen/assets/template-formateador.md`

## Resumen
- Divide mensajes largos en bloques de max 3 lineas
- Separa bullets pegados (• item1 • item2 → lineas separadas)
- Preguntas siempre en mensaje separado
- Output: JSON con "MENSAJE 1", "MENSAJE 2", etc.
- NO modifica el contenido, solo divide
