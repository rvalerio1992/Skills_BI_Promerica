---
description: "Revisar un archivo TMDL específico y sugerir mejoras"
mode: agent
argument-hint: "Ruta del archivo .tmdl a revisar"
---

# Revisar archivo TMDL

Revisá el archivo `.tmdl` proporcionado contra los estándares de Banco Promerica.

## Checklist de revisión

### Estructura
- [ ] Nombre de tabla sigue convención (`FACT_`, `DIM_`, `BRIDGE_`, `PARAM_`)
- [ ] `description` presente y útil
- [ ] Todas las columnas tienen `dataType` correcto
- [ ] Clave primaria marcada con `isKey: true` (si aplica)
- [ ] Columnas técnicas ocultas con `isHidden: true`

### Medidas
- [ ] Cada medida tiene `formatString`
- [ ] Cada medida tiene `displayFolder`
- [ ] Cada medida tiene `description` explicando la lógica
- [ ] DAX usa `DIVIDE()` en vez de `/`
- [ ] DAX usa `VAR` para cálculos intermedios
- [ ] No hay `CALCULATE()` sin filtros explícitos

### Performance
- [ ] No hay iteradores innecesarios (`SUMX`, `AVERAGEX`)
- [ ] No hay referencias circulares
- [ ] Columnas calculadas solo cuando es estrictamente necesario (preferir medidas)

## Output

Generá una tabla markdown con los hallazgos:

| # | Severidad | Línea | Hallazgo | Recomendación | Código sugerido |
|---|-----------|-------|----------|---------------|-----------------|
| 1 | 🔴 Crítico | 42 | División sin `DIVIDE()` | ... | `DIVIDE(...)` |

Al final, un **resumen ejecutivo** con conteo por severidad.

## Modo

Read-only. Solo reportar. Si el usuario pide aplicar fixes, generar un patch file en `outputs/audit/refactor_<filename>.patch`.
