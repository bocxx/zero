# Stap-voor-Stap Migratiegids

## Voorbereiding

### 1. Backup Maken
```bash
# Kopieer huidige CSS bestanden naar backup folder
cp -r src/styles src/styles.backup

# Of maak een git commit
git add src/styles
git commit -m "Backup: CSS before refactor"
```

### 2. Dependencies Checken
Geen extra dependencies nodig! Alles is vanilla CSS.

## Migratie Stappen

### Stap 1: Nieuwe Bestanden Toevoegen (5 min)

1. Plaats de volgende bestanden in `src/styles/`:
   - `00-tokens.css`
   - `01-animations.css`
   - `02-base.css`
   - `03-components.css`
   - `04-claws-specialized.css`
   - `05-utilities.css`
   - `main.css`

2. Verifieer dat alle bestanden aanwezig zijn:
```bash
ls src/styles/
```

### Stap 2: Update Layout.astro (2 min)

**Vervang:**
```astro
<!-- Oude imports -->
<link rel="stylesheet" href="/src/styles/theme.css" />
<link rel="stylesheet" href="/src/styles/components.css" />
<link rel="stylesheet" href="/src/styles/claws.css" />
```

**Door:**
```astro
<!-- Nieuwe import -->
<link rel="stylesheet" href="/src/styles/main.css" />
```

**Verwijder ook** de inline `<style is:global>` block met legacy aliases (deze zitten nu in main.css).

### Stap 3: Oude Bestanden Archiveren (1 min)

**Niet meteen verwijderen!** Bewaar voor referentie:

```bash
mkdir src/styles/legacy
mv src/styles/theme.css src/styles/legacy/
mv src/styles/components.css src/styles/legacy/
mv src/styles/claws.css src/styles/legacy/
```

### Stap 4: Test de Website (10 min)

1. Start development server:
```bash
npm run dev
# of
pnpm dev
```

2. Check deze pagina's:
   - Homepage (/)
   - Blog posts
   - Integrations
   - Showcase
   - Any custom pages

3. Let op:
   - ✅ Kleuren kloppen
   - ✅ Spacing is consistent
   - ✅ Animaties werken
   - ✅ Hover states functioneren
   - ✅ Responsive design werkt

### Stap 5: Opschonen Inline Styles (20 min)

In je Astro componenten (bijv. `index.astro`), vervang inline `<style>` blocks door utility classes waar mogelijk.

**Voorbeeld transformatie:**

**Voor:**
```html
<div style="display: flex; gap: 12px; margin-bottom: 24px;">
  ...
</div>
```

**Na:**
```html
<div class="flex gap-3 mb-6">
  ...
</div>
```

**Voor complexe componenten**, laat de `<style>` block staan maar:
- Gebruik design tokens in plaats van hardcoded values
- Verwijder duplicate animatie definities

**Voorbeeld:**
```css
/* Voor */
<style>
.custom-component {
  padding: 20px;
  background: rgba(10, 15, 26, 0.6);
  border-radius: 12px;
}
</style>

/* Na */
<style>
.custom-component {
  padding: var(--space-5);
  background: color-mix(in oklch, var(--bg-panel) 60%, transparent);
  border-radius: var(--radius-lg);
}
</style>
```

### Stap 6: Update Animaties (15 min)

Zoek naar custom animatie definities in componenten en vervang door utility classes:

**Voor:**
```astro
<div style="animation: fadeInUp 0.8s ease-out;">
  Content
</div>
```

**Na:**
```astro
<div class="animate-fade-in-up">
  Content
</div>
```

Of met custom timing:
```astro
<div class="animate-fade-in-up" style="animation-delay: 0.3s;">
  Content
</div>
```

### Stap 7: Modernize Colors (10 min)

Als je custom kleuren hebt in componenten, update naar OKLCH:

**Voor:**
```css
background: rgba(255, 77, 109, 0.4);
```

**Na:**
```css
background: color-mix(in oklch, var(--primary-bright) 40%, transparent);
```

### Stap 8: Browser Testing (15 min)

Test in verschillende browsers:
- ✅ Chrome/Edge (Chromium)
- ✅ Firefox
- ✅ Safari (als beschikbaar)

Check vooral:
- Gradient rendering (OKLCH voordeel!)
- Backdrop filters (Safari compatibility)
- Animaties (performance)

### Stap 9: Performance Check (10 min)

1. Open Chrome DevTools > Performance
2. Record een sessie terwijl je door de site navigeert
3. Check:
   - Layout shifts (should be minimal)
   - Paint times (should be quick)
   - Animation frame rate (should be 60fps)

### Stap 10: Accessibility Audit (10 min)

1. Chrome DevTools > Lighthouse
2. Run accessibility audit
3. Verify:
   - ✅ Focus states zichtbaar
   - ✅ Color contrast sufficient
   - ✅ Reduced motion respected

## Verificatie Checklist

Na migratie, controleer:

### Visual
- [ ] Kleuren zijn hetzelfde/beter
- [ ] Spacing is consistent
- [ ] Typography is correct
- [ ] Gradients zien er smooth uit
- [ ] Shadows en glows werken

### Functionality
- [ ] Alle animaties werken
- [ ] Hover states functioneren
- [ ] Focus states zichtbaar
- [ ] Responsive breakpoints correct
- [ ] Mobile view werkt goed

### Performance
- [ ] Pagina laadt snel
- [ ] Animaties zijn smooth (60fps)
- [ ] Geen layout shifts
- [ ] CSS bundle size acceptabel

### Accessibility
- [ ] Keyboard navigatie werkt
- [ ] Screen reader friendly
- [ ] Reduced motion werkt
- [ ] High contrast mode support

## Troubleshooting

### Probleem: Kleuren zien er anders uit

**Oplossing**: Check of browser OKLCH ondersteunt. Zo niet, voeg fallback toe:
```css
background: #ff4d6d; /* fallback */
background: var(--primary-bright); /* OKLCH */
```

### Probleem: Animaties werken niet

**Oorzaak**: Mogelijk incorrect layer order of missing imports.

**Oplossing**: 
1. Verify `main.css` import order
2. Check browser console voor errors
3. Ensure `@layer` declarations match

### Probleem: Spacing lijkt verschillend

**Oorzaak**: Mogelijk gebruik van oude variable names.

**Oplossing**: Find/replace oude names:
```
--space-xs → --space-1
--space-sm → --space-2
--space-md → --space-4
--space-lg → --space-6
```

### Probleem: CSS bundle te groot

**Oplossing**: 
1. Remove unused legacy files
2. Use PurgeCSS in production
3. Split by route if using SSR

## Post-Migratie Optimalisaties

### 1. Component Audit
Review alle componenten en vervang custom styles door utilities waar mogelijk.

### 2. Performance Tuning
Add `will-change` alleen waar nodig, remove na animatie.

### 3. Documentation
Update component documentation met nieuwe class names.

### 4. Team Training
Deel deze gids met team members.

## Rollback Plan

Als er problemen zijn:

```bash
# Restore backup
rm -rf src/styles
mv src/styles.backup src/styles

# In Layout.astro, restore old imports
```

Of via git:
```bash
git checkout HEAD -- src/styles
```

## Support

Bij vragen of problemen:
1. Check README.md voor documentatie
2. Review deze migratiegids
3. Check browser console voor errors
4. Gebruik DEBUG mode (zie README)

## Tijdsinschatting

| Stap | Tijd | Cumulatief |
|------|------|------------|
| Voorbereiding | 5 min | 5 min |
| Bestanden toevoegen | 5 min | 10 min |
| Layout update | 2 min | 12 min |
| Testing | 10 min | 22 min |
| Inline styles opschonen | 20 min | 42 min |
| Animaties updaten | 15 min | 57 min |
| Colors modernizen | 10 min | 67 min |
| Browser testing | 15 min | 82 min |
| Performance check | 10 min | 92 min |
| Accessibility audit | 10 min | 102 min |

**Totaal: ~1.5-2 uur** (afhankelijk van custom code)

## Success Criteria

Migratie is geslaagd wanneer:

✅ Alle pagina's zien er visueel hetzelfde/beter uit
✅ Geen console errors
✅ Performance metrics gelijk of beter
✅ Accessibility score gelijk of beter
✅ Smaller CSS bundle size
✅ Team kan nieuwe system gebruiken

## Next Steps

Na succesvolle migratie:

1. **Document custom components** - Add to component library
2. **Create pattern library** - Showcase all components
3. **Setup Storybook** (optional) - For component development
4. **Optimize bundle** - Remove unused CSS
5. **Monitor performance** - Track metrics over time
