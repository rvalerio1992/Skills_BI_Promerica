# 🔄 Continuación — Handoff a Claude Project

Este documento resume el **estado actual** del sistema de agentes BI de Banco Promerica y sirve como punto de partida para continuar el trabajo en un **Claude Project** con el repo cargado como knowledge.

---

## 📊 Estado actual (último commit: a6bf98d)

### ✅ Fase 0 — MVP original
- `source-lineage-auditor` — extrae fuentes y clasifica por Medallion
- `semantic-model-auditor` — scoring dual (estructural + documentación)
- `model-documenter` — documenta modelo de forma segura (DRY-RUN)
- `dax-reviewer` — revisa y refactoriza medidas DAX con confirmación

### ✅ Fase 1 — Fundación
- `model-explorer` — genera `outputs/context/<proyecto>_context.json`
- **Todos los agentes de Fase 0 refactorizados** para consumir el context
- Bug fix: `location` en findings siempre con extensión `.tmdl`
- Multi-proyecto por diseño

### 🚧 Fase 2 — Auditoría extendida
- ✅ `m-code-auditor` — implementado y probado (score 60/100 en RPAUT084)
- ✅ `sql-code-auditor` — implementado y probado (score 92/100 en RPAUT084)
- ⏳ `pbir-report-auditor` — pendiente

### 🔮 Fases 3 y 4 — Pendientes
- `m-code-formatter`, `sql-code-formatter`
- `measure-generator`, `custom-visual-builder`, `multi-report-aggregator`

---

## 🎯 Próximos pasos recomendados

### Ahora mismo — recoger datos reales
1. **Usar el sistema actual con 3-5 reportes más** del banco
2. Identificar **patrones transversales**: ¿qué reportes están bien documentados? ¿cuáles usan Bronze directamente? ¿dónde hay `SELECT *`?
3. Esto te da **datos reales** para justificar el roadmap ante gerencia

### Corto plazo — completar Fase 2
4. ✅ ~~Implementar `sql-code-auditor`~~ — completado
5. Implementar `pbir-report-auditor` (páginas con name GUID, visuales "Chart 1", filtros huérfanos, bookmarks rotos)

### Medio plazo — Fase 3 (formatters)
6. `m-code-formatter` — renombrar steps de forma masiva con mapeo asistido
7. `sql-code-formatter` — expandir `SELECT *` usando schema del context

### Largo plazo — Fase 4 (producción)
8. `multi-report-aggregator` — vista banco-wide consolidando CCUs
9. `measure-generator` — crear medidas DAX bajo pedido
10. `custom-visual-builder` — generar visuales Deneb/Vega-Lite

---

## 🐛 Gotchas conocidos (importante para el Project)

### 1. `location` siempre debe llevar extensión
Cuando un agente genera findings, el campo `location` debe apuntar al nombre de archivo completo:
- ✅ `"tables/Medidas.tmdl"` — correcto
- ❌ `"tables/Medidas"` — rompe el Model Documenter downstream

**Solución:** usar el mapping del context:
```python
name_to_file = {t["name"]: t["file"] for t in ctx["tables"]}
location = f"tables/{name_to_file[measure['table']]}"
```

### 2. `DAX-USE-DIVIDE` tiene que excluir literales numéricos
Divisiones como `[Valor]/1000000` (escalamiento) o `[x]/100` (porcentaje) NO son riesgosas — el divisor nunca es cero. Solo reportar divisiones donde el divisor sea una medida, columna o variable.

**Regex que funciona:**
```python
r'(?:\]|\w)\s*/\s*(?![\d.,]+\b)(?:\[|\w|CALCULATE|SUM|COUNT|MAX|AVERAGE)'
```

### 3. `Source`, `Origen`, `Navigation` NO son anti-patterns
Son los nombres estándar de la comunidad Power Query para el primer/segundo step. Renombrarlos va contra convención. Solo reportar default steps tipo `#"Tipo cambiado"`, `#"Valor reemplazado"`, etc.

### 4. DAX multi-línea viene entre backticks
Al parsear TMDL, las expresiones DAX largas vienen como:
```tmdl
measure [X] = ```
    VAR ...
    RETURN ...
    ```
```
Hay que hacer `dax.strip('`').strip()` para limpiar.

### 5. LocalDateTables y DateTableTemplate — excluir de auditoría
Son auto-generadas por Power BI cuando Auto Date/Time está activo. No tiene sentido auditar su naming o documentación. El context las marca con `type_inferred: "dimension_auto"`.

### 6. Tablas con caracteres especiales (⚙, á, ú)
El modelo RPAUT084 tiene `'⚙ Medidas'`. Los regex deben manejar Unicode. Usar `re.search(r"measure\s+(?:'([^']+)'|(\S+?))")` captura nombres entre comillas O sin comillas.

### 7. Scoring con penalizaciones duras
Tener cuidado con scores de documentación: si hay 190 hallazgos y cada uno penaliza -1, el score se va a 0 muy rápido. Por eso separamos:
- Estructural: pesos -20/-5/-1 (crítico/mejora/observación)
- Documentación: mismos pesos pero con cap en 0
- Global: `round(estructural * 0.7 + documentación * 0.3)`

---

## 📁 Archivos clave del repo (para subir al Project)

### Obligatorios (conocimiento base)
- `ARCHITECTURE.md` — blueprint completo de los 12 agentes en 4 capas
- `README.md` — visión general y roadmap
- `.github/copilot-instructions.md` — convenciones BI globales

### Agentes (para referencia del flujo)
- `.github/agents/model-explorer.agent.md`
- `.github/agents/source-lineage-auditor.agent.md`
- `.github/agents/semantic-model-auditor.agent.md`
- `.github/agents/model-documenter.agent.md`
- `.github/agents/dax-reviewer.agent.md`
- `.github/agents/m-code-auditor.agent.md`

### Skills (patrones de parseo)
- `.github/skills/tmdl-parser/SKILL.md`
- `.github/skills/m-query-parser/SKILL.md`
- `.github/skills/dax-patterns/SKILL.md`
- `.github/skills/naming-conventions/SKILL.md`

### Instructions (reglas de estilo)
- `.github/instructions/dax.instructions.md`
- `.github/instructions/tmdl.instructions.md`
- `.github/instructions/pbir.instructions.md`

### Ejemplos de output (muestra de calidad esperada)
- `outputs/context/RPAUT084_context.json` — ejemplo de context generado
- `outputs/audit/RPAUT084_semantic_model_findings.json` — ejemplo findings estructurales
- `outputs/audit/RPAUT084_m_review.json` — ejemplo M audit

---

## 🎯 Cómo usar el Project

### Para el setup inicial
1. Crear Project en Claude con nombre `Skills BI Promerica`
2. Copiar las **instrucciones** del archivo `PROJECT_INSTRUCTIONS.md` (ver aparte)
3. Subir los **archivos clave** listados arriba al knowledge
4. Primer chat: "Leé ARCHITECTURE.md y contame en qué fase estamos"

### Patrones de chats útiles
- **Un chat por fase:** *"Fase 2: sql-code-auditor"* — foco en implementar algo específico
- **Un chat para decisiones:** *"¿Cómo presento esto a gerencia?"* — discusión estratégica
- **Un chat de dudas puntuales:** *"¿Cómo funciona X en DAX?"* — consultas rápidas
- **Un chat de retrospectiva:** *"Revisar cómo funcionó el piloto con 5 reportes"*

### Qué NO hacer
- ❌ Subir los archivos `.pbip` binarios (muy pesados y no aportan al contexto)
- ❌ Subir todos los outputs/documented/ (son derivables del context + findings)
- ❌ Subir el repo completo (mucho ruido, sube solo lo curado)

---

## 💡 Reflexiones finales sobre la sesión actual

**Lo que funcionó bien:**
- Diseño iterativo con preguntas estructuradas (ask_user_input)
- Validar cada agente con datos reales (RPAUT084) antes de avanzar
- Documentar la arquitectura ANTES de seguir sumando agentes
- Separar scoring dual (ayudó a entender que el modelo era funcional pero no documentado)

**Lo que hubiera sido más eficiente en un Project:**
- No re-explicar el contexto de Banco Promerica cada turno
- No re-clonar el repo en cada comando bash
- Tener conversaciones paralelas para temas distintos
- Mantener continuidad entre días

**Insights importantes del trabajo:**
1. El modelo RPAUT084 es **estructuralmente sólido** (46/100) pero **sin documentar** (0/100) — patrón probable en muchos reportes del banco
2. Todos los reportes GOLD consumen desde `synw-modeling-prod-westus2-001-ondemand` — ese servidor es crítico
3. La convención Promerica (FACT_/DIM_) no se está siguiendo en muchos reportes (usan Fct/Dim estilo Power BI)
4. Auto Date/Time habilitado es generalizado (genera LocalDateTables innecesarias)

---

## 📞 Si algo se rompe

### Model Explorer falla
- Verificar que existe `*.pbip` en `powerbi-project/`
- Verificar permisos de lectura en `*.SemanticModel/`
- Revisar encoding (debe ser UTF-8)

### Documenter genera cambios vacíos
- Verificar que `location` en findings tiene `.tmdl`
- Verificar que el mapping `measure.table → file` matcheé con archivos reales

### Scores raros
- Ver `outputs/audit/*_semantic_model_findings.json` sección `summary.by_rule`
- Si una regla tiene demasiados casos, probablemente es un falso positivo a ajustar

---

**Última actualización:** 2026-04-23
**Estado:** Fase 2 en progreso (1 de 3 agentes completado)
**Próximo objetivo:** `sql-code-auditor`
