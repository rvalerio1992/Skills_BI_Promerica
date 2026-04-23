# 🔍 M Code Audit — RPAUT084

**Score:** **60/100**  
**Tablas con M:** 12  
**Total hallazgos:** 18

## Por severidad

| Severidad | Cantidad |
|---|---:|
| ⚠️ mejora | 11 |
| ℹ️ observación | 7 |

## Por regla

| Regla | Casos | Significado |
|---|---:|---|
| `M-STEP-DEFAULT-ES` | 8 | Step con nombre default (español) |
| `M-EXCESSIVE-STEPS` | 3 | >15 steps sin sub-lets |
| `M-NO-DATA-STEWARD` | 3 | Sin anotación de Data Steward |
| `M-TYPE-IN-SQL` | 2 | Conversión de tipos post-SQL |
| `M-NO-COMMENTS` | 2 | M largo sin comentarios |

## Hallazgos por tabla


### `FctProductos` (4 hallazgos)

- ⚠️ **M-STEP-DEFAULT-ES** — Step `#"Tipo cambiado"` tiene nombre default generado por Power BI Desktop (español)
- ⚠️ **M-STEP-DEFAULT-ES** — Step `#"Texto recortado"` tiene nombre default generado por Power BI Desktop (español)
- ⚠️ **M-STEP-DEFAULT-ES** — Step `#"Valor reemplazado"` tiene nombre default generado por Power BI Desktop (español)
- ℹ️ **M-TYPE-IN-SQL** — Conversión de tipos (Table.TransformColumnTypes) después de Sql.Database

### `DimMetas` (3 hallazgos)

- ⚠️ **M-STEP-DEFAULT-ES** — Step `#"Tipo cambiado"` tiene nombre default generado por Power BI Desktop (español)
- ⚠️ **M-EXCESSIVE-STEPS** — Expresión M con 34 steps en un solo let
- ℹ️ **M-NO-DATA-STEWARD** — Expresión M sin anotación `// Data Steward:` (convención Promerica)

### `DimPromotor` (3 hallazgos)

- ⚠️ **M-STEP-DEFAULT-ES** — Step `#"Texto recortado"` tiene nombre default generado por Power BI Desktop (español)
- ⚠️ **M-STEP-DEFAULT-ES** — Step `#"Duplicados quitados"` tiene nombre default generado por Power BI Desktop (español)
- ⚠️ **M-STEP-DEFAULT-ES** — Step `#"Valor reemplazado"` tiene nombre default generado por Power BI Desktop (español)

### `DimCalendario` (2 hallazgos)

- ⚠️ **M-EXCESSIVE-STEPS** — Expresión M con 18 steps en un solo let
- ℹ️ **M-NO-DATA-STEWARD** — Expresión M sin anotación `// Data Steward:` (convención Promerica)

### `DimProducto` (2 hallazgos)

- ⚠️ **M-STEP-DEFAULT-ES** — Step `#"Tipo cambiado"` tiene nombre default generado por Power BI Desktop (español)
- ℹ️ **M-TYPE-IN-SQL** — Conversión de tipos (Table.TransformColumnTypes) después de Sql.Database

### `DimTipoProducto` (2 hallazgos)

- ⚠️ **M-EXCESSIVE-STEPS** — Expresión M con 22 steps en un solo let
- ℹ️ **M-NO-COMMENTS** — Expresión M con 22 steps y solo 1 comentarios

### `Metricas` (2 hallazgos)

- ℹ️ **M-NO-COMMENTS** — Expresión M con 7 steps y solo 0 comentarios
- ℹ️ **M-NO-DATA-STEWARD** — Expresión M sin anotación `// Data Steward:` (convención Promerica)