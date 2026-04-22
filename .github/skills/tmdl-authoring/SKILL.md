---
name: tmdl-authoring
description: >
  Use this skill whenever the user needs to author, edit, or understand
  TMDL (Tabular Model Definition Language) files in a Power BI PBIP project.
  Covers table definitions, column properties, relationships, hierarchies,
  roles, and calculation groups. Activate when user mentions TMDL,
  .tmdl files, model.tmdl, or Power BI Project structure.
---

# TMDL Authoring

Guía para escribir y editar archivos TMDL en proyectos PBIP.

## Estructura de un proyecto PBIP

```
MiReporte.pbip
MiReporte.SemanticModel/
├── definition/
│   ├── model.tmdl              ← Configuración global
│   ├── database.tmdl           ← Metadata de base
│   ├── cultures/               ← Traducciones
│   ├── tables/                 ← Una carpeta por tabla
│   │   ├── FACT_Ventas.tmdl
│   │   └── DIM_Cliente.tmdl
│   └── relationships.tmdl      ← Relaciones del modelo
MiReporte.Report/
└── definition/                 ← PBIR (reportes)
```

## Anatomía de una tabla

```tmdl
table FACT_Ventas
    lineageTag: 12345678-...     ← NO tocar
    sourceLineageTag: ...

    /// Descripción de la tabla (formato triple slash)

    column Fecha
        dataType: dateTime
        formatString: dd/mm/yyyy
        sourceColumn: Fecha

    column Monto
        dataType: double
        formatString: "₡#,##0"
        summarizeBy: sum
        sourceColumn: Monto

    column Cliente_Key
        dataType: int64
        isHidden
        summarizeBy: none
        sourceColumn: Cliente_Key

    measure 'Ventas Total' = SUM('FACT_Ventas'[Monto])
        formatString: "₡#,##0"
        displayFolder: Ventas/Totales
        description: "Suma total de ventas en colones."

    partition FACT_Ventas = m
        mode: directLake
        source = ...
```

## Propiedades clave por tipo

### Columnas numéricas
- `dataType: double` o `int64`
- `formatString`: obligatorio
- `summarizeBy: sum` para hechos; `none` para llaves
- `isHidden` si es técnica

### Columnas de texto
- `dataType: string`
- `sortByColumn` si el orden lógico difiere del alfabético

### Columnas de fecha
- `dataType: dateTime`
- `formatString: dd/mm/yyyy` (estándar Promerica)

## Relaciones

Se definen en `relationships.tmdl`:

```tmdl
relationship abc-123
    fromColumn: FACT_Ventas.Cliente_Key
    toColumn: DIM_Cliente.Cliente_Key
    crossFilteringBehavior: singleDirection  ← preferido
```

## Calculation Groups

Útiles para time intelligence reutilizable:

```tmdl
table TimeIntelligence
    calculationGroup

    column Periodo = SELECTEDMEASURE()

    calculationItem 'Mes Actual' = SELECTEDMEASURE()
    calculationItem 'Mes Anterior' = CALCULATE(SELECTEDMEASURE(), PREVIOUSMONTH(DIM_Fecha[Fecha]))
    calculationItem 'YTD' = CALCULATE(SELECTEDMEASURE(), DATESYTD(DIM_Fecha[Fecha]))
```

## Errores comunes

| Error | Causa | Fix |
|---|---|---|
| `The model could not be loaded` | `lineageTag` inválido o duplicado | Dejar que Power BI los genere |
| `Relationship is ambiguous` | Múltiples caminos entre tablas | Desactivar relaciones redundantes |
| `Column not found` | Referencia en DAX a columna inexistente | Verificar naming exacto |

## Workflow recomendado

1. **Abrir el .pbip en Power BI Desktop** al menos una vez antes de editar TMDL manualmente (genera los `lineageTag`)
2. Editar TMDL en VS Code con la extensión TMDL
3. Guardar y validar sintácticamente
4. Volver a Power BI Desktop → Refresh → verificar que el modelo cargue
5. Commit al repo
