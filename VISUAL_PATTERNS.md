# Visual Patterns Reference

CSS/HTML recipes that make monday.com presentation templates look polished. Use alongside [SLIDE_INVENTORY.md](SLIDE_INVENTORY.md) (which covers structure/content) when reproducing a specific visual effect.

## Contents
1. [Opening & Title Slide Recipes](#1-opening--title-slide-recipes)
2. [Data Visualization Recipes](#2-data-visualization-recipes)
3. [Metrics & Stats Display](#3-metrics--stats-display)
4. [Card & Panel Recipes](#4-card--panel-recipes)
5. [Image Placeholder System](#5-image-placeholder-system)
6. [Typography Weight-Mixing](#6-typography-weight-mixing)
7. [Process & Diagram Recipes](#7-process--diagram-recipes)
8. [Animation Entrance Patterns](#8-animation-entrance-patterns)

---

## 1. Opening & Title Slide Recipes

Seven visual signatures. Each describes the CSS that creates the effect.

### 1A. Centered Minimal (001)
**When:** Simple title + subtitle, no imagery needed.
```css
.slide-container { display: flex; flex-direction: column; justify-content: center; align-items: center; }
.content-wrapper { text-align: center; margin-bottom: 12vmin; }
h1 { font-weight: var(--weight-regular); letter-spacing: -0.03em; }
.highlight { color: var(--color-yellow); font-weight: var(--weight-semibold); }
p { font-weight: var(--weight-light); opacity: 0.9; }
```
Logo absolutely positioned at `bottom: var(--space-8)`.

### 1B. Bordered Card + Accent Panel (007)
**When:** Casual opening with large display text + supporting info below.
```css
.slide-container { display: flex; flex-direction: column; gap: var(--space-4); }
.top-section { flex: 2.5; border: 1px solid var(--color-text); border-radius: var(--radius-lg); padding: var(--space-6); }
.bottom-section { flex: 1; background-color: var(--color-purple); border-radius: var(--radius-lg); padding: 0 var(--space-6); }
h1 { font-size: var(--text-display); font-weight: var(--weight-regular); line-height: 1.2; }
```
Logo absolutely positioned top-left inside bordered card.

### 1C. Display Type Grid with Image Slots (010)
**When:** Workshop/event opening with date, department, and visual previews.
```css
.slide-container { display: grid; grid-template-columns: 0.9fr 1.1fr; grid-template-rows: 1fr 1fr; gap: 2.5vmin; }
.title-card { grid-row: span 2; border: 1px solid var(--color-text); border-radius: var(--radius-lg); padding: var(--space-9); }
h1 { font-size: var(--text-display); line-height: 1.05; letter-spacing: -0.03em; }
```
Top-right: two side-by-side image placeholders (`data-image-placeholder="photo"`). Bottom-right: purple info card with department + date.

### 1D. Speaker Card with Portrait (011)
**When:** Single speaker introduction with photo.
```css
.slide-container { display: flex; flex-direction: column; gap: var(--space-3); }
.top-card { flex: 1.6; border: 1px solid var(--color-text); border-radius: var(--radius-xl); padding: var(--space-9) var(--space-10); }
.bottom-section { flex: 1; display: flex; gap: var(--space-2); }
.profile-placeholder { width: 28vmin; height: 28vmin; border-radius: 50%; }
.info-card { flex: 1; background-color: var(--color-purple); border-radius: var(--radius-lg); padding: var(--space-8) var(--space-9); }
```
Profile uses `data-image-placeholder="portrait"`.

### 1E. Dual Speaker (012)
**When:** Two speakers. Same as 1D but with grid bottom section.
```css
.bottom-section { display: grid; grid-template-columns: 1fr 1fr; gap: var(--space-3); }
.profile-placeholder { width: 22vmin; height: 22vmin; border-radius: 50%; }
.speaker-group { display: flex; gap: var(--space-1); }
```
Smaller circles (22vmin vs 28vmin) to fit two speakers.

### 1F. Product Opening with Dashed Image Overlap (009)
**When:** Product/feature intro with large screenshot placeholder.
```css
.slide-container { display: flex; align-items: center; position: relative; }
.content-left { z-index: 2; display: flex; flex-direction: column; gap: 5.5vmin; }
.image-placeholder {
  position: absolute; right: -6vmin; top: 50%; transform: translateY(-50%);
  width: 62vmin; height: 62vmin;
  border: 2px dashed rgba(255, 255, 255, 0.15); border-radius: var(--radius-lg);
}
```
The dashed border signals "replace this with an actual image." Placeholder text at `rgba(255,255,255,0.25)`.

### 1G. Orbital Graphic System (099)
**When:** Grand opening with brand personality (shapes, logo orbit).
```css
.slide-container { display: flex; width: 100%; gap: var(--space-7); }
.text-content { flex-basis: 42%; }
.graphic-content { flex-basis: 58%; background-color: var(--color-surface); transform: translateY(-4vmin); }
/* Orbit: 29vmin central circle, 54x52vmin elliptical orbit path */
.orbit-path { border: 1px solid rgba(255,255,255,0.7); border-radius: 50%; transform: rotate(10deg); }
```
Colored shapes (yellow rectangles, purple circles, SVG hexagon/star) positioned absolutely around orbit.

---

## 2. Data Visualization Recipes

### 2A. Horizontal Timeline with Dot-Connectors (050)
**When:** Historical timeline, product milestones, 4-6 events.

```css
/* Ultra-thin horizontal line */
.timeline-line {
  position: absolute; top: 7.6vw; left: 7%; width: 86%;
  height: 0.1vw; background-color: var(--color-text);
}
/* Endpoint dots via pseudo-elements */
.timeline-line::before, .timeline-line::after {
  content: ''; position: absolute; width: 0.4vw; height: 0.4vw;
  border-radius: 50%; background-color: var(--color-text); top: 50%;
}

/* Each milestone: date bubble → connector → dot → content */
.milestone { width: 14%; display: flex; flex-direction: column; align-items: center; }
.date-bubble { border: 1px solid var(--color-text); border-radius: var(--radius-full); padding: 0.45vw 1.5vw; }
.date-bubble.today { background: linear-gradient(135deg, var(--color-purple-light), var(--color-purple)); border: none; }
.connector { width: 0.1vw; height: 4.5vw; background-color: var(--color-text); }
.dot { width: 0.6vw; height: 0.6vw; border-radius: 50%; background-color: var(--color-text); }
```

**HTML structure:** Milestones in a `flex; space-between` container. Each has date-bubble, connector line, dot, and content block below.

### 2B. Circular Step Timeline (051)
**When:** Process steps (3-5 steps), workflow stages.

```css
:root { --circle-size: calc(1vmin * 26); }

/* Purple line behind circles */
.timeline-line {
  position: absolute; z-index: 0;
  top: calc(var(--circle-size) / 2); left: calc(var(--circle-size) / 2); right: calc(var(--circle-size) / 2);
  height: calc(1vmin * 0.3); background-color: var(--color-purple);
}

/* Step circles */
.circle { width: var(--circle-size); height: var(--circle-size); border-radius: 50%; }
.circle.active { background-color: var(--color-purple); }
.circle.inactive { border: 1px solid var(--color-purple); background-color: var(--color-bg); }
.circle .icon { width: calc(1vmin * 9); height: calc(1vmin * 9); }
```

Steps in `flex; center; gap: var(--space-8)`. Each circle contains an icon + step label. Title + description below each circle.

### 2C. Alternating Timeline (052)
**When:** Dense timeline with many events, alternating above/below the line.

```css
.line { width: 100%; height: 2px; background-color: var(--color-purple-light); }
.dot { width: 1.2vmin; height: 1.2vmin; background-color: var(--color-purple-light); border: 1px solid var(--color-bg); border-radius: 50%; }

/* Dashed connectors via gradient trick */
.milestone::before {
  content: ''; position: absolute; width: 2px;
  background-image: linear-gradient(to bottom, var(--color-purple-light) 40%, transparent 40%);
  background-size: 2px 8px; background-repeat: repeat-y;
}
.milestone.top::before { top: 100%; height: 10vmin; }
.milestone.bottom::before { bottom: 100%; height: 10vmin; }

/* Dates colored purple-light */
.date { color: var(--color-purple-light); font-weight: var(--weight-semibold); }
```

Items positioned absolutely with `left: N%` values. Milestones alternate `.top` (above) and `.bottom` (below).

### 2D. Concentric Ring Chart (054)
**When:** Proportional data, nested categories, market segments.

```css
/* Three stacked circles */
.ring { position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%); border-radius: 50%; }
.ring.outer { width: 48vmin; height: 48vmin; background-color: var(--color-purple); }
.ring.middle { width: 34vmin; height: 34vmin; background-color: var(--color-purple-light); }
.ring.inner { width: 16vmin; height: 16vmin; background-color: var(--color-bg); }

/* Annotation positions */
.annotation-left { top: 50%; right: calc(50% + 17vmin); flex-direction: row-reverse; }
.annotation-top-right { top: calc(50% - 15vmin); left: calc(50% + 18vmin); }
.annotation-bottom-right { top: calc(50% + 15vmin); left: calc(50% + 18vmin); }
```

**SVG arrow connectors** (define once in hidden SVG defs):
```html
<svg style="position: absolute; width: 0; height: 0; overflow: hidden;">
  <defs>
    <marker id="arrow" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto">
      <path d="M 0 0 L 10 5 L 0 10 z" fill="currentColor"/>
    </marker>
    <marker id="dot" viewBox="0 0 10 10" refX="5" refY="5" markerWidth="8" markerHeight="8">
      <circle cx="5" cy="5" r="4.5" fill="currentColor"/>
    </marker>
  </defs>
</svg>
```

Each annotation uses: `<svg class="line-svg" width="12vmin" height="1vmin" viewBox="0 0 120 10"><path d="M 115 5 L 5 5" marker-start="url(#dot)" marker-end="url(#arrow)"/></svg>`

### 2E. Bar Chart with Animated Bars (080)
**When:** Comparative data across categories/time periods.

```css
:root { --c-axis-text: #B1B1B9; --c-grid-line: #4A455A; }

.chart-container { background-color: var(--color-surface); border-radius: var(--radius-md); padding: 2.5vw; }
.y-axis { display: flex; flex-direction: column-reverse; justify-content: space-between; width: 4.5vw; color: var(--c-axis-text); }
.grid-line { height: 1px; background-color: var(--c-grid-line); }

/* Bar groups */
.bar-group { display: flex; align-items: flex-end; gap: 0.3vw; flex-basis: 12.5%; }
.bar-wrapper { width: 2.8vw; }
.bar {
  width: 100%; border-radius: var(--radius-sm) var(--radius-sm) 0 0;
  transform-origin: bottom;
  animation: chartBarGrow 0.6s ease-out both;
}
.bar-label { position: absolute; top: -1.5vw; font-weight: var(--weight-semibold); }

/* Staggered animation delays */
.bar-group:nth-child(1) .bar { animation-delay: 0.1s; }
.bar-group:nth-child(2) .bar { animation-delay: 0.18s; }
/* increment: +0.08s per group */

/* Category colors */
.bar.cat1 { background-color: var(--color-purple); }
.bar.cat2 { background-color: var(--color-green); }
.bar.cat3 { background-color: var(--color-purple-light); }

/* Legend */
.legend { display: flex; justify-content: center; gap: 2vw; }
.legend-color-box { width: 0.9vw; height: 0.9vw; border-radius: 0.15vw; }
```

Bar heights set via inline `style="height: N%"` where N = (value / maxValue) * 100.

---

## 3. Metrics & Stats Display

### 3A. Metric Rows in Panel (032)
**When:** 3-4 key metrics alongside explanatory text.
```css
.right-column {
  background-color: var(--color-surface); border-radius: var(--radius-lg);
  padding: var(--space-9) var(--space-8); height: 90%;
  display: flex; flex-direction: column; justify-content: space-around;
}
.metric-value { font-size: var(--text-h1); font-weight: var(--weight-medium); letter-spacing: -0.02em; line-height: 1.1; }
.metric-desc { font-size: var(--text-body); opacity: 0.8; }
```
`justify-content: space-around` distributes 3-4 metrics evenly in the column.

### 3B. Hero Metric (033)
**When:** Single impressive number dominates the slide.
```css
.right-column {
  background-color: var(--color-surface); border-radius: var(--radius-lg);
  padding: var(--space-9); height: 90%;
  display: flex; flex-direction: column; justify-content: center; text-align: center;
}
.metric-value { font-size: 15vmin; font-weight: var(--weight-medium); line-height: 1; }
.metric-desc { font-size: var(--text-h4); opacity: 0.8; max-width: 80%; margin: 0 auto; }
```
The `15vmin` font size scales responsively and creates massive visual impact.

### 3C. Title + Content Panel (031)
**When:** Title left, bulleted content in surface panel right.
```css
.slide-container { display: grid; grid-template-columns: 1fr 1fr; gap: var(--space-9); }
.right-column {
  background-color: var(--color-surface); border-radius: var(--radius-lg);
  padding: var(--space-8); height: 75%;
  display: flex; flex-direction: column; justify-content: center; gap: var(--space-5);
}
```

---

## 4. Card & Panel Recipes

### 4A. Bordered Card (framing against dark bg)
```css
border: 1px solid var(--color-text);
border-radius: var(--radius-lg);  /* 3vmin */
padding: var(--space-6);          /* 4vmin minimum */
```
**Use for:** Title cards, primary containers that need visual weight. Logo goes inside, absolutely positioned top-left. Examples: 007 top-section, 010 title-card, 011 top-card.

### 4B. Surface Card (content panel)
```css
background-color: var(--color-surface);  /* #232427 */
border-radius: var(--radius-lg);
padding: var(--space-8);                 /* 6vmin standard */
```
**Use for:** Right-side content panels, feature grids, metric panels, chart containers. No border needed -- the surface color creates enough contrast. Examples: 031-033, 044-046, 074.

### 4C. Purple Accent Card (info/CTA)
```css
background-color: var(--color-purple);   /* #6164ff */
border-radius: var(--radius-lg);
padding: var(--space-8) var(--space-9);
```
**Use for:** Speaker info, department labels, calls-to-action, active states. Text stays white. Examples: 007 bottom-section, 010 bottom-right, 011-012 info-card.

### 4D. Image Card (media grid)
```css
.card { background-color: var(--color-surface); border-radius: var(--radius-md); overflow: hidden; }
.card-image { height: 30vh; background-size: cover; background-position: center; }
.card-text { padding: var(--space-6); }
```
**Use for:** 3-column card grids, portfolio displays. `overflow: hidden` clips the image to the card radius. Data attribute: `data-image-placeholder="photo"`. Example: 068.

### 4E. Feature Grid (2x2 or 2x3)
```css
.grid-panel {
  background-color: var(--color-surface); border-radius: var(--radius-lg);
  padding: var(--space-8); height: 90%;
  display: grid; grid-template-columns: 1fr 1fr; gap: var(--space-6);
}
.feature-item { display: flex; flex-direction: column; gap: var(--space-3); }
```
**Use for:** Feature lists with icons, numbered items. 2x2 for 4 items (044), 2x3 for 6 items (047). Left column is always a narrow title: `grid-template-columns: 0.6fr 1.4fr`.

---

## 5. Image Placeholder System

### When to Add Images

| Slide Content | Recommendation |
|--------------|----------------|
| 4+ bullet points or 2+ paragraphs | Add right-side image panel to break density |
| Speaker/team introductions | Always use circular portrait placeholders |
| Product/feature showcases | Use screenshot placeholder (dashed border style) |
| Card grids (3+ cards) | Add 30vh image section at top of each card |
| Stats/metrics slides | Keep text-only -- metrics ARE the visual |
| Comparison slides | Consider image in each comparison panel |

### Placeholder Styles

**Standard placeholder** (design-system.css handles this automatically):
```html
<div data-image-placeholder="photo"><!-- User replaces with image --></div>
```
Attribute values: `"photo"` (general), `"portrait"` (headshot), `"screenshot"` (product UI), `"icon"` (small tile).

CSS auto-applies: dark `#333` background + centered 8vmin placeholder icon at 20% opacity.

**Dashed border placeholder** (for optional/suggested content):
```css
border: 2px dashed rgba(255, 255, 255, 0.15);
border-radius: var(--radius-lg);
```
Use when the image is optional and the slide works without it. Example: 009.

**Circular portrait placeholder:**
```css
width: 28vmin; height: 28vmin; border-radius: 50%; overflow: hidden;
/* Scale down for multiple: 22vmin for 2 people, 18vmin for 3+ */
```
Always pair with `data-image-placeholder="portrait"`.

### Templates Using Placeholders

| Template | Placeholder Type | Size/Position |
|----------|-----------------|---------------|
| 009 | Screenshot (dashed) | 62vmin square, overlapping right edge |
| 010 | Photo x2 | Side-by-side in top-right grid cells |
| 011 | Portrait | 28vmin circle in bottom-left |
| 012 | Portrait x2 | 22vmin circles, one per speaker |
| 023 | Photo | Right column of quote slide |
| 034-036 | Photo | Inside comparison panels |
| 037 | Photo | Right panel, 1.5 flex ratio |
| 068 | Photo x3 | 30vh top section in 3-column cards |

---

## 6. Typography Weight-Mixing

The "monday display typography" technique alternates font weights within a single heading to create visual rhythm.

### The Pattern
```html
<h1>
  <span class="light">The</span> <span class="regular">speaker's</span><br>
  <span class="regular">opening</span> <span class="light">slide.</span>
</h1>
```
```css
h1 { font-size: var(--text-display); line-height: 1.05; letter-spacing: -0.025em; word-spacing: -0.1em; }
.light { font-weight: var(--weight-light); }    /* 300 */
.regular { font-weight: var(--weight-regular); } /* 400 */
.bold { font-weight: var(--weight-medium); }     /* 500 */
```

### Rules
- **Where:** Title/opening slides only (001, 007, 010, 011, 012). Never on content slides.
- **How:** Alternate light (300) with regular (400) or medium (500). Typically articles/prepositions are light, nouns/verbs are heavier.
- **Spacing:** Always use `letter-spacing: -0.025em` to `-0.03em` for display text. Add `word-spacing: -0.1em` for extra compactness.
- **Line height:** `1.0` to `1.05` for display (tight). `1.4`-`1.5` for body text.
- **Highlight word:** Use `.highlight` span with `color: var(--color-yellow)` + `font-weight: var(--weight-semibold)` for a single emphasis word (001 pattern).

### Font Size Hierarchy
| Token | Size | Usage |
|-------|------|-------|
| `--text-display` | 10.5vmin | Title slide headings only |
| `--text-h1` | 8vmin | Section headings, metric values |
| `--text-h2` | 6vmin | Slide titles |
| `--text-h3` | 4vmin | Subtitles, chart titles |
| `--text-h4` | 3vmin | Supporting headings |
| `--text-body` | 2.2vmin | Body text, bullet items |
| `--text-body-sm` | 1.8vmin | Descriptions, captions |
| `--text-caption` | 1.4vmin | Small labels, timeline details |

---

## 7. Process & Diagram Recipes

### 7A. Radial Diagram (057)
**When:** Central concept with 3-5 related items radiating outward.

```css
/* Outer dotted circle */
.outer-circle { border: 0.15vmin dotted var(--color-text); border-radius: 50%; }

/* Inner solid circle */
.inner-circle { width: calc(1vmin * 27); height: calc(1vmin * 27); border: 1px solid; border-radius: 50%; }

/* Dotted connector lines */
.connector {
  background-image: radial-gradient(circle, var(--color-text) calc(1vmin * 0.075), transparent calc(1vmin * 0.075));
  background-size: calc(1vmin * 0.5) 100%; background-repeat: repeat-x;
}

/* Endpoint dots */
.endpoint { width: calc(1vmin * 1.2); height: calc(1vmin * 1.2); border-radius: 50%; background-color: var(--color-purple-light); }
```

Items positioned absolutely on the right side with content stacking vertically.

### 7B. Process Flow Track (058)
**When:** Sequential process with 3-4 stages, emphasis on current stage.

```css
/* Pill-shaped track */
.flow-path { border: 1px solid; border-radius: 11.575vw; width: 82.5vw; height: 23.15vw; }

/* Step circles positioned along track */
.step-circle { width: 13.02vw; height: 13.02vw; border-radius: 50%; }
.step-circle.outlined { border: 1px solid var(--color-text); }
.step-circle.active { background-color: var(--color-text); color: var(--color-bg); }

/* Arrow chevrons on track */
.flow-path::before {
  content: ''; width: 1.4vw; height: 1.4vw;
  border-top: 1px solid; border-right: 1px solid;
  transform: rotate(45deg);
}
```

---

## 8. Animation Entrance Patterns

### Available Keyframes (from design-system.css)

| Keyframe | Effect | Use For |
|----------|--------|---------|
| `revealUp` | Fade in + slide up 1.5vmin | General content entrance |
| `revealFade` | Fade in only | Elements with existing transforms |
| `chartBarGrow` | scaleY(0) → scaleY(1) | Bar chart growth |
| `chartScaleIn` | scale(0) → scale(1) + fade | Pie/ring/circle elements |
| `chartLineDraw` | stroke-dashoffset → 0 | SVG line/path drawing |
| `chartFadeUp` | Fade + translateY up | Chart labels, annotations |

### Stagger Pattern for Charts
```css
.bar-group:nth-child(1) .bar { animation-delay: 0.1s; }
.bar-group:nth-child(2) .bar { animation-delay: 0.18s; }
.bar-group:nth-child(3) .bar { animation-delay: 0.26s; }
/* Pattern: start at 0.1s, increment by 0.08s per group */
```

### Applying Animations
```css
.bar {
  transform-origin: bottom;          /* Grow from bottom up */
  animation: chartBarGrow 0.6s ease-out both;
}

.ring {
  animation: chartScaleIn 0.5s ease-out both;
}
/* Stagger rings: inner first, outer last */
.ring.inner { animation-delay: 0.1s; }
.ring.middle { animation-delay: 0.25s; }
.ring.outer { animation-delay: 0.4s; }
```

The `entrance-animations.js` script auto-applies `revealUp` with staggered delays to standard content. Manual animation CSS is only needed for chart-specific effects (bars, rings, lines).

### Reduced Motion
All animations automatically respect `prefers-reduced-motion: reduce` via design-system.css -- no extra code needed.
