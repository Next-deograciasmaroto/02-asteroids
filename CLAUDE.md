# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running

No build, bundler, linter, or tests. Open `index.html` in a browser, or serve locally with `npx serve .` (http://localhost:3000). Not a git repository.

## Architecture

Asteroids clone in plain HTML5 Canvas; all logic lives in a single script, `game.js` (loaded by `index.html` onto an 800×600 `<canvas id="canvas">`). UI text and comments are in Spanish — keep that convention.

`game.js` is organized in sections:
- **Input**: `keys` (held) and `justPressed` (edge-triggered). `pressed(code)` consumes the just-pressed flag, so call it once per frame per key (used for Space to shoot / restart).
- **Entities** (`Bullet`, `Asteroid`, `Ship`, `Particle`): each has `update(dt)`, `draw()`, and a `dead` flag; they draw directly with the global `ctx`. Positions wrap toroidally via `wrap(v, max)` (except `Particle`, which doesn't wrap).
- **Game state**: module-level globals (`ship`, `bullets`, `asteroids`, `particles`, `score`, `lives`, `level`) plus `state` = `'playing' | 'dead' | 'gameover'`. `update(dt)` branches on `state`; `'dead'` is a 2s respawn timer during which asteroids keep moving.
- **Loop**: `requestAnimationFrame` with `dt` clamped to 0.05s; `update(dt)` then `draw()`.

Gameplay details that span the file:
- Asteroid size is 1–3, indexed into the parallel arrays `RADII`, `SPEEDS`, `POINTS` (index 0 unused). Destroyed asteroids `split()` into two of `size - 1`; size 1 yields none.
- Collisions are circle-based using `dist()` and `radius`; ship-vs-asteroid uses `a.radius * 0.82` forgiveness and is skipped while `ship.invincible > 0` (also drives respawn blinking).
- `nextLevel()` fires when `asteroids` is empty and spawns `3 + level` large asteroids outside a safe radius around screen center.

Note: README mentions power-ups and a shooting-star asteroid type, but they are not implemented in the code.
