---
description: "Documentar el modelo semántico a partir de los hallazgos del auditor (modo DRY-RUN)"
mode: agent
---

# Documentar modelo semántico

Ejecutá el agente **Model Documenter** en modo DRY-RUN (por defecto).

## Pasos

1. **Verificar prerrequisitos:**
   - Debe existir `outputs/context/<proyecto>_context.json` (del Model Explorer).
   - Debe existir `outputs/audit/<proyecto>_semantic_model_findings.json` (del Auditor).
   - Si faltan: avisá al usuario que corra `/explore-model` y `/audit-semantic-model` primero.
2. Para cada findings.json, filtrar hallazgos con `category: "documentacion"` y reglas permitidas:
   - `MEASURE-NO-FORMAT`
   - `MEASURE-NO-FOLDER`
   - `MEASURE-NO-DESCRIPTION`
   - `TABLE-DESCRIPTION`
3. Para cada medida afectada, obtener su DAX del `context.measures[]`.
4. Generar propuestas de cambio con niveles de confianza (alta/media/baja).
5. Crear copias modificadas en `outputs/documented/<archivo>.tmdl` + diffs.
6. Generar `outputs/documented/_summary.md` con el resumen.

## Output en chat

```
✅ Documentación propuesta (DRY-RUN)

RPAUT084:
  Archivos modificados: 14
  Total cambios:       181
    🟢 Alta confianza:  108
    🟡 Media confianza:  54
    🟠 Baja confianza:   19

📁 outputs/documented/
   • _summary.md
   • Medidas.tmdl.diff (65 cambios)
   • ⚙ Medidas.tmdl.diff (59 cambios)
   • ...

🛡️ Los archivos originales siguen INTACTOS.

Para aplicar al modelo real: pedime "aplicá los cambios".
```

## Modo

**DRY-RUN por defecto.** Solo modifica originales si el usuario
solicita explícitamente la aplicación.
