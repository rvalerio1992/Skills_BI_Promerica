---
description: "Generar una medida DAX siguiendo los estándares de Banco Promerica"
mode: agent
argument-hint: "Nombre de la medida y qué debe calcular"
---

# Generar medida DAX

Generá una medida DAX completa siguiendo las convenciones del Banco Promerica.

## Preguntas obligatorias antes de generar

Antes de escribir la medida, preguntá al usuario:

1. **Nombre de la medida** (seguir formato `[Categoría] Nombre`)
2. **Qué calcula en términos de negocio** (1-2 líneas)
3. **Tabla donde va a vivir la medida**
4. **Columnas/medidas que usa**
5. **Unidad de negocio aplicable** (Personas / Empresas / Privada / Todas)
6. **Tipo de formato esperado** (monto CRC, USD, %, conteo)

## Output

Generá el bloque TMDL completo:

```tmdl
measure '[Categoría] Nombre_medida' =
    VAR _Variable1 = ...
    VAR _Variable2 = ...
    RETURN
        DIVIDE(_Variable1, _Variable2, 0)
    formatString: "..."
    displayFolder: "Categoria/Subcategoria"
    description: "..."
```

## Reglas a aplicar

- Usar `DIVIDE()` en lugar de `/`
- Declarar variables con `VAR` para cálculos intermedios
- Incluir `formatString`, `displayFolder` y `description` obligatoriamente
- Description debe mencionar la unidad de negocio aplicable

## Lo que NO hacés

- No modificar el archivo `.tmdl` directamente en el primer paso
- Primero mostrar la medida generada y pedir aprobación
- Luego, si se aprueba, sugerir dónde insertarla
