# ClawsHub CSS Design System v2.0

## 📋 Overzicht

Een volledig vernieuwde, modulaire CSS architectuur voor ClawsHub met focus op onderhoudbaarheid, performance en herbruikbaarheid.

## 🏗️ Architectuur

### Laag Structuur
Maakt gebruik van CSS `@layer` voor betere cascade controle:

```
@layer tokens, animations, reset, base, components, specialized, atmospherics, utilities;
```

### Bestandsstructuur

```
src/styles/
├── main.css                    # Master entry point
├── 00-tokens.css               # Design tokens (colors, spacing, typography)
├── 01-animations.css           # Keyframes en animatie utilities
├── 02-base.css                 # Reset en global styles
├── 03-components.css           # Herbruikbare componenten
├── 04-claws-specialized.css    # ClawsHub-specifieke componenten
└── 05-utilities.css            # Helper classes en atmospherics
```

## 🎨 Design Tokens

### Kleursysteem (OKLCH)
We gebruiken OKLCH kleurruimte voor:
- Perceptueel uniforme kleuren
- Betere gradient rendering
- Betrouwbare color-mix operaties

```css
--primary-bright: oklch(66% 0.22 355);
--secondary-bright: oklch(78% 0.15 185);
```

### Spacing Scale
T-shirt sizing pattern voor consistentie:

```css
--space-1: 0.25rem;   /* 4px */
--space-2: 0.5rem;    /* 8px */
--space-4: 1rem;      /* 16px */
--space-6: 1.5rem;    /* 24px */
--space-8: 2rem;      /* 32px */
/* etc. */
```

### Fluid Typography
Gebruikt `clamp()` voor responsieve tekst zonder media queries:

```css
--text-base: clamp(0.95rem, 0.9rem + 0.25vw, 1rem);
--text-2xl: clamp(1.35rem, 1.25rem + 0.5vw, 1.5rem);
```

## 🎭 Animaties

### Centraal Beheer
Alle keyframes in één bestand voor:
- Geen duplicatie
- Eenvoudig onderhoud
- Performance optimalisatie

### Utility Classes
```css
.animate-fade-in-up    /* Entrance animation */
.animate-float         /* Continuous floating */
.animate-pulse         /* Pulsing effect */
.animate-gradient-shift /* Animated gradients */
```

### Customization via CSS Variables
```css
.lobster-icon {
  --float-end: -10px;        /* Custom float distance */
  --float-duration: 3s;       /* Custom timing */
}
```

### Performance
- `will-change` toegevoegd waar nuttig
- `contain` property voor isolatie
- Hardware acceleration voor transforms

## 🧩 Componenten

### Button Systeem
```html
<button class="btn btn-primary">Primary</button>
<button class="btn btn-secondary btn-lg">Large Secondary</button>
<button class="btn btn-outline btn-sm">Small Outline</button>
```

Varianten:
- `btn-primary` - Main CTA met gradient
- `btn-secondary` - Secondary actions
- `btn-outline` - Tertiary actions
- `btn-ghost` - Minimal styling

Sizes: `btn-sm`, default, `btn-lg`, `btn-xl`

### Cards
```html
<div class="card">
  <h3>Card Title</h3>
  <p>Card content</p>
</div>

<div class="card card-elevated card-interactive">
  <!-- Enhanced card -->
</div>
```

### Forms
```html
<label class="field-label">
  Email
  <span class="field-description">Optional</span>
</label>
<input type="email" class="input" placeholder="you@example.com">

<textarea class="textarea"></textarea>
<select class="select">...</select>
```

### Badges & Pills
```html
<span class="badge badge-primary">New</span>
<span class="pill">
  <Icon />
  WhatsApp
</span>
```

## 🦞 ClawsHub Specialisaties

### Hero Section
```html
<div class="hero">
  <div class="lobster-icon"><!-- SVG --></div>
  <h1 class="title">
    <span class="title-main">OpenClaw</span>
  </h1>
  <p class="tagline">The AI that actually does things.</p>
  <p class="description">...</p>
</div>
```

### Testimonials Carousel
```html
<section class="testimonials">
  <div class="testimonials-track">
    <div class="testimonials-row row-1" style="--duration: 80s">
      <div class="testimonial-card">...</div>
    </div>
  </div>
</section>
```

### Features Grid
```html
<section class="features">
  <div class="features-grid">
    <div class="feature-card">
      <div class="feature-icon"><!-- Icon --></div>
      <h3 class="feature-title">Feature</h3>
      <p class="feature-desc">Description</p>
    </div>
  </div>
</section>
```

## 🌌 Atmosferische Effecten

### Background Effects
```html
<div class="stars"></div>
<div class="nebula"></div>
```

Of via CSS:
```css
.custom-bg {
  background: var(--gradient-midnight), var(--gradient-sheen);
}
```

### Glass Morphism
```html
<div class="glass">Glass effect content</div>
<div class="glass-strong">Stronger glass</div>
```

### Glow Effects
```html
<div class="glow-primary">Content with primary glow</div>
<div class="shadow-bleed">Content with bleeding glow</div>
```

## 🛠️ Utility Classes

### Layout
```css
.container          /* Max-width container */
.container-wide     /* Wider variant */
.grid               /* CSS Grid */
.grid-auto-fit      /* Responsive grid */
.flex-center        /* Centered flex */
```

### Typography
```css
.text-gradient      /* Gradient text */
.text-primary       /* Primary text color */
.truncate           /* Single line truncate */
.line-clamp-3       /* Multi-line truncate */
```

### Interactions
```css
.interactive        /* Hover lift effect */
.clickable          /* Cursor pointer */
.no-select          /* Disable text selection */
```

### Visibility
```css
.hidden             /* Display none */
.sr-only            /* Screen reader only */
.opacity-50         /* 50% opacity */
```

## ⚡ Performance Optimalisaties

### 1. CSS Containment
```css
.card {
  contain: layout style paint;
}
```

### 2. Will-Change
Alleen op geanimeerde elementen:
```css
.animate-float {
  will-change: transform;
}
```

### 3. Hardware Acceleration
```css
.gpu-accelerated {
  transform: translateZ(0);
  backface-visibility: hidden;
}
```

### 4. Lazy Loading
Complexe effecten worden niet toegepast op off-screen content.

## ♿ Toegankelijkheid

### Focus States
Alle interactieve elementen hebben duidelijke focus states:
```css
:focus-visible {
  outline: 3px solid var(--secondary-bright);
  outline-offset: 2px;
}
```

### Reduced Motion
```css
@media (prefers-reduced-motion: reduce) {
  * {
    animation-duration: 0.01ms !important;
  }
}
```

### Screen Reader Support
```html
<span class="sr-only">Hidden text for screen readers</span>
```

### Skip Links
```html
<a href="#main" class="skip-to-main">Skip to main content</a>
```

## 📱 Responsive Design

### Container Queries Ready
Componenten gebruiken waar mogelijk container queries in plaats van media queries.

### Breakpoints
```css
--breakpoint-sm: 640px;
--breakpoint-md: 768px;
--breakpoint-lg: 1024px;
--breakpoint-xl: 1280px;
```

### Mobile-First Utilities
```css
.sm\:hidden         /* Hidden on small screens */
.md\:grid-cols-2    /* 2 columns on medium+ */
.lg\:grid-cols-4    /* 4 columns on large+ */
```

## 🔄 Migratie Gids

### Van Oude naar Nieuwe Structuur

#### 1. Update Imports
**Oud:**
```html
<link rel="stylesheet" href="/src/styles/theme.css" />
<link rel="stylesheet" href="/src/styles/components.css" />
<link rel="stylesheet" href="/src/styles/claws.css" />
```

**Nieuw:**
```html
<link rel="stylesheet" href="/src/styles/main.css" />
```

#### 2. Update Variabele Namen
**Oud:**
```css
color: var(--coral-bright);
background: var(--cyan-glow);
```

**Nieuw:**
```css
color: var(--primary-bright);
background: var(--glow-secondary);
```

*Opmerking: Legacy aliases zijn beschikbaar voor backwards compatibility.*

#### 3. Update Animaties
**Oud:**
```css
animation: fadeInUp 0.8s ease-out;
```

**Nieuw:**
```css
/* Option 1: Use utility class */
class="animate-fade-in-up"

/* Option 2: Custom timing */
animation: fadeInUp var(--duration-base) var(--ease-out);
```

#### 4. Update Component Classes
De meeste component classes blijven hetzelfde, maar check de documentatie voor nieuwe varianten en modifiers.

## 🎯 Best Practices

### 1. Use Design Tokens
```css
/* ❌ Avoid hardcoded values */
padding: 16px;
color: #ff4d6d;

/* ✅ Use tokens */
padding: var(--space-4);
color: var(--primary-bright);
```

### 2. Compose Utilities
```html
<!-- ✅ Build with utilities where possible -->
<div class="flex flex-center gap-4">...</div>
```

### 3. Semantic Class Names
```css
/* ❌ Non-descriptive */
.box1 { }

/* ✅ Descriptive */
.feature-card { }
```

### 4. Avoid !important
Gebruik CSS layers in plaats van `!important`:
```css
@layer utilities {
  .text-primary {
    color: var(--text-primary);
  }
}
```

### 5. Performance First
```css
/* Add will-change sparingly */
.animated-element {
  will-change: transform;
  /* Remove after animation completes */
}
```

## 🐛 Debugging

### Show Grid Overlay
```html
<body class="show-grid">
```

### Performance Hints
```html
<body class="perf-hints">
```

### Missing Alt Text Detection
Automatisch rood outline op images zonder `alt` attribute.

## 📦 Browser Support

- **Modern Browsers**: Chrome 90+, Firefox 88+, Safari 14+, Edge 90+
- **Features**: CSS Layers, Container Queries, OKLCH colors
- **Fallbacks**: Provided for older browsers waar mogelijk

## 🚀 Toekomstige Verbeteringen

1. **CSS Modules** - Per-component scoping
2. **View Transitions API** - Smooth page transitions
3. **Scroll-Driven Animations** - Parallax effects
4. **Cascade Layers** - Better specificity management
5. **Custom Properties Animation** - Smoother gradient transitions

## 📚 Bronnen

- [CSS Layers MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/@layer)
- [OKLCH Color Space](https://oklch.com/)
- [Container Queries](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Container_Queries)
- [CSS Containment](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Containment)

## 🤝 Contributing

Bij het toevoegen van nieuwe componenten:

1. Plaats in juiste bestand (components vs specialized)
2. Gebruik design tokens
3. Voeg utility class toe indien herbruikbaar
4. Document in README
5. Test op verschillende schermformaten
6. Check toegankelijkheid

## 📄 License

MIT License - zie LICENSE.txt voor details
