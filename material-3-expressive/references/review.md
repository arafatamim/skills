# Reviewing a Material 3 / Expressive UI

## Output format (required)

Report findings as one markdown table, most impactful first:

| Before | After | Why |
| --- | --- | --- |
| `Color(0xFF6750A4)` on the send button | `MaterialTheme.colorScheme.primary` | Breaks dynamic color, dark and high-contrast themes |
| `Card(elevation = 8.dp)` for every row | `SegmentedListItem` on `surfaceContainer` | Tonal depth, not shadows; rows read as one group |
| `tween(300)` on chip selection | `motionScheme.defaultEffectsSpec()` for color, `fastSpatialSpec()` for size | Theme-driven, interruptible, color must not overshoot |
| Two filled buttons in the dialog | `TextButton("Cancel")` + `Button("Delete")` | One high-emphasis action per region |

Cite `file:line` in Before when reviewing code. Follow with a short "Hero and hierarchy" note: what the screen's hero is and whether the emphasis budget points at it.

## Checklist

**Hierarchy**
- [ ] One identifiable hero per screen; it has the strongest color/size/shape.
- [ ] Exactly one high-emphasis action per region.
- [ ] Expressive accents (abstract shapes, bounce, tertiary) used ≤ 2 times per screen.
- [ ] Emphasis scales with goal rank: hero screens use many tactics, secondary/tertiary screens fewer (`usability.md`).
- [ ] Different color roles for actions vs data; not primary/primaryContainer on everything.

**Color**
- [ ] No raw colors or tonal-palette values in composables (`grep -nE 'Color\(0x|Color\.(White|Black)'`).
- [ ] Every container color paired with its `on*` role; no alpha-faded text.
- [ ] `outlineVariant` for dividers/card borders; `outline` (or 3:1) for target boundaries.
- [ ] No fixed roles where contrast matters; semantic colors (error/success) not replaced by dynamic/content color.
- [ ] Surface role per region identical across breakpoints; overlapping areas use different roles.
- [ ] Content-based color stays next to its visible source; ≤ 2 scheme source types per screen.

**Tokens**
- [ ] No raw corner radii, text sizes, or animation specs (`grep -nE 'RoundedCornerShape\([0-9]|fontSize = [0-9]|tween\(|spring\('`).
- [ ] Spacing from the 8dp scale; padding/gaps on parents rather than margins on children; `start`/`end` not `left`/`right`.

**Shape**
- [ ] Corner tokens match element size; nested radii follow optical roundness (outer − padding).
- [ ] No large/full corners on information-dense components; abstract shapes not on text-heavy containers.
- [ ] Interactive Expressive components keep their pressed/selected shape morphs.

**Elevation**
- [ ] Component default elevations unchanged; separation via surface tones, shadows only over busy backgrounds or for interaction lift.
- [ ] Scrim = `scrim` role at 32% under modals.

**Icons**
- [ ] One Symbols style and weight; weight ≥ 200 at 24dp; filled variant for selected state.
- [ ] Icons match adjacent text size; nav items labelled; icon-only controls have descriptions.

**Typography**
- [ ] Body text not in expressive/decorative fonts; editorial treatments not on labels or plain info.
- [ ] Body line length 40–60 characters; tabular figures for changing numbers; links underlined.

**Motion**
- [ ] `motionScheme` specs only; spatial for movement, effects for color/alpha.
- [ ] No motion on keyboard-initiated or very frequent actions; one attention-grabbing animation at a time.
- [ ] Exits faster than enters; in-screen components expand along an axis from their anchor/edge (no scale-from-0, no z-motion).
- [ ] Transition pattern matches navigation type (container transform / forward-backward / lateral / top level / enter-exit / skeleton); no bounce, no slow cross-fades, no fading bottom sheets.
- [ ] Predictive back supported for screen transitions.
- [ ] Reduced-motion path: fades instead of slides, no parallax or shape morph.

**Layout**
- [ ] Works at Compact, Medium, Expanded (resize the emulator window or use `@PreviewScreenSizes`).
- [ ] All content in panes (1–3); components adapt (resize, show/hide, presentation change such as FAB → extended FAB).
- [ ] Edge-to-edge with correct insets; IME doesn't cover the input.
- [ ] Reading width constrained on large windows; touch, pointer and keyboard all work.

**Accessibility**
- [ ] 4.5:1 body text, 3:1 large text and component boundaries — in light, dark, dynamic, and medium/high contrast levels.
- [ ] 48dp targets; `contentDescription` on icon-only controls; `Role` and state semantics on custom toggles.
- [ ] Usable at 200% font scale (no clipped or fixed-height text).
- [ ] State not conveyed by color alone (shape, icon or label too).
- [ ] TalkBack order matches visual order; related text merged (`semantics(mergeDescendants = true)`).

## Previews to ask for (don't run them yourself unless asked)

`@PreviewLightDark`, `@PreviewFontScale`, `@PreviewScreenSizes`, `@PreviewDynamicColors` on the changed composables. If the project forbids driving emulators/screenshots, list which previews the maintainer should check instead.
