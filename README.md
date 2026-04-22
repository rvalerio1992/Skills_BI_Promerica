# Skills_BI_Promerica

Customizations de VS Code para el equipo de **Business Intelligence** del Banco Promerica.
Enfocado en **PBIP + TMDL + DAX + Microsoft Fabric**.

## ¿Qué hay acá?

Este repo contiene:
- **Instructions** — reglas que los agentes de IA siguen automáticamente
- **Prompts** — workflows one-shot que invocás con `/`
- **Agents** — roles especializados (auditor, reviewer)
- **Skills** — capacidades reutilizables
- **VS Code config** — settings y extensiones recomendadas

---

## 🚀 Instalación rápida

```bash
git clone https://github.com/rvalerio1992/Skills_BI_Promerica.git
code Skills_BI_Promerica
```

Al abrir, VS Code preguntará si instalar las extensiones recomendadas → **Sí**.

Las skills quedan disponibles automáticamente en el chat de Copilot.

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
│   ├── generate-measure.prompt.md           ← /generate-measure
│   └── review-tmdl.prompt.md                ← /review-tmdl
├── agents/
│   ├── semantic-model-auditor.agent.md      ← Auditor (read-only)
│   └── dax-reviewer.agent.md                ← Reviewer (read-write + confirm)
└── skills/
    ├── dax-patterns/                        ← Patrones DAX seguros
    ├── tmdl-authoring/                      ← Autoría de TMDL
    ├── pbir-format/                         ← Esquema PBIR
    └── naming-conventions/                  ← Estándares institucionales

.vscode/
├── settings.json                            ← Config VS Code
└── extensions.json                          ← Extensiones recomendadas

README.md
```

---

## 🎯 Casos de uso principales

### Auditar un modelo semántico

1. Abrí el chat de Copilot (`Ctrl+Alt+I`)
2. Seleccioná el agente **Semantic Model Auditor**
3. Escribí: *"Auditá el modelo en `Ventas.SemanticModel/`"*

El agente genera `outputs/audit/semantic_model_audit.md` con hallazgos priorizados.

### Generar una medida DAX

1. En el chat, escribí `/generate-measure`
2. El prompt te preguntará nombre, categoría, contexto de negocio, etc.
3. Recibís el bloque TMDL completo listo para pegar

### Refactor de medidas con problemas

1. Seleccioná el agente **DAX Reviewer**
2. Pasale el archivo TMDL con problemas
3. Va a proponer cada cambio y esperar tu confirmación antes de aplicarlo

---

## 🗺️ Roadmap

### ✅ Fase 1 — MVP (actual)
- 2 agentes: auditor + reviewer
- 3 prompts operativos
- 4 skills core
- 3 archivos de instructions

### 🔜 Fase 2 — Expansión (próximos 3 meses)
- Agente **Source Lineage Auditor** — audita fuentes (SQL, Power Query)
- Agente **PBIR Report Orchestrator** — batch edits sobre reportes
- Skill **bpa-rules** — Best Practice Analyzer rules
- Skill **fabric-git-sync** — patrones para sync con Fabric Workspace

### 🔮 Fase 3 — Avanzado (2026)
- Agente **Visual Recommender** — propone visualizaciones y genera Deneb/Vega-Lite specs
- Integración MCP con Power BI Semantic Model
- Hooks para auditoría automática pre-commit
- Custom Visual scaffolding (.pbiviz)

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
- [data-goblin/power-bi-agentic-development](https://github.com/data-goblin/power-bi-agentic-development) — fuente de inspiración para skills
- [VS Code Agent Skills docs](https://code.visualstudio.com/docs/copilot/customization/agent-skills)

---

## 🤝 Cómo contribuir

1. Crear una rama: `feature/nombre-skill`
2. Agregar la skill en `.github/skills/<nombre>/SKILL.md`
3. Probar localmente (el `/skill-name` debe aparecer en el chat)
4. Pull request con descripción del caso de uso

---

**Equipo de Data Science & BI — Banco Promerica**
