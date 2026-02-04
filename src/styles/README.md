# Design System Documentation

The Claws design system centralizes tokens, surfaces, and components so the site stays visually consistent while still feeling alive and sharp.

## 📁 Structure

```
src/styles/
├── theme.css               # Core design tokens (colors, typography, spacing)
├── components.css          # Shared UI components & utilities
├── openclaw-components.css # Product-specific patterns layered on top
└── README.md               # This file
```

## 🎨 How to Use

### 1. Importing

`Layout.astro` already loads the theme and component layers:

```astro
<link rel="stylesheet" href="/src/styles/theme.css" />
<link rel="stylesheet" href="/src/styles/components.css" />
<link rel="stylesheet" href="/src/styles/openclaw-components.css" />
```

### 2. Tokens First

Grab design tokens via CSS variables—no hard-coded values:

```css
.claw-card {
  /* Colors */
  color: var(--text-primary);
  background: var(--bg-panel);

  /* Type */
  font-family: var(--font-display);
  font-size: var(--text-lg);

  /* Layout */
  padding: var(--space-4);
  border-radius: var(--radius-xl);
  box-shadow: var(--shadow-sm);

  /* Motion */
  transition: transform var(--transition-base), box-shadow var(--transition-base);
}
```

### 3. Drop-in Components

Use the pre-built classes to compose layouts quickly:

```astro
<!-- Buttons -->
<div class="grid grid-4">
  <button class="btn btn-primary">Deploy Agent</button>
  <button class="btn btn-secondary">Join Discord</button>
  <button class="btn btn-outline">Docs</button>
  <button class="btn btn-destructive">Shut Down</button>
</div>

<!-- Top Navigation -->
<nav class="topbar">
  <a class="brand" href="#">🦞 Claws</a>
  <div class="nav-links">
    <a class="nav-link is-active" href="#">Overview</a>
    <a class="nav-link" href="#">Channels</a>
    <a class="nav-link" href="#">Skills</a>
  </div>
  <button class="btn btn-secondary btn-sm">Launch App</button>
</nav>

<!-- Tabs -->
<div class="tabs">
  <div class="tab-list">
    <button class="tab is-active">Setup</button>
    <button class="tab">Automation</button>
    <button class="tab">Security</button>
  </div>
  <div class="tab-panel">
    <p class="media-subtitle">Guided instructions render here.</p>
  </div>
</div>

<!-- Stats -->
<div class="stat-group grid grid-3">
  <div class="stat-card">
    <p class="stat-label">Active Agents</p>
    <p class="stat-value">42</p>
    <p class="stat-trend">▲ 12% WoW</p>
  </div>
</div>
```

## 🔧 Customizing the Theme

### Update the Claws Palette

`src/styles/theme.css` holds all core tokens:

```css
:root {
  /* Primary Claw Coral */
  --color-primary-bright: #ff4d6d;
  --color-primary-mid: #ff2151;
  --color-primary-dark: #c10f3f;

  /* Secondary Abyssal Cyan */
  --color-secondary-bright: #16f2d7;
  --color-secondary-mid: #0fb8c8;
  --color-secondary-dark: #0b839c;
}
```

All components pick up new colors automatically.

### Alternate Palettes

```css
/* Deep Current */
--color-primary-bright: #ff6f61;
--color-primary-mid: #f94144;
--color-primary-dark: #a4161a;
--color-secondary-bright: #4d96ff;
--color-secondary-mid: #3a6de5;
--color-secondary-dark: #274690;

/* Tidal Emerald */
--color-primary-bright: #1dd3b0;
--color-primary-mid: #19b79c;
--color-primary-dark: #12876f;
--color-secondary-bright: #f9a03f;
--color-secondary-mid: #f6871f;
--color-secondary-dark: #c86b11;
```

## 📚 Token Reference

### Colors

| Token | Purpose | Default |
|-------|---------|---------|
| `--color-primary-bright` | Hero accents, primary buttons | `#ff4d6d` |
| `--color-primary-mid` | Hover states | `#ff2151` |
| `--color-primary-dark` | Active states, shadows | `#c10f3f` |
| `--color-secondary-bright` | Secondary actions, tabs | `#16f2d7` |
| `--color-tertiary-bright` | Charts, supporting highlights | `#6d8bff` |
| `--bg-deep` | Page background | `#04060d` |
| `--bg-panel` | Elevated surfaces | `#162137` |
| `--bg-glass` | Frosted overlays | `rgba(10, 16, 28, 0.72)` |
| `--text-primary` | Main text | `#f4f7ff` |
| `--text-secondary` | Secondary copy | `#9aa6ca` |
| `--border-subtle` | Low emphasis borders | `rgba(154, 166, 202, 0.16)` |

### Typography

| Token | Size | Usage |
|-------|------|-------|
| `--text-xs` | 12px | Labels, stats |
| `--text-sm` | 14px | Secondary text |
| `--text-base` | 16px | Body copy |
| `--text-lg` | 18px | Lead paragraphs |
| `--text-2xl` | 24px | Section headings |
| `--text-3xl` | 30px | Page headings |
| `--text-5xl` | 48px | Hero headlines |

### Spacing

| Token | Size | Usage |
|-------|------|-------|
| `--space-1` | 4px | Tight gutters |
| `--space-2` | 8px | Chips, pills |
| `--space-3` | 12px | Form controls |
| `--space-4` | 16px | Default padding |
| `--space-6` | 24px | Section rhythm |
| `--space-8` | 32px | Feature blocks |
| `--space-16` | 64px | Hero spacing |

## 🧱 Component Cheat Sheet

**Buttons** – `.btn`, `.btn-primary`, `.btn-secondary`, `.btn-tertiary`, `.btn-outline`, `.btn-ghost`, `.btn-destructive`, `.btn-sm`, `.btn-lg`

**Cards & Surfaces** – `.card`, `.card-elevated`, `.card-accent`, `.card-outline`, `.surface`, `.surface-glass`, `.surface-gradient`, `.divider`

**Forms** – `.field-label`, `.field-description`, `.input`, `.textarea`, `.select`, `.input-group`, `.input-addon`, `.toggle`

**Navigation** – `.topbar`, `.brand`, `.nav-links`, `.nav-link`, `.tabs`, `.tab-list`, `.tab`, `.tab-panel`

**Feedback & Data** – `.badge`, `.alert`, `.stat-group`, `.stat-card`, `.table`, `.timeline`, `.toast`

**Overlays & Media** – `.modal-backdrop`, `.modal`, `.tooltip`, `.avatar`, `.avatar-stack`, `.media-object`

**Effects & Motion** – `.glass`, `.glow-primary`, `.starfield`, `.nebula`, `.animate-fadeInUp`, `.shadow-ring`, `.shadow-bleed`

## 🔄 Migration Tips

- Swap inline styles for token-driven classes (`btn`, `card`, `surface`).
- Compose surfaces: start with `.card` or `.surface`, then add utilities like `.glass` or `.shadow-bleed`.
- Prefer `.nav-link.is-active` and `.tab[aria-selected="true"]` for state styling.
- Keep custom CSS minimal—extend the design system when something repeats.
- Test accessibility: focus states rely on the new `--ring-*` tokens.

## 📎 Examples

- `src/pages/index.astro` – hero, testimonials, quick-start code blocks.
- `src/pages/showcase.astro` – grid layouts, callouts, badges.
- `src/pages/integrations.astro` – cards with accent overrides (`--accent`).

Use these pages as live references for combining the Claws design system with product-specific flavor.
