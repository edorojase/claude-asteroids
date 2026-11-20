# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the game

Open `index.html` directly in the browser, or use a local server:

```bash
npx serve .
```

There is no build step, no bundler, and no dependencies.

## Architecture

All game logic lives in a single file: `game.js`. The entry point is at the bottom — `initGame()` followed by `requestAnimationFrame(loop)`.

**Game loop:** `loop(ts)` → `update(dt)` → `draw()`, called via `requestAnimationFrame`. `dt` is capped at 50 ms to prevent spiral-of-death on tab blur.

**State machine:** a single `state` variable drives `update()` — values are `'playing'`, `'dead'`, and `'gameover'`.

**Entity classes** (all in `game.js`):
- `Ship` — player, with invincibility timer and shoot cooldown
- `Asteroid` — irregular polygon, splits into two smaller ones (`size - 1`) on hit; size 1 does not split
- `Bullet` — TTL-based projectile
- `Particle` — explosion debris, TTL-based

**Collision** is simple circle vs circle (`dist(a, b) < r1 + r2`). The ship collision uses `radius * 0.82` for a forgiving hitbox.

**Coordinate wrapping:** `wrap(v, max)` handles toroidal space for all moving objects.

**Input:** `keys` holds continuous state (held keys), `justPressed` holds one-frame state consumed by `pressed(code)`.

## Constants worth knowing

| Constant | Location | Purpose |
|---|---|---|
| `W`, `H` | top of `game.js` | Canvas dimensions (800 × 600) |
| `RADII[size]` | Asteroid section | Collision radius per asteroid size (1–3) |
| `SPEEDS[size]` | Asteroid section | Base speed per size |
| `POINTS[size]` | Asteroid section | Score per size |
