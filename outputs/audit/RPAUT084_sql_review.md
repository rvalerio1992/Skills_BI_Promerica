# 🔍 SQL Code Audit — RPAUT084

**Fecha:** 2026-04-24  
**Context:** `outputs/context/RPAUT084_context.json`  
**Tablas con SQL nativo:** 7  
**Score:** **92/100**

## Por severidad

| Severidad | Cantidad |
|---|---:|
| ⚠️ mejora | 2 |
| ℹ️ observación | 2 |

## Por regla

| Regla | Casos | Significado |
|---|---:|---|
| `SQL-SELECT-STAR` | 2 | SELECT * sin columnas explícitas |
| `SQL-CTE-NO-DOC` | 1 | CTEs sin comentarios |
| `SQL-HARDCODED-VALUES` | 1 | Lista IN (...) con muchos valores |

## Hallazgos por tabla


### `DimProducto` (1 hallazgos)

- ⚠️ **SQL-SELECT-STAR** — Query usa SELECT * sin especificar columnas explícitas
  - _Snippet:_ `SELECT * FROM dbo.dimproducto`

### `DimPromotor` (1 hallazgos)

- ⚠️ **SQL-SELECT-STAR** — Query usa SELECT * sin especificar columnas explícitas
  - _Snippet:_ `SELECT 
 *
FROM 
 dbo.dimpromotor`

### `DimTipoProducto` (1 hallazgos)

- ℹ️ **SQL-CTE-NO-DOC** — CTE(s) sin comentarios: ['base']

### `FctProductos` (1 hallazgos)

- ℹ️ **SQL-HARDCODED-VALUES** — Lista `IN (...)` con 5 valores hardcodeados
  - _Snippet:_ `IN ('CE_', 'CDI', 'PR_', 'TC_', 'XF_'...)`

---

## 📌 Plan de acción priorizado

1. **Expandir 2 `SELECT *`** — candidato al `sql-code-formatter` (Fase 3)
2. **Documentar 1 CTEs** — agregar `--` explicando propósito
3. **Revisar 1 listas IN hardcodeadas** — considerar tabla paramétrica

## 🛡️ Qué este auditor NO hace

- ❌ No sugiere optimizaciones estructurales (joins, subqueries, hints)
- ❌ No reescribe lógica de queries
- ❌ No agrega/quita índices
- ❌ No modifica archivos .tmdl