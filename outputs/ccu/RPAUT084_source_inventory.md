# 📊 Inventario de Fuentes — RPAUT084

**Fecha:** 2026-04-23  
**Context:** `outputs/context/RPAUT084_context.json`  
**Total tablas:** 19

## Resumen

| Clasificación | Tablas | % |
|---|---:|---:|
| ⚪ Calculada | 10 | 52.6% |
| 🟡 GOLD | 7 | 36.8% |
| ❓ Desconocido | 2 | 10.5% |

## Tablas con fuente externa

| Tabla | Tipo | Clasif. | Servidor | Base | Esquema | Origen | Query |
|---|---|---|---|---|---|---|---|
| `DimCliente` | dimension | 🟡 GOLD | `synw-modeling-prod-westus2-001-onde` | `gold` | `dbo` | `masteranalytics` | `SELECT   COD_CLIENTE,   NOMBRE,  TIPO_CLIENTE,  SE...` |
| `DimEstadoProducto` | dimension | 🟡 GOLD | `synw-modeling-prod-westus2-001-onde` | `gold` | `dbo` | `dimestadoproducto` | `SELECT   COD_ESTADO,  ESTADO FROM   dbo.dimestadop...` |
| `DimMoneda` | dimension | 🟡 GOLD | `synw-modeling-prod-westus2-001-onde` | `gold` | `dbo` | `DimMoneda` | `SELECT   COD_MONEDA,  DESCRIPCION AS MONEDA FROM  ...` |
| `DimProducto` | dimension | 🟡 GOLD | `synw-modeling-prod-westus2-001-onde` | `gold` | `dbo` | `dimproducto` | `SELECT * FROM dbo.dimproducto` |
| `DimPromotor` | dimension | 🟡 GOLD | `synw-modeling-prod-westus2-001-onde` | `gold` | `dbo` | `dimpromotor` | `SELECT   * FROM   dbo.dimpromotor` |
| `DimTipoProducto` | dimension | 🟡 GOLD | `synw-modeling-prod-westus2-001-onde` | `gold` | `dbo` | `dimtipoproducto` | `WITH base AS (    SELECT    COD_TIPO_PRODUCTO,    ...` |
| `FctProductos` | hechos | 🟡 GOLD | `synw-modeling-prod-westus2-001-onde` | `gold` | `dbo` | `fctproductos` | `SELECT  FEC_CORTE,  COD_CLIENTE,  COD_PRODUCTO,  C...` |

## Calculadas / Sin fuente externa

| Tabla | Tipo | Fuente |
|---|---|---|
| `DateTableTemplate_5e88a427-25a5-4994-a4fe-ebf1ac5e88b3` | dimension_auto | ⚪ tabla_calculada |
| `DimCalendario` | dimension | ⚪ m_dinamica |
| `DimMetas` | dimension | ⚪ tabla_calculada |
| `LocalDateTable_00444826-d1ae-48ed-9fe1-f6339b8a289a` | dimension_auto | ⚪ tabla_calculada |
| `LocalDateTable_385a457a-20df-4be8-ac4f-190433881064` | dimension_auto | ⚪ tabla_calculada |
| `LocalDateTable_a0176f28-d511-490b-aa1c-fc4edc033051` | dimension_auto | ⚪ tabla_calculada |
| `LocalDateTable_c38c851c-baf1-425a-b46b-6fc5b8e62172` | dimension_auto | ⚪ tabla_calculada |
| `Medidas` | medidas | ⚪ desconocido |
| `Metricas` | medidas | ⚪ tabla_calculada |
| `Parámetros` | parametros | ❓ desconocido |
| `Tabla` | desconocido | ⚪ tabla_calculada |
| `⚙ Medidas` | medidas | ❓ desconocido |

## 🔎 Hallazgos

✅ **Todas las fuentes externas son GOLD** — 7 tablas cumplen Medallion.

⚠️ **5 tablas `LocalDateTable_*` auto-generadas** — desactivar Auto Date/Time en el modelo.

ℹ️ **`SELECT *` detectado en:** `DimProducto` — especificar columnas.
