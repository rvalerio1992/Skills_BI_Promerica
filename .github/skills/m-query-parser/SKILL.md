---
name: m-query-parser
description: >
  Use this skill when extracting source metadata from Power Query (M) expressions
  in TMDL partitions. Provides regex patterns and Python snippets to identify
  SQL databases, CSV files, Excel, SharePoint, Synapse, Fabric Lakehouse sources.
  Activate when the user mentions Power Query, M language, source extraction,
  lineage, or the Source Lineage Auditor agent needs it.
---

# M Query Parser

Guía para extraer metadata de fuentes a partir de expresiones Power Query (M).

## Patrones comunes en M

### SQL Server / Azure SQL
```m
let
    Source = Sql.Database("ms-sqldwh-01", "STAGE_TCG"),
    dbo_TablaVentas = Source{[Schema="dbo",Item="TablaVentas"]}[Data]
in
    dbo_TablaVentas
```

**Extraer:**
- Servidor: `ms-sqldwh-01`
- Base de Datos: `STAGE_TCG`
- Esquema: `dbo`
- Tabla: `TablaVentas`

### SQL con Native Query
```m
let
    Source = Sql.Database("ms-sqldwh-01", "GAD", [Query="SELECT cod_cliente, saldo FROM dbo.TC_ACTIVAS"])
in
    Source
```

**Extraer también:**
- Query SQL completo del parámetro `Query`

### CSV
```m
let
    Source = Csv.Document(File.Contents("//ms-files-01/Datos2/archivo.csv"),[Delimiter=",", Encoding=65001])
in
    Source
```

**Extraer:**
- Tipo: `csv_document`
- Ruta: `//ms-files-01/Datos2/archivo.csv`

### Excel
```m
let
    Source = Excel.Workbook(File.Contents("C:\Reportes\datos.xlsx"), null, true),
    Hoja1_Sheet = Source{[Item="Hoja1",Kind="Sheet"]}[Data]
in
    Hoja1_Sheet
```

### Fabric Lakehouse (Direct Lake)
```m
let
    Source = Lakehouse.Contents([CreateNavigationProperties=false]){[workspaceId="..."]}[Data]{[lakehouseId="..."]}[Data],
    Tables = Source{[Id="Tables"]}[Data],
    Gold_dim_cliente = Tables{[Name="gold_dim_cliente"]}[Data]
in
    Gold_dim_cliente
```

### Azure Synapse
```m
let
    Source = Synapse.Database("ms-synapse-prod.dfs.fabric.microsoft.com", "BancoPromerica_DW"),
    Table = Source{[Schema="gold",Item="FactVentas"]}[Data]
in
    Table
```

### Tabla calculada (DAX)
Cuando la partición usa `mode: calculated` o la expresión es pura DAX:
```tmdl
partition MiTabla = calculated
    source = SUMMARIZE(FACT_Ventas, DIM_Cliente[ID])
```

## Snippet Python para extracción

```python
import re
from pathlib import Path

# Patrones
PATTERNS = {
    "sql_database": re.compile(r'Sql\.Database\s*\(\s*"([^"]+)"\s*,\s*"([^"]+)"'),
    "sql_native_query": re.compile(r'\[Query\s*=\s*"((?:[^"\\]|\\.)*)"', re.DOTALL),
    "sql_schema_table": re.compile(r'Schema\s*=\s*"([^"]+)"[^}]*?Item\s*=\s*"([^"]+)"'),
    "csv": re.compile(r'Csv\.Document\s*\(\s*File\.Contents\s*\(\s*"([^"]+)"'),
    "excel": re.compile(r'Excel\.Workbook\s*\(\s*File\.Contents\s*\(\s*"([^"]+)"'),
    "synapse": re.compile(r'Synapse\.Database\s*\(\s*"([^"]+)"\s*,\s*"([^"]+)"'),
    "lakehouse": re.compile(r'lakehouseId\s*=\s*"([^"]+)"'),
    "sharepoint": re.compile(r'SharePoint\.Files\s*\(\s*"([^"]+)"'),
    "web_api": re.compile(r'Web\.Contents\s*\(\s*"([^"]+)"'),
    "table_from_rows": re.compile(r'Table\.FromRows\s*\('),
}

def classify_source(m_expression: str) -> dict:
    """Analiza una expresión M y devuelve metadata estructurada."""
    result = {
        "fuente": "desconocido",
        "servidor": None,
        "base_datos": None,
        "esquema": None,
        "tabla_origen": None,
        "query_sql": None,
    }

    # SQL Database
    if m := PATTERNS["sql_database"].search(m_expression):
        result["fuente"] = "sql_database"
        result["servidor"] = m.group(1)
        result["base_datos"] = m.group(2)

        # ¿Tiene native query?
        if q := PATTERNS["sql_native_query"].search(m_expression):
            result["query_sql"] = q.group(1).replace('""', '"')

        # ¿Tiene schema + table?
        if st := PATTERNS["sql_schema_table"].search(m_expression):
            result["esquema"] = st.group(1)
            result["tabla_origen"] = st.group(2)
        return result

    # CSV
    if m := PATTERNS["csv"].search(m_expression):
        result["fuente"] = "csv_document"
        result["servidor"] = "Archivo Plano"
        result["base_datos"] = "Archivo Plano"
        result["esquema"] = "Archivo Plano"
        result["tabla_origen"] = m.group(1)
        return result

    # Excel
    if m := PATTERNS["excel"].search(m_expression):
        result["fuente"] = "excel_workbook"
        result["tabla_origen"] = m.group(1)
        return result

    # Synapse
    if m := PATTERNS["synapse"].search(m_expression):
        result["fuente"] = "azure_synapse"
        result["servidor"] = m.group(1)
        result["base_datos"] = m.group(2)
        return result

    # Lakehouse
    if PATTERNS["lakehouse"].search(m_expression):
        result["fuente"] = "fabric_lakehouse"
        return result

    # Table.FromRows → calculada
    if PATTERNS["table_from_rows"].search(m_expression):
        result["fuente"] = "tabla_calculada"
        return result

    return result
```

## Clasificación Medallion

```python
def classify_medallion(source_info: dict) -> str:
    servidor = (source_info.get("servidor") or "").lower()
    esquema = (source_info.get("esquema") or "").upper()
    ruta = (source_info.get("tabla_origen") or "").lower()

    # GOLD
    if any(k in servidor for k in ["gold", "-mart", "dwh-gold"]):
        return "GOLD"
    if esquema.startswith("GOLD_") or esquema.startswith("MART_"):
        return "GOLD"
    if "/gold/" in ruta:
        return "GOLD"

    # LOCAL (archivos)
    if source_info["fuente"] in ["csv_document", "excel_workbook", "sharepoint"]:
        return "LOCAL"
    if ruta.startswith("//") or ruta.startswith("\\\\") or ":\\" in ruta:
        return "LOCAL"

    # Calculada
    if source_info["fuente"] == "tabla_calculada":
        return "Calculada"

    # NONGOLD (todo lo demás SQL/Synapse/Lakehouse)
    if source_info["fuente"] in ["sql_database", "azure_synapse", "fabric_lakehouse"]:
        return "NONGOLD"

    return "Desconocido"
```

## Limitaciones

- Expresiones M con muchas transformaciones (Table.Combine, merges) pueden tener múltiples fuentes → reportar la primera y marcar `"tiene_multiples_fuentes": true`
- Parámetros de M (`#"Parámetro1"`) requieren resolver el valor del parámetro antes de clasificar
- Algunas conexiones con tokens OAuth no exponen el servidor en texto plano

## Cuándo usar esta skill

- El agente Source Lineage Auditor la invoca automáticamente
- El usuario pide entender de dónde viene una tabla específica
- Hay que extraer el SQL de una partición para optimizarlo
