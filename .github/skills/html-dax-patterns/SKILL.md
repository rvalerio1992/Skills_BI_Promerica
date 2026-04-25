---
name: html-dax-patterns
description: >
  Biblioteca de 8 templates HTML embebidos en DAX para el visual "HTML Content"
  (Daniel Marsh-Patrick) en Power BI. Cada template es parametrizable y usa
  la paleta de colores Promerica. Invocar cuando el html-measure-generator
  necesita construir una medida DAX que devuelva HTML renderizado.
---

# HTML-en-DAX Patterns

Biblioteca de 8 templates HTML embebidos en medidas DAX, diseñados para el visual
**"HTML Content" de Daniel Marsh-Patrick** (el más usado en banca).

## ⚙️ Reglas fundamentales para HTML-en-DAX

### 1. Escapado de comillas

El HTML usa comillas, DAX también. Convenciones:

- **Strings DAX:** comillas dobles `"..."`
- **Atributos HTML:** preferir comillas simples `'...'` para no conflictuar
  - ✅ `"<div style='color:red'>"`
  - ⚠️ `"<div style=""color:red"">"` (válido pero difícil de leer)

### 2. Concatenación con `&`

```dax
"<div>" & texto & "</div>"
```

No usar `CONCATENATE()` para >2 elementos (incómodo). Preferir `&` encadenado.

### 3. Variables para lógica

Usar `VAR` para:
- Calcular valores una sola vez
- Condicionales (color según signo)
- Formatear una vez y reusar

```dax
VAR _YoY = [YoY%]
VAR _Color = IF(_YoY >= 0, "#006338", "#A20000")
VAR _Flecha = IF(_YoY >= 0, "▲", "▼")
RETURN ...
```

### 4. FORMAT() para números y fechas

```dax
FORMAT([Saldo], "$#,0")          // $1,234,567
FORMAT([YoY%], "0.0%")           // 8.3%
FORMAT([Fecha], "dd/mm/yyyy")    // 23/04/2026
```

### 5. Límites del visual

HTML Content tiene límites:
- Máximo **~30,000 caracteres** en la medida resultante
- No soporta `<script>` (bloqueado por seguridad)
- No soporta `<iframe>`
- No soporta recursos externos (imágenes deben ser base64 o URLs HTTPS públicas)
- CSS inline o en `<style>` al inicio — no hojas externas

### 6. Caracteres especiales

- Unicode soportado: ▲ ▼ ● ■ ◆ ★ → ↑ ↓ ✓ ✗
- Evitar caracteres HTML especiales en contenido: usar entidades si es necesario
  - `&amp;` para `&`
  - `&lt;` para `<`
  - `&gt;` para `>`

---

## 📐 Template 1: KPI Card

**Caso de uso:** mostrar un valor principal con comparativo YoY.

```dax
KPI Card = 
VAR _Valor = [Saldo]
VAR _YoY = [YoY %]
VAR _ColorYoY = IF(_YoY >= 0, "#006338", "#A20000")
VAR _Flecha = IF(_YoY >= 0, "▲", "▼")
RETURN
  "<div style='font-family:Segoe UI; padding:12px; border-radius:8px; " &
    "background:#FFFFFF; border-left:4px solid #006338; " &
    "box-shadow:0 2px 4px rgba(0,0,0,0.08); max-width:280px;'>" &
  "<div style='font-size:11px; color:#A6A6A6; " &
    "text-transform:uppercase; letter-spacing:0.5px;'>Saldo Actual</div>" &
  "<div style='font-size:24px; font-weight:700; color:#000000; " &
    "margin-top:4px;'>" & FORMAT(_Valor, "$#,0") & "</div>" &
  "<div style='font-size:12px; color:" & _ColorYoY & "; " &
    "margin-top:6px; font-weight:600;'>" &
    _Flecha & " " & FORMAT(_YoY, "0.0%") & " vs año anterior" &
  "</div>" &
  "</div>"
```

**Parámetros del template:**
- `_Valor` → medida principal
- `_YoY` → medida de variación (negativa/positiva)
- Label "Saldo Actual" → customizable
- Color del accent (`#006338`) → customizable

---

## 📊 Template 2: KPI Grid (3-4 cards en grilla)

**Caso de uso:** dashboard row con varios KPIs del mismo tipo.

```dax
KPI Grid = 
VAR _Saldo = [Saldo]
VAR _Clientes = [Cantidad Clientes]
VAR _Productos = [Cantidad Productos]
VAR _Tasa = [Tasa Ponderada]
RETURN
  "<div style='display:flex; gap:12px; flex-wrap:wrap; font-family:Segoe UI;'>" &
  
  // Card 1
  "<div style='flex:1; min-width:140px; padding:12px; border-radius:8px; " &
    "background:#FFFFFF; border-left:4px solid #006338; " &
    "box-shadow:0 2px 4px rgba(0,0,0,0.08);'>" &
    "<div style='font-size:11px; color:#A6A6A6; text-transform:uppercase;'>Saldo</div>" &
    "<div style='font-size:20px; font-weight:700;'>" & FORMAT(_Saldo, "$#,0") & "</div>" &
  "</div>" &
  
  // Card 2
  "<div style='flex:1; min-width:140px; padding:12px; border-radius:8px; " &
    "background:#FFFFFF; border-left:4px solid #003E89; " &
    "box-shadow:0 2px 4px rgba(0,0,0,0.08);'>" &
    "<div style='font-size:11px; color:#A6A6A6; text-transform:uppercase;'>Clientes</div>" &
    "<div style='font-size:20px; font-weight:700;'>" & FORMAT(_Clientes, "#,0") & "</div>" &
  "</div>" &
  
  // Card 3
  "<div style='flex:1; min-width:140px; padding:12px; border-radius:8px; " &
    "background:#FFFFFF; border-left:4px solid #61BF1A; " &
    "box-shadow:0 2px 4px rgba(0,0,0,0.08);'>" &
    "<div style='font-size:11px; color:#A6A6A6; text-transform:uppercase;'>Productos</div>" &
    "<div style='font-size:20px; font-weight:700;'>" & FORMAT(_Productos, "#,0") & "</div>" &
  "</div>" &
  
  // Card 4
  "<div style='flex:1; min-width:140px; padding:12px; border-radius:8px; " &
    "background:#FFFFFF; border-left:4px solid #69C7C7; " &
    "box-shadow:0 2px 4px rgba(0,0,0,0.08);'>" &
    "<div style='font-size:11px; color:#A6A6A6; text-transform:uppercase;'>Tasa</div>" &
    "<div style='font-size:20px; font-weight:700;'>" & FORMAT(_Tasa, "0.00%") & "</div>" &
  "</div>" &
  
  "</div>"
```

---

## 📈 Template 3: Comparativo vs Meta (progress bar)

**Caso de uso:** mostrar avance contra target con barra de progreso.

```dax
Comparativo Meta = 
VAR _Actual = [Valor Actual]
VAR _Meta = [Valor Meta]
VAR _Pct = DIVIDE(_Actual, _Meta, 0)
VAR _PctCapped = MIN(_Pct, 1)  // cap visual en 100%
VAR _Color = 
  SWITCH(TRUE(),
    _Pct >= 1,    "#006338",  // verde: cumplió
    _Pct >= 0.8,  "#61BF1A",  // verde brand: cerca
    _Pct >= 0.5,  "#C35A00",  // naranja: a medio camino
    "#A20000"                  // rojo: lejos
  )
RETURN
  "<div style='font-family:Segoe UI; padding:12px; " &
    "background:#FFFFFF; border-radius:8px; max-width:320px; " &
    "box-shadow:0 2px 4px rgba(0,0,0,0.08);'>" &
  
  "<div style='display:flex; justify-content:space-between; margin-bottom:4px;'>" &
    "<span style='font-size:11px; color:#A6A6A6; text-transform:uppercase;'>Avance vs Meta</span>" &
    "<span style='font-size:11px; color:" & _Color & "; font-weight:600;'>" &
      FORMAT(_Pct, "0.0%") & "</span>" &
  "</div>" &
  
  "<div style='height:8px; background:#F0F0F0; border-radius:4px; " &
    "overflow:hidden; margin:6px 0;'>" &
    "<div style='height:100%; width:" & FORMAT(_PctCapped, "0.0%") & "; " &
      "background:" & _Color & "; transition:width 0.3s;'></div>" &
  "</div>" &
  
  "<div style='display:flex; justify-content:space-between; " &
    "font-size:11px; color:#A6A6A6; margin-top:4px;'>" &
    "<span>" & FORMAT(_Actual, "$#,0") & "</span>" &
    "<span>Meta: " & FORMAT(_Meta, "$#,0") & "</span>" &
  "</div>" &
  
  "</div>"
```

---

## 🏷️ Template 4: Status Badge

**Caso de uso:** indicador de estado con color según umbral.

```dax
Status Badge = 
VAR _Valor = [Ratio Morosidad]
VAR _Estado = 
  SWITCH(TRUE(),
    _Valor <= 0.02, "ÓPTIMO",
    _Valor <= 0.05, "ACEPTABLE",
    _Valor <= 0.10, "RIESGO",
    "CRÍTICO"
  )
VAR _Color = 
  SWITCH(TRUE(),
    _Valor <= 0.02, "#006338",
    _Valor <= 0.05, "#61BF1A",
    _Valor <= 0.10, "#C35A00",
    "#A20000"
  )
VAR _BgColor =  // fondo con 15% opacidad (aproximado con hex)
  SWITCH(TRUE(),
    _Valor <= 0.02, "#E0EFE8",  // verde muy claro
    _Valor <= 0.05, "#EFF9E0",  // verde brand claro
    _Valor <= 0.10, "#FAEEE0",  // naranja claro
    "#FAE0E0"                    // rojo claro
  )
RETURN
  "<span style='font-family:Segoe UI; display:inline-block; " &
    "padding:4px 10px; border-radius:12px; " &
    "background:" & _BgColor & "; color:" & _Color & "; " &
    "font-size:11px; font-weight:700; letter-spacing:0.5px;'>" &
    _Estado & " — " & FORMAT(_Valor, "0.00%") &
  "</span>"
```

---

## 📋 Template 5: Mini tabla con formato condicional

**Caso de uso:** tabla HTML con background/color por celda según valor.

```dax
Mini Tabla Banca = 
VAR _Personas = CALCULATE([Saldo], DimLineaNegocio[LINEA] = "PERSONAS")
VAR _Empresas = CALCULATE([Saldo], DimLineaNegocio[LINEA] = "EMPRESAS")
VAR _Privada = CALCULATE([Saldo], DimLineaNegocio[LINEA] = "PRIVADA")
VAR _PersonasYoY = CALCULATE([YoY%], DimLineaNegocio[LINEA] = "PERSONAS")
VAR _EmpresasYoY = CALCULATE([YoY%], DimLineaNegocio[LINEA] = "EMPRESAS")
VAR _PrivadaYoY = CALCULATE([YoY%], DimLineaNegocio[LINEA] = "PRIVADA")
RETURN
  "<table style='font-family:Segoe UI; border-collapse:collapse; " &
    "width:100%; font-size:12px;'>" &
  
  "<thead><tr style='background:#006338; color:white;'>" &
    "<th style='padding:8px; text-align:left;'>Banca</th>" &
    "<th style='padding:8px; text-align:right;'>Saldo</th>" &
    "<th style='padding:8px; text-align:right;'>YoY</th>" &
  "</tr></thead>" &
  
  "<tbody>" &
  "<tr style='border-bottom:1px solid #E0E0E0;'>" &
    "<td style='padding:8px;'>Personas</td>" &
    "<td style='padding:8px; text-align:right;'>" & FORMAT(_Personas, "$#,0") & "</td>" &
    "<td style='padding:8px; text-align:right; color:" & 
      IF(_PersonasYoY >= 0, "#006338", "#A20000") & "; font-weight:600;'>" &
      FORMAT(_PersonasYoY, "0.0%") & "</td>" &
  "</tr>" &
  
  "<tr style='border-bottom:1px solid #E0E0E0; background:#F9F9F9;'>" &
    "<td style='padding:8px;'>Empresas</td>" &
    "<td style='padding:8px; text-align:right;'>" & FORMAT(_Empresas, "$#,0") & "</td>" &
    "<td style='padding:8px; text-align:right; color:" & 
      IF(_EmpresasYoY >= 0, "#006338", "#A20000") & "; font-weight:600;'>" &
      FORMAT(_EmpresasYoY, "0.0%") & "</td>" &
  "</tr>" &
  
  "<tr style='border-bottom:1px solid #E0E0E0;'>" &
    "<td style='padding:8px;'>Privada</td>" &
    "<td style='padding:8px; text-align:right;'>" & FORMAT(_Privada, "$#,0") & "</td>" &
    "<td style='padding:8px; text-align:right; color:" & 
      IF(_PrivadaYoY >= 0, "#006338", "#A20000") & "; font-weight:600;'>" &
      FORMAT(_PrivadaYoY, "0.0%") & "</td>" &
  "</tr>" &
  
  "</tbody></table>"
```

---

## 🎯 Template 6: Gauge simulado (semicírculo)

**Caso de uso:** KPI con visualización tipo gauge (limitado en HTML puro).

```dax
Gauge = 
VAR _Valor = [Cumplimiento Meta]  // 0 a 1
VAR _ValorCapped = MIN(_Valor, 1)
VAR _Grados = _ValorCapped * 180
VAR _Color = 
  SWITCH(TRUE(),
    _Valor >= 1,    "#006338",
    _Valor >= 0.8,  "#61BF1A",
    _Valor >= 0.5,  "#C35A00",
    "#A20000"
  )
RETURN
  "<div style='font-family:Segoe UI; padding:12px; text-align:center; " &
    "background:#FFFFFF; border-radius:8px; max-width:200px;'>" &
  
  "<svg width='180' height='100' viewBox='0 0 180 100'>" &
    // Arco de fondo
    "<path d='M 10 90 A 80 80 0 0 1 170 90' " &
      "stroke='#F0F0F0' stroke-width='16' fill='none'/>" &
    // Arco de valor (usa stroke-dasharray aproximado)
    "<path d='M 10 90 A 80 80 0 0 1 170 90' " &
      "stroke='" & _Color & "' stroke-width='16' fill='none' " &
      "stroke-dasharray='" & FORMAT(_Valor * 251, "0") & " 251' " &
      "stroke-linecap='round'/>" &
    // Texto central
    "<text x='90' y='85' text-anchor='middle' " &
      "font-size='24' font-weight='700' fill='#000000'>" &
      FORMAT(_Valor, "0%") & "</text>" &
  "</svg>" &
  
  "<div style='font-size:11px; color:#A6A6A6; " &
    "text-transform:uppercase; margin-top:-8px;'>Cumplimiento</div>" &
  
  "</div>"
```

⚠️ **Nota:** gauges HTML puros son limitados. Para gauges sofisticados preferir
visuales nativos de Power BI (Card with States by Charty, Gauge oficial).

---

## 🚦 Template 7: Semáforo

**Caso de uso:** indicador rojo/ámbar/verde simple según umbrales.

```dax
Semaforo = 
VAR _Valor = [NPL Ratio]
VAR _Color = 
  SWITCH(TRUE(),
    _Valor <= 0.03, "#006338",
    _Valor <= 0.06, "#C35A00",
    "#A20000"
  )
VAR _Label = 
  SWITCH(TRUE(),
    _Valor <= 0.03, "Saludable",
    _Valor <= 0.06, "Atención",
    "Crítico"
  )
RETURN
  "<div style='font-family:Segoe UI; display:flex; align-items:center; " &
    "gap:12px; padding:12px; background:#FFFFFF; border-radius:8px;'>" &
  
  // Círculo del semáforo
  "<div style='width:32px; height:32px; border-radius:50%; " &
    "background:" & _Color & "; " &
    "box-shadow:0 0 12px " & _Color & "66;'></div>" &
  
  "<div>" &
    "<div style='font-size:11px; color:#A6A6A6; text-transform:uppercase;'>NPL Ratio</div>" &
    "<div style='font-size:16px; font-weight:700;'>" & FORMAT(_Valor, "0.00%") & "</div>" &
    "<div style='font-size:11px; color:" & _Color & "; font-weight:600;'>" & 
      _Label & "</div>" &
  "</div>" &
  
  "</div>"
```

---

## 👤 Template 8: Card de cliente (con iniciales)

**Caso de uso:** mostrar info de cliente seleccionado con avatar generado.

```dax
Card Cliente = 
VAR _Nombre = SELECTEDVALUE(DimCliente[NOMBRE], "—")
VAR _Segmento = SELECTEDVALUE(DimCliente[SEGMENTO], "")
VAR _Saldo = [Saldo]
VAR _Productos = [Cantidad Productos]
// Generar iniciales: tomar primera letra de primera palabra + primera de última
VAR _Palabras = SUBSTITUTE(_Nombre, "  ", " ")  // normalizar dobles espacios
VAR _PrimeraLetra = LEFT(_Palabras, 1)
VAR _PosEspacio = FIND(" ", _Palabras, 1, 0)
VAR _UltimaLetra = IF(_PosEspacio > 0, MID(_Palabras, _PosEspacio + 1, 1), "")
VAR _Iniciales = UPPER(_PrimeraLetra & _UltimaLetra)
VAR _ColorFondo = 
  SWITCH(_Segmento,
    "PERSONAS", "#006338",
    "EMPRESAS", "#003E89",
    "PRIVADA",  "#61BF1A",
    "#A6A6A6"
  )
RETURN
  "<div style='font-family:Segoe UI; display:flex; gap:12px; " &
    "padding:16px; background:#FFFFFF; border-radius:8px; " &
    "box-shadow:0 2px 4px rgba(0,0,0,0.08); max-width:320px;'>" &
  
  // Avatar con iniciales
  "<div style='width:48px; height:48px; border-radius:50%; " &
    "background:" & _ColorFondo & "; color:white; " &
    "display:flex; align-items:center; justify-content:center; " &
    "font-size:18px; font-weight:700; flex-shrink:0;'>" &
    _Iniciales &
  "</div>" &
  
  // Info
  "<div style='flex:1;'>" &
    "<div style='font-size:14px; font-weight:700; color:#000000;'>" & _Nombre & "</div>" &
    "<div style='font-size:11px; color:#A6A6A6; text-transform:uppercase; " &
      "margin-top:2px;'>" & _Segmento & "</div>" &
    "<div style='display:flex; gap:16px; margin-top:8px;'>" &
      "<div>" &
        "<div style='font-size:9px; color:#A6A6A6; text-transform:uppercase;'>Saldo</div>" &
        "<div style='font-size:13px; font-weight:600;'>" & FORMAT(_Saldo, "$#,0") & "</div>" &
      "</div>" &
      "<div>" &
        "<div style='font-size:9px; color:#A6A6A6; text-transform:uppercase;'>Productos</div>" &
        "<div style='font-size:13px; font-weight:600;'>" & FORMAT(_Productos, "#,0") & "</div>" &
      "</div>" &
    "</div>" &
  "</div>" &
  
  "</div>"
```

---

## 🧪 Cómo validar antes de pegar en Power BI

1. **Validar sintaxis DAX:** pegar en DAX Studio o en el editor de medidas
2. **Validar HTML:** el agente genera un archivo `.preview.html` con valores de ejemplo
3. **Validar en Power BI:**
   - Instalar "HTML Content" (Daniel Marsh-Patrick) desde marketplace
   - Agregar visual a la página
   - Drag & drop la medida al campo "Values"
   - El visual debe renderizar el HTML

## 🛡️ Qué NO incluir en HTML-en-DAX

- ❌ `<script>` (bloqueado por seguridad del visual)
- ❌ `<iframe>` (no soportado)
- ❌ Imágenes de URLs externas no-HTTPS (muchas se bloquean)
- ❌ CSS en archivos externos (`<link rel='stylesheet'>` no funciona)
- ❌ Animaciones CSS que dependen de interacción (limitado)
- ❌ Form inputs (`<input>`, `<button>` — los clicks no hacen nada útil)

## ✅ Qué SÍ funciona bien

- ✅ Flexbox, Grid (CSS moderno)
- ✅ SVG inline
- ✅ Imágenes base64 (`<img src='data:image/png;base64,...'>`)
- ✅ Unicode symbols (▲ ▼ ● ★ etc.)
- ✅ Gradientes, sombras, border-radius
- ✅ CSS variables dentro de `<style>` si se pone al inicio

## 📚 Referencias

- [HTML Content visual en AppSource](https://appsource.microsoft.com/en-us/product/power-bi-visuals/WA200001930)
- [Documentación del visual](https://github.com/pbi-tools/html-content)
- Para lógica compleja considerar **Deneb/Vega-Lite** (otro camino, templates aparte)
