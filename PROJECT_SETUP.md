# 🚀 Setup del Claude Project — Pasos rápidos

Guía de 5 minutos para crear el Project con el repo cargado.

## Paso 1 — Crear el Project

1. Abrí **Claude.ai** (web o app)
2. Andá a **Projects** (sidebar izquierdo)
3. Click en **"Create project"** o **"New project"**
4. Nombre: `Skills BI Promerica`
5. Descripción sugerida: `Sistema de agentes de IA para auditar, documentar y mejorar reportes Power BI del Banco Promerica`

## Paso 2 — Pegar instrucciones

1. En el Project, andá a **"Instructions"** o **"Custom instructions"**
2. Abrí `PROJECT_INSTRUCTIONS.md` en GitHub
3. Copiá todo el contenido entre las líneas `---`
4. Pegalo en el campo de instrucciones del Project

## Paso 3 — Subir knowledge

Subí estos archivos al Project (sección "Knowledge" o "Files"):

### Obligatorios
- [ ] `ARCHITECTURE.md`
- [ ] `README.md`
- [ ] `CONTINUATION.md`

### Agentes (definen cómo trabajan)
- [ ] `.github/agents/model-explorer.agent.md`
- [ ] `.github/agents/source-lineage-auditor.agent.md`
- [ ] `.github/agents/semantic-model-auditor.agent.md`
- [ ] `.github/agents/model-documenter.agent.md`
- [ ] `.github/agents/dax-reviewer.agent.md`
- [ ] `.github/agents/m-code-auditor.agent.md`

### Skills (patrones de parseo)
- [ ] `.github/skills/tmdl-parser/SKILL.md`
- [ ] `.github/skills/m-query-parser/SKILL.md`
- [ ] `.github/skills/dax-patterns/SKILL.md`
- [ ] `.github/skills/naming-conventions/SKILL.md`

### Instructions (reglas de estilo)
- [ ] `.github/instructions/dax.instructions.md`
- [ ] `.github/instructions/tmdl.instructions.md`
- [ ] `.github/instructions/pbir.instructions.md`
- [ ] `.github/copilot-instructions.md`

### Ejemplos (para referencia de calidad)
- [ ] `outputs/context/RPAUT084_context.json`
- [ ] `outputs/audit/RPAUT084_semantic_model_findings.json`
- [ ] `outputs/audit/RPAUT084_m_review.json`

**Total:** ~18 archivos. Pesa poco, no te preocupes por límites.

## Paso 4 — Primer chat del Project

En el Project recién creado, iniciá un chat con:

```
Acabo de migrar desde un chat anterior. Leé CONTINUATION.md para
entender dónde quedamos. Confirmame qué fase tenemos en curso y
cuál sería el próximo paso concreto.
```

Claude debería responder con:
- ✅ Fase 2 en curso
- ✅ `m-code-auditor` ya implementado
- ⏳ Próximo: `sql-code-auditor` o `pbir-report-auditor`
- 📊 Score actual de RPAUT084

Si responde eso, el Project está bien configurado.

## Paso 5 — Organizar chats

Patrón sugerido de organización:

```
Project: Skills BI Promerica
├── 💬 "Fase 2: sql-code-auditor"         ← un chat para construir
├── 💬 "Fase 2: pbir-report-auditor"      ← otro chat para el siguiente
├── 💬 "Piloto con 3 reportes"            ← recoger feedback real
├── 💬 "Presentación a gerencia"          ← cuando sea momento
├── 💬 "Dudas puntuales DAX"              ← consultas rápidas
└── 💬 "Retrospectiva mensual"            ← revisar qué funcionó
```

**Ventaja clave:** cada chat arranca con el mismo contexto de knowledge,
pero podés tener conversaciones paralelas sin perder coherencia.

## Paso 6 — Mantener actualizado el knowledge

Cuando agregues nuevos agentes o cambies los existentes:

1. Subí la versión actualizada al Project (reemplaza la anterior)
2. También commit + push al repo como fuente de verdad
3. Opcional: actualizar `CONTINUATION.md` con el nuevo estado

---

## 💡 Tips prácticos

- **Nombrá los chats con prefijos** (`[Fase2]`, `[Duda]`, `[Meeting]`) para filtrarlos rápido
- **Usa Projects separados** si el equipo empieza otros repos (Data Science, Data Engineering, etc.)
- **El knowledge NO es editable desde chats** — si cambia un agente, hay que subirlo de nuevo
- **Los chats del Project consumen cuota** — igual que chats normales, sin costo extra por Project

---

## ✅ Checklist de migración

Una vez que hayas hecho los pasos 1-5:

- [ ] Project creado con nombre correcto
- [ ] Instrucciones pegadas y guardadas
- [ ] 18 archivos subidos al knowledge
- [ ] Primer chat funciona (Claude conoce el estado actual)
- [ ] Organización de chats definida

Cuando tildes los 5, estás listo para seguir trabajando con total continuidad.

---

**Última actualización:** 2026-04-23
