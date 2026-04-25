---
name: promerica-brand
description: >
  Paleta de colores corporativos, tipografías y guidelines visuales de Banco
  Promerica Costa Rica. Extraídos del theme oficial Promerica1 usado en los
  reportes Power BI del banco. Invocar cuando un agente genera artefactos
  visuales (HTML, SVG, CSS, charts) que deban respetar el branding.
---

# Promerica Brand Guidelines

Paleta y recursos visuales oficiales de Banco Promerica CR.
Fuente: theme `Promerica1` extraído de reportes Power BI del banco.

## 🎨 Paleta principal

### Colores corporativos

| Rol | Hex | Uso |
|---|---|---|
| **Verde corporativo** | `#006338` | Color primario, énfasis principal, CTA |
| **Verde brand** | `#61BF1A` | Accents, resaltes positivos, highlights |
| **Azul corporativo** | `#003E89` | Secundario, headers, info |
| **Cian** | `#69C7C7` | Accents secundarios, iconos |

### Colores semánticos

| Rol | Hex | Uso |
|---|---|---|
| **Éxito / Positivo** | `#006338` | Valores positivos, crecimientos, OK (mismo que verde corporativo) |
| **Error / Negativo** | `#A20000` | Valores negativos, decrecimientos, críticos |
| **Advertencia** | `#C35A00` | Alertas, valores en riesgo, pendientes |
| **Neutro / Deshabilitado** | `#A6A6A6` | Placeholders, texto secundario, labels |
| **Fondo** | `#FFFFFF` | Fondo principal |
| **Texto principal** | `#000000` | Texto sobre fondo claro |

### Paleta de data (categorías)

Cuando se necesita diferenciar categorías en un chart (ej: Banca Personas / Empresas / Privada):

1. `#006338` — Verde corporativo
2. `#61BF1A` — Verde brand
3. `#003E89` — Azul corporativo
4. `#A6A6A6` — Gris
5. `#69C7C7` — Cian
6. `#A20000` — Rojo
7. `#C35A00` — Naranja
8. `#B9C301` — Oliva
9. `#3599B8` — Azul claro

**Principio:** nunca usar más de 6 colores simultáneos en un chart. Si hay más categorías, agrupar las menores como "Otros".

## 🔤 Tipografía

| Contexto | Font | Tamaño | Peso |
|---|---|---|---|
| **Default** | Segoe UI | — | — |
| **Título principal** | Segoe UI | 24-32px | 700 (bold) |
| **Título card** | Segoe UI | 18-24px | 700 (bold) |
| **Label** | Segoe UI | 11-12px | 400 (regular) |
| **Valor destacado** | Segoe UI | 20-28px | 700 (bold) |
| **Texto secundario** | Segoe UI | 11-12px | 400 |
| **Micro copy** | Segoe UI | 10px | 400 |

Segoe UI es la fuente estándar de Windows y se renderiza bien en Power BI sin requerir fallback.

## 📐 Espaciados y dimensiones

### Cards
- Padding interno: `12-16px`
- Border-radius: `6-8px`
- Border-left accent: `4px solid <color-semántico>`
- Box-shadow sutil: `0 2px 4px rgba(0,0,0,0.08)`
- Max-width recomendado: `280-320px`

### Separadores
- Margin vertical entre elementos: `4-8px`
- Margin horizontal en grids: `12-16px`

### Uppercase labels
- `text-transform: uppercase`
- `letter-spacing: 0.5px` (mejora legibilidad)
- Color: `#A6A6A6` (neutro)
- Font-size: `11px`

## 🎯 Accesibilidad

- **Contraste mínimo:** texto sobre fondo blanco usa `#000000` o colores corporativos (nunca grises claros)
- **Nunca usar solo color para diferenciar** (agregar íconos ▲▼, símbolos, o texto)
- **Tamaño mínimo texto:** 10px para micro copy, 12px para texto general
- **Tocar targets:** si los elementos son interactivos, mínimo 24x24px

## 🇨🇷 Convenciones locales (Costa Rica)

- **Moneda:** colones `₡` o dólares `$` según contexto
- **Formato números:** separador de miles `,` y decimal `.`
  - Ej: `$1,234,567.89` o `₡1,234,567.89`
- **Formato fechas:** `dd/mm/yyyy` (ej: `23/04/2026`)
- **Decimales:** 2 para montos, 1 para porcentajes

## 💼 Líneas de negocio

Si el HTML diferencia por banca, usar estos colores:

| Banca | Color principal |
|---|---|
| **Banca Personas** | `#006338` (verde corporativo) |
| **Banca Empresas** | `#003E89` (azul corporativo) |
| **Banca Privada** | `#61BF1A` (verde brand) |

Esta asignación NO está definida oficialmente por el banco — es una sugerencia basada en el peso visual de cada color. Validar con negocio antes de estandarizar.

## ⚡ Uso rápido para HTML-en-DAX

Las constantes recomendadas para usar en medidas DAX con HTML:

```dax
// Colores
VAR _VerdeOK = "#006338"
VAR _RojoKO = "#A20000"
VAR _NaranjaAlerta = "#C35A00"
VAR _GrisNeutro = "#A6A6A6"
VAR _AzulInfo = "#003E89"

// Familia de fuente
VAR _Font = "Segoe UI"

// Radios
VAR _Radius = "8px"
```

## 📚 Referencias

- Theme oficial: `Report/StaticResources/RegisteredResources/Promerica*.json`
- Paleta completa (480 colores) disponible en el theme JSON
- Estas guidelines aplican a TODOS los artefactos visuales generados por agentes (HTML, SVG, charts, etc.)
