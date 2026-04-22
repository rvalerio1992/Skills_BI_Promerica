# powerbi-project/

📦 **Esta es la carpeta donde va el proyecto Power BI (PBIP)** que los agentes van a analizar.

## Cómo usar

1. Copiá el proyecto `.pbip` del banco a esta carpeta.
2. La estructura esperada es:

```
powerbi-project/
├── MiReporte.pbip
├── MiReporte.SemanticModel/
│   └── definition/
│       ├── model.tmdl
│       ├── database.tmdl
│       ├── relationships.tmdl
│       └── tables/
│           ├── FACT_Ventas.tmdl
│           ├── DIM_Cliente.tmdl
│           └── ...
└── MiReporte.Report/
    └── definition/
        ├── report.json
        └── pages/
```

3. Los agentes del repo (`Semantic Model Auditor`, `DAX Reviewer`, `Source Lineage Auditor`) automáticamente buscan archivos en esta carpeta.

## Git LFS (opcional)

Si tus PBIP son pesados (>50MB), configurá Git LFS:

```bash
git lfs install
git lfs track "*.pbip"
git lfs track "*.pbix"
```

## Privacidad

⚠️ **Nunca subas al repo modelos con datos sensibles de clientes.**
Los agentes trabajan sobre la **estructura** (metadatos), no sobre los datos reales.

Si el proyecto contiene particiones con datos cacheados, considerá:
- Limpiar caché antes del commit
- Añadir a `.gitignore`: `*.cache`, `*/data/`
