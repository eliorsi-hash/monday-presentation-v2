# monday-presentation-v2

A Claude skill that generates professional, self-contained monday.com branded HTML presentations from a topic, outline, or existing content.

---

## What it does

Invoke `/monday-presentation-v2` and Claude will:

1. Ask you to pick a recipe (Quick Update, Product Launch, Team Review, Proposal) and theme (dark/light)
2. Collect your content — paste it, upload a file, or give Claude just a topic and it will write the slides
3. Map content to the right slide templates from the 67-template library
4. Generate a single `.html` file with all CSS, JS, and logos inlined — no external dependencies
5. Open it in your browser

**Output:** One `.html` file saved to your current working directory. Open it, present it, share it. No build step, no server, no installs.

---

## Installation

### Claude Code (CLI or Desktop App) — recommended, full feature set

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
   - `SLIDE_INVENTORY.md`
   - `VISUAL_PATTERNS.md`
   - `ICON_MATCHING.md`
   - `BRAND_ASSETS.md`
   - `NAVIGATION.md`
   - `design-system.css`
4. Start a conversation and describe your presentation

**Limitations on claude.ai:** The interactive recipe picker and automatic file writing are not available. Claude will ask questions in plain text and output the finished HTML as a code block for you to save. Icons cannot be auto-inlined from the local `Icons/` folder — Claude will generate fallback inline SVGs instead.

### Claude for Teams / Enterprise

Same as the claude.ai Projects setup above. Use your team's Project or Workspace to share the skill with colleagues.

---

## Platform support

| Feature | Claude Code | claude.ai | Claude API |
|---------|------------|-----------|------------|
| Interactive recipe & theme picker | Full UI | Text questions | Text questions |
| Read templates + icons from disk | Auto | Via Project Knowledge | Inline only |
| Write output file to disk | Auto | Save code block manually | Save code block manually |
| Open in browser after generation | Auto | Manual | Manual |
| Generated HTML quality | Full | Full | Full |

---

## Recipes

| Recipe | Slides | Time | Best for |
|--------|--------|------|---------|
| Quick Update | 5–8 | 5–10 min | Status updates, announcements |
| Product Launch | 10–15 | 15–30 min | New features, launches |
| Team Review | 8–12 | 10–20 min | Retrospectives, OKR reviews |
| Proposal / Initiative | 8–12 | 10–20 min | Business cases, roadmaps |

All recipes start with a cover slide and end with a closing slide.

---

## Output

Generated files go to your **current working directory**, not the skill directory. The filename is derived from your topic (e.g. `monday-ai-features.html`, `q3-roadmap.html`).

Files in the `output/` folder of this repo are gitignored and never committed.

---

## Design system

Every deck uses the official monday.com design tokens:

- **Colors:** Purple `#6164ff`, Green `#00c875`, Yellow `#ffcb00`, Red `#ff3d57`
- **Font:** Poppins (300/400/500/600)
- **Spacing:** Responsive `vmin` scale — works on any screen size
- **Themes:** Dark (default) and light via `data-theme="light"` on `<html>`
- **Components:** Bar charts, donut charts, stat blocks, tables, step cards, feature grids

---

## File structure

```
monday-presentation-v2/
├── SKILL.md              — Main skill definition (phases, rules, generation strategy)
├── RECIPES.md            — 5 presentation recipes with slide-by-slide structure
├── SLIDE_INVENTORY.md    — Metadata for every template (structure, capacity, editable slots)
├── VISUAL_PATTERNS.md    — CSS/HTML recipes for charts, timelines, image placeholders
├── ICON_MATCHING.md      — Semantic icon selection guide by content type
├── BRAND_ASSETS.md       — monday.com SVG logos (dark + light)
├── NAVIGATION.md         — Keyboard/swipe navigation JS (inlined into output)
├── design-system.css     — CSS design tokens (inlined verbatim into every output)
├── ARCHITECTURE.md       — Technical decisions and architecture notes
├── DESIGN_SYSTEM.md      — Design token reference
├── install.sh            — One-command install for Claude Code
├── convert-to-pptx.py    — Export HTML to PowerPoint (optional)
├── Selected/             — 67 HTML slide templates (Deck_Dark_Page_001 through 100)
├── Icons/                — 267 monday.com SVG icons
├── Logos/                — monday.com wordmark (white + black)
└── output/               — Generated presentations (gitignored)
```

---

## Navigation (in generated decks)

- **Right arrow / Space** — next slide
- **Left arrow** — previous slide
- **Click slide counter** — jump to any slide
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
<img src="your-image.jpg" style="width: 40vmin; height: 25vmin; border-radius: var(--radius-lg);">
```

---

## Reference

- [SKILL.md](SKILL.md) — Full workflow and generation rules
- [SLIDE_INVENTORY.md](SLIDE_INVENTORY.md) — Every template: structure, capacity, editable elements
- [VISUAL_PATTERNS.md](VISUAL_PATTERNS.md) — Charts, timelines, image placeholders
- [ICON_MATCHING.md](ICON_MATCHING.md) — 267 icons organized by semantic meaning
- [DESIGN_SYSTEM.md](DESIGN_SYSTEM.md) — Complete token and component reference
