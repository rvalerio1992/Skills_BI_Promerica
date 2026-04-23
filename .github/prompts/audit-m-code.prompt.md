---
description: "Auditar calidad del código Power Query (M) — naming, legibilidad, anti-patterns"
mode: agent
---

# Auditar código M (Power Query)

Ejecutá el agente **M Code Auditor** sobre todos los contexts disponibles.

## Pasos

1. **Verificar prerrequisito:** existir `outputs/context/*_context.json`.
   - Si no: avisá al usuario que corra primero `/explore-model`.
2. Para cada proyecto:
   - Identificar tablas con `partition.kind == "m"`
   - Leer la expresión M completa del `.tmdl` correspondiente
   - Aplicar reglas de naming, performance, documentación, query folding
3. Generar `outputs/audit/<proyecto>_m_review.{md,json}`

## Output en chat

```
✅ M Code Audit completado

RPAUT084:
  Tablas con M: 9
  Score: 68/100 🟡

  Hallazgos por regla:
    • M-STEP-DEFAULT-ES:  12 (steps con nombre default en español)
    • M-NO-COMMENTS:       3 (M largo sin comentarios)
    • M-HARDCODED-SERVER:  2 (servidor hardcodeado)
    • M-EXCESSIVE-STEPS:   1 (>15 steps en un let)

📁 outputs/audit/RPAUT084_m_review.{md,json}

🤖 Sugerencia: para renombrar steps masivamente,
   usar M Code Formatter (Fase 3 — próximamente).
```

## Modo

**Read-only estricto.** No modifica ningún archivo del proyecto.
