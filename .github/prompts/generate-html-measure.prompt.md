---
description: "Generar una medida DAX que devuelva HTML para el visual HTML Content"
mode: agent
---

# Generar medida HTML

Ejecutá el agente **HTML Measure Generator**.

## Pasos

1. **Verificar prerrequisito:** existir `outputs/context/<proyecto>_context.json`.
   - Si no: avisar y sugerir `/explore-model` primero.
2. **Analizar la petición del usuario** (puede venir como argumento o en el siguiente mensaje):
   - Identificar tipo de visual (card/grid/meta/badge/tabla/gauge/semaforo/cliente)
   - Identificar medidas a usar
   - Detectar umbrales, labels custom, etc.
3. **Validar referencias** contra `context.measures[]`:
   - Match exacto: OK
   - Match fuzzy: confirmar con usuario
   - No match: preguntar cuál usar
4. **Si algo no está claro, preguntar** (modo interactivo)
5. **Si está todo claro, generar directamente** (modo directo)
6. **Crear 3 outputs** en `outputs/html-measures/<nombre>/`:
   - `.dax` — medida lista para pegar
   - `.md` — documentación
   - `.preview.html` — preview visual con valores de ejemplo

## Templates disponibles

1. **KPI Card** — valor principal + YoY con color según signo
2. **KPI Grid** — 3-4 cards en grilla
3. **Comparativo vs Meta** — progress bar con % avance
4. **Status Badge** — pastilla con color según umbral
5. **Mini tabla** — tabla con formato condicional por celda
6. **Gauge simulado** — semicírculo SVG con %
7. **Semáforo** — círculo rojo/ámbar/verde
8. **Card de cliente** — avatar con iniciales + info

## Output en chat

```
✅ Medida HTML generada: HTML_Card_Saldo_YoY

📁 outputs/html-measures/HTML_Card_Saldo_YoY/
   • HTML_Card_Saldo_YoY.dax
   • HTML_Card_Saldo_YoY.md
   • HTML_Card_Saldo_YoY.preview.html

Medidas referenciadas (validadas):
  ✅ [Saldo]
  ✅ [1_YoY_%]

Para usar en Power BI:
1. Agregar visual "HTML Content" (Daniel Marsh-Patrick) desde marketplace
2. Copiar contenido del .dax como nueva medida
3. Arrastrar la medida al campo "Values" del visual

⚠️ Revisá el .preview.html primero para validar el diseño.
```

## Modo

**Capa 4 - Producción.** Solo genera archivos nuevos. NO modifica el modelo.
