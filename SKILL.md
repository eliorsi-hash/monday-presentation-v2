---
name: monday-presentation-v2
description: Creates branded monday.com HTML presentations using a design-system-first approach with responsive layouts, keyboard navigation, and professional monday branding. Use when the user asks to create a presentation, build slides, make a deck, or generate a slide deck with monday.com branding. Supports content from markdown files, rough outlines, or just a topic. Outputs a single self-contained HTML file.
---

# monday-presentation-v2

Generate professional, single-file monday.com branded HTML presentations with zero dependencies.

## Core Principles

1. **Design-System First** — All output uses official monday.com design tokens (dark bg, Poppins, purple #6164ff)
2. **Existing + Generated** — Reuse proven Deck_Dark_Page templates; generate novel slide types from design system
3. **Zero Dependencies** — Single, self-contained HTML file with inline CSS/JS. No external scripts.
4. **Responsive 16:9** — Every slide fits exactly within viewport (no scrolling, ever)
5. **Keyboard Navigation** — Arrow keys, Space, or click to advance. Slide counter included.

---

## Phase 0: Content Intake & Recipe Selection

### Step 0.1: Identify Recipe

**Read [RECIPES.md](RECIPES.md)** to find the best recipe for the user's presentation type.

Ask the user:

> **Which presentation type matches yours?**
>
> - **Product Launch** — Introduce a new product or major feature (10-15 slides)
> - **Team Review** — Update team on progress and plans (8-12 slides)
> - **Business Proposal** — Make a case for a project or initiative (12-18 slides)
> - **Training** — Teach a topic or skill (15-20 slides)
> - **Quick Update** — Brief status update or announcement (5-8 slides)

**Wait for user selection.** Once chosen, explain the recipe structure:

> **Your Recipe: [Recipe Name]**
>
> This recipe includes [X] slides in this order:
> - Slide 1: Title (Cover Slide)
> - Slide 2: [Purpose]
> - ...
> - Slide N: Thank You (Closing Slide)
>
> All presentations follow this proven structure: **always start with a cover slide (Deck_Dark_Page_001) and end with a thank you slide (Deck_Dark_Page_001).**

### Step 0.2: Content Intake

After recipe selection, ask:

> **Tell me about your content:**
>
> Do you have content ready? (Ready to go / Rough outline / Just a topic)

If user has content, ask them to paste or upload it.

**If `content.md` exists in cwd**, skip questions and read it directly.

---

## Phase 1: Layout Mapping

### Step 1.0: Consult Slide Inventory (MANDATORY)

**Read [SLIDE_INVENTORY.md](SLIDE_INVENTORY.md) FIRST** — Complete metadata catalog of every available `Deck_Dark_Page_*.html` template with:
- Exact HTML structure (never invent or modify)
- Content capacity limits per slide
- Editable vs. fixed elements
- Layout specifications (grid cols, flex direction, gaps, sizing)
- Design variables used
- Quick-reference matching table by purpose

**CRITICAL RULE:** Use ONLY templates listed in SLIDE_INVENTORY.md. Never mix elements from different slides. Never create new layouts—work only with existing templates.

### Step 1.1: Map Content to Recipe Slides

Using the recipe structure from Step 0.1:
1. For each slide in the recipe, read the user's content
2. Map content to the recipe's slide position
3. Verify content fits the template specified in the recipe

For example, if using **Recipe 1 (Product Launch)**:
- Slide 1: Use Deck_Dark_Page_001 (cover) — map headline + subtitle
- Slide 2: Use Deck_Dark_Page_010 (problem) — map problem statement + bullets
- Slide 3: Use Deck_Dark_Page_030 (solution) — map solution + key points
- ...and so on per recipe

### Step 1.2: Consult SLIDE_INVENTORY for Template Details

For each recipe slide, **read [SLIDE_INVENTORY.md](SLIDE_INVENTORY.md)** to understand:
- Exact HTML structure (never invent)
- Content capacity limits
- Editable vs. fixed elements
- Layout specifications

### Step 1.3: Create Layout Plan with Template Sources

Build a slide-by-slide plan following the recipe:

```
Slide 1 (Title)        → Deck_Dark_Page_001 (centered, h1 + p + logo)
Slide 2 (Two-Column)   → Deck_Dark_Page_030 (grid 1fr|1fr, title + bullets)
Slide 3 (Features)     → Deck_Dark_Page_044 (grid 0.6fr|1.4fr, title + 2x2 feature grid)
Slide 4 (Metrics)      → Deck_Dark_Page_031 (grid auto-fit, 4 stat blocks)
Slide 5 (Closing)      → Deck_Dark_Page_099 (centered, h1 + contact + logo)
```

**Every slide MUST map to an existing template. No exceptions.**

---

## Phase 2: Review & Approval

**Show the user the recipe-based layout plan:**

> **Proposed Layout (Following [Recipe Name])**
>
> Slide 1: Cover — Deck_Dark_Page_001 (headline + subtitle)
> Slide 2: [Purpose] — Deck_Dark_Page_XXX (content type)
> Slide 3: [Purpose] — Deck_Dark_Page_XXX (content type)
> ...
> Slide N: Thank You — Deck_Dark_Page_001 (closing)
>
> All slides follow the [Recipe Name] recipe structure. Does this look right? Any changes?

**Important:** Always verify that:
- Slide 1 is Deck_Dark_Page_001 (cover/title)
- Final slide is Deck_Dark_Page_001 (thank you/closing)
- All intermediate slides match recipe templates

**Wait for user feedback.** If changes needed, adjust the plan but maintain the recipe structure.

---

## Phase 3: Generate Presentation

When generating, **read these files in order:**
1. [SLIDE_INVENTORY.md](SLIDE_INVENTORY.md) — Confirm exact template structure
2. [DESIGN_SYSTEM.md](DESIGN_SYSTEM.md) — CSS variables and component patterns
3. [BRAND_ASSETS.md](BRAND_ASSETS.md) — **Official monday.com SVG logo (required)**
4. [NAVIGATION.md](NAVIGATION.md) — Keyboard navigation JS controller

### Step 3.0: Choose Generation Strategy

There are **two approaches** depending on deck size. Choose the right one:

#### Strategy A: Exact Template Copy (for small decks, 3-10 slides)

For each slide mapped in Phase 1:
1. **Read the source `Deck_Dark_Page_*.html` file** from `/Selected/` directory
2. **Extract EXACTLY** the `.slide-container` HTML and CSS
3. **Replace ONLY the editable content** per SLIDE_INVENTORY specs
4. **Preserve all structural HTML** — do not modify classes, grid, flex, etc.

This is the safest approach but becomes impractical for large decks (20+ slides) because:
- You'd need to read 8-10 template files individually
- CSS class names collide across templates (both use `.main-title`, `.bullet-list`, etc.)
- Each template has its own `<style>` block that must be merged and de-duplicated

#### Strategy B: Template Class Architecture (for large decks, 15+ slides) — RECOMMENDED

Instead of copying each template file verbatim, **extract the layout patterns into reusable CSS template classes**. This was validated in production on a 44-slide workshop deck.

**The 6 template classes** (mapped from Deck_Dark_Page templates):

| Template Class | Source Template | Display | Purpose |
|---------------|----------------|---------|---------|
| `tmpl-cover` | Page_001 | `flex` (column, centered) | Title, closing, minimal slides |
| `tmpl-center` | Page_021 | `flex` (column, centered) | Quotes, code blocks, centered content |
| `tmpl-twocol` | Page_030 | `grid` (1fr 1fr) | Title left + content right |
| `tmpl-compare` | Page_034 | `grid` (1fr 1fr) | Side-by-side comparison panels |
| `tmpl-features` | Page_044 | `grid` (0.6fr 1.4fr) | Title left + 2x2 feature grid right |
| `tmpl-content-img` | Page_038 | `grid` (1fr 1fr) | Title + bullets left, image right |

**How it works:**
1. Define all 6 template classes in `<style>` once — each with `.tmpl-xxx.slide-active { display: flex/grid; ... }` rules
2. Each slide gets: `class="slide-container tmpl-xxx slide-N"` and `data-slide-index="N"`
3. CSS handles show/hide via `.slide-container:not(.slide-active) { display: none !important; }`
4. Navigation JS only toggles `slide-active` class — never touches `style.display`

**CSS structure in the `<head>`:**
```css
/* 1. Design system variables (:root) */
/* 2. Reset + body */
/* 3. Slide base (.slide-container positioning + :not(.slide-active) hide rule) */
/* 4. Template classes (.tmpl-cover, .tmpl-center, etc.) */
/* 5. Shared components (.code-block, .quote-text, .part-label, etc.) */
```

**Benefits:**
- No CSS class name collisions — template classes are unique
- Easy to add/remove slides without touching CSS
- Grid/flex display types are always correct (never overridden by JS)
- Forward/backward navigation never breaks grid layouts
- Much smaller CSS footprint than duplicating per-slide styles

**Slide centering (critical for viewport fit):**
```css
.slide-container {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 100vw;
    height: 56.25vw;
    max-height: 100vh;
    max-width: 177.78vh;
}
```

**When to extend:** If content doesn't fit any of the 6 template classes, first try adapting the content to fit. If truly needed, create a new `.tmpl-xxx` class following the same pattern. Never inline layout styles per slide.

### Step 3.1: Icon Selection

**For each slide that includes icons** (feature cards, timeline steps, bullet points with icons, etc.):

1. **Check recipe icon strategy** — [RECIPES.md](RECIPES.md) specifies icon categories for each recipe
2. **Identify icon slots** — Where are icons used in the slide template?
3. **Read [ICON_MATCHING.md](ICON_MATCHING.md)** — Find recommended icons by category for slide purpose
4. **Select best match** — Choose icon from category that fits semantic meaning
5. **Verify path exists** — Confirm icon file exists: `Icons/Property 1=IconName.svg`
6. **Replace in HTML** — Update icon path: `src="Icons/Property 1=SelectediconName.svg"`
7. **Validate rendering** — Ensure icon displays correctly with `ds-icon` class and sizing

**Icon Selection Rules:**
- Always use semantic icons (match meaning of content)
- Prefer consistency (use icons from same visual family)
- Avoid clutter (remove icon if it adds no value)
- Size appropriately (use `ds-icon-sm`, `ds-icon-md`, `ds-icon-lg` based on layout)
- Check file exists before using (Icon folder: `/Icons/`)

**Example:**
```
Slide 7 (Track 1 - Training):
- Purpose: Training, education, learning
- Recommended: Academy, Book open, Graduation cap
- Selected: Academy (best fit for training concept)
- Path: Icons/Property 1=Academy.svg
- HTML: <img src="Icons/Property 1=Academy.svg" class="ds-icon ds-icon-md" alt="Training">
```

### Step 3.2: Generation Requirements

**HTML Structure:**
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Presentation Title</title>
  <style>
    /* INLINE ALL OF design-system.css HERE */
    /* PLUS slide-specific styles */
  </style>
</head>
<body>
  <!-- Slides container with data-slide-index -->
  <div class="slide-container" data-slide-index="0">
    <!-- Slide content -->
  </div>

  <script>
    /* INLINE NAVIGATION JS FROM NAVIGATION.md */
  </script>
</body>
</html>
```

**Critical CSS Rules:**
- Inline the **entire** [DESIGN_SYSTEM.md](DESIGN_SYSTEM.md) CSS variables in `:root` first
- Use only CSS variables for sizing (no hardcoded px)
- Font: Poppins from Google Fonts `wght@300;400;500;600`
- Logo: Inline SVG from [BRAND_ASSETS.md](BRAND_ASSETS.md)
- No `text-transform: uppercase` — use letter-spacing instead
- **Multi-slide architecture:** Use `.slide-container:not(.slide-active) { display: none !important }` to hide inactive slides. Each template class (`.tmpl-xxx.slide-active`) defines its own display type (flex or grid). NEVER use inline `style.display` from JavaScript.
- **Slide centering:** Use `position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%)` on `.slide-container` for proper viewport centering across all screen sizes.

**Per-Slide Requirements:**
1. `.slide-container` with `data-slide-index="N"`, a template class (e.g., `tmpl-twocol`), AND a unique class like `.slide-1`, `.slide-2`, etc.
2. Color accents: use CSS vars like `var(--color-purple)`, `var(--color-yellow)`, etc.
3. Spacing: `var(--space-4)` through `var(--space-10)` only
4. If using icons: reference via relative path `Icons/Property 1=Name.svg` — SVGs can be recolored with inline `style="stroke: var(--color-xxx); color: var(--color-xxx)"`
5. **Monday logo on title + closing slides**: Inline SVG from [BRAND_ASSETS.md](BRAND_ASSETS.md) — NEVER use external image files like `<img src="monday_logo.svg">`
6. **Part labels** (optional): Add `<span class="part-label">PART N — SECTION NAME</span>` for training/workshop decks where the speaker needs section tracking.

**Available Components (from [DESIGN_SYSTEM.md](DESIGN_SYSTEM.md)):**
- `.code-block` — Styled monospace code/prompt display (use for technical slides, file previews, prompt examples)
- `.quote-text` — Large centered italic quote with `.em` spans for highlighted words
- `.part-label` — Section tracker in top-left corner
- `.spectrum-bar` + `.spectrum-segment` — Horizontal progression bar
- `.panel-code` — Code block inside comparison panels

**Navigation JS:**
- Arrow Right / Space to advance
- Arrow Left to go back
- Display slide counter (e.g., "Slide 3 of 5")
- **IMPORTANT**: Use the **updated** navigation from [NAVIGATION.md](NAVIGATION.md) which uses `classList.toggle('slide-active')` — NEVER inline `style.display`. The old version in NAVIGATION.md was replaced.

---

## Phase 4: PPT Conversion (Optional)

If user has a .pptx file:

1. Extract content using `python scripts/extract-pptx.py <file.pptx> output/`
   - Install python-pptx if needed: `pip install python-pptx`
2. Review extracted slides
3. Return to Phase 0 (content intake)
4. Follow phases 1-3 normally

---

## Phase 5: Delivery

**Open the file:**
```
open presentation.html
```

**Summarize for the user:**

> **Your deck is ready!**
>
> **File:** `presentation.html` (self-contained, no external dependencies)
>
> **Navigation:**
> - Arrow keys or Space to advance
> - Click slide counter to jump to a specific slide
> - Bottom-right: current slide / total slides
>
> **Customization:**
> Want to tweak colors, text, or layout? Edit the HTML file directly:
> - Colors: Find `:root { --color-purple: ...}` to change brand color
> - Text: Edit content inside `<h1>`, `<h2>`, `<p>` tags
> - Layout: Modify grid or flex properties in `<style>` block
>
> To regenerate: Create a `content.md` file and run the skill again with `update`.

**Optional:** Offer to create a `content.md` template for future updates.

---

## `content.md` Format (Optional)

Users can provide a `content.md` file in the working directory for repeatable deck generation. Format:

```markdown
# Presentation Meta
title: [Title]
audience: [Audience]

## Slide N: [Name]
type: [title|text-body|bullets|metrics|two-col|feature-grid|quote|comparison|steps|closing]
headline: [Text]
subtitle/body/bullets/stats: [Content]
accent: [purple|green|yellow|red]
```

See [SLIDE_TEMPLATES.md](SLIDE_TEMPLATES.md) for all supported slide types.

---

## Supported Slide Types

See [SLIDE_TEMPLATES.md](SLIDE_TEMPLATES.md) for full documentation:

- `title` — Cover slide with logo, headline, subtitle
- `text-body` — Text + optional image/content
- `bullets` — Headline + bullet list with optional icons
- `metrics` — Large numbers with labels (stats)
- `two-col` — Two-column layout with distinct sections
- `feature-grid` — 2x2 card grid (cards with icons/descriptions)
- `quote` — Full-slide pull quote
- `table` — Styled data table
- `team` — Team member cards (portrait + name + role)
- `comparison` — Side-by-side comparison
- `full-bleed` — Full background image with overlay text
- `section-divider` — Large section number + label
- `steps` — Numbered timeline/action steps
- `closing` — Closing slide with CTA + logo

---

## Supporting Files

| File | Purpose |
|------|---------|
| [RECIPES.md](RECIPES.md) | **Phase 0** — 5 pre-designed presentation recipes (Product Launch, Team Review, Proposal, Training, Quick Update) with slide sequences, icon strategies, and best practices |
| [SLIDE_INVENTORY.md](SLIDE_INVENTORY.md) | **Phase 1** — MANDATORY metadata of every Deck_Dark_Page_*.html template with exact structure, content limits, and editable elements |
| [ICON_MATCHING.md](ICON_MATCHING.md) | **Phase 3.1** — Semantic icon recommendations by slide purpose and content type; icon selection guide |
| [DESIGN_SYSTEM.md](DESIGN_SYSTEM.md) | CSS variables, colors, typography, spacing |
| [SLIDE_TEMPLATES.md](SLIDE_TEMPLATES.md) | Legacy template patterns (use SLIDE_INVENTORY instead) |
| [BRAND_ASSETS.md](BRAND_ASSETS.md) | monday.com SVG logos (inline) |
| [ICON_GUIDE.md](ICON_GUIDE.md) | 267 icon names and semantic matching |
| [NAVIGATION.md](NAVIGATION.md) | Keyboard/click navigation JS controller |

---

## Examples

### Example 1: 3-Slide Quick Deck

User: "Create a 3-slide board update for leadership"

Layout plan:
1. Title (Impact Snapshot)
2. Metrics (Key outcomes)
3. Closing (Next steps)

Output: `board-update.html` (~15KB self-contained)

### Example 2: Full Product Presentation

User: "Build a 10-slide product launch deck. I have these talking points: [...]"

Layout plan:
1. Title
2. Problem
3. Solution overview
4. Feature 1 + demo
5. Feature 2 + demo
6. Metrics / social proof
7. Roadmap
8. Pricing
9. Q&A
10. Closing

Output: `product-launch.html` (~35KB self-contained)

### Example 3: 44-Slide Workshop (Template Class Architecture)

User: "Build a training deck from claude-code-workshop.md — 68-minute workshop with demos"

**Strategy:** Used Template Class Architecture (Strategy B) because 44 slides is too many for exact template copy.

**Template class usage:**
- `tmpl-cover` (2 slides): Title + closing
- `tmpl-center` (14 slides): Quotes, code blocks, tasks, centered content
- `tmpl-twocol` (9 slides): Two-column title + content
- `tmpl-compare` (7 slides): Side-by-side comparisons
- `tmpl-features` (8 slides): Feature grids and card layouts
- `tmpl-content-img` (1 slide): Content + image placeholder

**Components used:** `.code-block` for 8 file-preview slides, `.quote-text` for emphasis, `.part-label` on all slides for speaker section tracking, `.spectrum-bar` for progression concepts, `.panel-code` inside comparison panels.

**Key decisions:**
- Part labels on every slide so speaker knows which section they're in
- Code block slides (showing .md file contents) condensed to 5-8 lines each
- Comparison panels adapted for text-only content (no image placeholders needed)

Output: `claude-code-workshop.html` (~35KB self-contained, 44 slides)

---

## Quality Checklist

Before delivery, ensure:

- [ ] All slides fit within viewport (no scroll)
- [ ] Typography uses only Poppins 300/400/500/600
- [ ] Colors use CSS variables from design system
- [ ] No external images/scripts (all inline or relative paths)
- [ ] Keyboard navigation works (arrows, space, slide counter)
- [ ] Logo appears on title and closing slides (inline SVG, not `<img>`)
- [ ] Spacing uses `var(--space-*)` consistently
- [ ] On mobile 16:9 still locked (no reflow)
- [ ] **Each template class has `.tmpl-xxx.slide-active { display: flex/grid; }` — no inline style.display from JS**
- [ ] **Navigation uses only `.classList.toggle('slide-active', ...)` — never touches `style.display`**
- [ ] **Test navigation: move forward/backward/forward on grid slides to ensure layout is preserved**
- [ ] **Slides use absolute centering (`position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%)`) for proper viewport fit**
- [ ] Icons render with correct colors (test on all slides)
- [ ] `.slide-container:not(.slide-active) { display: none !important }` rule is present

