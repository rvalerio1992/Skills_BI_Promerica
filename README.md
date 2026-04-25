# AI-Agents-BI

> Sistema de agentes de IA para **Business Intelligence** — Banco Promerica CR
> Enfocado en **PBIP + TMDL + DAX + Microsoft Fabric**.

Parte del ecosistema de agentes de IA de Banco Promerica, siguiendo el patrón
arquitectónico de **4 capas** (Observación → Auditoría → Enriquecimiento → Producción).

## 🔗 Ecosistema

| Repo | Dominio | Estado |
|---|---|---|
| **AI-Agents-BI** (este) | Power BI y reportes | 🟢 Fase 2 en curso |
| [AI-Agents-DataScience](https://github.com/rvalerio1992/AI-Agents-DataScience) | Ciencia de datos | 🟡 MVP estructural |
| [AI-Agents-DataEngineering](https://github.com/rvalerio1992/AI-Agents-DataEngineering) | Pipelines y warehouse | 🟡 MVP estructural |
| [AI-Agents-CopilotStudio](https://github.com/rvalerio1992/AI-Agents-CopilotStudio) | Copilots conversacionales | 🟡 MVP estructural |

Los 4 repos comparten:
- Misma arquitectura de 4 capas
- Mismo setup de VS Code + Copilot
- Mismos guardrails de seguridad
- Mismas convenciones de contribución

Este repo (`AI-Agents-BI`) es el **más maduro** y sirve como referencia
arquitectónica para los demás.

## ¿Qué hay acá?

Este repo contiene:
- **Instructions** — reglas que los agentes de IA siguen automáticamente
- **Prompts** — workflows one-shot que invocás con `/`
- **Agents** — roles especializados (auditor, reviewer, lineage)
- **Skills** — capacidades reutilizables
- **powerbi-project/** — carpeta donde colocás tu `.pbip` a analizar
- **outputs/** — reportes generados por los agentes

---

## 🚀 Instalación rápida

```bash
git clone https://github.com/rvalerio1992/AI-Agents-BI.git
cd AI-Agents-BI

# Copiá tu proyecto Power BI aquí:
cp -r /ruta/a/MiReporte.pbip powerbi-project/
cp -r /ruta/a/MiReporte.SemanticModel/ powerbi-project/
cp -r /ruta/a/MiReporte.Report/ powerbi-project/

code .
```

Al abrir en VS Code, instalá las extensiones recomendadas cuando te pregunte.
Las skills y agents quedan disponibles automáticamente en el chat de Copilot.

---

## 📂 Estructura

```
.github/
├── copilot-instructions.md                  ← Convenciones BI globales
├── instructions/
│   ├── dax.instructions.md                  ← Reglas DAX (applyTo: *.tmdl)
│   ├── tmdl.instructions.md                 ← Estructura del modelo
│   └── pbir.instructions.md                 ← Reglas PBIR (reportes)
├── prompts/
│   ├── audit-semantic-model.prompt.md       ← /audit-semantic-model
│   ├── audit-sources.prompt.md              ← /audit-sources
│   ├── generate-measure.prompt.md           ← /generate-measure
│   └── review-tmdl.prompt.md                ← /review-tmdl
├── agents/
│   ├── semantic-model-auditor.agent.md      ← Auditor modelo (read-only)
│   ├── source-lineage-auditor.agent.md      ← Auditor fuentes (read-only) ⭐ NEW
│   └── dax-reviewer.agent.md                ← Reviewer DAX (read-write)
└── skills/
    ├── dax-patterns/                        ← Patrones DAX seguros
    ├── tmdl-authoring/                      ← Autoría de TMDL
    ├── pbir-format/                         ← Esquema PBIR
    ├── naming-conventions/                  ← Estándares institucionales
    └── m-query-parser/                      ← Parser de Power Query ⭐ NEW

powerbi-project/                             ← ⭐ Acá va el PBIP a analizar
└── README.md

outputs/                                     ← Artefactos generados por agentes
├── context/        ← Context base (model-explorer)        ⭐ Fase 1
├── audit/          ← Findings de auditoría
├── ccu/            ← Inventario de fuentes (lineage)
├── documented/    ← Propuestas de documentación (DRY-RUN)
└── recommendations/ ← Propuestas de visualizaciones (Fase 4)

.vscode/
├── settings.json
└── extensions.json

README.md
```

---

## 🎯 Casos de uso principales

### 1. Auditar fuentes de datos (lineage completo) ⭐

```
Usuario en chat: /audit-sources
```

Genera un inventario CCU con estas columnas:

| Nombre Reporte | Nombre Tabla | Tipo Tabla | Modo | Fuente | Clasificación | Servidor | Base de Datos | Esquema | Tabla | Query SQL |
|---|---|---|---|---|---|---|---|---|---|---|
| HERAUT65 | 03 PA_MONEDA | desconocido | Import | sql_database | 🟠 NONGOLD | ms-sqldwh-01 | STAGE_TCG | PA | MONEDA | SELECT ... |
| HERAUT65 | 01 DC_SM_EVENTOS | desconocido | Import | csv_document | 🔵 LOCAL | Archivo Plano | Archivo Plano | Archivo Plano | //ms-files-01/... | — |

Outputs:
- `outputs/ccu/source_inventory.csv`
- `outputs/ccu/source_inventory.json`
- `outputs/ccu/source_inventory.md`

### 2. Auditar modelo semántico

```
Chat → agente "Semantic Model Auditor" → "Auditá el modelo"
```

Revisa DAX, naming, relaciones, format strings.

### 3. Generar medida DAX estándar

```
Usuario en chat: /generate-measure
```

El prompt te guía con preguntas y entrega el bloque TMDL listo.

### 4. Refactor de medidas DAX con confirmación

```
Chat → agente "DAX Reviewer" → pasale un TMDL con problemas
```

Propone cada cambio → espera tu OK → aplica solo lo aprobado.

---

## 🏷️ Clasificación Medallion

El Source Lineage Auditor clasifica cada tabla según su origen:

| Clasificación | Criterio |
|---|---|
| 🟡 **GOLD** | Servidor/esquema con `gold`, `mart`, `dwh-gold` o ruta `/Gold/` |
| 🟠 **NONGOLD** | SQL/Synapse/Lakehouse que NO es Gold (incluye Bronze/Silver/Stage) |
| 🔵 **LOCAL** | CSV, Excel, SharePoint, archivos de red |
| ⚪ **Calculada** | Tablas creadas con `Table.FromRows` o expresiones DAX puras |
| ❓ **Desconocido** | No pudo clasificarse automáticamente |

---

## 🗺️ Roadmap

📐 **Arquitectura completa:** ver [ARCHITECTURE.md](./ARCHITECTURE.md) — blueprint de los 12 agentes en 4 capas.

### ✅ Fase 0 — MVP original
- `source-lineage-auditor` · `semantic-model-auditor` · `model-documenter` · `dax-reviewer`

### ✅ Fase 1 — Fundación (completa)
- `model-explorer` (Capa 1)
- Agentes Fase 0 refactorizados para consumir `model_context.json`
- Scoring dual (estructural + documentación)
- Skill `tmdl-parser` con patrones regex de referencia

### ✅ Fase 2 — Auditoría extendida (completa)
- ✅ `m-code-auditor` — auditor de Power Query
- ✅ `sql-code-auditor` — auditor de SQL embebido
- ✅ `pbir-report-auditor` — auditor del reporte (páginas, visuales, bookmarks)

### 🔜 Fase 3 — Enriquecimiento
- `m-code-formatter`, `sql-code-formatter`

### 🚀 Fase 4 — Producción
- `measure-generator`, `custom-visual-builder`, `multi-report-aggregator`

---

## 🛡️ Guardrails

Todos los agentes operan bajo estas reglas:
1. **No modifican archivos** sin confirmación explícita
2. **Generan diffs** antes de aplicar cambios
3. **No hacen commits automáticos**
4. **No inventan metadata** — si no existe en el modelo, no lo completan
5. **Output estructurado** — markdown + JSON para trazabilidad

---

## 🧰 Herramientas recomendadas

| Herramienta | Uso |
|---|---|
| **VS Code** | Editor principal |
| **Power BI Desktop** | Validación visual del modelo |
| **Tabular Editor 3** | Autoría avanzada del modelo semántico |
| **GerhardBrueckl.powerbi-vscode** | Gestión del tenant desde VS Code |
| **Fabric Git Integration** | Sync bidireccional con Fabric Workspace |

---

## 📚 Referencias

- [Power BI Project (PBIP) format](https://learn.microsoft.com/power-bi/developer/projects/projects-overview)
- [TMDL documentation](https://learn.microsoft.com/analysis-services/tmdl/tmdl-overview)
- [DAX Patterns by SQLBI](https://www.daxpatterns.com/)
- [data-goblin/power-bi-agentic-development](https://github.com/data-goblin/power-bi-agentic-development)
- [VS Code Agent Skills docs](https://code.visualstudio.com/docs/copilot/customization/agent-skills)

---

**Equipo de Data Science & BI — Banco Promerica**
