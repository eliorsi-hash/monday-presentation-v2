# monday-presentation-v2 — Architecture & Rebuild Guide

This document describes every file in this system, the tech decisions behind them, and what an agent needs to know to rebuild or port the skill to a different platform.

---

## What This System Is

A **Claude Code agent skill** that generates professional, single-file HTML presentations with monday.com branding. The agent reads structured reference files, maps user content to proven slide layouts, and outputs a fully self-contained `.html` file — zero external dependencies, no build step, no framework.

**Output:** One `.html` file, 15–100 KB depending on slide count, that opens in any browser and navigates with keyboard arrows.

---

## Tech Stack

| Layer | Technology | Why |
|-------|-----------|-----|
| Agent runtime | Claude Code CLI skill (`.claude/skills/`) | Executed by Claude when user invokes `/monday-presentation-v2` |
| Output format | Single-file HTML | Zero dependencies, works offline, embeds in email, no deployment |
| Styling | Vanilla CSS with custom properties (CSS variables) | No build step; design tokens cascade naturally; `vmin` units lock 16:9 at any screen size |
| Typography | Google Fonts — Poppins `wght@300;400;500;600` | monday.com brand font, loaded via `@import` in design-system.css |
| Layout engine | CSS Grid and Flexbox | Slide layouts defined as named template classes, toggled by JS |
| Navigation | Vanilla JS (IIFE, ~80 lines) | Class-based toggling only — never touches `style.display` |
| Icons | SVG files, always inlined | 267 monday.com brand icons; inlining = portability, recolorable via CSS |
| Logos | SVG files, always inlined | Two variants: white (dark theme), black (light theme) |
| Data viz | Pure CSS (conic-gradient, grid bar charts) | No chart library; custom properties make parameterization trivial |
| Theming | `data-theme` attribute on `<html>` | Single CSS block handles full dark/light cascade |
| PPTX extraction | Python (`python-pptx` library) | `convert-to-pptx.py` converts uploaded `.pptx` files to content for the agent |

---

## Directory Structure

```
monday-presentation-v2/
├── SKILL.md                  ← Agent skill entrypoint
├── CLAUDE.md                 ← Claude Code project config (points to SKILL.md)
├── design-system.css         ← CSS design token file (inlined into every output)
│
├── RECIPES.md                ← 5 pre-designed presentation recipes
├── SLIDE_INVENTORY.md        ← Metadata catalog of all 69 HTML templates
├── SLIDE_TEMPLATES.md        ← Slide type patterns (content.md format reference)
├── VISUAL_PATTERNS.md        ← CSS visual effect recipes
├── ICON_MATCHING.md          ← Semantic icon recommendations by slide purpose
├── ICON_GUIDE.md             ← Full list of all 267 icon names by category
├── BRAND_ASSETS.md           ← Logo SVG source (fallback if file read fails)
├── NAVIGATION.md             ← Navigation JS (authoritative copy, also inlined in SKILL.md)
│
├── Selected/                 ← 69 proven HTML slide templates
│   ├── Deck_Dark_Page_001.html
│   ├── Deck_Dark_Page_007.html
│   ├── Deck_Dark_Page_009.html
│   └── ... (Deck_Dark_Page_NNN.html, not all numbers present)
│
├── Icons/                    ← 267 monday.com SVG icons
│   └── Property 1=IconName.svg
│
├── Logos/                    ← Brand logo files
│   ├── monday_White.svg      ← Use on dark backgrounds
│   └── monday_Black.svg      ← Use on light backgrounds
│
├── Visuals/                  ← Decorative SVG shapes (7 files)
│   └── Group 126116569N.svg
│
└── convert-to-pptx.py        ← Python utility: extracts content from .pptx files
```

---

## File-by-File Reference

### `SKILL.md` — The Agent Brain

**What it is:** The skill definition file. Claude Code loads this when the user invokes `/monday-presentation-v2`. It contains the entire multi-phase workflow in markdown, two critical inlined assets (the full navigation JS and the logo SVG fallback), and all decision trees the agent uses.

**Structure:**
- Frontmatter (`name`, `description`) — how Claude Code registers the skill
- Phase 0: Content intake via `AskUserQuestion` tool (recipe selection, theme, content readiness)
- Phase 1: Layout mapping (read SLIDE_INVENTORY, map content to templates)
- Phase 2: User approval of proposed layout
- Phase 3: HTML generation (Strategy A for small decks, Strategy B for large decks)
- Phase 4: Optional PPTX conversion
- Phase 5: Delivery and summary

**Two generation strategies defined here:**
- **Strategy A** (3–10 slides): Read each `Deck_Dark_Page_*.html` template verbatim, extract `.slide-container`, replace only editable content
- **Strategy B** (15+ slides): Use 6 reusable CSS template classes (`tmpl-cover`, `tmpl-center`, `tmpl-twocol`, `tmpl-compare`, `tmpl-features`, `tmpl-content-img`) instead of per-slide CSS duplication

**Critical rules encoded here:**
- Always start with `Deck_Dark_Page_001` (cover) and end with `Deck_Dark_Page_001` (closing)
- Never use `style.display` from JS — only toggle `slide-active` class
- Icons must be inlined SVG, never `<img src>`
- Logos must be inlined SVG, never `<img src>`
- No hardcoded hex colors in light mode — always use `var(--color-*)`

**When rebuilding on another platform:** This file is the core "system prompt" for the agent's behavior. Port it as the agent's instruction set.

---

### `design-system.css` — The Design Token Foundation

**What it is:** A single CSS file that gets **inlined verbatim** into every generated presentation's `<style>` block. This is the entire design system.

**Contents:**
- `@import` for Poppins from Google Fonts
- `:root` block with all CSS custom properties (variables):
  - Typography scale: `--text-display` (10.5vmin) through `--text-caption` (1.4vmin)
  - Font weights: `--weight-light` (300) through `--weight-semibold` (600)
  - Colors: `--color-bg` (#000), `--color-surface` (#232427), `--color-purple` (#6164ff), `--color-red`, `--color-yellow`, `--color-green`, etc.
  - Spacing: `--space-1` (0.5vmin) through `--space-10`
  - Border radius: `--radius-xs` through `--radius-full`
- Light theme override block: `[data-theme="light"] :root { ... }` — flips all color vars
- Reusable component classes:
  - Bar charts: `.chart-container`, `.chart-body`, `.bars-row`, `.bar`, `.bar-purple`, `.bar-green`, etc.
  - Pie/donut charts: `.pie-chart`, `.donut-chart`, `.donut-wrap`, `.donut-center`
  - Stat blocks: `.stat-block`, `.stat-value`, `.stat-label`, `.stat-value-hero`
  - Step cards: `.step-card`, `.step-number`, `.step-text`, `.dot-purple`, `.dot-yellow`
  - Tables: `.table-container`, `.table-row`, `.header-row`, `.data-rows-wrapper`, `.data-row`, `.cell`
  - Text: `.code-block`, `.quote-text`, `.part-label`
  - Icons: `.ds-icon`, `.ds-icon-sm`, `.ds-icon-md`, `.ds-icon-lg`, `.ds-icon-white`
  - Layout helpers: `.spectrum-bar`, `.spectrum-segment`, `.panel-code`

**Key design decision — `vmin` units everywhere:** All font sizes and spacing use `vmin` (viewport minimum). Since slides are locked to 16:9 (width > height), `vmin` = viewport height. This means type scales proportionally at any screen size with no media queries.

**When rebuilding:** Inline this file into whatever templating system you use. It is the ground truth for all visual consistency.

---

### `RECIPES.md` — Presentation Blueprints

**What it is:** 5 pre-designed slide sequences for common presentation types. The agent reads this in Phase 0 to give the user a starting structure.

**5 recipes:**
1. **Product Launch** (10–15 slides) — Cover, Problem, Solution, Features, Demo, Metrics, Roadmap, Pricing, Q&A, Closing
2. **Team Review** (8–12 slides) — Cover, Agenda, Progress, Blockers, Metrics, Next Steps, Closing
3. **Proposal / Business Case** (8–12 slides) — Cover, Executive Summary, Problem, Solution, ROI, Timeline, Risks, Ask, Closing
4. **Training / Education** (15–20 slides) — Cover, Agenda, Part 1–N (concept + exercise), Summary, Closing
5. **Quick Update** (5–8 slides) — Cover, Headline Metric, Status, Actions, Closing

**Each recipe specifies:**
- Which `Deck_Dark_Page_*.html` template to use per slide
- Content type and capacity for each slide
- Icon strategy (which slides get icons and from what category)
- Speaker tips

**When rebuilding:** Encode these as template objects or prompt structures. The recipe is the backbone of Phase 0 → Phase 1 handoff.

---

### `SLIDE_INVENTORY.md` — Template Metadata Catalog

**What it is:** The authoritative reference for all 69 HTML templates. The agent **must** read this before Phase 1 (layout planning) and Phase 3 (generation). It is the anti-hallucination guardrail — the agent is instructed: "use only templates listed here, never invent layouts."

**For each template, it documents:**
- HTML element tree (`.slide-container` → children → grandchildren)
- `display` type (`flex` or `grid`) and exact properties
- Content capacity limits (e.g., "h1: 1–2 short lines", "bullet list: 4–6 items max")
- Which elements are editable vs. fixed structure
- Which CSS custom properties the template uses
- Common mistakes to avoid

**Template categories covered:**
- Title & Opening (001, 007, 009, 099)
- Two-Column Layouts (003, 030, 037, 044)
- Agenda (017)
- Content Slides (007)
- List & Bullet (010, 011, 012)
- Quote & Testimonial (021, 023)
- Stats & Metrics (031, 032, 033)
- Comparison (034, 035, 036)
- Process / Timeline (038, 041, 042, 043)
- Team & People (057, 058, 059, 061, 062)
- Image & Media (014, 015, 016, 046+)

**When rebuilding:** This is your "component library spec." If you're rebuilding in React or another framework, these are your component interfaces.

---

### `SLIDE_TEMPLATES.md` — Content Schema Reference

**What it is:** Documents the `content.md` input format — the structured markdown file users can provide to drive generation reproducibly. Also catalogs all 14 slide types with their HTML patterns.

**14 slide types with HTML patterns:**
`title`, `text-body`, `bullets`, `metrics`, `two-col`, `feature-grid`, `quote`, `table`, `team`, `comparison`, `full-bleed`, `section-divider`, `steps`, `closing`

**`content.md` format:**
```
# Presentation Meta
title: My Presentation
audience: Leadership team

## Slide 1: Cover
type: title
headline: The Headline
subtitle: Supporting text
accent: purple
```

**When rebuilding:** Use this as your input schema definition. If building an API, this becomes your request body shape.

---

### `VISUAL_PATTERNS.md` — CSS Effect Recipes

**What it is:** Cookbook of CSS/HTML patterns for visual polish. Complements SLIDE_INVENTORY (which covers structure) with the "how to make it look good" layer.

**8 categories:**
1. **Opening & Title Slide Recipes** — 7 visual signatures (Centered Minimal, Bordered Card, Display Type Grid, etc.)
2. **Data Visualization Recipes** — Bar charts, grouped bars, stacked bars (all pure CSS grid)
3. **Metrics & Stats Display** — Hero numbers, stat grids, progress indicators
4. **Card & Panel Recipes** — Feature cards, comparison panels, icon cards
5. **Image Placeholder System** — Placeholder divs with `data-image-placeholder` attribute and aspect ratio rules
6. **Typography Weight-Mixing** — How to combine `--weight-light` (300) + `--weight-semibold` (600) for visual contrast
7. **Process & Diagram Recipes** — Timeline rows, orbital rings, concentric circles, radial diagrams
8. **Animation Entrance Patterns** — `@keyframes chartFadeUp`, stagger delays for up to 8 elements

**Critical note:** Bar charts, stat blocks, step cards, and tables are now **component classes in `design-system.css`** — VISUAL_PATTERNS is the reference for timeline, orbital, and decorative patterns not yet promoted to the component library.

**When rebuilding:** If porting to React/Vue, these CSS recipes become your component implementations.

---

### `ICON_MATCHING.md` — Semantic Icon Selector

**What it is:** Lookup table mapping slide purpose → recommended icon names. Prevents the agent from guessing or hallucinating icon names.

**Categories covered:**
- Process & Workflow (steps, training, implementation)
- Problem & Solution (alert, fix, bottleneck, growth)
- Data & Metrics (charts, KPIs, reports)
- People & Teams (roles, collaboration, communication)
- Technology (AI, automation, integrations, security)
- Business (strategy, revenue, customers, products)
- Time & Planning (deadlines, roadmaps, scheduling)
- monday.com Specific (boards, pulses, views, automations)

**Format:** Each row is `Purpose | Recommended Icon Names | Use When`

**When rebuilding:** This is your semantic search index for icons. Encode as a lookup function or vector embedding.

---

### `ICON_GUIDE.md` — Full Icon Catalog

**What it is:** Complete list of all 267 icon names grouped by category. Used to verify icon existence before referencing it.

**File naming convention:** `Icons/Property 1=IconName.svg`

**Categories:** Navigation & UI, Charts & Data, Business & Work, People & Communication, Technology, Monday.com Specific, Misc.

**When rebuilding:** This is your icon registry. The `Property 1=` prefix is part of the actual filename — do not strip it.

---

### `BRAND_ASSETS.md` — Logo SVG Source

**What it is:** The monday.com logo SVG markup stored as a markdown code block. Used as fallback if the logo files in `Logos/` cannot be read.

**Contains:**
- Full SVG markup for the white logo (dark backgrounds) — colorful dots + white wordmark
- CSS class `.monday-logo { height: 4vmin; width: auto; max-width: 15vmin; }`
- Placement instructions (position absolute, bottom of slide, centered)

**When rebuilding:** Embed this SVG inline in your template. Never use an `<img src>` reference for the logo — it breaks portability.

---

### `NAVIGATION.md` — JavaScript Controller

**What it is:** The authoritative source for the navigation JavaScript. Also inlined directly in `SKILL.md` for convenience (so the agent doesn't need a separate file read during generation).

**What the JS does:**
- Tracks `currentSlide` index
- Selects all `[data-slide-index]` elements on init
- `showSlide(n)` — toggles `slide-active` class, wraps at boundaries, updates counter
- Keyboard events: `ArrowRight`, `Space` (advance), `ArrowLeft` (back)
- Touch events: swipe left/right with 50px threshold
- Slide counter UI: fixed bottom-right, shows "N / Total", click to jump by prompt

**Critical design rule:** The JS **never** reads or sets `style.display`. Each slide's display type (`flex` or `grid`) is defined in CSS under `.tmpl-xxx.slide-active { display: ... }`. The CSS rule `.slide-container:not(.slide-active) { display: none !important }` handles hiding. This prevents grid layouts from collapsing to `inline` when JS-controlled display is re-applied after navigating forward then backward.

**When rebuilding:** If porting to a framework with state management (React, Vue), replace this with a `currentSlide` state variable and conditional rendering. Preserve the class-toggle approach if staying in vanilla HTML.

---

### `Selected/` — 69 Proven HTML Templates

**What it is:** The template library. Each file is a standalone `.html` file containing one complete slide — full `<html>`, `<head>`, `<style>`, and `<body>` with a single `.slide-container` div.

**Naming convention:** `Deck_Dark_Page_NNN.html` (not sequential — gaps exist, e.g., 001, 007, 009, 010... not every number)

**Also contains:** `agent-creation-at-scale.html` — a full 44-slide production deck used to validate Strategy B (Template Class Architecture).

**How the agent uses these:**
- **Strategy A** (small decks): Reads each file, extracts the `.slide-container` HTML + per-slide CSS, replaces only editable content
- **Strategy B** (large decks): Uses these as visual reference only; generates slides using shared template classes

**Light mode caveat:** Templates are all dark-mode source files. When generating a light-mode deck with Strategy A, hardcoded hex colors (`#232427`, `#000`, etc.) must be replaced with CSS vars.

**When rebuilding:** These are your "component snapshots" — design source of truth. If building in Figma or Storybook, derive your components from these. If building in code, they are reference implementations.

---

### `Icons/` — 267 SVG Icons

**What it is:** The monday.com icon library. All files follow the naming pattern `Property 1=IconName.svg`.

**How they are used:** The agent reads each needed SVG file and embeds its content inline in the HTML. The `<svg>` element gets class `ds-icon ds-icon-{size}` and optionally `ds-icon-white` (which applies `filter: brightness(0) invert(1)`).

**Size classes:** `ds-icon-sm` (~3vmin), `ds-icon-md` (~4vmin), `ds-icon-lg` (~5vmin)

**Color rule:** Icons default to the monday.com purple from the SVG fill. In presentations, always apply white color via `ds-icon-white` class or `style="fill: white"` — raw purple is too saturated on dark backgrounds.

**When rebuilding:** If your platform can't inline SVGs dynamically, pre-process them into a sprite sheet or JSON map (name → SVG string). The inline approach is preferred for portability.

---

### `Logos/` — Brand Logos

**What it is:** Two SVG files for the monday.com wordmark logo.

| File | Use when |
|------|---------|
| `monday_White.svg` | Dark theme (white wordmark on black background) |
| `monday_Black.svg` | Light theme (dark wordmark on white background) |

**How they are used:** Agent reads the appropriate file based on the user's chosen theme and embeds SVG inline into title and closing slides. CSS class `monday-logo` controls sizing.

---

### `Visuals/` — Decorative SVG Shapes

**What it is:** 7 decorative SVG shapes (`Group 126116569N.svg`) used as background elements or visual accents. Abstract geometric forms from monday.com's brand identity.

**Usage:** Optional enhancement for opening slides or section dividers. Positioned absolutely with low opacity.

---

### `convert-to-pptx.py` — PPTX Extractor

**What it is:** Python script that reads a `.pptx` file using the `python-pptx` library and outputs structured content (slide titles, text, notes) into a format the agent can process as content intake.

**Dependency:** `pip install python-pptx`

**Usage:** Phase 4 of the skill — if a user uploads an existing PowerPoint, the agent runs this script to extract text content, then treats the output as "rough outline" content for a new generation pass.

**When rebuilding:** Any platform with PowerPoint parsing (LibreOffice, Pandoc, python-pptx, or an API like Unstructured.io) can fulfill this role.

---

## Core Architectural Decisions for Rebuild

### 1. Single-file output is intentional

The entire HTML (CSS tokens, component classes, navigation JS, slide HTML, inlined SVGs) lives in one file. This is a deliberate tradeoff — it makes the output portable, shareable, email-embeddable, and dependency-free. The file sizes are acceptable (15–100 KB).

**Do not split into separate CSS/JS files unless you have a bundler and deployment target.**

### 2. `vmin` units for all sizing

Using `vmin` throughout means the presentation scales perfectly from a 1024px laptop to a 4K projector without any media queries or responsive breakpoints. Since slides are wider than tall (16:9), `1vmin ≈ 1vh`.

### 3. Class-based navigation, never inline `style.display`

This is the most subtle and critical rule. CSS Grid and Flexbox display types are set in CSS under `.tmpl-xxx.slide-active`. If JavaScript were to set `element.style.display = 'flex'` directly, then when it later sets `display = 'none'` and then restores, it would restore to `block` (the JS's last assignment) rather than the CSS-defined `grid` or `flex`. This breaks grid layouts on backward navigation.

**The pattern:**
```css
/* CSS owns display type */
.tmpl-twocol.slide-active { display: grid; grid-template-columns: 1fr 1fr; }
.slide-container:not(.slide-active) { display: none !important; }
```
```js
/* JS only toggles the class */
slide.classList.toggle('slide-active', index === currentSlide);
```

### 4. Template class architecture (Strategy B)

For decks over 15 slides, instead of duplicating CSS per-slide, define 6 template classes once and apply them as classes. Each template class maps to a proven layout from the `Selected/` folder:

| Class | Layout | Deck_Dark_Page source |
|-------|--------|-----------------------|
| `tmpl-cover` | `flex`, column, centered | 001 |
| `tmpl-center` | `flex`, column, centered (for quotes/code) | 021 |
| `tmpl-twocol` | `grid` 1fr 1fr | 030 |
| `tmpl-compare` | `grid` 1fr 1fr (panel style) | 034 |
| `tmpl-features` | `grid` 0.6fr 1.4fr | 044 |
| `tmpl-content-img` | `grid` 1fr 1fr | 038 |

### 5. Light theme via `data-theme` cascade

The entire light theme is a single CSS override block:
```css
[data-theme="light"] :root {
  --color-bg: #ffffff;
  --color-surface: #f5f5f5;
  --color-text: #1f1f1f;
  /* etc. */
}
```
Set `<html data-theme="dark">` or `<html data-theme="light">` on the root element. Every component automatically adapts via `var(--color-*)` references.

---

## Porting Checklist

When rebuilding on a different platform (e.g., Notion AI, GPT, a web app, or a different agent framework):

- [ ] Port `SKILL.md` as your agent's system prompt / instruction set
- [ ] Make `design-system.css` available to inline (or translate tokens to your target framework)
- [ ] Preserve `RECIPES.md`, `SLIDE_INVENTORY.md`, `VISUAL_PATTERNS.md`, `ICON_MATCHING.md` as agent-readable context documents
- [ ] Host or embed the 267 icons from `Icons/` — they must be inlineable as SVG text
- [ ] Host or embed both logo files from `Logos/`
- [ ] Implement the navigation JS (from `NAVIGATION.md`) verbatim or adapt to your framework's state
- [ ] Enforce the class-toggle rule (never `style.display` from JS)
- [ ] Enforce the inline-SVG rule for icons and logos
- [ ] Implement the `content.md` input schema (from `SLIDE_TEMPLATES.md`) for repeatable generation
- [ ] If PPTX upload is needed, integrate a `python-pptx` step or equivalent API

---

## Generated Output Contract

Every HTML file this system produces must satisfy:

1. Single `<!DOCTYPE html>` file, no external dependencies
2. `<html lang="en" data-theme="dark|light">` on root
3. All slides are `<div class="slide-container tmpl-xxx slide-N" data-slide-index="N">`
4. `.slide-container:not(.slide-active) { display: none !important }` present in `<style>`
5. Each `.tmpl-xxx.slide-active` defines its own `display: flex/grid`
6. Navigation JS is inlined in `<script>` — class-toggle only
7. Poppins loaded via `@import` (requires internet on first load; cached thereafter)
8. Monday.com logo inlined as SVG on title and closing slides
9. All icons inlined as SVG (not `<img src>`)
10. All sizes in CSS custom properties or `vmin` units — no hardcoded `px` for layout
11. Slides lock to 16:9 via `position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%); width: 100vw; height: 56.25vw; max-height: 100vh; max-width: 177.78vh;`
