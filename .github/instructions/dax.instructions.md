---
applyTo: "**/*.tmdl"
description: "Convenciones DAX para Banco Promerica"
---

# Reglas DAX — Banco Promerica

Aplicá estas reglas cuando estés leyendo o escribiendo medidas DAX en archivos TMDL.

## Reglas críticas

1. **Usá `DIVIDE()` en vez de `/`** — evita división por cero.
   ```dax
   // ❌ MAL
   Ratio = [Ingresos] / [Ventas]

   // ✅ BIEN
   Ratio = DIVIDE([Ingresos], [Ventas], 0)
   ```

2. **Declará variables con `VAR` para cálculos intermedios** — mejora performance y legibilidad.
   ```dax
   // ❌ MAL
   Margen % = ([Ventas] - [Costos]) / [Ventas]

   // ✅ BIEN
   Margen % =
   VAR _Ventas = [Ventas]
   VAR _Margen = _Ventas - [Costos]
   RETURN DIVIDE(_Margen, _Ventas, 0)
   ```

3. **Evitá `CALCULATE()` sin filtros explícitos** dentro de medidas reutilizables.

4. **No uses funciones de iteración innecesarias** (`SUMX`, `AVERAGEX`) cuando una agregación simple funciona.

## Naming de medidas

Formato: `[Categoría] Nombre_de_medida [Unidad opcional]`

Ejemplos:
- `[Ventas] Total Mensual`
- `[Cartera] Saldo Promedio`
- `[Clientes] % Activos`
- `[Riesgo] NPL Ratio`

## Format strings obligatorios

| Tipo de medida | Format string |
|---|---|
| Montos en colones | `"₡#,##0"` |
| Montos en dólares | `"$#,##0.00"` |
| Porcentajes | `"0.00%"` |
| Conteos | `"#,##0"` |
| Fechas | `"dd/mm/yyyy"` |

## Display folders

Toda medida debe tener un `displayFolder`. Estructura sugerida:

```
📁 Ventas
  📁 Totales
  📁 Comparativos
📁 Cartera
  📁 Saldos
  📁 Calidad
📁 KPIs
```

## Descripciones

Toda medida debe tener una `description` que explique:
- Qué calcula (en términos de negocio)
- A qué unidad aplica (Personas / Empresas / Privada)
- Cualquier supuesto o filtro implícito

Ejemplo:
```tmdl
measure 'Saldo Promedio Cartera' = AVERAGE('Cartera'[Saldo])
    formatString: "₡#,##0"
    displayFolder: "Cartera/Saldos"
    description: "Saldo promedio de la cartera de crédito. Aplica a Banca Personas y Banca Empresas. Excluye cuentas canceladas."
```
