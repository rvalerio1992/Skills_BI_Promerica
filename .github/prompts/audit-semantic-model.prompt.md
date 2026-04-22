---
description: "Auditar el modelo semántico TMDL completo y generar reporte priorizado"
mode: agent
---

# Auditar modelo semántico

Realizá una auditoría completa del modelo semántico del proyecto PBIP actual.

## Pasos

1. Identificá todos los archivos `.tmdl` en `Modelo.SemanticModel/definition/tables/`
2. Para cada tabla, revisá:
   - Naming conventions (`FACT_`, `DIM_`, etc.)
   - `description` presente y útil
   - Columnas con `displayFolder`
   - Medidas con `formatString` correcto
   - Medidas con `description` explicando lógica de negocio
3. Revisá el archivo `model.tmdl` para:
   - Relaciones bidireccionales (señalar como riesgo)
   - `DIM_Fecha` marcada como tabla de fechas
4. Revisá cada medida DAX según `.github/instructions/dax.instructions.md`

## Output esperado

Generá `outputs/audit/semantic_model_audit.md` con:

```markdown
# Auditoría del Modelo Semántico
Fecha: YYYY-MM-DD

## Resumen
- Total tablas: X
- Total medidas: Y
- Hallazgos críticos: Z
- Hallazgos de mejora: W
- Observaciones: V

## Hallazgos críticos 🔴
| # | Ubicación | Hallazgo | Recomendación |
|---|-----------|----------|---------------|
| 1 | DIM_Cliente | Sin lineageTag | ... |

## Mejoras sugeridas ⚠️
...

## Observaciones ℹ️
...
```

También generá `outputs/audit/semantic_model_findings.json` con el mismo contenido en formato estructurado.

## Modo por defecto

**Read-only.** No modificar ningún archivo. Solo reportar.
