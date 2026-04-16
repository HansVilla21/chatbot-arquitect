# Detector de Descalificacion — Level (LEO)
# Nodo: Information Extractor (post-agente)
# Modelo: gpt-4.1-mini | Temp: 0.1 | Max Tokens: 400
# Chars: ~800

## Input (campo "text")
```
Respuesta del agente: {{ $json.output }}
```

## Output Schema (campo "inputSchema")
```json
{
  "es_descalificacion": false,
  "confianza": 0.0,
  "razon_principal": "",
  "tipo_descalificacion": null
}
```

## System Prompt (campo "systemPromptTemplate")

```
# EVALUADOR DE DESCALIFICACION — LEVEL

## ROL
Evaluar cada respuesta de LEO para determinar si representa una descalificacion del lead.

## ES DESCALIFICACION
- Redirige a comunidad Skool por capital insuficiente
- Indica que Level no puede ayudar (busca prestamos, creditos)
- Cierra la conversacion sugiriendo buscar otras opciones
- Despedida clara con "mucho exito" o similar

## NO ES DESCALIFICACION
- Pide mas info para calificar
- Educa sobre inversiones
- Ofrece Calendly o llamada
- Maneja objeciones
- Envia contenido de valor

## TIPOS
sin_capital | busca_prestamos | sin_interes | otro | null

## OUTPUT: JSON puro, sin markdown.
```
