# Icons

**Material Symbols** are the default (variable font; styles: outlined, rounded, sharp). Legacy Material Icons still exist but lack the variable axes. Get them at fonts.google.com/icons (resize/recolor/copy) or the Figma plugin.

## Choosing a style

- **Outlined** — light, clean, best in dense UIs; stroke weight can complement or contrast the type weight.
- **Rounded** — pairs with heavier type, curved logos, circular brand elements (fits Expressive's rounded shapes).
- **Sharp** — crisp, legible small; suits brands not expressed by rounded shapes.
- Keep one style and one weight throughout an icon set; don't mix them.
- Some symbols stay filled regardless of state (full-body human figures, proprietary icons) for legibility.

## Axes

| Axis | Range | Use |
|---|---|---|
| Weight | 100–700 (default 400 ≈ 2dp stroke) | Match type weight. At 24dp never go below **200**; be careful with very heavy weights |
| Fill | 0–1 | State transitions: unfilled → filled for selected/active (e.g. selected nav item) |
| Grade | negative (−25), 0, positive | Finer thickness without size change. **−25 for light icons on dark backgrounds**, 0 for dark on light (compensates visual bleed). Positive grade for emphasized/active state. Match the text font's grade |
| Optical size | 20–48dp | Keeps stroke weight visually constant as size changes; set it to the rendered size instead of scaling a 24dp vector |

Compose: Symbols are usually imported as vector drawables (Android Studio *Vector Asset* or the Google Fonts download) — one drawable per state (outlined + filled) for fill transitions; the `material-icons-extended` artifact is the legacy set. For live axes, render the Symbols variable font as text with `FontVariation` settings.

## Sizes and targets

- Standard 24dp. Optical sizes 20dp (desktop, dense layouts), 40dp and 48dp (next to display/headline type, large screens, and to highlight primary actions).
- **Touch target 48dp for a 24dp icon.** The icons page allows a 20dp icon a 40dp target when mouse/keyboard is the primary input, but the layout/density guidance says accessible targets keep **48×48dp minimum** even when the icon is smaller — default to 48dp; go below only for pointer-only dense UIs, deliberately.

## Icons with text

- Same size and same optical weight as the adjacent text.
- Shift the symbol's baseline down by **≈11.5% of the text size** — don't share the text baseline.
- Labels for abstract icons, and **navigation items always have labels**.
- Below 20dp, complex icons and icons for key actions need a text label; simple ones (rating stars) can stand alone.
- Icon-only buttons need `contentDescription` (accessibility requirement; tooltip optional).
- Localize: test icon meaning across cultures, ages, and tech familiarity (owl = wisdom vs bad omen; white = purity vs mourning).

## Designing custom icons

Only when the 2,000+ Symbols don't cover it. Use the 24dp keyline template.

- Simple, modern, friendly, graphic and bold. **Don't** be overly literal, complex, delicate, or use loose organic shapes.
- Content in the **20×20dp live area** with 2dp padding; extra visual weight may enter the padding; nothing outside the 24dp trim area.
- Build on keyline shapes and the icon grid; place on-pixel.
- Stroke 2dp (weight 400), consistent weights, **squared stroke terminals** (not rounded). Corner radius 2dp; outlined style keeps interior corners square; shapes ≤ 2dp wide have unrounded stroke corners. Rounded style rounds interior and exterior; sharp style goes to 0dp.
- Consistent corner radii; overly round corners hurt legibility.
- Face forward: no tilt, rotation, or 3D look. Optical corrections must use the same geometric forms, no skewing.

Sources: m3.material.io/styles/icons/{overview, designing-icons, applying-icons}.
