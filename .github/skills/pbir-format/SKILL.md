---
name: pbir-format
description: >
  Use this skill when working with PBIR files (Power BI Enhanced Report Format):
  report.json, page.json, visual.json, themes, filters. Use whenever the user
  wants to read, audit, or modify report pages in a PBIP project. Cover JSON
  schemas, visual containers, filter syntax, and theme structure.
---

# PBIR Format

Guía para trabajar con archivos JSON del formato PBIR (Power BI Enhanced Report).

## Estructura

```
MiReporte.Report/
└── definition/
    ├── report.json                    ← Config global del reporte
    ├── reportExtensions.json          ← Thin measures (medidas del reporte)
    ├── pages/
    │   └── pages.json                 ← Lista de páginas
    │   └── <pageId>/
    │       ├── page.json              ← Config de página
    │       └── visuals/
    │           └── <visualId>/
    │               └── visual.json    ← Definición de cada visual
    └── themes/
        └── baseTheme/
            └── theme.json
```

## report.json

Contiene:
- `name` del reporte
- `settings` globales
- `filterConfig` a nivel reporte

## page.json

```json
{
  "name": "01_Resumen",
  "displayName": "Resumen Ejecutivo",
  "displayOption": "FitToPage",
  "height": 720,
  "width": 1280,
  "visualInteractions": []
}
```

## visual.json

Cada visual tiene:
- `name` (GUID)
- `visualType` (ej: `barChart`, `tableEx`, `card`)
- `position` (x, y, z, width, height)
- `query` (campos y medidas usados)
- `visualContainerObjects` (formato)
- `filterConfig`

## Reglas al modificar PBIR

1. **Validar JSON siempre** antes de guardar
2. **Nunca cambiar `lineageTag`** (son GUIDs gestionados)
3. **Nombres de páginas:** `NN_NombreEnMinusculas` (name) + `NN - Nombre Descriptivo` (displayName)
4. **Visuales:** dar `displayName` descriptivos, no "Chart 1"

## Thin Measures (Report-level measures)

Medidas definidas en el reporte (no en el modelo). Se guardan en `reportExtensions.json`:

```json
{
  "entities": [
    {
      "name": "Ventas Mes",
      "extends": "FACT_Ventas",
      "measures": [
        {
          "name": "Ventas YTD Report",
          "expression": "CALCULATE([Ventas Total], DATESYTD(DIM_Fecha[Fecha]))",
          "formatInformation": {
            "formatString": "₡#,##0"
          }
        }
      ]
    }
  ]
}
```

Usá thin measures cuando la medida es **específica del reporte** y no aporta al modelo semántico general.

## Límites actuales

- El MCP de Power BI NO modifica páginas de reportes
- Edición manual de PBIR requiere entender bien el schema
- No hay validador oficial gratuito del schema (workaround: abrir en Power BI Desktop)

## Workflow recomendado

1. Diseñar visualmente en Power BI Desktop
2. Guardar como PBIP
3. Editar detalles finos en VS Code (tema, thin measures, posicionamiento)
4. Volver a Desktop para validar visualmente
5. Commit
