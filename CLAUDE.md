# monday-presentation-v2

Design-system-first HTML presentation builder with monday.com branding.

## Quick Start

Use the skill: `/monday-presentation-v2`

## Architecture

- **SKILL.md** — The skill definition (invoked via `/monday-presentation-v2`). Contains the phased workflow, inlined brand SVG, and navigation JS.
- **design-system.css** — CSS tokens, components, and 19 template classes. Inlined verbatim into every generated HTML. Supports dark (default) and light themes via `data-theme` attribute on `<html>`.
- **slide-templates.html** — Live previews of all 19 template classes and component combinations. The single source of truth for slide layouts.
- **design-system-showcase.html** — Design token and component reference page.
- **DESIGN_SYSTEM.md** — Agent reference for design-system.css: every token value, component HTML pattern, and template class.
- **Icons/** — 268 monday.com SVG icons (including Quote.svg for testimonials). Read and inlined into generated HTML for portability.
- **Logos/** — monday.com wordmark SVGs (white + black variants).
- **RECIPES.md** — 5 presentation recipes (Product Launch, Team Review, Proposal, Training, Quick Update).
- **ICON_MATCHING.md** — Semantic icon selection guide by content type.

## Key Rules

- Generated presentations are single self-contained HTML files — zero external dependencies.
- All slides use CSS template classes (`tmpl-*`) from design-system.css — 16 available.
- All icons are inlined as SVG, not referenced via `<img src>`.
- Navigation uses class-based toggling (`slide-active`), never inline `style.display`.
- All slides lock to 16:9 aspect ratio using vmin units.
- Font: Poppins Regular (400) + SemiBold (600) only. No other weights.
- This directory is symlinked to `~/.claude/skills/monday-presentation-v2/`.
