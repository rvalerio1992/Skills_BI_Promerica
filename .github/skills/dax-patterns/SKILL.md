---
name: dax-patterns
description: >
  Use this skill when the user needs to write, review, or refactor DAX measures.
  Contiene patrones seguros (DIVIDE, VAR), anti-patterns conocidos, y ejemplos
  contextualizados al sector bancario (saldos, ratios, NPL, ROA, ROE).
  Activar siempre que aparezcan las palabras: DAX, medida, measure, CALCULATE,
  SUMX, ratio, KPI bancario.
---

# Patrones DAX para Banco Promerica

Catálogo de patrones DAX validados por el equipo de BI.

## 1. División segura

**Regla:** Siempre `DIVIDE()`, nunca `/`.

```dax
// ✅ Patrón estándar
Ratio Eficiencia =
VAR _Gastos = [Gastos Operativos]
VAR _Ingresos = [Ingresos Operativos]
RETURN DIVIDE(_Gastos, _Ingresos, 0)
```

## 2. Variación período anterior

```dax
Δ vs Mes Anterior =
VAR _Actual = [Saldo Total]
VAR _Anterior = CALCULATE([Saldo Total], PREVIOUSMONTH(DIM_Fecha[Fecha]))
RETURN DIVIDE(_Actual - _Anterior, _Anterior, 0)
```

## 3. YTD (Year-to-date)

```dax
Ventas YTD =
CALCULATE(
    [Ventas Total],
    DATESYTD(DIM_Fecha[Fecha])
)
```

## 4. Ratios bancarios

### NPL Ratio (Non-Performing Loans)
```dax
NPL Ratio =
VAR _Cartera_Vencida = CALCULATE([Saldo Cartera], FACT_Cartera[Estado] = "Vencida")
VAR _Cartera_Total = [Saldo Cartera]
RETURN DIVIDE(_Cartera_Vencida, _Cartera_Total, 0)
```

### ROA (Return on Assets)
```dax
ROA =
VAR _Utilidad = [Utilidad Neta]
VAR _Activos = [Activos Totales Promedio]
RETURN DIVIDE(_Utilidad, _Activos, 0)
```

## 5. Cálculo de saldo promedio

```dax
Saldo Promedio =
AVERAGEX(
    VALUES(DIM_Fecha[Fecha]),
    [Saldo Total]
)
```

## Anti-patterns comunes

### ❌ CALCULATE sin filtro explícito
```dax
// MAL
Total = CALCULATE([Ventas])

// BIEN: usar la medida directo si no agregás filtros
Total = [Ventas]
```

### ❌ Columnas calculadas en vez de medidas
Preferir medidas siempre que sea posible — se recalculan bajo demanda y ocupan menos memoria.

### ❌ FILTER dentro de CALCULATE cuando podés usar sintaxis directa
```dax
// MAL
CALCULATE([Ventas], FILTER(DIM_Producto, DIM_Producto[Categoria] = "Créditos"))

// BIEN
CALCULATE([Ventas], DIM_Producto[Categoria] = "Créditos")
```

## Cuándo usar esta skill

- Usuario pide escribir una medida DAX nueva
- Usuario pide revisar una medida existente
- Usuario menciona "performance", "optimización", "refactor" en contexto DAX
- Detectás en un TMDL un patrón problemático
