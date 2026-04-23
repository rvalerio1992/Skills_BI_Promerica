---
name: tmdl-parser
description: >
  Use this skill when parsing TMDL files (tables, columns, measures, relationships).
  Contains regex patterns, edge cases, and Python/bash snippets to reliably extract
  structure from Power BI Project semantic models. Invocar cuando el agente
  model-explorer necesita parsear TMDL, o cualquier agente que necesite
  información estructural de una tabla .tmdl específica.
---

# TMDL Parser

Guía para extraer estructura de archivos TMDL de forma confiable.

## Anatomía de un archivo TMDL

```tmdl
table FctProductos                                  ← table_name
	lineageTag: abc-123
	/// Descripción (opcional, una o más líneas)

	column VALOR                                    ← column_name
		dataType: double
		formatString: "#,##0.00"
		summarizeBy: sum
		sourceColumn: VALOR

		annotation SummarizationSetBy = Automatic

	measure 'Total Ventas' = SUM(FctProductos[VALOR])  ← measure_name + dax
		formatString: "$#,##0"
		displayFolder: Ventas/Totales
		/// Suma total de ventas
		lineageTag: xyz-789

	partition FctProductos = m                       ← partition_name
		mode: import
		source =
			let
			    Source = Sql.Database(...)
			in
			    Source
```

## Patrones regex clave

### Nombre de tabla
```regex
^table\s+(?:'([^']+)'|(\S+))
```
Group 1 = nombre entre comillas (con espacios), Group 2 = nombre sin comillas.

### Columnas
```regex
^\s*column\s+([^\s=\n]+)(?:\s*=|\s*\n)
```
El `=` indica columna calculada (DAX), sin `=` indica columna de tabla origen.

### Medidas
```regex
^\s*measure\s+(?:'([^']+)'|(\S+?))\s*=\s*(.*?)(?=\n\s*(?:formatString:|displayFolder:|lineageTag:|annotation\s|///|measure\s|column\s|partition\s|$))
```

Capturar DAX hasta la próxima propiedad o siguiente bloque.

### Bloques multi-línea con backticks
```tmdl
measure 'Complejo' = ```
		VAR _x = ...
		RETURN ...
		```
```

El DAX viene entre triple-backticks. Remover los backticks externos al parsear.

### formatString
```regex
^\s*formatString:\s*(.+?)$
```
El valor puede tener comillas o no. Preservar exactamente como está.

### displayFolder
```regex
^\s*displayFolder:\s*(.+?)$
```
Puede contener `/` para jerarquía: `Ventas/Totales`.

### Description (triple slash)
```regex
^(\s*)///\s*(.+?)$
```
Una medida puede tener varias líneas `///` consecutivas antes de `measure`.

### Partitions
```regex
^\s*partition\s+(\S+)\s*=\s*(\w+)
```
Group 2 = `m` o `calculated`.

Para capturar el `source =`:
```regex
^\s*source\s*=\s*(.*?)(?=\n\s*annotation\s+PBI_|\Z)
```

## Extracción de dependencias DAX

Dado un string de DAX, extraer:

### Columnas referenciadas
```regex
(?:'([^']+)'|([A-Z][A-Za-z_]+))\[([^\]]+)\]
```
Matches: `'FctProductos'[VALOR]`, `DimCliente[NOMBRE]`

### Medidas referenciadas
```regex
(?<![\]A-Za-z_])\[([A-Za-z_][^\]]*)\](?![A-Za-z_])
```
Matches: `[Valor Meta]`, pero no `[Column]` dentro de `Table[Column]`.

**Truco:** primero capturar todas las referencias `Tabla[X]`, luego ver qué `[X]` sueltos quedan → esos son medidas.

## Clasificación de tablas por naming

| Prefijo / Patrón | Tipo |
|---|---|
| `FACT_*`, `FCT*`, `Fct*` | hechos |
| `DIM_*`, `DIM*`, `Dim*` | dimensión |
| `LocalDateTable_*`, `DateTableTemplate_*` | dimensión (auto-generada) |
| `BRIDGE_*` | puente |
| `PARAM_*`, `Parámetros`, `Parametros` | parámetros |
| `CG_*` | calculation group |
| `_*` (empieza con underscore) | auxiliar/oculta |
| Contiene `Medida`, `Métrica`, `⚙` | tabla de medidas |
| Sin match | desconocido |

## Detección de isHidden

Una columna/medida está oculta si:
- Tiene línea `isHidden` (sola o con valor `true`)
- Tiene `changedProperty = IsHidden` (forma alternativa común)

```regex
^\s*(?:isHidden|changedProperty\s*=\s*IsHidden)
```

## Patrones M en particiones

Ver skill [`m-query-parser`](../m-query-parser/SKILL.md) para parseo detallado de:
- `Sql.Database(...)`, `Synapse.Database(...)`
- `Value.NativeQuery(...)` y parámetro `[Query="..."]`
- `Csv.Document`, `Excel.Workbook`
- `Lakehouse.Contents`

## Limpieza de SQL extraído de M

El SQL en M viene con caracteres especiales que hay que limpiar:

```python
def clean_sql_from_m(sql: str) -> str:
    # #(lf) → newline
    sql = sql.replace('#(lf)', '\n')
    # #(tab) → tab
    sql = sql.replace('#(tab)', '\t')
    # "" → " (escaping M)
    sql = sql.replace('""', '"')
    # Collapse múltiples espacios
    sql = re.sub(r' +', ' ', sql)
    return sql.strip()
```

## Edge cases a tener en cuenta

### 1. Nombres con caracteres especiales
```tmdl
table '⚙ Medidas'
measure 'Q Clientes' = ...
measure 'Título Medida' = ...
```
Siempre manejar comillas simples Y Unicode (⚙, á, ú).

### 2. Medidas sin DAX visible (shell measures)
```tmdl
measure Medida
	lineageTag: abc
	annotation XYZ = True
```
Son placeholders. Marcar `dax: null` y `is_shell: true`.

### 3. Columnas con DAX multi-línea
```tmdl
column NIVEL_1 = ```
		VAR _cod = ...
		RETURN SWITCH(...)
		```
```
Capturar hasta el cierre de backticks.

### 4. Tablas auto-generadas por Auto Date/Time
`LocalDateTable_<guid>` y `DateTableTemplate_<guid>` siempre tienen:
- Prefijo conocido
- Son calculadas (partition mode: calculated)
- Su DAX es `Calendar(...)` o similar

Detectarlas y marcarlas con flag `is_auto_generated: true`.

### 5. Particiones con Table.FromRows (datos hardcodeados)
```tmdl
partition Parametros = m
	source = Table.FromRows(Json.Document(Binary.Decompress(...))
```
Son tablas con datos estáticos embebidos. Clasificar como `tabla_calculada`.

## Snippet bash para contar rápidamente

```bash
# Cantidad de tablas
find . -name "*.tmdl" -path "*/tables/*" | wc -l

# Cantidad de medidas
grep -r "^[[:space:]]*measure " tables/ | wc -l

# Cantidad de relaciones
grep -c "^relationship " relationships.tmdl

# Medidas sin formatString (rápido)
awk '/^[[:space:]]*measure / {measure=$0; has_fs=0} /formatString:/ {has_fs=1} /^[[:space:]]*(column|partition|measure )/ && measure && !has_fs {print measure; measure=""}' tables/*.tmdl
```

## Cuándo usar esta skill

- El `model-explorer` la invoca al arrancar
- Cualquier agente que necesite parsear un archivo `.tmdl` específico
- Cuando el usuario pregunta "qué tiene tal tabla" o "cuántas medidas hay"
