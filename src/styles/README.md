# Design System Documentation

This directory contains the centralized design system for the application, making it easy to maintain consistent styling and quickly update themes.

## 📁 Structure

```
src/styles/
├── theme.css       # Core design tokens (colors, typography, spacing)
├── components.css  # Reusable component styles
└── README.md       # This file
```

## 🎨 How to Use

### 1. Importing the Design System

The design system is automatically imported in `Layout.astro`:

```astro
<link rel="stylesheet" href="/src/styles/theme.css" />
<link rel="stylesheet" href="/src/styles/components.css" />
```

### 2. Using Design Tokens

All design tokens are available as CSS custom properties (variables):

```css
.my-element {
  /* Colors */
  color: var(--color-primary-bright);
  background: var(--bg-elevated);
  
  /* Typography */
  font-family: var(--font-display);
  font-size: var(--text-lg);
  font-weight: var(--font-semibold);
  
  /* Spacing */
  padding: var(--space-4);
  margin-bottom: var(--space-6);
  
  /* Border Radius */
  border-radius: var(--radius-lg);
  
  /* Transitions */
  transition: all var(--transition-base);
}
```

### 3. Using Component Classes

Apply pre-built component classes directly in your HTML:

```astro
<!-- Buttons -->
<button class="btn btn-primary">Primary Button</button>
<button class="btn btn-secondary">Secondary Button</button>
<button class="btn btn-outline">Outline Button</button>

<!-- Cards -->
<div class="card card-interactive">
  <h3>Card Title</h3>
  <p>Card content</p>
</div>

<!-- Badges -->
<span class="badge badge-primary">New</span>

<!-- Layout -->
<div class="container">
  <section class="section">
    <div class="section-header">
      <h2 class="section-title">
        <span class="section-title-accent">⟩</span> Title
      </h2>
    </div>
    <div class="grid grid-3">
      <!-- Grid items -->
    </div>
  </section>
</div>
```

## 🔧 Customizing the Theme

### Changing Colors

To update the color scheme, edit `src/styles/theme.css`:

```css
:root {
  /* Primary Brand Colors */
  --color-primary-bright: #a855f7;  /* Main accent color */
  --color-primary-mid: #9333ea;
  --color-primary-dark: #6b21a8;
  
  /* Secondary Brand Colors */
  --color-secondary-bright: #84cc16;
  --color-secondary-mid: #65a30d;
  --color-secondary-dark: #4d7c0f;
}
```

**All components automatically update** to use the new colors!

### Example Color Schemes

#### Ocean Blue & Coral
```css
--color-primary-bright: #06b6d4;  /* Cyan */
--color-primary-mid: #0891b2;
--color-primary-dark: #0e7490;

--color-secondary-bright: #f97316; /* Orange */
--color-secondary-mid: #ea580c;
--color-secondary-dark: #c2410c;
```

#### Forest Green & Gold
```css
--color-primary-bright: #10b981;  /* Emerald */
--color-primary-mid: #059669;
--color-primary-dark: #047857;

--color-secondary-bright: #f59e0b; /* Amber */
--color-secondary-mid: #d97706;
--color-secondary-dark: #b45309;
```

#### Royal Purple & Rose
```css
--color-primary-bright: #8b5cf6;  /* Violet */
--color-primary-mid: #7c3aed;
--color-primary-dark: #6d28d9;

--color-secondary-bright: #ec4899; /* Pink */
--color-secondary-mid: #db2777;
--color-secondary-dark: #be185d;
```

## 📚 Design Token Reference

### Colors

| Token | Purpose | Default |
|-------|---------|---------|
| `--color-primary-bright` | Primary accent (buttons, links) | `#a855f7` |
| `--color-primary-mid` | Primary hover states | `#9333ea` |
| `--color-primary-dark` | Primary active states | `#6b21a8` |
| `--color-secondary-bright` | Secondary accent | `#84cc16` |
| `--color-secondary-mid` | Secondary hover | `#65a30d` |
| `--color-secondary-dark` | Secondary active | `#4d7c0f` |
| `--text-primary` | Main text | `#f0f4ff` |
| `--text-secondary` | Secondary text | `#8892b0` |
| `--text-muted` | Muted text | `#5a6480` |

### Typography

| Token | Size | Usage |
|-------|------|-------|
| `--text-xs` | 12px | Small labels |
| `--text-sm` | 14px | Secondary text |
| `--text-base` | 16px | Body text |
| `--text-lg` | 18px | Large body |
| `--text-xl` | 20px | Subtitles |
| `--text-2xl` | 24px | Section titles |
| `--text-3xl` | 30px | Page titles |
| `--text-4xl` | 36px | Hero titles |
| `--text-5xl` | 48px | Large hero |

### Spacing

| Token | Size | Common Usage |
|-------|------|--------------|
| `--space-1` | 4px | Tight spacing |
| `--space-2` | 8px | Small gaps |
| `--space-3` | 12px | Default gaps |
| `--space-4` | 16px | Medium spacing |
| `--space-6` | 24px | Large spacing |
| `--space-8` | 32px | Section spacing |
| `--space-12` | 48px | Large sections |

## 🎯 Component Classes

### Buttons
- `.btn` - Base button styles
- `.btn-primary` - Primary action button
- `.btn-secondary` - Secondary action button
- `.btn-outline` - Outline button
- `.btn-ghost` - Ghost button
- `.btn-sm` / `.btn-lg` - Size variants

### Cards
- `.card` - Base card styles
- `.card-elevated` - Elevated background
- `.card-interactive` - Adds pointer cursor

### Layout
- `.container` - Main content container (860px max)
- `.container-wide` - Wide container (1200px max)
- `.container-narrow` - Narrow container (640px max)
- `.grid` / `.grid-2` / `.grid-3` / `.grid-4` - Responsive grids

### Effects
- `.glass` - Glass morphism effect
- `.glow-primary` - Primary color glow
- `.animate-fadeInUp` - Fade in animation
- `.starfield` - Starfield background
- `.nebula` - Nebula gradient background

## 🔄 Migration Guide

If you have existing components with inline styles, migrate them to use the design system:

### Before:
```astro
<button style="background: #a855f7; padding: 12px 24px; border-radius: 12px;">
  Click me
</button>
```

### After:
```astro
<button class="btn btn-primary">
  Click me
</button>
```

### Benefits:
✅ Consistent styling across the app  
✅ Instant theme changes by updating `theme.css`  
✅ Better maintainability  
✅ Smaller bundle size (reused classes)  
✅ Built-in responsive design  
✅ Automatic dark mode support  

## 🚀 Best Practices

1. **Always use design tokens** instead of hardcoded values
2. **Prefer component classes** over custom styles when possible
3. **Update theme.css** for global changes, not individual components
4. **Test color changes** with both light and dark text
5. **Keep custom styles minimal** - extend the design system instead

## 📖 Examples

See `src/pages/index.astro` for real-world usage examples of the design system.
