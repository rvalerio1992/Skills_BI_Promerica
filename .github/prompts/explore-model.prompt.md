---
description: "Explorar proyectos PBIP y generar el contexto base que todos los demás agentes consumen"
mode: agent
---

# Explorar modelo

Ejecutá el agente **Model Explorer** sobre la carpeta `powerbi-project/`.

## Pasos

1. **Descubrir proyectos:** buscar todos los `.pbip` en `powerbi-project/`.
2. Para cada proyecto, procesarlo de forma independiente:
   - Parsear el SemanticModel (tablas, medidas, relaciones, particiones)
   - Parsear el Report (páginas, visuales, bookmarks — solo metadata)
   - Construir grafo de dependencias de medidas
   - Generar `outputs/context/<proyecto>_context.json`
3. Si hay >1 proyecto, generar `outputs/context/_index.json` global.
4. Generar `outputs/context/_summary.md` con resumen legible.

## Modo

**READ-ONLY estricto.** Nada de modificar archivos del PBIP.

## Output esperado en chat

```
🔍 Exploración completa
   Proyectos encontrados: 2
   ├─ RPAUT084 (19 tablas, 87 medidas, 7 fuentes GOLD)
   └─ RPAUT085 (12 tablas, 45 medidas, 5 fuentes GOLD)

📁 outputs/context/
   ├─ _index.json
   ├─ _summary.md
   ├─ RPAUT084_context.json
   └─ RPAUT085_context.json

Próximos agentes recomendados:
   • /audit-sources
   • /audit-semantic-model
```

Si no hay PBIP:
```
❌ No se encontraron archivos .pbip en powerbi-project/
   Copiá tu proyecto allí antes de ejecutar este comando.
```
