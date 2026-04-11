# ggplot2 Layer Explorer

Interactive browser tool for PSYC 200 (Psych Stats). Teaches students how ggplot2 builds plots by stacking layers, and why the placement of `aes()` matters. Students can toggle layers on/off and see real R code update live.

**Live URL:** https://psyc200-ggplot2-visualizer.netlify.app/
**Platform:** Netlify Drop (manual deploy — single `index.html`, no build step)

---

## Files

```
ggplot_visualizer/
├── index.html        # The entire app — self-contained, no dependencies
└── README.md         # This file
```

Everything lives in `index.html`. No framework, no npm, no build process. The Google Fonts link is the only external dependency (the page degrades gracefully without it).

---

## Updating on Netlify

1. Log into [app.netlify.com](https://app.netlify.com)
2. Click the **psyc200-ggplot2-visualizer** site
3. Go to the **Deploys** tab
4. Drag and drop the updated `index.html` onto the deploy drop zone at the bottom of the page

The URL stays the same — no student links break.

---

## What it teaches

| Control | Concept |
|---|---|
| Active layers (violin/point/stat) | Plots are built by stacking `+` layers |
| Layer order (violin→point vs point→violin) | Code order = draw order; layers obscure each other |
| Fixed vs. mapped color | `aes()` is for data mappings; fixed args go outside |
| Color which geoms? (both/violin/point) | Different geoms use `fill` vs `color`; each inherits or overrides |
| Map to species vs. sex | Same variable, same aesthetic, different groupings |
| alpha | Uniform style arg — always outside `aes()` |
| `aes()` location (global vs. local) | Global = write once, all layers inherit; local = repetitive and fragile |
| stat_summary() with local aes() | Intentional "break" — shows what happens when a layer has no aes() |
| theme_*() | Cosmetic layer — no effect on data or aesthetics |
| labs() | Another cosmetic layer; also reveals that default labels = variable names |

---

## Data

~60 simulated observations generated from a seeded PRNG in JavaScript, based on the `palmerpenguins` dataset parameters (Gorman et al. 2020). Three species: Adelie (~3700g), Chinstrap (~3730g), Gentoo (~5080g). The real dataset has 344 observations and is noted in the code comments. Students are told to expect different-looking output when they run the code in R.

---

## Architecture notes (for Claude)

**Read `lab/CLAUDE.md` first** — it has the full CSS variable set, component class reference, JS architecture pattern, and build strategy for all tools in this series. Do not read `index.html` to learn the style system.

- **State:** single `S` object; all UI handlers mutate it and call `render()`
- **render()** calls `renderCode()` (builds syntax-highlighted HTML lines) and `renderPlot()` (draws SVG from scratch)
- **Plot:** hand-rolled SVG — no charting library. KDE bandwidth = 185g. Violin half-width scaled to `PW*0.18`. Jitter uses a seeded per-point offset (`d._j`).
- **Code generation:** `buildCode()` returns an array of `{id, layer, t}` objects; layers map to CSS border-left color classes
- **Annotations:** `getAnn()` returns `{cls, html}` based on `S.changed`; shown below the code panel
- **Tooltips:** `data-tip` attributes on `<span>` elements inside the code; positioned on mouseover

Key layout constants: `VW=480, VH=360, ML=58, MR=24, MT=18, MB=52`

---

## Known pedagogical choices

- The "broken" `stat_summary()` state (no aes() in local mode) is intentional — students see the diamonds disappear and get a "Fix it" button
- Axis titles show the raw variable name (`species`, `body_mass_g`) by default and update to pretty names only when `labs()` is added — this makes the labs() layer feel meaningful
- The color mapping UI (species/sex, both/violin/point) is hidden until "Map to variable" is selected, to avoid overwhelming students on first load

---

## Other tools in this series

- **`lab/indexing_explorer/`** ✓ built — `$`, `[row, col]`, and Boolean subsetting with live table highlights and plain-English filter summaries

## Possible future tools (same format)

- **t-test explorer** — show the t-distribution, shaded p-value region, and live R code as students adjust means/SDs/n
- **Correlation/regression explorer** — scatterplot with live `lm()` line, residuals toggleable, R² display
