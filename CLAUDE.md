# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Arkanoid game: HTML, CSS, vanilla JavaScript, zero dependencies. Playable — `index.html` + `style.css` + `game.js` implement paddle, ball, bricks, scoring, sound, win/game-over screens. No build/test setup; open `index.html` directly (or serve statically) to play.

## Files

- `index.html`: canvas (`#game`, 800x600) + end-screen overlay (`#end-screen`, message/score/restart button).
- `style.css`: layout for canvas + end-screen overlay.
- `game.js`: entire game loop, state, physics, collisions, scoring, sound triggers.

## Game state (`game.js`)

- `gameState`: `'playing' | 'gameover' | 'win'`.
- `paddle`, `ball`, `bricks`, `score` rebuilt by `resetGame()`.
- `ball.attached` — ball rides the paddle until launched (Space or canvas click); `launchBall()` releases it.
- Bricks: 5 rows x 10 cols. Rows map to colors via `ROW_COLORS` (red/yellow/cyan/magenta/green), each with its own `BLOCK_SCORES`. 3 random bricks become `gray` (indestructible), 3 become `brown` (2 hits, shows cracked sprite after first hit). `hitsRequired`/`hitsTaken`/`breakable` drive collision resolution.
- Destroying a brick plays an explosion animation (`EXPLOSION_FRAMES`, timed via `explodeStart`/`EXPLOSION_DURATION`) and adds its score.
- Collisions: `circleRectCollide` (circle-vs-AABB) for ball-vs-paddle and ball-vs-brick; paddle bounce angle depends on hit position (`hitPos` → up to 60°).
- Win = every `breakable` brick destroyed; lose = ball falls past `CANVAS_H`.
- Sound: `playSound()` clones and plays `bounceSound`/`breakSound` at low volume so overlapping hits don't cut each other off — fires on wall/paddle/gray-brick/brown-first-hit bounces and on brick destruction.

## Assets

- `assets/spritesheet-breakout.png` + `assets/spritesheet.js`: sprite atlas definitions (paddle, ball, blocks by color, explosion animation frames) and an async `loadSpritesheet(cb)` loader that draws the raw image into an offscreen canvas. `game.js` calls `loadSpritesheet(cb)` once at the bottom and starts `resetGame()` + the render loop inside the callback.
- `assets/sounds/`: `ball-bounce.mp3`, `break-sound.mp3`.

Since the target is zero dependencies, game code should consume `assets/spritesheet.js` directly via `<script>` tag, not bundled.

## Spec-driven workflow

This repo uses a two-step slash-command workflow (skills in `.agents/skills/`) instead of ad-hoc feature requests:

- `/spec <description>` — clarifies requirements through questions, writes `specs/NN-slug.md` (state starts as `Draft`).
- `/spec-impl <NN-slug>` — only runs if the spec's state means `Approved`; creates branch `spec-NN-slug`, implements the plan step by step with pauses for review, never commits automatically.

When asked to build a feature for this game, prefer routing through `/spec` first rather than writing code directly, unless the user explicitly asks for a quick/direct change. Full rules for each phase live in `.agents/skills/spec/SKILL.md` and `.agents/skills/spec-impl/SKILL.md` — read those before acting on either command.

`specs/` holds one file per feature (`NN-slug.md`), each with an `Estado`/`Depende de` header. All four specs implemented so far:
1. `01-mvp-jugable` — paddle/ball/bricks core loop, win/gameover/restart.
2. `02-destruccion-bloques-y-puntaje` — explosion animation + score on brick destroy.
3. `03-bloques-de-colores-y-obstaculos` — 5 colored brick rows + gray/brown obstacle bricks.
4. `04-sonidos-de-rebote-y-rotura` — bounce/break sound effects.
