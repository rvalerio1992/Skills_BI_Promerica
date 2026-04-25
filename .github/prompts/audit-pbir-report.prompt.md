---
description: "Auditar calidad del reporte PBIR — páginas, visuales, bookmarks, filtros"
mode: agent
---

# Auditar reporte PBIR

Ejecutá el agente **PBIR Report Auditor** sobre los proyectos disponibles.

## Pasos

1. **Detectar proyectos:** buscar `powerbi-project/*.Report/` en el repo
2. **Cargar context** (opcional) desde `outputs/context/` para cross-validar
   referencias a tablas/columnas/medidas. Si no existe, las reglas que
   requieran cross-validación se marcan como "no verificable".
3. **Parsear PBIR directamente:**
   - `pages/` → metadata de cada página
   - `pages/*/visuals/` → cada visual con su queryState
   - `bookmarks/` → cada bookmark con su explorationState
4. **Aplicar 8 reglas** (3 críticas + 5 mejoras)
5. **Generar outputs:**
   - `outputs/audit/<proyecto>_pbir_review.json`
   - `outputs/audit/<proyecto>_pbir_review.md`

## Output en chat

```
✅ PBIR Report Audit completado

RPAUT084:
  Páginas: 7 (4 visibles, 3 ocultas)
  Visuales: 251 (155 con datos, 96 decorativos)
  Bookmarks: 13
  Score: 67/100 🟡

  Hallazgos por regla:
    • PBIR-HIDDEN-DUPLICATE-PAGE: 1 (página "Duplicado de Top Variaciones")
    • PBIR-TOO-MANY-VISUALS:      1 (página "Panel" con 103 visuales)
    • PBIR-VISUAL-EMPTY-FIELDS:   0
    • ...

📁 outputs/audit/RPAUT084_pbir_review.{md,json}
```

## Modo

**Read-only estricto.** No modifica ni el `.Report/` ni el modelo.

## Cross-validación con context (opcional)

Si existe `outputs/context/<proyecto>_context.json`, la regla
`PBIR-FILTER-ORPHAN` valida que cada filtro del reporte apunte a tablas/
columnas que existen en el modelo semántico. Sin context, esta regla no
puede ejecutarse.
