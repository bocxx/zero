# AGENTS.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Project Overview

OpenClaw landing page — a static Astro 5 site for showcasing the OpenClaw AI assistant product. Features a dark theme with coral/cyan accent colors, testimonial carousels, showcase grid, and blog.

## Commands

```bash
npm run dev       # Start dev server (localhost:4321)
npm run build     # Build static site to dist/
npm run preview   # Preview production build
```

## Architecture

### Core Structure

- `src/layouts/Layout.astro` — Base HTML template with meta tags, fonts (Clash Display + Satoshi from Fontshare), and CSS imports
- `src/pages/` — Astro pages (index, blog, showcase, shoutouts, integrations)
- `src/components/Icon.astro` — Wrapper for lucide icons and simple-icons SVG paths
- `src/content/blog/` — Markdown blog posts using Astro content collections
- `src/content/config.ts` — Blog collection schema (title, description, date, author, authorHandle, draft, tags, image)

### Design System (`src/styles/`)

- `theme.css` — Design tokens: colors, typography, spacing, shadows, transitions. Colors use `--color-primary-*` (violet) and `--color-secondary-*` (lime) naming, with legacy aliases `--coral-*` and `--cyan-*` in Layout.astro
- `components.css` — Reusable component classes (`.btn`, `.card`, `.grid`, etc.)
- `openclaw-components.css` — Project-specific component overrides

### Data Files (`src/data/`)

**Important**: JSON order is rendered directly — the code does NOT re-sort. Maintain ordering in the files.

#### `showcase.json`
- "What People Are Building" page content
- **Pre-sorted into uniform row groups** for consistent CSS grid heights:
  - SHORT rows (<200 chars)
  - MED rows (200-400 chars)  
  - LONG rows (>400 chars)
- Fields: `id`, `author`, `quote`, `category`, `likes`, `images?` (array of URLs)

#### `testimonials.json`
- Short praise quotes for homepage carousel and shoutouts page
- Sorted by quote length (longest/most detailed first)
- Deduplicated by author (keep longest quote)
- Backup: `testimonials-backup.json` contains removed entries

#### `testimonials-extra.json`
- Additional testimonials shown only on `/shoutouts` page

### Adding New Testimonials/Showcase Items

1. Use `bird read <tweet_id>` to fetch tweet content and like count
2. "Building" tweets with projects/workflows → `showcase.json`
3. "Praise" tweets (short reactions) → `testimonials.json`
4. Re-sort after batch additions following the grouping rules above

### Showcase sorting pattern

```js
// Group by size for uniform row heights
const longs = sorted.filter(t => t.quote.length > 400);
const meds = sorted.filter(t => t.quote.length > 200 && t.quote.length <= 400);
const shorts = sorted.filter(t => t.quote.length <= 200);
// Alternate: 3 shorts, 3 meds, 3 longs, repeat
// Keep high-engagement items near top within each category
```

### Icons

Uses two icon systems:
- `simple-icons` — Brand icons (siWhatsapp, siTelegram, etc.) accessed via `.path` property
- `@lucide/astro` — UI icons via `<Icon icon="lucide:icon-name" />`

### Site Configuration

`astro.config.mjs`:
- Static output mode
- Site URL: `https://clawd.bot`
- Assets output to `assets/` directory
