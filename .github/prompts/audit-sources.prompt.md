---
description: "Auditoría de fuentes de datos del modelo Power BI (lineage + clasificación Medallion)"
mode: agent
---

# Auditar fuentes de datos

Ejecutá el agente **Source Lineage Auditor**. Este agente consume el
`context.json` generado por el `Model Explorer`.

## Pasos

1. **Verificar prerrequisito:** que exista al menos un `outputs/context/*_context.json`.
   - Si no existe: avisá al usuario que corra primero `/explore-model`.
2. Cargar todos los `outputs/context/*_context.json` (soporta multi-proyecto).
3. Para cada proyecto, clasificar cada tabla según Medallion: `GOLD` / `NONGOLD` / `LOCAL` / `Calculada` / `Desconocido`.
4. Generar **un conjunto de outputs por proyecto**:
   - `outputs/ccu/<proyecto>_source_inventory.csv`
   - `outputs/ccu/<proyecto>_source_inventory.json`
   - `outputs/ccu/<proyecto>_source_inventory.md`

## Output inmediato en el chat

```
✅ Auditoría de fuentes completada
   Proyectos procesados: 1

   RPAUT084: 19 tablas
     🟡 GOLD:        7
     🟠 NONGOLD:     0
     🔵 LOCAL:       0
     ⚪ Calculada:  10
     ❓ Desconocido: 2

📁 outputs/ccu/
   • RPAUT084_source_inventory.csv
   • RPAUT084_source_inventory.json
   • RPAUT084_source_inventory.md
```

## Modo

**Read-only estricto.** No se modifica nada del proyecto.
