# Eightfold Visual Engine of Moss

A design spine for a unified 8-mode visualization playground. Use this to brief an AI coding assistant or guide future implementation. The focus is one engine with modular modes that share base-60, prime-aware utilities, and a consistent UI.

## Architecture Overview

- **Stack:** Single-page web app (HTML/JS or TypeScript) with Canvas (WebGL optional). Keep modules readable and composable.
- **GlobalState:**
  - Time: `t`, `frame`, `dt`.
  - Canvas/viewport dimensions and aspect ratio.
  - Prime utilities (sieve + `isPrime`, `nextPrime`, etc.).
  - Base-60 helpers (`mod60`, hue/angle mapping).
  - Color system with prime emphasis.
  - Global params: `speed`, `zoom`, `primeEmphasis`, `lineThickness`, `modeBlend`, `audioReactive`, etc.
- **Mode Interface:**
  ```ts
  interface VizMode {
    id: string;
    name: string;
    params: any; // or typed per mode
    init(state: GlobalState): void;
    update(dt: number, state: GlobalState): void;
    draw(ctx: CanvasRenderingContext2D, state: GlobalState): void;
    handleInput?(event: InputEvent, state: GlobalState): void;
  }
  ```
- **Engine Loop:**
  - `update(dt)` → `modes[currentMode].update(dt, state)`.
  - `draw()` → `modes[currentMode].draw(ctx, state)`.
  - Mode switch (UI or keys 1–8) calls `init` on the newly active mode.
- **Shared Utilities:**
  - Prime sieve up to N (e.g., 100k).
  - Base-60 helpers: `mod60`, `stateToHue` mapping `n mod 60` → `0..360°`.
  - `colorForState(n, isPrime)` with brighter primes.
  - Coordinate helpers: grid↔canvas, polar↔Cartesian, simple 4D→3D→2D projection.
- **UI Skeleton:**
  - Mode selector (1–8) plus labels.
  - Sliders: `speed`, `zoom`, `primeEmphasis`, `lineThickness`.
  - Checkbox: `audioReactive` (stub or basic mic FFT).
  - Debug overlay: FPS, mode name, key params.

## Mode Catalog

Each mode consumes shared utilities and respects global params. Implement fully or scaffold with comments.

1. **Quantum Field (Phyllotaxis with Prime Glow)**
   - Points: `n = 1..N`, angle `θ = n * φ` (golden angle), radius `r = c * sqrt(n)`.
   - Prime indices draw brighter/larger; global rotation and mild modulation of `φ` or `c` over time.
   - Params: `maxPoints`, `primeGlowStrength`, `rotationSpeed`.

2. **Prime Lattice (16×16 Grid)**
   - Cell value `n = i + j * 16`. Color via `colorForState(n, isPrime(n))`.
   - Animated wave using `(n + frame) mod 60` for base-60 rhythm; optional prime symbols.
   - Params: `gridSize`, `cellPadding`, `waveSpeed`, `waveAmplitude`.

3. **Harmonic Web**
   - Nodes on concentric rings: `r_k = baseRadius * k`, angles from harmonic ratios `θ = 2π * (p/q)`.
   - Connect nodes with simple integer ratios; thickness/brightness tracks simplicity or prime links.
   - Params: `ringsCount`, `nodesPerRing`, `maxHarmonicDenominator`.

4. **CA Evolution (Prime-Driven Grid)**
   - Grid `W×H` (e.g., 60×60), states in `[0..59]`, Moore or von Neumann neighbors.
   - Example rule: if `isPrime(sumNeighbors)`, next = `(state + 1) mod 60`; else decay `(state - 1 + 60) mod 60`; boost if prime neighbor count crosses threshold.
   - Params: `width`, `height`, `primeNeighborThreshold`, `decayRate`, `stepsPerFrame`.

5. **Yantra Bloom (Sri Yantra Inspired)**
   - Interlocking triangles, concentric circles, optional outer square/gates.
   - Prime-indexed elements glow or pulse; gentle scale/rotation and bloom sequencing over time.
   - Params: `triangleCount`, `circleCount`, `bloomSpeed`.

6. **Cryptic Dance (Bitwise Choreography)**
   - Dancers = integers `[0..255]`; render bits as stacks or radial glyphs.
   - Update by XOR with prime or time-derived mask; colors from `value mod 60`.
   - Params: `dancerCount`, `xorMaskMode`, `bitGlyphType`.

7. **Hepta-Sync (Nested Heptagons)**
   - `L` concentric heptagons with radii `r_k` and rotations `θ_k(t)` using base-60 step fractions.
   - Highlight prime vertex/layer indices; connect vertices across layers for lattice feel.
   - Params: `layersCount`, `rotationSpeeds[]`, `vertexGlowStrength`.

8. **4D Tesseract (Hypercube Projection)**
   - 16 vertices (±1 in four dimensions); rotate in planes (XW, YW, ZW), project 4D→3D→2D.
   - Highlight prime-indexed vertices or edges whose index sums/XORs are prime.
   - Params: `rotationSpeedXW`, `rotationSpeedYW`, `rotationSpeedZW`, `projectionScale`, `depth`.

## Ready-to-Paste Master Prompt

Use this with an AI coding assistant to generate the app:

> **Prompt:**
> Build a single-page web app (HTML/JS or TS + Canvas, WebGL optional) that implements a unified visualization engine with 8 modes: Quantum Field, Prime Lattice, Harmonic Web, CA Evolution, Yantra Bloom, Cryptic Dance, Hepta-Sync, and 4D Tesseract. The engine shares prime/base-60 utilities, a global color system, and UI controls.
>
> **Architecture:**
> - Provide `GlobalState` with time (`t`, `frame`, `dt`), canvas sizing, prime sieve utilities (`isPrime`, `nextPrime`, etc.), base-60 helpers (`mod60`, angle mapping), palette functions (`stateToHue`, `colorForState` with prime emphasis), and global params (`speed`, `zoom`, `primeEmphasis`, `lineThickness`, `modeBlend`, `audioReactive`).
> - Define a `VizMode` interface (`id`, `name`, `params`, `init`, `update`, `draw`, optional `handleInput`). Maintain a `modes[]` registry and `currentModeIndex`; mode switches call `init` for the new mode.
> - Main loop: `update(dt)` then `draw()`, wiring through to the active mode. Target 60 FPS.
>
> **Shared Utilities:**
> - Prime sieve up to ~100k and helpers (`isPrime`, `primeIndicesInRange`).
> - Base-60 helpers: `mod60`, `stateToHue(n mod 60 → 0..360°)`.
> - `colorForState(n, isPrime)` with brighter primes; optional hue offset for primes.
> - Coordinate helpers: grid↔canvas, polar↔Cartesian, simple 4D→3D→2D projection for the tesseract.
>
> **UI:**
> - Mode selector (buttons or dropdown) with keyboard shortcuts 1–8.
> - Sliders: `speed`, `zoom`, `primeEmphasis`, `lineThickness`. Checkbox: `audioReactive` (stub mic FFT is fine).
> - Debug overlay showing FPS, active mode, and key params.
>
> **Modes (implement or scaffold):**
> 1) **Quantum Field:** Phyllotaxis spiral (`θ = n*φ`, `r = c*sqrt(n)`), prime indices glow; rotate and pulse over time. Params: `maxPoints`, `primeGlowStrength`, `rotationSpeed`.
> 2) **Prime Lattice:** 16×16 grid; value `n = i + j*16`; color via `colorForState`; animated wave using `(n + frame) mod 60`; optional prime glyphs. Params: `gridSize`, `cellPadding`, `waveSpeed`, `waveAmplitude`.
> 3) **Harmonic Web:** Nodes on concentric rings with angles from small integer ratios; connect prime/simple ratios; orbit/pulse over time. Params: `ringsCount`, `nodesPerRing`, `maxHarmonicDenominator`.
> 4) **CA Evolution:** Grid `W×H` (e.g., 60×60), states mod 60; rule example: if `isPrime(sumNeighbors)` → `(state + 1) mod 60`, else decay; boost on high prime-neighbor counts. Params: `width`, `height`, `primeNeighborThreshold`, `decayRate`, `stepsPerFrame`.
> 5) **Yantra Bloom:** Interlocking triangles/circles with prime-index bloom highlighting; gentle rotation/scale. Params: `triangleCount`, `circleCount`, `bloomSpeed`.
> 6) **Cryptic Dance:** Dancers as 8-bit integers; visualize bits (stack or radial); XOR updates with primes/time masks; colors from `value mod 60`. Params: `dancerCount`, `xorMaskMode`, `bitGlyphType`.
> 7) **Hepta-Sync:** Nested heptagons with base-60 rotations; prime vertex/layer highlighting; optional vertex connections. Params: `layersCount`, `rotationSpeeds[]`, `vertexGlowStrength`.
> 8) **4D Tesseract:** 4D hypercube rotated in planes (XW, YW, ZW), projected to 2D; prime-indexed vertices/edges highlighted. Params: `rotationSpeedXW`, `rotationSpeedYW`, `rotationSpeedZW`, `projectionScale`, `depth`.
>
> **Extensibility:**
> - Adding a mode = implement `VizMode`, register in `modes[]`, and give it default params.
> - Keep utilities centralized and documented for reuse.
> - Favor clarity: comment math choices (golden angle, base-60 mapping, prime emphasis) so future contributors can iterate.

