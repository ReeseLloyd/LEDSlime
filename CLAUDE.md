# LEDSlime — Prototype

## What We're Building

A browser-based Physarum polycephalum (slime mould) simulator with an LED dot-matrix aesthetic. Thousands of agents sense, steer, and deposit chemical trail — the emergent result is a self-organising transport network that looks alive. Four presets (Network, Radiant, Blobs, Tendrils) each produce distinct emergent behaviors. The goal is a mesmerizing, generative screensaver-style piece in the LED project family aesthetic.

## Tech Constraints

- Vanilla HTML, CSS, and JavaScript only — no frameworks, no libraries, no CDNs
- Single `.html` file — do not split into multiple files
- No npm, no build process, no transpilation
- Must work when opened directly as a local file (`file://`) with no server
- Seeded deterministic PRNG (mulberry32) — never use `Math.random()` in generation/placement code; `Math.random()` is only acceptable for the wander/steer coin-flip randomness that intentionally varies each run
- Every change increments the version number in both `ledslime.html` and `README.md`
- Standard LED dot-matrix rendering: 100×100 grid, 6px cells, soft circular kernel, ambient blue bleed

## Current State

v0.3. Four working presets with distinct parameter sets. Speed slider (10–400%) with step accumulator for sub-1× speeds. Seed-derived random color on each restart. Per-channel gamma curves for richer color depth. Wander parameter prevents equilibrium lock-in.

## What's Next

TBD — improvements to be discussed.

## Decisions Log

<!-- Format: [date] Decision made — reason / what was tried before -->
[2026-03-28] Added to GitHub — project ready for tracked development.
[v0.3] Wander parameter added — prevents equilibrium lock-in, keeps Tendrils perpetually searching.
[v0.2] Speed slider with step accumulator — sub-1× speeds needed fractional accumulation, not just skipping frames.
[v0.2] Blobs placement changed from collapsing ring to random clusters — ring converged too quickly and lost visual interest.
[v0.1] Seeded PRNG for placement + color — reproducibility and aesthetics both require determinism.

## What Hasn't Worked

- Single radiant center (v0.1) — too symmetric, not visually interesting. Replaced with multi-center in v0.3.
- Blobs with ring placement — agents collapsed to center too fast. Random clusters produce better merging behavior.
