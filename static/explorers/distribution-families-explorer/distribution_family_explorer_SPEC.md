# Distribution Family Explorer — Build Spec
## For Claude Code: read CLAUDE.md and index_template.html first, then follow this spec.

---

## Pedagogical goal

Show students that the major statistical distributions are not separate inventions — they form one connected family. Everything descends from Bernoulli. The key insight is visual: students watch each distribution *become* the next one by adjusting parameters, and for chi-square, t, and F they watch the distribution *build point by point* from the parent distribution.

The most important moments:
- Binomial → Normal: crank n up, watch the bar chart become a bell curve
- Binomial → Poisson: hold n*p constant while n↑ and p↓, watch them become identical
- Normal → Chi-square: **the key animation** — draw k standard normals, square them, sum → one chi-square value. Do that 1000 times. Students watch the chi-square histogram build from scratch and understand *why* it has that shape
- Chi-square → t: t = Z / sqrt(χ²/df) — show the two components and the resulting t
- Chi-square → F: ratio of two chi-squares — show both and the resulting F

---

## Color tokens (add to `:root`)

```css
--c-bern:   #e8a030;   /* Bernoulli — amber */
--c-binom:  #4eaaec;   /* Binomial — blue */
--c-pois:   #52c97e;   /* Poisson — green */
--c-norm:   #b87fd4;   /* Normal — purple */
--c-chi:    #e05c5c;   /* Chi-square — red */
--c-t:      #5b9bd5;   /* t — steel blue */
--c-f:      #c89850;   /* F — gold */
--c-hi:     #ffe066;   /* highlight for active node */
```

---

## Layout

**Header:** "Distribution Family Explorer" / "How the major statistical distributions are related — and why" / pills: `stats`, `pnorm()`, `pt()`, `pchisq()`, `pf()`

**Full-width tree panel** (above the 3-column grid): A clickable SVG family tree. Always visible. Highlights the active node. Clicking a node navigates to that distribution.

**3-column grid** (standard layout from template): left controls, middle visual, right code panel.

**Below grid:** Annotation box (standard).

---

## The family tree (SVG, full-width panel)

Draw as a directed graph, top to bottom. Nodes are colored pills matching color tokens. Edges are arrows. Active node gets `--c-hi` ring.

```
          [Bernoulli]
         /           \
    [Binomial]      (large n, small p, np=λ fixed)
    /        \                 \
[Normal]   [Poisson]          [Poisson]  ← same node, two paths lead here
    |
[Chi-square]  (also labeled: "sum of squared standard normals")
   / \
  [t] [F]
```

More precisely:
- Bernoulli → Binomial (sum n Bernoulli trials)
- Binomial → Normal (n large, CLT)
- Binomial → Poisson (n large, p small, np = λ)
- Normal → Chi-square (sum of k squared standard normals)
- Chi-square → t (Z / sqrt(χ²/df))
- Chi-square → F (ratio of two chi-squares / their df)

Label each edge with the transition condition in small italic text:
- Bernoulli→Binomial: "sum n trials"
- Binomial→Normal: "n large (CLT)"
- Binomial→Poisson: "n↑, p↓, np=λ fixed"
- Normal→Chi-square: "square & sum k normals"
- Chi-square→t: "Z / √(χ²/df)"
- Chi-square→F: "χ²₁/df₁ ÷ χ²₂/df₂"

On click: set `S.dist` to the clicked distribution and call `render()`.

---

## Distributions and their panels

Each distribution has:
1. **Left panel controls** — sliders/buttons specific to that distribution
2. **Middle panel visual** — described below per distribution
3. **Right panel code** — R code for that distribution (density, probability, quantile functions)
4. **Annotation** — explanation of what's shown and the key relationship

---

### 1. Bernoulli

**Controls:** p slider (0.1–0.9, step 0.05)

**Visual:** Two bars. Bar for 0 (height = 1−p, color muted) and bar for 1 (height = p, color `--c-bern`). Label each bar with its probability. Show: "P(X=1) = p = [value]"

**Code:**
```r
# Bernoulli is a special case of Binomial with n = 1
p <- 0.3
dbinom(1, size = 1, prob = p)   # P(success)
dbinom(0, size = 1, prob = p)   # P(failure)
rbinom(n = 10, size = 1, prob = p)  # simulate 10 trials
```

**Annotation:** A Bernoulli trial has exactly two outcomes: success (probability p) and failure (probability 1−p). It's the simplest possible random variable — a single coin flip. Every other distribution in this family is built from Bernoulli trials in some way.

---

### 2. Binomial

**Controls:**
- n slider (1–100)
- p slider (0.05–0.95, step 0.05)
- Toggle: "Show Normal overlay" (appears only when n ≥ 20)

**Visual:** Bar chart of Binomial(n, p) probabilities. X-axis: 0 to n. When overlay is on and n ≥ 20: overlay N(np, np(1−p)) as a smooth curve in `--c-norm`.

Key annotation moment: when n is cranked high, the bars become a bell curve. The overlay makes this explicit.

Show: mean = np, SD = sqrt(np(1−p))

**Code:**
```r
n <- 20; p <- 0.5
# Probability of exactly k successes
dbinom(k, size = n, prob = p)
# Cumulative probability
pbinom(k, size = n, prob = p)
# Simulate
rbinom(n = 100, size = n, prob = p)
```

**Annotation (n small):** Each bar shows the probability of getting exactly that many successes in n independent Bernoulli trials. This is the Binomial distribution — a sum of n Bernoulli trials.

**Annotation (n large, overlay off):** As n grows, the Binomial starts to look bell-shaped. This is the Central Limit Theorem at work: sums of many independent random variables approach a Normal distribution, regardless of what those variables look like individually.

**Annotation (overlay on):** The purple curve is the Normal distribution with the same mean (np = [val]) and SD (√(np(1−p)) = [val]). At large n, the two are nearly identical — which is why we can use Normal probabilities to approximate Binomial probabilities.

---

### 3. Poisson

**Controls:**
- λ (lambda) slider (0.5–20, step 0.5)
- Toggle: "Show Binomial with same mean" — shows Binomial(n=500, p=λ/500) overlay
- Toggle: "Show Normal overlay" (appears when λ ≥ 10)

**Visual:** Bar chart of Poisson(λ) probabilities. With Binomial overlay: bars in `--c-binom` behind Poisson bars in `--c-pois`. The two should be nearly identical — that's the point.

Show: mean = λ, variance = λ (note they're equal — this is a Poisson property worth calling out)

**Code:**
```r
lambda <- 5
dpois(k, lambda = lambda)     # P(X = k)
ppois(k, lambda = lambda)     # P(X ≤ k)
rpois(n = 100, lambda = lambda)  # simulate
```

**Annotation:** Poisson describes counts of rare events in a fixed window — calls per hour, mutations per gene, emails per day. It looks like Binomial because it *is* the limit of Binomial when n is very large and p is very small, but np (the expected count) stays fixed at λ. Notice that mean = variance = λ; this equality is a signature of the Poisson distribution.

**Annotation (Binomial overlay on):** The blue bars show Binomial(n=500, p=λ/500), which has the same mean (λ) but a fixed n. They're nearly identical — Poisson is what you get when n→∞ and p→0 with np held constant.

---

### 4. Normal

**Controls:**
- μ slider (−3 to 3, step 0.5) — labeled "mean (μ)"
- σ slider (0.5 to 3, step 0.5) — labeled "SD (σ)"
- Checkbox: "Standardize" — when checked, overlays N(0,1) and shows z = (x−μ)/σ formula
- Toggle: "Show area" with cutoff slider (show P(X < value))

**Visual:** Smooth density curve in `--c-norm`. When standardized overlay on: show N(0,1) in muted dashed line. When area shown: shade the tail.

**Code:**
```r
mu <- 0; sigma <- 1
# Density at x
dnorm(x, mean = mu, sd = sigma)
# P(X < q) — area to the left
pnorm(q, mean = mu, sd = sigma)
# Quantile (inverse)
qnorm(p, mean = mu, sd = sigma)
# Simulate
rnorm(n = 100, mean = mu, sd = sigma)
```

**Annotation:** The Normal distribution is symmetric and bell-shaped, defined entirely by its mean (μ) and standard deviation (σ). The standard Normal N(0,1) is a special case with μ=0 and σ=1. Any Normal can be standardized: z = (x−μ)/σ shifts and scales it to N(0,1). This distribution sits at the center of the family tree because the CLT says that sums of many random variables — whatever their distribution — converge to Normal.

---

### 5. Chi-square ← THE KEY ANIMATION

**Controls:**
- df slider (1–20) — labeled "degrees of freedom (k)"
- Button: "Draw one sample" — draws k values from N(0,1), shows them, squares them, sums → one χ² value added to histogram
- Button: "Draw 1000 samples" — runs the above 1000 times rapidly
- Button: "Reset histogram"
- Toggle: "Show theoretical χ²(k) curve" — overlays the true density

**Visual (three sub-panels stacked or side by side):**

**Sub-panel A — "The ingredients" (top):**
When "Draw one sample" is clicked: show k dots on a N(0,1) number line. Highlight each dot. Then animate: each dot gets squared (show the squaring step: x → x²). Show the k squared values. Show their sum → this is one χ² value. This sub-panel resets on each new draw.

**Sub-panel B — "The chi-square histogram" (bottom):**
Accumulates drawn χ² values as a histogram in `--c-chi`. After enough draws, the theoretical curve overlay (toggle) shows the match.

Show below histogram: current sample's χ² value highlighted with a gold tick mark.

**Code:**
```r
k <- 5   # degrees of freedom

# Simulate one chi-square value from scratch:
z <- rnorm(k, mean = 0, sd = 1)   # draw k standard normals
z_squared <- z^2                   # square each one
chi_sq_value <- sum(z_squared)     # sum them → one χ²(k) value

# Built-in chi-square functions:
dchisq(x, df = k)        # density
pchisq(q, df = k)        # P(X < q)
rchisq(n = 100, df = k)  # simulate directly
```

**Annotation (on load):** Chi-square is not a separate invention — it's what you get when you square standard normal values and add them up. If you draw k values from N(0,1), square each one, and sum, that sum follows a chi-square distribution with k degrees of freedom. Click "Draw one sample" to watch it happen one value at a time.

**Annotation (after drawing):** That χ² value = [val] came from squaring and summing [k] standard normal values: [list them]. Each squared value is always positive (squaring removes the sign). The distribution is right-skewed because squared values pile up near zero but occasionally get large. As df grows, the distribution shifts right (its mean = df) and becomes more symmetric — eventually looking Normal again (another CLT result: sum of many chi-squares).

**Annotation (why this matters):** The sample variance s² is a sum of squared deviations from the mean. It turns out that (n−1)s²/σ² follows χ²(n−1). So chi-square isn't just a mathematical curiosity — it's the sampling distribution of the sample variance. Just as the Normal distribution describes how sample means vary, chi-square describes how sample variances vary.

---

### 6. t-distribution

**Controls:**
- df slider (1–30) — labeled "degrees of freedom (n−1)"
- Button: "Draw one sample" — draws one Z ~ N(0,1) and one χ²(df), computes t = Z / sqrt(χ²/df)
- Button: "Draw 1000 samples"
- Button: "Reset"
- Toggle: "Show N(0,1) overlay" — overlays standard normal to show fat tails

**Visual (two sub-panels):**

**Sub-panel A — "The ingredients":**
Show two number lines side by side: one for Z (the numerator, in `--c-norm`) and one for χ²/df (the denominator, in `--c-chi`). When "Draw one sample" is clicked: highlight where this sample's Z and χ²/df landed. Show the division: t = Z / sqrt(χ²/df) = [val]. Animate the t value dropping into the histogram below.

**Sub-panel B — "t histogram":**
Accumulating histogram in `--c-t`. Normal overlay in muted dashed line when toggled. The fat tails should be visually obvious at low df.

**Code:**
```r
df <- 10   # n - 1

# Simulate one t value from scratch:
z   <- rnorm(1)              # numerator: one standard normal
chi <- rchisq(1, df = df)    # chi-square with df degrees of freedom
t_value <- z / sqrt(chi / df)  # this IS the t-distribution

# Built-in t functions:
dt(x, df = df)          # density
pt(q, df = df)          # P(X < q)  — use for p-values
qt(p, df = df)          # critical values
rt(n = 100, df = df)    # simulate
```

**Annotation:** A t-statistic divides a standard normal (Z) by the square root of a chi-square divided by its degrees of freedom. The numerator is the signal (how far the mean is from zero, in standard units). The denominator is noisy — it's estimated from data, and at small sample sizes that estimate is unstable. When the denominator is small by chance, the t-statistic gets inflated. That's what makes the tails fat — not that means vary more, but that the denominator (based on s) varies. As df grows, s stabilizes, and the t-distribution converges to N(0,1).

---

### 7. F-distribution

**Controls:**
- df1 slider (1–20) — labeled "numerator df"
- df2 slider (1–40) — labeled "denominator df"
- Button: "Draw one sample" — draws χ²(df1) and χ²(df2), computes F = (χ²₁/df1) / (χ²₂/df2)
- Button: "Draw 1000 samples"
- Button: "Reset"

**Visual (three sub-panels):**

**Sub-panel A:** Two mini histograms side by side — χ²(df1)/df1 in `--c-chi` (tinted lighter) and χ²(df2)/df2 in `--c-chi`. Label them "numerator chi-square / df1" and "denominator chi-square / df2". Highlight the current sample's values on each draw.

**Sub-panel B:** Arrow showing division: F = [χ²₁/df1] / [χ²₂/df2] = [val]

**Sub-panel C:** Accumulating F histogram in `--c-f`. Theoretical F(df1, df2) overlay toggle.

**Code:**
```r
df1 <- 3; df2 <- 20

# Simulate one F value from scratch:
chi1 <- rchisq(1, df = df1)
chi2 <- rchisq(1, df = df2)
f_value <- (chi1 / df1) / (chi2 / df2)  # this IS the F-distribution

# Built-in F functions:
df(x, df1 = df1, df2 = df2)    # density
pf(q, df1 = df1, df2 = df2)    # P(X < q)
qf(p, df1 = df1, df2 = df2)    # critical values — F* in ANOVA tables
rf(n = 100, df1 = df1, df2 = df2)  # simulate
```

**Annotation:** The F-distribution is the ratio of two independent chi-square distributions, each divided by its degrees of freedom. In ANOVA, the F-statistic compares two variances: variance between groups (numerator) divided by variance within groups (denominator). Both are sample variances, which follow chi-square distributions — so their ratio follows F. An F-statistic that's much larger than 1 means the between-group variance is larger than you'd expect by chance.

**Bonus annotation:** Notice that t² = F(1, df). A t-statistic squared is an F-statistic with 1 numerator df. This makes sense: t = Z/sqrt(χ²/df), so t² = Z²/(χ²/df) = χ²(1)/df ÷ χ²(df)/df, which is exactly F(1, df).

---

## State object

```js
const DEFAULTS = {
  dist: 'bernoulli',   // which distribution is active
  changed: 'welcome',

  // Bernoulli
  bern_p: 0.3,

  // Binomial
  binom_n: 10,
  binom_p: 0.5,
  binom_overlay: false,

  // Poisson
  pois_lambda: 3,
  pois_binom_overlay: false,
  pois_norm_overlay: false,

  // Normal
  norm_mu: 0,
  norm_sigma: 1,
  norm_standardize: false,
  norm_area: false,
  norm_cutoff: 0,

  // Chi-square
  chi_df: 3,
  chi_samples: [],       // accumulated chi-square values
  chi_last: null,        // most recent draw details {z_vals, z_sq, sum}
  chi_overlay: false,

  // t
  t_df: 10,
  t_samples: [],         // {z, chi, t} for each draw
  t_last: null,
  t_norm_overlay: false,

  // F
  f_df1: 3,
  f_df2: 20,
  f_samples: [],         // {chi1, chi2, f} for each draw
  f_last: null,
  f_overlay: false,
};
```

---

## Code panel

The right panel always shows R code for the currently active distribution. Code lines use the color token for that distribution (`.cl-bern`, `.cl-binom`, etc.). The "Draw one sample" / "Draw 1000" actions do NOT change the code — the code shows how to use the distribution's built-in R functions, always. The "from scratch" simulation is shown as commented lines to illustrate the relationship.

---

## Build stages (follow CLAUDE.md pattern — 4 edits max per session)

This is a large tool. Suggested split across two Claude Code sessions:

**Session 1:**
1. Write — shell + head (copy from index_template.html, customize title/colors/pills)
2. Edit — complete CSS (add tree panel styles, sub-panel styles, animation for the "squaring" step)
3. Edit — complete body HTML (tree SVG, all 7 distribution panels hidden/shown by JS, controls for all 7)
4. Edit — JS part 1: state, tree click handler, Bernoulli + Binomial + Poisson render + code functions

**Session 2:**
5. Edit — JS part 2: Normal + Chi-square render + animation logic (the key one — take time here)
6. Edit — JS part 3: t + F render + annotation system + init

---

## Notes for Claude Code

- The tree SVG should be drawn with hardcoded coordinates — don't try to compute layout dynamically. Approximately 800px wide, 200px tall. Node positions (approximate centers):
  - Bernoulli: (400, 30)
  - Binomial: (280, 90)
  - Poisson: (520, 90)  
  - Normal: (280, 150)
  - Chi-square: (280, 210)
  - t: (180, 270)
  - F: (380, 270)

- For the chi-square animation, use `setTimeout` chains — don't use CSS animation for the squaring step, it needs to be JS-driven so the histogram can update.

- The "Draw 1000" button should use a loop with no animation (just compute and push to the samples array, then render once at the end — do NOT call render() 1000 times).

- Each distribution panel is a `<div class="dist-panel" id="panel-[name]">` that is shown/hidden via `display: none / block` based on `S.dist`. The left controls panel, middle visual panel, and right code panel all swap their contents when the active distribution changes.

- Poisson note: be careful not to overstate the Binomial→Poisson connection. The edge label should say "n large, p small, np = λ" and the annotation should explain that Poisson has its own identity (events in a continuous interval, no fixed n of trials) — the Binomial connection is a *convergence*, not an equivalence.

- The t² = F(1, df) identity in the F annotation is optional/bonus — only add it if it fits cleanly.
