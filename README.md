# LEDSlime v0.5

A browser-based Physarum polycephalum (slime mould) simulator with an LED dot-matrix aesthetic. Thousands of microscopic agents sense, steer, and deposit chemical trail — the emergent result is a self-organising transport network that looks alive.

No server, no build step, no dependencies. Open `ledslime.html` directly in any browser.

---

## Quick Start

Open `ledslime.html` in a modern browser (`File > Open` or drag onto browser window). The simulation starts automatically in Network mode.

---

## Controls

| Control | Description |
|---|---|
| **Preset dropdown** | Switch between the four simulation modes (resets with current seed) |
| **Speed slider** | Scale simulation speed from 5% to 200%. Uses a step accumulator so sub-1× speeds work smoothly (some frames run zero steps). Default: 50%. |
| **↺ Restart** | Pick a new random seed and restart. Changes the initial agent placement *and* the display color (derived from the seed). |

---

## Presets

### Network
Dense, globally-connected vein networks. 4,000 agents placed randomly across the grid. A longer sensor distance (9 cells) lets agents find distant established trails, producing complex long-range connections rather than only local capillaries. Low deposit keeps a visible brightness gradient — paths have lanes of varying intensity rather than a flat-bright highway. 1.2% wander prevents full crystallisation.

**Best at:** default speed or slightly slower. Watch the network self-optimise over several minutes.

### Radiant
Spoke-like structures radiating from 2–4 random source points (seeded per restart). Agents in each cluster point outward; as the radiant fronts from adjacent sources reach each other they connect, producing branching lattices between sources. The tight sensor angle (22°) keeps the spokes straight and geometric.

**Best at:** default speed. The inter-source connection phase is the visual payoff — typically happens 30–60 seconds in.

### Blobs
Wide sensor angle (55°) and aggressive rotation (80°) cause agents to orbit and cluster rather than form straight paths. 3,500 agents distributed across 7–11 random clusters, each of which develops a local swirling blob. Over time blobs expand, touch, and merge through connecting bridges.

**Best at:** default or slightly faster. Blobs form quickly; the merger phase is interesting at any speed.

### Tendrils
Long, sweeping arms that search across the canvas. Agents start along all four edges pointing inward, with a very low rotation angle (20°) that keeps motion nearly straight. A 12-cell sensor distance lets agents detect faint trails from far away.

The key to this preset's ongoing life is the combination of fast decay (0.84 — paths fade without active reinforcement) and 6% wander (agents periodically abandon established paths and strike out in a fresh direction). Old tendrils fade; new ones constantly emerge. The system never fully settles.

**Best at:** 10–30% speed. At very low speed each tendril is clearly visible as it forms. At high speed the canvas quickly fills with a dense, constantly-shifting web.

---

## How Physarum Simulation Works

Each agent has a 2D position and a heading angle. Every simulation tick:

1. **Sense** — sample trail intensity at three points ahead: straight forward, ahead-left (by `sensorAngle`), and ahead-right. Each sensor is `sensorDist` cells away.
2. **Steer** — if the strongest signal is ahead, continue straight. If it's to the left, rotate left by `rotationAngle`. If right, rotate right. If tied and non-forward, pick randomly.
3. **Wander** (optional) — with probability `wander`, skip sensing entirely and pick a completely random new heading. Prevents equilibrium lock-in.
4. **Move** — advance by `stepSize` cells in the new heading direction. The grid wraps (torus topology).
5. **Deposit** — add `deposit` to the trail map at the agent's new cell (clamped to 1.0).

After all agents have moved, the trail map is updated:

6. **Diffuse** — blend each cell with the equal-weight average of its 3×3 neighbourhood at rate `spread`. This causes trail to bleed softly to adjacent cells.
7. **Decay** — multiply every trail cell by `decay` (< 1). Unvisited paths fade over time.

The feedback loop — agents follow trail, deposit more trail, which attracts other agents — produces self-reinforcing transport networks from random initial conditions.

---

## Rendering

The standard LED dot-matrix pipeline used across the LED project family:

- **Grid:** 100 × 100 cells
- **Cell size:** 6 × 6 pixels → 600 × 600 px canvas
- **Kernel:** pre-computed soft circular falloff (`1 - (d/r)²`) stamps each cell as a glowing LED dot
- **Ambient bleed:** a faint blue value `(1 - k) * 5` is added to the blue channel in the gaps between dots, recreating the ambient panel glow of a physical LED matrix
- **Color rendering:** trail intensity is mapped through per-channel gamma curves (blue 0.37, green 0.44, red 0.52) so the blue channel develops first at low intensities, creating a cool underglow. High-intensity paths bleach toward white (whitening exponent 1.8). The base hue is randomized from the seed on each restart.

---

## Preset Parameters

| Parameter | Network | Radiant | Blobs | Tendrils |
|---|---|---|---|---|
| Agents (n) | 4,000 | 3,000 | 3,500 | 1,800 |
| Sensor angle | 30° | 22° | 55° | 45° |
| Rotation angle | 45° | 40° | 80° | 20° |
| Sensor distance | 9 | 9 | 5 | 12 |
| Step size | 1.0 | 1.1 | 0.9 | 1.3 |
| Decay | 0.93 | 0.91 | 0.95 | 0.84 |
| Spread | 0.14 | 0.10 | 0.18 | 0.08 |
| Deposit | 0.08 | 0.09 | 0.08 | 0.07 |
| Wander | 1.2% | 0.8% | 1.5% | 6.0% |
| Placement | random | multi-center | clusters | edges |

---

## Version History

| Version | Changes |
|---|---|
| **v0.1** | Initial prototype. Four presets (Network, Radiant, Blobs, Tendrils). Seeded PRNG placement, LED kernel rendering, restart button. |
| **v0.2** | Speed slider (10–400%) with step accumulator for sub-1× speeds. Blobs placement changed from collapsing ring to random clusters. Color randomized from seed via HSL→RGB on each restart. |
| **v0.3** | Wander parameter added to all presets — prevents equilibrium lock-in, keeps Tendrils perpetually searching. Radiant changed to multi-center placement (2–4 sources). Network: more agents, longer sensor reach, lower deposit. Tendrils: faster decay + lower deposit so paths must be actively re-forged. Per-channel gamma curves for richer color depth. |
| **v0.4** | Speed slider rebased: range changed from 10–400% to 5–200%, default changed from 100% to 50%. The new 50% matches the old 10% — overall simulation runs slower by default, better for watching the network self-organise. |
| **v0.5** | Blobs: stagnation-triggered wander burst. An exponential moving average tracks trail-map delta per tick; when it drops below a threshold (system has settled), wander spikes from 1.5% to 25% for ~150 ticks, scattering agents to seed new formations and mergers. After the burst a cooldown prevents immediate re-triggering. |
