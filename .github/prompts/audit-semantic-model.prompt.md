---
description: "Auditar el modelo semántico y generar scoring dual (estructural + documentación)"
mode: agent
---

# Auditar modelo semántico

Ejecutá el agente **Semantic Model Auditor**. Consume el `context.json` del
`Model Explorer` y produce scoring dual.

## Pasos

1. **Verificar prerrequisito:** que exista `outputs/context/*_context.json`.
   - Si no: avisá al usuario que corra primero `/explore-model`.
2. Para cada `*_context.json`:
   - Aplicar reglas estructurales y de documentación
   - Calcular scoring dual (estructural + documentación + global)
   - Generar `outputs/audit/<proyecto>_semantic_model_findings.json`
   - Generar `outputs/audit/<proyecto>_semantic_model_audit.md`

## Output en chat

```
✅ Auditoría completada

RPAUT084:
  📐 Score Estructural:     47/100 (🟠 Necesita trabajo)
  📝 Score Documentación:    0/100 (🔴 Refactor masivo)
  🎯 Score Global:          33/100 (ponderado 70/30)

  Hallazgos: 200 total
    🔴 Crítico: 1
    ⚠️  Mejora: 55
    ℹ️  Obs:    144

📁 outputs/audit/RPAUT084_semantic_model_{audit.md,findings.json}

🤖 Sugerencia: correr /document-model para automatizar
   los 190 hallazgos de documentación.
```

## Modo

**Read-only.** No se modifica nada del proyecto.
