---
applyTo: "**/definition/**/*.json"
description: "Reglas para archivos PBIR (Power BI Enhanced Report Format)"
---

# Reglas PBIR — Archivos JSON del reporte

Los archivos PBIR (`report.json`, `page.json`, `visual.json`) definen la estructura del reporte.

## Reglas críticas

1. **Nunca rompas el esquema.** Validá contra el schema oficial antes de guardar.
2. **No modifiques `lineageTag`** — lo gestiona Power BI automáticamente.
3. **Nombres de páginas** siguiendo patrón: `NN - Nombre Descriptivo` (ej: `01 - Resumen Ejecutivo`).
4. **Los `displayName` de visuales deben ser descriptivos**, no "Chart 1".

## Antes de modificar un archivo PBIR

Siempre:
1. Leer el archivo completo
2. Entender el contexto (qué visuales dependen de qué medidas)
3. Generar un diff propuesto
4. **Pedir confirmación al usuario antes de aplicar**

## Estructura típica

```
Modelo.Report/
└── definition/
    ├── report.json          ← Configuración global del reporte
    ├── pages/
    │   ├── ReportSection1/
    │   │   ├── page.json    ← Config de página
    │   │   └── visuals/
    │   │       ├── 001_visual.json
    │   │       └── 002_visual.json
```

## Límites del MCP de Power BI

⚠️ El MCP de Power BI **no modifica páginas de reportes**. Solo gestiona el modelo semántico.

Para cambios en reportes:
- Editar manualmente los `.json` de PBIR, o
- Usar la extensión de Power BI Studio para VS Code
