---
name: naming-conventions
description: >
  Use this skill when the user needs to audit, enforce, or apply naming
  conventions on a Power BI semantic model (tables, columns, measures,
  relationships). Incluye estándares institucionales de Banco Promerica
  para FACT/DIM/BRIDGE, nombres de medidas, display folders.
---

# Naming Conventions — Banco Promerica

Estándares de nombres para modelos semánticos y reportes.

## Tablas

| Tipo | Prefijo | Ejemplos |
|---|---|---|
| Tabla de hechos | `FACT_` | `FACT_Ventas`, `FACT_Transacciones`, `FACT_Cartera` |
| Dimensión | `DIM_` | `DIM_Cliente`, `DIM_Producto`, `DIM_Fecha`, `DIM_Sucursal` |
| Puente (many-to-many) | `BRIDGE_` | `BRIDGE_Cliente_Producto` |
| Parámetros | `PARAM_` | `PARAM_Moneda`, `PARAM_Escenario` |
| Calculation Group | `CG_` | `CG_TimeIntelligence`, `CG_Moneda` |
| Auxiliar/Oculta | `_` prefijo | `_CalendarSlice`, `_Measures` |

## Columnas

- **PascalCase** siempre: `SaldoActual`, `FechaApertura`, `TipoProducto`
- **Llaves foráneas:** sufijo `_Key` → `Cliente_Key`, `Producto_Key`
- **Llaves primarias:** `<Nombre>_Key` también, marcadas con `isKey: true`
- **Columnas técnicas/ocultas:** prefijo `_` → `_RowHash`, `_LoadDate`
- **Evitar:** espacios, acentos, caracteres especiales

## Medidas

Formato: `[Categoría] Nombre Descriptivo [Unidad opcional]`

| Categoría | Ejemplos |
|---|---|
| Ventas | `[Ventas] Total Mensual`, `[Ventas] Ticket Promedio` |
| Cartera | `[Cartera] Saldo Promedio`, `[Cartera] NPL Ratio` |
| Clientes | `[Clientes] Activos`, `[Clientes] % Retención` |
| Riesgo | `[Riesgo] Provisión Requerida`, `[Riesgo] PD Promedio` |
| KPI | `[KPI] ROE`, `[KPI] Eficiencia Operativa` |

## Display Folders

Estructura jerárquica sugerida:

```
📁 Ventas
  📁 Totales
  📁 Comparativos
  📁 Por Canal
📁 Cartera
  📁 Saldos
  📁 Calidad
  📁 Por Producto
📁 Clientes
  📁 Conteo
  📁 Segmentación
📁 Riesgo
📁 KPIs
📁 _Hidden   ← para medidas técnicas auxiliares
```

## Reportes (PBIR)

### Páginas

| Elemento | Formato | Ejemplo |
|---|---|---|
| `name` (identificador) | `NN_nombre_corto` | `01_resumen`, `02_ventas_mes` |
| `displayName` | `NN - Nombre Descriptivo` | `01 - Resumen Ejecutivo` |

### Visuales

- `displayName` descriptivo del contenido, no "Chart 1"
- Ejemplos: "Evolución Ventas por Mes", "NPL por Segmento", "Top 10 Clientes"

## Relationships

Nombres auto-generados por Power BI (GUID), pero documentar en `description`:
```
"Relación FACT_Ventas → DIM_Cliente via Cliente_Key. Active, single direction."
```

## Anti-patterns

❌ `Tabla1`, `Hoja1`, `ventas_final_v2`
❌ `Medida sin categoría`
❌ Mezclar idiomas: `[Sales] Ventas Total`
❌ Columnas con espacios en nombres técnicos

## Checklist de auditoría

Al revisar un modelo, verificá:
- [ ] Todas las tablas siguen prefijo `FACT_`/`DIM_`/`BRIDGE_`/`PARAM_`
- [ ] Columnas en PascalCase sin espacios
- [ ] Llaves con sufijo `_Key`
- [ ] Medidas con categoría `[...]` y display folder
- [ ] Columnas técnicas ocultas con prefijo `_`
- [ ] Páginas del reporte con formato `NN - Nombre`
