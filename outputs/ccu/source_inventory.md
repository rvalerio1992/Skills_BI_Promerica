# 📊 Inventario de Fuentes — RPAUT084

**Fecha:** 2026-04-22  
**Proyecto:** `RPAUT084`  
**Total de tablas analizadas:** 19

## Resumen ejecutivo

### Distribución por clasificación (Medallion)

| Clasificación | Tablas | % |
|---|---:|---:|
| ⚪ Calculada | 10 | 52.6% |
| 🟡 GOLD | 7 | 36.8% |
| ❓ Desconocido | 2 | 10.5% |

### Distribución por tipo de fuente

| Tipo | Tablas |
|---|---:|
| tabla_calculada | 8 |
| azure_synapse | 7 |
| m_dinamica | 2 |
| desconocido | 2 |

---

## Tablas con fuente externa (NONGOLD + GOLD)

| Tabla | Tipo | Clasif. | Servidor | Base | Esquema | Tabla Origen | Query SQL |
|---|---|---|---|---|---|---|---|
| `DimCliente` | dimensión | 🟡 GOLD | `synw-modeling-prod-westus2-001-ondemand.` | `gold` | `dbo` | `masteranalytics` | `SELECT COD_CLIENTE, NOMBRE, TIPO_CLIENTE, SEGMENTACION_ESTRA...` |
| `DimEstadoProducto` | dimensión | 🟡 GOLD | `synw-modeling-prod-westus2-001-ondemand.` | `gold` | `dbo` | `dimestadoproducto` | `SELECT COD_ESTADO, ESTADO FROM dbo.dimestadoproducto` |
| `DimMoneda` | dimensión | 🟡 GOLD | `synw-modeling-prod-westus2-001-ondemand.` | `gold` | `dbo` | `DimMoneda` | `SELECT COD_MONEDA, DESCRIPCION AS MONEDA FROM dbo.DimMoneda ...` |
| `DimProducto` | dimensión | 🟡 GOLD | `synw-modeling-prod-westus2-001-ondemand.` | `gold` | `dbo` | `dimproducto` | `SELECT * FROM dbo.dimproducto` |
| `DimPromotor` | dimensión | 🟡 GOLD | `synw-modeling-prod-westus2-001-ondemand.` | `gold` | `dbo` | `dimpromotor` | `SELECT * FROM dbo.dimpromotor` |
| `DimTipoProducto` | dimensión | 🟡 GOLD | `synw-modeling-prod-westus2-001-ondemand.` | `gold` | `dbo` | `dimtipoproducto` | `WITH base AS ( SELECT COD_TIPO_PRODUCTO, NOM_PRODUCTO, CASE ...` |
| `FctProductos` | hechos | 🟡 GOLD | `synw-modeling-prod-westus2-001-ondemand.` | `gold` | `dbo` | `fctproductos` | `SELECT FEC_CORTE, COD_CLIENTE, COD_PRODUCTO, COD_TIPO_PRODUC...` |

## Tablas calculadas / sin fuente externa

| Tabla | Tipo | Fuente |
|---|---|---|
| `DateTableTemplate_5e88a427-25a5-4994-a4fe-ebf1ac5e88b3` | dimensión | ⚪ tabla_calculada |
| `DimCalendario` | dimensión | ⚪ m_dinamica |
| `DimMetas` | dimensión | ⚪ m_dinamica |
| `LocalDateTable_00444826-d1ae-48ed-9fe1-f6339b8a289a` | dimensión | ⚪ tabla_calculada |
| `LocalDateTable_385a457a-20df-4be8-ac4f-190433881064` | dimensión | ⚪ tabla_calculada |
| `LocalDateTable_a0176f28-d511-490b-aa1c-fc4edc033051` | dimensión | ⚪ tabla_calculada |
| `LocalDateTable_c38c851c-baf1-425a-b46b-6fc5b8e62172` | dimensión | ⚪ tabla_calculada |
| `Medidas` | medidas | ❓ desconocido |
| `Metricas` | medidas | ⚪ tabla_calculada |
| `Parámetros` | parámetros | ❓ desconocido |
| `Tabla` | desconocido | ⚪ tabla_calculada |
| `'⚙ Medidas'` | medidas | ⚪ tabla_calculada |

---

## 🔎 Hallazgos y observaciones

### ✅ Todas las fuentes externas son GOLD
7 tablas consumen directamente del warehouse GOLD en Azure Synapse. Esto cumple con las mejores prácticas de Medallion Architecture.

### ⚠️ 5 tablas `LocalDateTable_*` auto-generadas
Power BI crea estas tablas cuando está activada la opción 'Auto Date/Time'. Se recomienda desactivarla para reducir tamaño del modelo y usar únicamente `DimCalendario`.

### ⚠️ Tablas con naming no estándar
Tablas sin prefijo FACT_/DIM_/PARAM_: `Medidas`, `Metricas`, `Parámetros`, `Tabla`. Considerar renombrar según convenciones del banco.

### ℹ️ Queries con `SELECT *` detectadas
Tablas que importan todas las columnas: `DimProducto`, `DimPromotor`. Se recomienda especificar columnas explícitamente para reducir tamaño del modelo y evitar romper al cambiar el schema origen.


---

## 📌 Recomendaciones


1. **Desactivar Auto Date/Time** en Opciones del archivo → configuración del modelo actual → Inteligencia de Tiempo. Elimina las 4+1 tablas `LocalDateTable_*`.
2. **Renombrar tablas genéricas** siguiendo convenciones: `Medidas` → `_Medidas` (oculta), `Parámetros` → `PARAM_Filtros`, `Tabla` → `_Auxiliar` o eliminar si no se usa.
3. **Reemplazar `SELECT *`** por listas explícitas de columnas en las queries de `DimProducto`, `DimPromotor`.
4. **Centralizar en DimCalendario**: ya existe, solo falta asegurar que todas las relaciones temporales apunten a ella y no a las auto-generadas.

