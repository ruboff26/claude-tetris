# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Proyecto

Tetris en JavaScript vanilla + HTML5 Canvas + CSS. No hay `package.json`, build, linter ni tests. El README (en español) documenta controles y personalización.

## Ejecutar

Abrir `index.html` directamente en el navegador, o servir la carpeta con cualquier servidor estático (`python -m http.server 8000`, `npx serve .`).

## Arquitectura

Tres archivos: `index.html` (DOM + dos canvas: `#board` 300×600 y `#next-canvas` 120×120 + overlay), `style.css` y `game.js` (toda la lógica, script clásico con `'use strict'`, sin módulos).

Puntos de `game.js` que conviene conocer:

- **Estado global mutable** declarado en una sola línea con `let` (`board`, `current`, `next`, `score`, `lines`, `level`, `paused`, `gameOver`, `dropInterval`, `animId`...). `init()` lo reinicia todo y también sirve de reinicio (botón `#restart-btn`).
- **Piezas**: `PIECES[type]` son matrices cuyo valor de celda es el propio índice de tipo (1–8; la 8 es la tuerca, un anillo 3×3 con hueco central), que a su vez indexa `COLORS`. El tablero guarda ese mismo índice (0 = vacío). `randomPiece()` clona la forma y la coloca centrada en `y: 0`.
- **Ciclo de vida de una pieza**: `lockPiece()` = `merge()` → `clearLines()` → `spawn()`. `spawn()` promueve `next` a `current` y llama a `endGame()` si colisiona al aparecer.
- **Bucle**: `loop` con `requestAnimationFrame` acumula `dropAccum` y baja una fila al superar `dropInterval`. Pausa y game over cancelan el frame con `cancelAnimationFrame(animId)`; al reanudar se resetea `lastTime` y se llama a `loop` a mano.
- **Rotación**: `rotateCW` + wall kicks simples (`[0, -1, 1, -2, 2]` en columnas) en `tryRotate`.
- **Puntuación/velocidad**: `LINE_SCORES × level`; hard drop +2/celda, soft drop +1/fila; nivel = `floor(lines/10)+1`; `dropInterval = max(100, 1000 - (level-1)*90)`.

## Al modificar

- Si cambias `COLS`, `ROWS` o `BLOCK`, actualiza también `width`/`height` del `<canvas id="board">` en `index.html` (`COLS×BLOCK` × `ROWS×BLOCK`).
- `game.js` obtiene los elementos por `id` (`board`, `next-canvas`, `score`, `lines`, `level`, `overlay`, `overlay-title`, `overlay-score`, `restart-btn`); renombrarlos en el HTML rompe el script.
