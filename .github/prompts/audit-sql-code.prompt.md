---
description: "Auditar calidad del SQL embebido en particiones M — legibilidad y documentación"
mode: agent
---

# Auditar SQL embebido

Ejecutá el agente **SQL Code Auditor** sobre todos los contexts disponibles.

## Pasos

1. **Verificar prerrequisito:** que existan `outputs/context/*_context.json`.
   - Si no: avisá al usuario que corra primero `/explore-model`.
2. Para cada proyecto:
   - Filtrar tablas con `partition.has_native_query == true`
   - Aplicar reglas de legibilidad, documentación, naming, seguridad
3. Generar `outputs/audit/<proyecto>_sql_review.{md,json}`

## Output en chat

```
✅ SQL Code Audit completado

RPAUT084:
  Tablas con SQL: 7
  Score: 75/100 🟡

  Hallazgos por regla:
    • SQL-SELECT-STAR:        2 (DimProducto, DimPromotor)
    • SQL-CTE-NO-DOC:         3 (CTEs sin comentario)
    • SQL-HARDCODED-VALUES:   1 (lista de 5+ productos)
    • ...

📁 outputs/audit/RPAUT084_sql_review.{md,json}

🤖 Sugerencia: los SELECT * son candidatos ideales
   para el SQL Code Formatter (Fase 3 — próximamente).
```

## Modo

**Read-only estricto.** No modifica ni el .tmdl ni el SQL.
