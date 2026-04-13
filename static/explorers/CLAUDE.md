# PSYC 200 Interactive Tools — Reference for Claude

Self-contained single-file browser tools for teaching statistics concepts. Each tool is one `index.html` — no build step, no npm, deployed via Netlify Drop. The only external dependency is Google Fonts (the page degrades gracefully without it).

**Tools so far:**
- `ggplot_visualizer/index.html` — ggplot2 layer explorer — https://psyc200-ggplot2-visualizer.netlify.app/
- `indexing_explorer/index.html` — R indexing ($, [ , ], Boolean filter) — https://psych200-indexing-explorer.netlify.app/
- `dplyr_explorer/index.html` — dplyr pipeline builder (filter, select, mutate/case_when, group_by+summarize, arrange, separate, unite)
- `pivot_explorer/index.html` — pivot_longer / pivot_wider visualizer
- `sampling_distribution_explorer/index.html` — CLT + t-distribution explorer (in progress)
**Tools in progress / planned:**
- `file_path_explorer/index.html` — working directory and read_csv path navigator

---

## Style preferences

- **R pipes:** Always use `%>%` (tidyverse), not `|>` (native pipe), in all generated R code and tool annotations.
- **Em dashes:** Use sparingly. Prefer colons, semicolons, or plain words. When em dashes do appear, no spaces around them.

---

## Planned tool designs

### sampling_distribution_explorer

**Pedagogical goal:** Bridge CLT to the t-distribution. The CLT assumes you know σ. You almost never do. That extra uncertainty is exactly what inflates the tails of the t-distribution — not wider spread of means, but a second layer of variability from estimating σ with s.

**Phase 1 — CLT simulation (top of page)**
- Controls: population shape (Normal / Right-skewed / Uniform / Bimodal), n slider
- "Sample once" and "Sample 1000" buttons; no other controls
- Left SVG: population distribution (static density curve once shape is chosen)
- Right SVG: sampling distribution of x̄ building up as a histogram, with theoretical N(μ, σ²/n) overlay once enough samples accumulate
- Annotation explains CLT: means go normal regardless of population shape, spread shrinks as n grows

**Phase 2 — "But we never know σ" (scroll down, same page)**
This phase reframes Phase 1: z uses the true σ (known); t uses s (estimated, varies).
Three SVG panels side by side — all linked:
1. **z-statistic panel**: distribution of (x̄ − μ) / (σ/√n), overlaid with N(0,1)
2. **s panel**: distribution of sample s values, with a reference line at σ
3. **t-statistic panel**: distribution of (x̄ − μ) / (s/√n), overlaid with both t(df) and N(0,1) for comparison

**The causal chain — the whole point of the tool:**
When you hit "Sample once," all three panels highlight the SAME sample simultaneously:
- Panel 1: where that sample's x̄ (as a z-score) landed
- Panel 2: where that sample's s landed (annotated: "s < σ" or "s > σ")
- Panel 3: where the resulting t landed — and WHY it's further out than z when s was small

Students should be able to trace: "s was small → divided by something too small → t landed further out than z" in one concrete example before accumulating 1000 draws.

**What makes fat tails feel earned (not arbitrary):**
s can't go below 0 but has no upper bound. When s is small, t gets inflated (more extreme). The asymmetry in the errors is visible in the s-distribution panel — it's right-skewed, especially at small n. As n grows, s stabilizes (histogram tightens around σ) and the t-distribution converges to normal. df = n−1 controls this; show df explicitly alongside n.

**Layout:**
- Phase 1: standard 3-column grid (controls 244px | two stacked SVGs | annotation)
- Phase 2: full-width section with its own header, then three equal-width SVG panels, controls below, annotation below that. This is intentionally wider than 3-column to give the three panels room.
- The two phases are separated by a full-width "But we never know σ" divider band.

**Color tokens:**
- `--c-pop`: population fill (warm amber)
- `--c-samp`: sampling distribution of means (blue)
- `--c-z`: z-statistic (cyan)
- `--c-s`: sample s (orange/amber)
- `--c-t`: t-statistic (purple)
- `--c-hi`: highlight color for the linked single-draw (bright gold/yellow, used across all three panels simultaneously)

**Build stages (this tool is larger than most — follow these carefully):**
1. Write shell + head
2. Edit: complete CSS (includes Phase 2 three-panel layout and highlight animation)
3. Edit: complete body HTML (Phase 1 controls + SVGs, Phase 2 divider + three panels + controls)
4. Edit: data/state + Phase 1 render functions + sampling logic
5. Edit: Phase 2 render functions + linked highlight logic + annotations + init

**Do not conflate these two things — students always do:**
- The WIDTH of the sampling distribution (controlled by σ/√n — how much means vary)
- The FAT TAILS of the t-distribution (controlled by how noisy s is — a second layer of uncertainty)
These are different phenomena. The annotation system should call this out explicitly whenever the n slider is moved.

---

### file_path_explorer
Solves a very concrete spatial problem: students have no mental model of where they "are" in the file system. Show a realistic mock folder tree (e.g., Desktop / PSYC200 / data / penguins.csv) and let students click to "be in" different folders. The code panel shows what getwd() returns, and what read_csv() looks like from that location — relative path, ../relative path, and absolute path variants. The annotation explains why a path works or breaks.

---

## ggplot_visualizer — pedagogical notes

Do not "fix" the following — they are intentional design choices:

- **Broken stat_summary state:** When aes() placement is set to "local" and stat_summary() has no local aes(), the diamonds disappear. This is deliberate — students see the failure and get a "Fix it" button. The breakage is the lesson.
- **Axis titles show raw variable names by default:** `species`, `body_mass_g` etc. appear as axis labels until the labs() layer is added, at which point they update to pretty names. This makes the labs() layer feel meaningful rather than cosmetic.
- **Color mapping UI is hidden on first load:** The controls for mapping color to species/sex and targeting violin/point are only revealed after "Map to variable" is selected. This avoids overwhelming students before they've engaged with the basic layer concept.

**What the controls teach:**

| Control | Concept |
|---|---|
| Active layers (violin/point/stat) | Plots are built by stacking + layers |
| Layer order (violin→point vs point→violin) | Code order = draw order; layers obscure each other |
| Fixed vs. mapped color | aes() is for data mappings; fixed args go outside |
| Color which geoms? (both/violin/point) | Different geoms use fill vs color; each inherits or overrides |
| Map to species vs. sex | Same aesthetic, different groupings |
| alpha | Uniform style arg — always outside aes() |
| aes() location (global vs. local) | Global = write once, all layers inherit; local = repetitive and fragile |
| stat_summary() with local aes() | Intentional break — shows what happens when a layer has no aes() |
| theme_*() | Cosmetic layer — no effect on data or aesthetics |
| labs() | Cosmetic layer; also reveals that default labels = variable names |

---

## Do not read existing HTML files to learn the style

Read this file instead. All CSS variables and component classes below are copy-paste ready.

---

## How to write a new tool without hitting the output token limit

A finished tool is ~1000 lines of HTML and easily exceeds the 32K-token single-response limit if written in one shot. Always build in four sequential edits:

1. **Write** — document shell only: `<!DOCTYPE html>`, `<head>` with font/favicon links and an empty `<style>` block, `<body>` with placeholder comments, empty `<script>` block, and the footer (see below)
2. **Edit** — fill in the complete `<style>` block
3. **Edit** — fill in the complete `<body>` markup (header, all three panels, all controls, table/SVG DOM)
4. **Edit** — fill in the complete `<script>` block (data, state, render functions, handlers, init)

No single edit should exceed ~300 lines. If the script is large, split it across two edits at a natural boundary (e.g., data+state in one, render+handlers in the next).

### Starting from the template

`index_template.html` (lives one level above the tool folders, i.e. `lab/explorers/index_template.html`) has Steps 1 and 2 already done. Copy it into the new tool folder and rename to `index.html`. Steps 1 and 2 then collapse to just two customizations before moving on to Step 3:

- In `:root`, add the per-tool color tokens (`--c-thing1`, `--c-thing2`, etc.)
- Add any tool-specific CSS (tables, special components) in the marked section
- Update `<title>`, favicon `href`, header text, and `.pill` contents

Everything else in the template — layout, buttons, code panel, annotation box, syntax tokens, animations, `copyCode()`, tooltip listener — is already correct and should not be changed.

---

## CSS variables (`:root` block)

```css
:root {
  --bg:      #f0ebe1;   /* warm off-white page background */
  --panel:   #fdfcf9;   /* panel card background */
  --border:  #dbd3c3;   /* all borders and dividers */
  --text:    #1c1811;   /* primary text */
  --muted:   #79705f;   /* secondary/label text */
  --accent:  #b85520;   /* active state, CTA, .btn.on */
  --code-bg: #181826;   /* dark code panel background */
}
```

Per-tool color tokens (add to `:root` in each tool — pick colors that don't clash):

```css
/* ggplot_visualizer example */
--c-violin: #4eaaec;
--c-point:  #52c97e;
--c-stat:   #c89850;
--c-theme:  #b87fd4;
--c-labs:   #e8a030;

/* indexing_explorer example */
--c-dollar:  #4eaaec;
--c-bracket: #52c97e;
--c-filter:  #c89850;
```

---

## Fonts

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Fira+Code:wght@400;500&family=Lora:wght@500;600&family=DM+Sans:wght@400;500;600;700&display=swap" rel="stylesheet">
```

- **DM Sans** — body, buttons, labels: `font-family:'DM Sans',system-ui,sans-serif`
- **Fira Code** — all code, column names, inline code snippets: `font-family:'Fira Code',monospace`
- **Lora** — `<h1>` only: `font-family:'Lora',Georgia,serif; font-size:1.45rem; font-weight:600`

---

## Layout

Three-column CSS grid. Always this exact template unless otherwise noted:

```css
.grid {
  display:grid;
  grid-template-columns: 244px 1fr 420px; /* controls | main | code */
  gap:10px; padding:0 11px 15px;
  max-width:1360px; margin:0 auto;
}
.panel {
  background:var(--panel); border:1px solid var(--border);
  border-radius:10px; padding:14px;
}
.panel-label {
  font-size:0.66rem; font-weight:700; text-transform:uppercase;
  letter-spacing:0.12em; color:var(--muted); margin-bottom:11px;
}
```

Header above the grid:

```css
header {
  text-align:center; padding:15px 20px 9px;
  border-bottom:1px solid var(--border);
  background:var(--panel); margin-bottom:11px;
}
header p { font-size:0.86rem; color:var(--muted); margin-bottom:5px; }
.pill {
  display:inline-block; padding:2px 9px;
  background:#ece8de; border:1px solid var(--border); border-radius:20px;
  font-family:'Fira Code',monospace; font-size:0.7rem; color:var(--muted); margin:0 2px;
}
```

---

## Reusable component classes

| Class | Description |
|---|---|
| `.btn` | Default button — white bg, border, hover turns accent-colored |
| `.btn.on` | Active/selected state — accent bg, white text |
| `.hr` | 1px horizontal rule: `height:1px; background:var(--border); margin:8px 0` |
| `.sub` | Small muted helper text below a control label: `font-size:0.73rem; color:var(--muted)` |
| `.br` | Flex wrap row for a group of `.btn` elements: `display:flex; flex-wrap:wrap; gap:5px` |
| `.cs` | Control section wrapper: `margin-bottom:9px` |
| `.ch` | Control section heading: `font-size:0.8rem; font-weight:600; margin-bottom:7px` |
| `.copy-btn` | Small secondary button (copy/reset) — top-right of a panel. `padding:4px 11px; border-radius:6px; font-family:'DM Sans'; font-weight:600` |
| `.copy-btn.copied` | Flash state: accent bg, white text |

**Reset button placement:** Every tool has a Reset button in the upper-right of the controls panel, using `.copy-btn` inside a `.panel-header` — the same pattern as the Copy button in the code panel. Never place Reset at the bottom of the controls panel.

```html
<div class="panel-header">
  <div class="panel-label">Controls</div>
  <button class="copy-btn" onclick="resetAll()">Reset</button>
</div>
```
| `.ann` | Annotation box below code panel — hidden by default |
| `.ann.show` | Makes annotation visible |
| `.code-scroll` | Dark scrollable code area inside the code panel — `font-family:'Fira Code',monospace; font-size:0.79rem; line-height:1.9` |
| `.cl` | One line of code — add a `border-left` color class for stripe |
| `.cl-setup` | Dim gray, no stripe — for `library()` lines and comments |

**Segmented toggle — 2 options only (horizontal pill):**

```css
.tog2 { display:flex; border:1.5px solid var(--border); border-radius:7px; overflow:hidden; }
.tog2 .btn { flex:1; border:none; border-radius:0; text-align:center; border-right:1.5px solid var(--border); }
.tog2 .btn:last-child { border-right:none; }
.tog2 .btn.on { background:var(--accent); color:white; }
```

**3+ options or longer labels — always use a vertical stack, never a horizontal pill.** Horizontal pills truncate text in the 244px controls panel.

```css
.mode-stack { display:flex; flex-direction:column; gap:5px; margin-bottom:11px; }
.mbtn {
  width:100%; padding:6px 12px; border:1.5px solid var(--border); border-radius:6px;
  background:white; cursor:pointer; font-size:0.79rem; font-weight:500;
  color:var(--text); text-align:left; transition:all .1s;
}
.mbtn:hover { border-color:var(--accent); color:var(--accent); }
.mbtn.on { background:var(--accent); border-color:var(--accent); color:white; }
```

**Annotation box variants** (swap class on the `.ann` element alongside `.show`):

```css
.ann-welcome { border-color:var(--accent);    background:#fdf6f0; }
/* Per-tool variants follow the same pattern: border matches the concept's color token */
```

---

## Syntax token classes (dark code panel)

All rendered inside `.code-scroll` on a `#181826` background:

| Class | Color | Used for |
|---|---|---|
| `.sf` | `#c792ea` purple | function names |
| `.sv` | `#f78c6c` orange | data frame / variable names |
| `.sk` | `#89ddff` cyan | argument keywords |
| `.ss` | `#c3e88d` green | strings |
| `.sn` | `#f0ab70` peach | numeric literals |
| `.so` | `#89ddff` cyan | operators (`$`, `[`, `]`) |
| `.sb` | `#c3e88d` green | boolean operators (`==`, `!=`, `>`, `&`) |
| `.sc` | `#6a7a6a` dim gray | comments (add `font-style:italic`) |

Code line styles and flash animation:

```css
.cl { display:block; padding:1px 6px 1px 9px; border-radius:3px; border-left:3px solid transparent; color:#cdd3de; white-space:pre; }
.cl-setup { color:#6a7a6a; }
/* add .cl-thing1 etc. per tool, one per color token */
/* IMPORTANT: define .cl-thing1 etc. AFTER .cl in the stylesheet.
   .cl uses the border-left shorthand which resets border-left-color to transparent.
   Any color override class must come later in the cascade or it will be silently ignored. */

@keyframes flash { 0% { background-color:rgba(255,255,255,0.22); } 100% { background-color:transparent; } }
.cl-flash { animation:flash 0.7s ease-out; }
```

Tooltip (`#code-tip`) — use DM Sans, not Fira Code:

```css
#code-tip {
  position:fixed; z-index:9999; pointer-events:none; display:none;
  background:#2d2d42; color:#e0ddd5; border:1px solid #4a4860;
  padding:5px 10px; border-radius:6px; font-size:0.73rem;
  font-family:'DM Sans',sans-serif; line-height:1.4;
  max-width:300px; box-shadow:0 2px 8px rgba(0,0,0,0.45);
}
```

---

## JS architecture pattern

Every tool follows the same pattern — do not deviate from it:

```js
// 1. Single state object
const DEFAULTS = { /* all state keys and default values */ };
let S = Object.assign({}, DEFAULTS);

// 2. All UI handlers mutate S and call render()
function someHandler(btn) {
  S.someKey = btn.dataset.val;
  S.changed = 'descriptive-key'; // used by getAnn()
  render();
}

// 3. render() is the only function that touches the DOM
function render() {
  renderMain();   // updates the visual panel (SVG, table, etc.)
  renderCode();   // updates the code panel
  const {cls, html} = getAnn();
  const ann = document.getElementById('ann-box');
  ann.className = 'ann show ' + cls;
  ann.innerHTML = html;
}

// 4. getAnn() returns {cls, html} based on S.changed — no DOM access
function getAnn() {
  // Welcome/default state — shown on load and after Reset.
  // Always write a rich welcome message explaining how the tool works.
  // Template (adapt the color/concept descriptions per tool):
  if (S.mode===null || S.changed==='welcome') return {cls:'ann-welcome', html:`
    Here's how this works.<br><br>
    The code panel above shows real R code you can paste into R. The colored stripe on the
    left of each line marks which concept it belongs to — [color] for [concept], etc.<br><br>
    The [visual panel] in the middle [what it shows]. Use the controls on the left to change
    the options. The code, the [visual], and this explanation all update instantly each time
    you click something.`};
  // ... cases per control
}

// 5. resetAll() restores DEFAULTS, syncs all button .on classes, calls render()
function resetAll() {
  S = Object.assign({}, DEFAULTS);
  S.changed = 'welcome';
  // re-sync button states, then:
  render();
}

// 6. Init at bottom of script
render();
```

**Split state for complex tools:** Simple tools use one `S` object. For tools with a multi-step configurator UI (like dplyr_explorer), split into two objects: `S` holds pipeline/data state and drives all rendering; `Cfg` holds transient UI state (which step is being edited, which verb is selected, toggle direction, etc.) that doesn't need to trigger a full render. Handlers for `Cfg` changes update the DOM directly; only changes to `S.pipeline` call `render()`.

```js
let S   = { pipeline: [], changed: 'welcome' };  // drives render()
const Cfg = { editIdx: null, verb: null, dir: 'asc' };  // UI only
```

**Code generation pattern:** `buildCode()` returns an HTML string of `<code class="cl cl-LAYER">...</code>` lines. Syntax tokens are `<span class="sf">...</span>` etc. Tooltips use `data-tip` on a wrapper span, positioned on `mousemove` via a fixed `#code-tip` div.

**`data-val` pitfall:** When injecting button values via `innerHTML`, string values that contain `"` characters (e.g. `'"Adelie"'`) will break the HTML attribute. Always escape them:
```js
const attrVal = v.replace(/"/g, '&quot;');
// then: data-val="${attrVal}"
```

**Plain-English summaries:** For tools where the user is constructing a query or filter, add a plain-English readout below the visual panel translating the current state into a sentence. Example pattern from the indexing explorer:
```js
function plainEnglishCond(col, op, val) {
  const words = {'==':'is exactly','!=':'is not','>':'is greater than','>=':'is at least','<':'is less than','<=':'is at most'};
  return `${col} ${words[op]} ${val}`;
}
```

---

## Data pattern

Each tool uses a small fixed dataset generated from hardcoded values matching palmerpenguins distributions. Students are told real R output will look different (more rows). Six columns: `species` (Adelie/Chinstrap/Gentoo), `island` (Biscoe/Dream/Torgersen), `bill_length_mm`, `flipper_length_mm`, `body_mass_g`, `sex`.

The dataset is a JS `const DATA` array of arrays, with a parallel `const COLS` array of column names and a `COL_IDX` lookup object.

---

## Favicon

Each tool has a favicon. Add to `<head>`:
```html
<link rel="icon" href="tool_icon.png">
```

**Package pills:** Add `.pill-pkg` to any `.pill` that names an R package (not a function). This gives it a cool blue tint to visually distinguish the package from function names.

```html
<span class="pill pill-pkg">psych</span>
<span class="pill">alpha()</span>
```

```css
.pill-pkg { background: #e2ecf4; border-color: #b8cedd; color: #3a6278; }
```

---

## Footer

Every tool has a copyright footer as the last element inside `<body>`, after `</script>`:

```html
  <footer style="text-align:center;padding:10px 0 14px;font-size:0.72rem;color:var(--muted);font-family:'DM Sans',system-ui,sans-serif;">&copy; 2026 Violet Brown</footer>
```

---

## Deployment

Netlify Drop
