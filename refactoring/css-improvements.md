# ClawsHub CSS Verbetering Analyse

## Geïdentificeerde Problemen

### 1. **Duplicatie & Inconsistenties**
- Meerdere definities van dezelfde animaties (`fadeInUp` in zowel components.css als claws.css)
- Herhaalde gradient definities
- Inconsistente spacing/sizing units (mix van px en rem)
- Duplicaat color mixings en transparencies

### 2. **Structurele Problemen**
- Te veel inline styles in Astro component
- CSS custom properties worden soms overschreven met inline values
- Geen duidelijke scheiding tussen layout, components en utilities
- Media queries verspreid door verschillende files

### 3. **Performance Issues**
- Ongeoptimaliseerde animaties (geen will-change)
- Geen lazy loading voor complexe effecten
- Inefficiënte selectors in enkele gevallen

### 4. **Onderhoudbaarheid**
- Hardcoded values die variabelen zouden moeten zijn
- Geen documentatie van complexe CSS trucs
- Moeilijk te overzien welke styles waar vandaan komen

## Voorgestelde Nieuwe Structuur

```
src/styles/
├── 00-config/
│   ├── _tokens.css       # Design tokens (colors, spacing, typography)
│   ├── _functions.css    # CSS custom properties als "functies"
│   └── _animations.css   # Centrale animatie definities
├── 01-base/
│   ├── _reset.css        # Modern CSS reset
│   ├── _typography.css   # Font imports & base styles
│   └── _globals.css      # Body, html, selection, etc.
├── 02-layout/
│   ├── _containers.css   # Container varianten
│   ├── _grid.css         # Grid systems
│   └── _sections.css     # Section layouts
├── 03-components/
│   ├── _buttons.css
│   ├── _cards.css
│   ├── _forms.css
│   ├── _navigation.css
│   └── ... (per component)
├── 04-specialized/
│   ├── _hero.css         # Hero specific styles
│   ├── _testimonials.css
│   ├── _code-blocks.css
│   └── ... (page-specific)
├── 05-utilities/
│   ├── _accessibility.css
│   ├── _backgrounds.css
│   └── _helpers.css
└── main.css              # Import orchestrator
```

## Verbeteringen per Categorie

### Design Tokens Optimalisatie
- Gebruik van oklch() color space voor betere gradiënten
- Systematische spacing scale
- Responsive typography met fluid clamp()
- Semantic color naming

### Component Optimalisaties
- BEM-achtige naming voor specifieke onderdelen
- Composable utility classes
- CSS Modules approach waar mogelijk
- Better separation of concerns

### Performance Verbeteringen
- `contain` property voor isolated components
- `content-visibility` voor off-screen content
- Proper z-index management
- Optimized animations met `will-change`

### Toegankelijkheid
- Focus visible states voor alle interactieve elementen
- Reduced motion preferences
- Color contrast checks via custom properties
- ARIA-friendly styling

## Specifieke Aanbevelingen

### 1. Consolideer Animaties
Maak één `_animations.css` met alle keyframes en gebruik CSS variables voor customization:

```css
@keyframes float {
  0%, 100% { transform: translateY(var(--float-start, 0)); }
  50% { transform: translateY(var(--float-end, -8px)); }
}

.lobster-icon {
  --float-end: -8px;
  animation: float var(--float-duration, 4s) ease-in-out infinite;
}
```

### 2. Gebruik CSS Layers
Voor betere specificiteit management:

```css
@layer reset, base, layout, components, utilities, overrides;
```

### 3. Modern CSS Features
- Container queries voor echte component-gebaseerde responsive design
- CSS Grid subgrid voor nested layouts
- `:has()` selector voor parent styling
- `color-mix()` wordt al goed gebruikt - uitbreiden!

### 4. Dark Mode Systeem
Formaliseer met prefers-color-scheme (al dark nu, maar voorbereiden):

```css
:root {
  color-scheme: dark;
}

@media (prefers-color-scheme: light) {
  :root {
    /* Light mode overrides indien gewenst */
  }
}
```

### 5. Type-safe CSS
Gebruik `@property` voor geanimeerde custom properties:

```css
@property --gradient-angle {
  syntax: '<angle>';
  initial-value: 0deg;
  inherits: false;
}
```
