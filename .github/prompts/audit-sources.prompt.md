---
description: "Auditoría de fuentes de datos del modelo Power BI (lineage + clasificación Medallion)"
mode: agent
---

# Auditar fuentes de datos

Ejecutá el agente **Source Lineage Auditor** sobre el proyecto que está en `powerbi-project/`.

## Pasos

1. Verificá que exista un `.pbip` en `powerbi-project/`. Si está vacío, detené y avisá al usuario.
2. Escaneá todas las expresiones Power Query (M) de las particiones.
3. Clasificá cada fuente según Medallion: `GOLD` / `NONGOLD` / `LOCAL` / `Calculada` / `Desconocido`.
4. Extraé: servidor, base de datos, esquema, tabla origen, query SQL.
5. Generá los 3 archivos de output:
   - `outputs/ccu/source_inventory.csv`
   - `outputs/ccu/source_inventory.json`
   - `outputs/ccu/source_inventory.md`

## Output inmediato en el chat

Al terminar, mostrá en el chat un resumen corto:

```
✅ Auditoría completada
   Total tablas: 47
   🟡 GOLD:     3
   🟠 NONGOLD: 28
   🔵 LOCAL:   12
   ⚪ Otros:    4

📁 Reportes generados en outputs/ccu/
⚠️ 3 fuentes requieren clasificación manual (ver warnings en el MD)
```

## Modo

Read-only estricto. No modificar nada del proyecto.
