---
applyTo: "**/*.tmdl"
description: "Convenciones de estructura TMDL"
---

# Reglas TMDL — Estructura del modelo

## Naming de objetos

| Objeto | Convención | Ejemplo |
|---|---|---|
| Tabla Hecho | `FACT_<Tema>` | `FACT_Ventas`, `FACT_Transacciones` |
| Tabla Dimensión | `DIM_<Entidad>` | `DIM_Cliente`, `DIM_Producto`, `DIM_Fecha` |
| Tabla Puente | `BRIDGE_<Origen>_<Destino>` | `BRIDGE_Cliente_Producto` |
| Tabla de Parámetros | `PARAM_<Nombre>` | `PARAM_Moneda` |
| Columnas | PascalCase | `SaldoActual`, `FechaApertura` |
| Claves | sufijo `_Key` | `Cliente_Key`, `Producto_Key` |

## Estructura obligatoria

Cada tabla debe tener:
- `name` claro siguiendo convención
- `description` explicando el propósito
- Al menos una columna marcada como `isKey: true` (si es dimensión)
- `lineageTag` único (no manipular, lo genera Power BI)

## Relaciones

- **Una sola dirección** (evitar bidireccionales salvo que sea necesario)
- **Single** cardinalidad por defecto
- Documentar relaciones many-to-many en `description` del modelo

## Tabla DIM_Fecha

Debe estar marcada como tabla de fechas:
```tmdl
table DIM_Fecha
    dataCategory: Time
    ...
```

## Jerarquías recomendadas

- **DIM_Fecha**: Año → Trimestre → Mes → Día
- **DIM_Cliente**: Segmento → Tipo → Cliente
- **DIM_Producto**: Categoría → Subcategoría → Producto
