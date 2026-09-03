# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Arkanoid game: HTML, CSS, vanilla JavaScript, zero dependencies. Not implemented yet — repo currently only has assets and spec tooling, no game code, no build/test setup.

## Assets

- `assets/spritesheet-breakout.png` + `assets/spritesheet.js`: sprite atlas definitions (paddle, ball, blocks by color, explosion animation frames) and an async `loadSpritesheet(cb)` loader that draws the raw image into an offscreen canvas.
- `assets/sounds/`: `ball-bounce.mp3`, `break-sound.mp3`.

Since the target is zero dependencies, game code should consume `assets/spritesheet.js` directly via `<script>` tag, not bundled.

## Spec-driven workflow

This repo uses a two-step slash-command workflow (skills in `.agents/skills/`) instead of ad-hoc feature requests:

- `/spec <description>` — clarifies requirements through questions, writes `specs/NN-slug.md` (state starts as `Draft`).
- `/spec-impl <NN-slug>` — only runs if the spec's state means `Approved`; creates branch `spec-NN-slug`, implements the plan step by step with pauses for review, never commits automatically.

When asked to build a feature for this game, prefer routing through `/spec` first rather than writing code directly, unless the user explicitly asks for a quick/direct change. Full rules for each phase live in `.agents/skills/spec/SKILL.md` and `.agents/skills/spec-impl/SKILL.md` — read those before acting on either command.

No `specs/` folder exists yet; it is created by the first `/spec` run.
