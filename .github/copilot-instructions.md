# Convenciones de BI — Banco Promerica

Este documento define cómo deben comportarse los agentes de IA (GitHub Copilot, Codex, Claude Code) cuando trabajan en proyectos de Power BI / Fabric del Banco Promerica.

## Stack tecnológico

- **Formato de proyecto:** PBIP (Power BI Project)
- **Modelo semántico:** TMDL (`Modelo.SemanticModel/definition/`)
- **Reportes:** PBIR (`Modelo.Report/definition/`)
- **Plataforma:** Microsoft Fabric + OneLake (Direct Lake)
- **Control de versiones:** Git (sync con Fabric Workspace)

## Idioma y tono

- Respondé siempre en **español**
- Usá terminología bancaria cuando aplique (saldo, cartera, cliente, transacción)
- Sé directo: primero el hallazgo, luego la explicación

## Contexto de negocio

La institución se estructura en 3 unidades:
- **Banca Personas** — clientes individuales
- **Banca Empresas** — clientes corporativos
- **Banca Privada** — wealth management

Cuando una tabla o medida aplica a una unidad específica, mencionarlo en la documentación.

## Modos de operación

Todos los agentes deben operar en uno de dos modos:

- **read-only** (por defecto): solo lectura y análisis, genera reportes en `outputs/`
- **read-write**: puede modificar archivos `.tmdl` o `.json` de PBIR, **siempre con confirmación explícita del usuario antes de cada cambio destructivo**

## Reglas globales

1. **Nunca inventes metadata.** Si no existe en el modelo, no la completes.
2. **Separá model authoring de report authoring.** El MCP de Power BI modifica el modelo semántico, no las páginas del reporte.
3. **Todo hallazgo tiene severidad:** `crítico` / `mejora` / `observación`.
4. **Distinguí 3 niveles de output:**
   - *Hallazgo* — algo que existe y requiere atención
   - *Recomendación* — sugerencia de mejora
   - *Cambio automatizable* — acción que el agente puede ejecutar (con aprobación)
5. **Outputs reproducibles:** mismos inputs → mismos outputs.
6. **Validaciones idempotentes:** correr la auditoría 10 veces da el mismo resultado.

## Ubicación del proyecto Power BI

📁 **El proyecto PBIP siempre vive en `powerbi-project/`** en la raíz del repo.

Todos los agentes buscan archivos `.tmdl`, `.pbip` y JSON de PBIR en esa carpeta por defecto.
Si el usuario no especifica ruta, asumí `powerbi-project/`.

Si la carpeta está vacía, pedile al usuario que copie el proyecto ahí antes de ejecutar cualquier análisis.

## Estructura de outputs (cuando apliquen)

```
outputs/
├── context/          ← Context base generado por model-explorer (Capa 1)
├── audit/            ← Findings de auditoría (semántica, M, SQL, PBIR)
├── ccu/              ← Catálogo de fuentes / lineage (CSV + JSON + MD)
├── documented/      ← Propuestas de documentación DRY-RUN (Capa 3)
└── recommendations/ ← Propuestas de visualizaciones (Capa 4, futuro)
```

## Guardrails

- **No** modificar archivos `.tmdl` sin backup previo del estado actual
- **No** romper el esquema PBIR (validar antes de guardar)
- **No** hacer commits automáticos al repo
- **Sí** generar diffs sugeridos para revisión humana

## Referencias

- Skills disponibles en `.github/skills/`
- Agents disponibles en `.github/agents/`
- Prompts disponibles en `.github/prompts/`
