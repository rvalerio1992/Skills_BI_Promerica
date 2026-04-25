# HTML_Card_Saldo_YoY

**Template:** KPI Card (template #1 de `html-dax-patterns`)
**Visual destino:** HTML Content (Daniel Marsh-Patrick)
**Generado:** 2026-04-25

## Propósito

Tarjeta visual que muestra el saldo actual con su comparativo interanual (YoY),
con color verde si el crecimiento es positivo y rojo si es negativo.

## Medidas referenciadas (validadas en el modelo)

| Medida | Tabla | Uso |
|---|---|---|
| `[Saldo]` | ⚙ Medidas | Valor principal |
| `[1_YoY_%]` | Medidas | Variación YoY con color según signo |

## Parámetros customizables

Para adaptar el template, modificá en el DAX:

| Parámetro | Valor actual | Qué cambia |
|---|---|---|
| Label "Saldo Actual" | `"Saldo Actual"` | Título del card |
| Accent color izquierdo | `#006338` | Borde lateral (verde Promerica) |
| Formato del valor | `"$#,0"` | Sin decimales, con separador miles |
| Formato YoY | `"0.0%"` | Porcentaje con 1 decimal |

## Cómo usar en Power BI

1. **Instalar el visual** (una sola vez):
   - Marketplace → buscar "HTML Content" by Daniel Marsh-Patrick
   - [Link directo](https://appsource.microsoft.com/en-us/product/power-bi-visuals/WA200001930)

2. **Agregar la medida al modelo:**
   - Abrir Power BI Desktop
   - Click derecho en la tabla `⚙ Medidas` (o donde vayan tus medidas HTML)
   - New measure
   - Pegar todo el contenido del archivo `.dax`

3. **Agregar visual a la página:**
   - Visualizations → HTML Content (icono del visual instalado)
   - Arrastrar `HTML_Card_Saldo_YoY` al campo "Values"

4. **Validar:**
   - Primero mirá `HTML_Card_Saldo_YoY.preview.html` en el navegador
   - Compará contra lo que se ve en Power BI — deberían coincidir

## Preview

📄 Ver `HTML_Card_Saldo_YoY.preview.html` para render estático con valores simulados.

## Notas

- El color del accent izquierdo está fijo en verde Promerica (`#006338`). Si querés
  cambiar dinámicamente según condición, modificá la lógica del `VAR _ColorYoY`.
- Para responsive, el card usa `max-width: 280px` que funciona bien en dashboards.
  Si necesitás más ancho, ajustá este valor.
- El símbolo ▲/▼ es Unicode, no requiere fuente externa.
