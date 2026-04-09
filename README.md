# LaplaceDraw

Turn any doodle into a swarm of rotating vectors. LaplaceDraw takes a drawing — either sketched on the canvas or uploaded as an image — and redraws it using a chain of Fourier epicycles, showing the underlying equation as it traces the curve.

![LaplaceDraw](https://img.shields.io/badge/single--file-HTML-blue) ![No build](https://img.shields.io/badge/build-none-brightgreen)

## What it does

Every closed 2D curve can be decomposed into a sum of rotating complex exponentials via the Discrete Fourier Transform. LaplaceDraw:

1. Captures a path (freehand or from an uploaded image)
2. Resamples it via arc-length parameterization
3. Runs a DFT to extract amplitude + phase + frequency for each component
4. Animates the result as a chain of rotating circles whose tip traces the original drawing
5. Displays the reconstructed equation live as you watch

## Features

- **Draw mode** — sketch directly on the canvas (mouse or touch)
- **Image upload** — drop in a black-on-white line drawing; the app skeletonizes it (Zhang–Suen thinning), finds connected components, and walks them into a single path
- **Epicycle slider** — trade fidelity for simplicity, from 1 up to hundreds of terms
- **Speed slider** — control cycle duration
- **Walkthrough mode** — zooms in on the pen tip and slows down automatically in proportion to the epicycle count, so you can follow complex drawings
- **Live equation display** — readable, copyable HTML with proper superscripts, showing every term
- **Reference overlay** — original drawing stays visible alongside the animation

## Running it

No build tools, no dependencies. Just serve the directory:

```bash
python3 -m http.server 8080
```

Then open <http://localhost:8080> in a browser.

Or simply open `index.html` directly — everything (HTML, CSS, JS) lives in a single file.

## How it works

### Path extraction
- **Freehand**: raw pointer events collected during drag
- **Image**: grayscale → saturation-aware threshold → min-dilation (preserves thin strokes) → Zhang–Suen skeletonization → flood-fill components → Euler-tour DFS walk with backtrack compression → greedy nearest-endpoint chain linking

### DFT
Each point is treated as a complex number `z_n = x_n + i·y_n`, centered around the centroid. The coefficients are

```
C_k = (1/N) Σ z_n · e^(-2πikn/N)
```

Frequencies above `N/2` are remapped to negative values for bidirectional rotation, and terms are sorted by amplitude so the slider always keeps the most significant components first.

### Animation
A `requestAnimationFrame` loop with delta-time stepping chains the epicycles end-to-end each frame, draws the circles + radius lines, and records the tip into a trail that persists for the full cycle.

## Tech

- Vanilla HTML + CSS + JavaScript
- HTML5 Canvas with DPR-aware scaling
- Zero dependencies, single `index.html` (~900 lines)

## Repository

<https://github.com/sdurgi17/LaplaceDraw>
