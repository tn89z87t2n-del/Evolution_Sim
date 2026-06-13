# 🧬 Neuro-Evolution · Artificial Life

A mesmerizing **artificial life evolution simulation** in a single, self-contained
`index.html` — no build step, no dependencies, no external libraries. Open the file in
any modern browser and watch creatures with tiny neural-network brains evolve
intelligent foraging behavior through pure natural selection.

> **There is no explicit fitness function.** Surviving and reproducing *is* the
> selection. Creatures that find food live and reproduce; those that don't, die.
> Over generations, foraging intelligence emerges on its own.

## ▶️ Run it

Just open `index.html` in a browser. That's it.

Or view it live via the raw-HTML preview:
**https://htmlpreview.github.io/?https://github.com/tn89z87t2n-del/evolution_sim/blob/main/index.html**

## ✨ What you'll see

- A **toroidal world** (wraps at the edges) larger than the viewport — drag to pan,
  scroll / pinch to zoom.
- **Food** spawns continuously in noise-based patches that slowly drift, so creatures
  must keep adapting to a moving food supply.
- Each creature is a **soft glowing cell** with a heading indicator and little "eye"
  dots that point toward what its sensors detect. Size scales with energy.
- **Lineage coloring**: offspring inherit their parent's hue with a slight mutation, so
  successful families become visible as colored clusters spreading across the map.
- A healthy ecosystem settles into emergent **boom-and-bust population cycles** while
  the maximum and average generation numbers climb steadily — proof that cumulative
  evolution is happening.

## 🧠 The brain (neural network, from scratch)

Each creature carries its own feed-forward network, stored as a single `Float32Array`
genome (82 weights):

```
inputs (7)            hidden (8, tanh)        outputs (2, tanh)
─────────────         ─────────────────       ─────────────────
food proximity   ─┐
food angle        │
creature prox.    ├──►  8 neurons  ──────────►  turn amount
creature angle    │                             acceleration
own energy        │
own speed         │
bias             ─┘
```

- **Inputs** are normalized: distance + angle to nearest food, distance + angle to
  nearest other creature (both found via the spatial hash grid), own energy, own speed,
  and a bias.
- **Reproduction** is asexual: at the energy threshold a creature splits its energy and
  spawns a mutated copy nearby.
- **Mutation**: each weight has an adjustable chance (default 10%) of a small Gaussian
  perturbation, plus a rare 1% chance of a full reroll.

## ⚡ Energy economy

- Moving costs energy proportional to **speed²**; merely existing costs a small constant.
- Eating food grants energy; poison (optional hazard) deals damage.
- Energy ≤ 0 → **death**. Reaching the reproduction threshold → **offspring**.
- A small **immigration floor** introduces fresh random-brained creatures if the
  population ever crashes, so the world never goes permanently extinct and evolution
  always has substrate to work with.

## 🚀 Performance

- A **spatial hash grid** powers *all* neighbor queries (food sensing, creature sensing,
  eating) — no O(n²) loops anywhere.
- Data is stored in flat typed arrays (structure-of-arrays) and buffers are reused to
  avoid garbage-collection churn in the main loop.
- Handles **500+ creatures and 2000+ food particles** at 60 fps. Crank the food-spawn
  rate and population cap in the UI to push it.
- **Speed multiplier** (1× / 2× / 5× / 10×) runs multiple physics steps per rendered
  frame to fast-forward evolution.

## 📊 Visualization & interaction

- **Live charts** (drawn on plain canvas, no chart libs): population, average
  generation, average lifespan, and food eaten per minute.
- **Click any creature** to inspect it: its neural network is drawn live with nodes and
  weighted connections, activations pulsing as it "thinks", alongside its stats (age,
  generation, energy, children, lineage).
- **Follow mode** tracks the selected creature with the camera.
- Optional **sensor rays** show what the selected creature currently perceives.

## 🎛️ UI

Dark, glassmorphism side panel with controls for:

- Food spawn rate, mutation rate, movement & metabolism energy costs, max population cap
- Hazards (poison) toggle, follow toggle, sensor-ray toggle
- Pause / reset and the speed multiplier
- **Save / Load**: serialize the entire world (creatures, genomes, food, noise field,
  parameters, camera) to a JSON file and reload it later to continue a long-running
  evolution.

A top stats bar shows live population, max generation, food count, FPS, and elapsed
simulation time.

## 🛠️ Code structure

Everything lives in `index.html`. The script is organized into clearly labeled sections:
neural network, world & spatial hash, creature, rendering, charts, and UI. Identifiers
are in English; explanatory comments are in Slovak.

## 📜 License

Free to use, modify, and learn from.
