---
description: "Refactorizar código M (Power Query) — pulir naming, comentarios y formato sin cambiar comportamiento"
mode: agent
---

# Formatear código M (Power Query)

Ejecutá el agente **M Code Formatter** sobre los archivos `.tmdl` con
expresiones M. Modo DRY-RUN por defecto.

## Pasos

1. **Verificar prerrequisito:** que exista `outputs/context/<proyecto>_context.json`.
   - Si no: avisar y sugerir `/explore-model` primero.
2. **Identificar archivos a procesar:**
   - Si el usuario especificó un archivo (ej: `/format-m-code DimProducto.tmdl`),
     procesar solo ese
   - Si no, procesar todos los `.tmdl` con `partition.kind == "m"` del context
3. **Para cada archivo:**
   - Leer el `.tmdl` original
   - Extraer la expresión M
   - Aplicar el flujo del `m-code-formatter.agent.md`:
     - Identificar steps con nombres default
     - Verificar referencias entre steps (renombrado atómico)
     - Asignar nivel de confianza a cada cambio
     - Respetar las reglas no negociables
   - Generar 3 outputs en `outputs/m-formatted/<archivo>/`:
     - `<archivo>.m` — código M refactorizado
     - `<archivo>.diff` — diff vs original
     - `<archivo>.md` — reporte completo (las 7 secciones)
4. **Generar `outputs/m-formatted/_summary.md`** con vista global.

## Output en chat

```
✅ M Code Formatter completado (DRY-RUN)

RPAUT084:
  Archivos procesados: 12
  Total cambios propuestos: 47
    🟢 Alta confianza:   28 (aplicar con seguridad razonable)
    🟡 Media confianza:  15 (revisar antes de aplicar)
    🟠 Baja confianza:    4 (solo sugerir — NO aplicar)

📁 outputs/m-formatted/
   • _summary.md
   • DimProducto.tmdl/ (.m, .diff, .md)
   • FctProductos.tmdl/ (.m, .diff, .md)
   • ...

🛡️ Los archivos originales siguen INTACTOS.

Para aplicar: pedime "aplicá los cambios de m-code-formatter".
Antes de aplicar revisá:
  1. Los .diff archivo por archivo
  2. Los [RISK] y [TODO] del _summary.md
  3. El checklist de validación post-refactor
```

## Modo APLICAR

Solo si el usuario pide explícitamente *"aplicá los cambios"*:

1. Crear backup en `powerbi-project/.backup-YYYY-MM-DD-HHMM/`
2. Aplicar cambios solo de confianza 🟢 alta y 🟡 media
3. Los 🟠 baja confianza quedan como `[TODO]` en `outputs/m-formatted/`
4. Confirmar al usuario qué se aplicó y qué quedó pendiente

## Modo

**Capa 3 - Enriquecimiento.** DRY-RUN por defecto.
Aplica cambios solo con confirmación explícita.
