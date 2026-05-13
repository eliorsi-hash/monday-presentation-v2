# monday-presentation-v2

A Claude skill that generates professional, self-contained monday.com branded HTML presentations from a topic, outline, or existing content.

---

## How it works

Invoke `/monday-presentation-v2` in Claude Code and walk through a guided flow:

### 1. Recipe and theme
Pick a presentation type (Quick Update, Product Launch, Team Review, Training) and color theme (dark or light). Claude shows you the recipe structure — which slides in which order.

### 2. Content intake
Provide your content however you have it:
- **Paste it** — drop in talking points, bullet lists, or full text
- **Share a file** — give a path to a doc, PDF, MD, PPTX, or TXT file
- **Just a topic** — Claude generates all content from a topic description

### 3. Data visuals (optional)
If you want charts or metrics in the deck, choose what kind (bar charts, pie/donut, stat tiles) and provide the data — paste numbers, point to a CSV, or pull from a Monday.com board.

### 4. Layout planning
Claude maps your content to the right slide templates from 16 available layout classes, picks icons from the 268-icon library, and shows you the plan:

```
Slide 1: Cover        — tmpl-cover-image
Slide 2: Vision       — tmpl-twocol
Slide 3: Features     — tmpl-3col
Slide 4: Deep Dive    — tmpl-content-img
Slide 5: Metrics      — tmpl-stats
Slide 6: Timeline     — tmpl-timeline
Slide 7: Closing      — tmpl-cover
```

You review and adjust before generation begins.

### 5. Generation
Claude generates a single `.html` file with all CSS, JS, icons, and logos inlined. No external dependencies. Opens it in your browser automatically.

### 6. Refinement
After reviewing in the browser, Claude offers to edit content, swap images, or adjust layout — iterate until you're happy.

**Output:** One `.html` file saved to your current working directory. Open it, present it, share it.

---

## Installation

### Claude Code (CLI or Desktop App) — recommended

```bash
git clone https://github.com/eliorsi-hash/monday-presentation-v2.git
cd monday-presentation-v2
bash install.sh
```

Then in any Claude Code session:

```
/monday-presentation-v2
```

### claude.ai Projects (partial support)

1. Create a new Project in claude.ai
2. Open **Project Instructions** and paste the contents of `SKILL.md`
3. Upload these files as **Project Knowledge**:
   - `RECIPES.md`
   - `ICON_MATCHING.md`
   - `DESIGN_SYSTEM.md`
   - `design-system.css`
4. Start a conversation and describe your presentation

**Limitations:** Interactive recipe picker and file writing are not available on claude.ai. Claude asks questions in plain text and outputs the HTML as a code block for you to save. Icons from the `Icons/` folder cannot be auto-inlined — Claude generates fallback SVGs instead.

---

## Available templates

19 slide layout classes, all defined in `design-system.css`:

| Template | Layout | Best for |
|----------|--------|----------|
| `tmpl-cover` | Centered headline + logo | Cover, closing, section break |
| `tmpl-cover-split` | Bordered top + colored bottom bar | Casual/editorial cover |
| `tmpl-cover-image` | Left text + right image placeholder | Cover with hero visual |
| `tmpl-cover-gallery` | 2x2 grid: title + photos + info card | Cover with multiple images |
| `tmpl-cover-speaker` | Bordered card + portrait + info card | Single presenter cover |
| `tmpl-cover-speakers` | Bordered card + 2 portrait groups | Two presenters cover |
| `tmpl-speaker` | 1.1fr 0.9fr grid: name + portrait | Speaker detail / about me |
| `tmpl-center` | Centered single focus | Hero stat, pull quote, code block |
| `tmpl-twocol` | 1fr 1fr grid | Title + bullets, problem + solution |
| `tmpl-compare` | 1fr 1fr stretch | Before/after, challenge/solution |
| `tmpl-features` | 0.6fr 1.4fr grid | Title left + 2x2 feature grid right |
| `tmpl-content-img` | 1fr 1fr grid | Text + bullets left, image right |
| `tmpl-quote` | Full-width box | Testimonial with quote icon |
| `tmpl-stats` | Flex column | KPI tiles, metrics dashboard |
| `tmpl-steps` | Flex column | Numbered process steps |
| `tmpl-timeline` | Flex column | Horizontal timeline with markers |
| `tmpl-3col` | Flex column | 3 equal cards (with optional icons) |
| `tmpl-4col` | Flex column | 4 cards or colored tiles |
| `tmpl-image-cards` | Flex column | 3 cards with image headers |

Open `slide-templates.html` in a browser to see live previews of every template.

---

## Recipes

| Recipe | Slides | Best for |
|--------|--------|---------|
| Quick Update | 5-8 | Status updates, announcements |
| Product Launch | 10-15 | New features, launches |
| Team Review | 8-12 | Retrospectives, OKR reviews |
| Training | 15-20 | Workshops, education |

All recipes start with a cover slide and end with a closing slide.

---

## Design system

Every deck uses the official monday.com design tokens:

- **Colors:** Purple `#6164ff`, Green `#00c875`, Yellow `#ffcb00`, Red `#ff3d57`
- **Font:** Poppins (Regular 400 + SemiBold 600 only)
- **Spacing:** Responsive `vmin` scale — works on any screen size
- **Themes:** Dark (default) and light via `data-theme="light"`
- **Components:** Bar charts, donut/pie charts, stat blocks, tables, step cards, timelines, quote boxes, agenda cards, image placeholders

Card styling supports three modes: grey surface (default), brand colors per card, or highlight-one-important.

---

## File structure

```
monday-presentation-v2/
├── SKILL.md                  — Main skill definition (phases, rules, generation)
├── RECIPES.md                — 5 presentation recipes with slide-by-slide structure
├── DESIGN_SYSTEM.md          — Complete token, component, and template reference
├── ICON_MATCHING.md          — Semantic icon selection guide by content type
├── ICON_GUIDE.md             — 268 icon names and categories
├── BRAND_ASSETS.md           — monday.com SVG logos (dark + light)
├── design-system.css         — CSS tokens + components + 16 template classes
├── slide-templates.html      — Live previews of all templates and components
├── design-system-showcase.html — Design token and component reference page
├── install.sh                — One-command install for Claude Code
├── Icons/                    — 268 monday.com SVG icons (including Quote.svg)
├── Logos/                    — monday.com wordmark (white + black)
├── Fonts/                    — (not used — system loads Poppins via Google Fonts)
└── output/                   — Generated presentations (gitignored)
```

---

## Navigation (in generated decks)

- **Right arrow / Space** — next slide
- **Left arrow** — previous slide
- **Click slide counter** (bottom-right) — jump to any slide by number
- **Swipe** — touch and mobile support

---

## Customization

Generated decks are plain HTML — edit them directly:

```css
/* Change brand color */
:root { --color-purple: #your-color; }
```

```html
<!-- Replace image placeholder with a real image -->
<img src="your-image.jpg" style="width:100%; height:100%; object-fit:cover; border-radius: var(--radius-lg);">
```

---

## Platform support

| Feature | Claude Code | claude.ai | Claude API |
|---------|------------|-----------|------------|
| Interactive recipe picker | Full UI | Text questions | Text questions |
| Read icons from disk | Auto | Via Project Knowledge | Inline only |
| Write output file | Auto | Save code block | Save code block |
| Open in browser | Auto | Manual | Manual |

---

## Reference

- [SKILL.md](SKILL.md) — Full workflow and generation rules
- [DESIGN_SYSTEM.md](DESIGN_SYSTEM.md) — Every token, component, and template class
- [slide-templates.html](slide-templates.html) — Live template previews (open in browser)
- [ICON_MATCHING.md](ICON_MATCHING.md) — 268 icons organized by semantic meaning
- [RECIPES.md](RECIPES.md) — Presentation recipe structures
