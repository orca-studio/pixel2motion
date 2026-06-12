# Pixel2Motion

Pixel2Motion is a Codex skill for turning raster logos into smooth, minimal SVG and then into choreographed logo animation delivered as standalone HTML.

The skill combines vector reconstruction discipline with motion-design QA. It first fits the lowest-complexity geometry that can faithfully explain the raster source, then structures every animatable part as a named actor for timeline choreography. The final animation must land exactly on the verified static vector.

Use this repository when the request includes logo reveal, brand intro, animated SVG/HTML, motion studies, loading/idle/hover motion, or showcase HTML with playback controls. For static vectorization without motion, use the companion `pixel2svg-html` skill instead.

## Project Preview

[![Pixel2Motion project preview](docs/preview.png)](https://nolanlai.github.io/pixel2motion/)

The interactive preview is published from `docs/index.html`. After pushing this repository to GitHub, enable GitHub Pages with source `main` / `docs`; the demo will be available at `https://nolanlai.github.io/pixel2motion/`.

### Claude Motion Set

Rendered from `docs/index.html` at each animation's default speed: Horizon 1900ms, Continuum 2000ms, Focus 1700ms, CueRecord 1800ms, N 2400ms.

<table>
  <tr>
    <td><strong>Horizon</strong><br><img src="docs/gifs/claude-horizon.gif" width="320" alt="Claude Horizon logo motion preview"></td>
    <td><strong>Continuum</strong><br><img src="docs/gifs/claude-continuum.gif" width="320" alt="Claude Continuum logo motion preview"></td>
  </tr>
  <tr>
    <td><strong>Focus</strong><br><img src="docs/gifs/claude-focus.gif" width="320" alt="Claude Focus logo motion preview"></td>
    <td><strong>CueRecord</strong><br><img src="docs/gifs/claude-cuerecord.gif" width="320" alt="Claude CueRecord logo motion preview"></td>
  </tr>
  <tr>
    <td><strong>N</strong><br><img src="docs/gifs/claude-n.gif" width="320" alt="Claude N logo motion preview"></td>
    <td></td>
  </tr>
</table>

## What It Produces

- `logo.svg`: final static vector, structured for motion
- `motion.css`: authored choreography targeting semantic SVG ids
- `logo_motion.html`: dependency-free showcase HTML with replay, slow motion, speed control, QA hooks, and atomic motion studies
- `motion_spec.md`: motion brief, principles applied, timeline, easing tokens, and QA notes
- `outputs/fit_iterations/*.png`: geometry overlay evidence
- `outputs/motion_frames/*.png` and `outputs/motion_strip.png`: deterministic motion QA evidence
- `outputs/final_render.png` and `outputs/html_render.png`: static render checks

## Repository Contents

- `SKILL.md`: Codex-facing pixel-to-vector-to-motion workflow
- `agents/openai.yaml`: UI metadata for the skill
- `references/`: animation principles, motion personality, reveal patterns, HTML delivery template, and fitting references
- `scripts/`: helpers for tracing, rendering, overlays, path audits, showcase HTML generation, deterministic frame capture, and motion continuity probing

## Requirements

- Python 3.10+
- `Pillow` and `numpy` for image analysis helpers
- Chrome or Chromium for geometry and HTML rendering
- Playwright for deterministic frame capture and motion continuity QA

Recommended local setup:

```bash
python3 -m venv .venv
.venv/bin/pip install pillow numpy playwright
.venv/bin/python -m playwright install chromium
```

If Chrome is not on the default path, set `CHROME_BIN` before running render checks:

```bash
export CHROME_BIN="/Applications/Google Chrome.app/Contents/MacOS/Google Chrome"
```

## Basic Workflow

1. Read `SKILL.md` and the relevant reference files before fitting or choreographing.
2. Write the motion brief in `motion_spec.md`: personality, usage context, part inventory, and choreography sketch.
3. Fit and QA the static vector:

```bash
python3 scripts/render_overlay.py logo.svg source.png \
  --out outputs/fit_iterations/01_overlay.png \
  --render-out outputs/final_render.png \
  --report outputs/fit_metrics.json
```

4. Build the showcase HTML from the verified SVG and authored CSS:

```bash
python3 scripts/animate_svg_showcase.py logo.svg \
  --css motion.css \
  --out logo_motion.html \
  --title "Logo Motion" \
  --duration-hint 1500
```

5. Capture deterministic motion frames:

```bash
python3 scripts/capture_motion_frames.py logo_motion.html \
  --times 0,300,700,1000,1250,1500 \
  --out outputs/motion_frames \
  --strip outputs/motion_strip.png \
  --compare-final outputs/final_render.png
```

6. Probe risky motion windows when the animation uses draw-on, crossings, masks, or handoffs:

```bash
python3 scripts/probe_motion_continuity.py logo_motion.html \
  --times 500,700,900 \
  --probe "#draw-stroke:stroke-dashoffset,#pen-glint:offset-distance"
```

## GitHub Upload Checklist

- Confirm `SKILL.md`, `agents/openai.yaml`, `references/`, and `scripts/` are committed.
- Keep `docs/index.html`, `docs/preview.png`, and `docs/gifs/*.gif` committed if the GitHub Pages and README previews should ship with the repository.
- Keep generated logo deliverables, motion captures, local virtual environments, caches, and per-logo `outputs/` out of git.
- Enable GitHub Pages from branch `main`, folder `/docs`, after the first push.
- Add a `LICENSE` file before publishing if this repository should grant reuse rights.
- After creating the GitHub repository, add the remote and push:

```bash
git remote add origin git@github.com:<owner>/pixel2motion.git
git branch -M main
git push -u origin main
```
