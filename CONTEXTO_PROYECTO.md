---
name: Tablas de Nil — Project Overview
description: Complete context of the multiplication tables game built for Nil, including architecture, mechanics, assets, and key decisions
type: project
---

## Proyecto: TABLAS DE NIL

**Fichero único:** `index.html` (~1400 líneas JS + CSS). Todo autocontenido, sin build system.

**Why:** Juego personal de Gerardo para su hijo Nil, con tema NASA Artemis II. Se abre directamente en el navegador.

**How to apply:** Editar solo `index.html`. No separar en ficheros salvo petición explícita.

**GitHub:** https://github.com/kiwi8891/juego-tablas

---

## Flujo de pantallas

```
Welcome → Map → Brief → Game → Result → [Checkpoint si completa fase] → Summary → Celebration
```

| Pantalla | ID CSS | Función |
|----------|--------|---------|
| Welcome | `#welcome-screen` | Foto Nil + botón inicio |
| Mapa | `#map-screen` | Nodos de niveles en columna vertical |
| Brief | `#brief-screen` | Misión del nivel + botón iniciar |
| Juego | `#game-screen` | 10 preguntas, 4 opciones, cohete Orion |
| Resultado | `#result-screen` | Estrellas + botón siguiente/reintentar |
| Checkpoint | `#checkpoint-screen` | Celebración al completar una fase |
| Resumen | `#summary-screen` | Tabla resumen + botón recompensa |
| Celebración | `#celebration-screen` | Confeti + regalo + tarjeta descargable |

Cambio de pantalla: `showScreen(id)` — oculta todas, activa la indicada.

---

## Niveles (array LEVELS)

```javascript
var LEVELS = [
  { id:0, name:"LUNA I",      phase:"LUNA",     phaseIcon:"🌕", table:2, order:"orden"     },
  { id:1, name:"LUNA II",     phase:"LUNA",     phaseIcon:"🌕", table:2, order:"aleatorio" },
  { id:2, name:"TRAVESÍA I",  phase:"TRAVESÍA", phaseIcon:"🛸", table:5, order:"orden"     },
  { id:3, name:"TRAVESÍA II", phase:"TRAVESÍA", phaseIcon:"🛸", table:5, order:"aleatorio" },
  { id:4, name:"REGRESO I",   phase:"REGRESO",  phaseIcon:"🪂", table:3, order:"orden"     },
  { id:5, name:"REGRESO II",  phase:"REGRESO",  phaseIcon:"🪂", table:3, order:"aleatorio" },
];
```

Orden de tablas: **x2 → x5 → x3**

Checkpoints agrupan niveles por fase:
```javascript
var CHECKPOINTS = [
  { label:"LUNA",     levels:[0,1], icon:"🌕" },
  { label:"TRAVESÍA", levels:[2,3], icon:"🛸" },
  { label:"REGRESO",  levels:[4,5], icon:"🪂" },
];
```

---

## Mecánica de juego

- **10 preguntas** por nivel: `tabla × (1..10)`
- **4 opciones** (incluye siempre la correcta)
- **Estrellas:** 0 errores = 3★ | 1 error = 2★ | 2 errores = 1★ | 3+ errores = 0★ (fallo)
- **Racha:** 3+ correctas → "🔥 RACHA DE N!"
- Avance automático 1.3s tras acertar

---

## Estado global (`state`) y persistencia

- **localStorage key:** `"tablas_nil_v4"`
- `state.progress[levelId]` → `{ stars, time, perfect }`
- Solo sobreescribe si mejora la puntuación anterior

---

## Sistema de rangos

| Estrellas (max 18) | Rango |
|--------------------|-------|
| 18 | COMANDANTE ESPACIAL 🚀 |
| 14–17 | PILOTO EXPERTO 🌕 |
| 9–13 | ASTRONAUTA 🌍 |
| 4–8 | CADETE ESPACIAL 🛸 |
| 0–3 | EN ENTRENAMIENTO 📡 |

---

## Funciones JS clave

| Función | Qué hace |
|---------|---------|
| `showScreen(id)` | Cambia pantalla activa |
| `renderLevelMap()` | Dibuja nodos de niveles en el mapa |
| `showBrief(levelId)` | Muestra pantalla de misión previa al juego |
| `startGame()` | Inicializa partida |
| `renderQuestion()` | Renderiza pregunta y botones |
| `answer(chosen, correct, btn)` | Evalúa respuesta, actualiza UI |
| `endGame()` | Calcula estrellas, guarda, decide siguiente pantalla |
| `showCheckpointCelebration(cp)` | Celebración intermedia por fase |
| `showSummary()` | Tabla resumen final |
| `showCelebration()` | Pantalla final con confeti y regalo |
| `openGift()` | Revela tarjeta astronauta de Nil |
| `downloadCard()` | Descarga tarjeta como PNG (canvas) |
| `getMissionRank()` | Calcula rango según estrellas totales |
| `saveProgress()` | Guarda `state.progress` en localStorage |

---

## Diseño visual

- **Paleta:** `--bg: #080808` · `--gold: #c8a96e` · `--card: #141414` · `--green: #4caf7d` · `--red: #e05c5c`
- **Fuentes:** Syne (títulos) + DM Sans (cuerpo) — Google Fonts
- **Max-width:** 480px centrado — diseñado para móvil

---

## Assets y fotos

- Foto de bienvenida de Nil: base64 inline en `<img class="wel-photo">`
- `NIL_PHOTO_SRC`: variable JS con foto de Nil en base64 (para tarjeta descargable)
- `CELEBRATION_PHOTOS`: array de URLs NASA (fotos Artemis II reales)
- `LEVEL_PHOTOS[levelId]`: array de URLs NASA por nivel (se rotan en la pantalla de brief)

---

## Notas importantes para edición

- El fichero es muy grande. Usar `offset` + `limit` pequeños al leer con Read.
- Las imágenes base64 ocupan la mayor parte del tamaño del fichero.
- Todo está en un único scope global de JS (no módulos, no clases).
- `getElementById("cel-crew-photo")` requiere que el `<img>` tenga `id="cel-crew-photo"` (no solo class).
- Idioma: español. Nombre del jugador "NIL" hardcodeado.
