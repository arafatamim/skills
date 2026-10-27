# Typography

## Type scale

Five roles × three sizes. Each also has an **emphasized** twin (`displayLargeEmphasized` … `labelSmallEmphasized`, heavier weight).

| Role | Sizes L/M/S (sp) | Use | Font guidance |
|---|---|---|---|
| Display | 57 / 45 / 36 | Largest text: short, important text or numerals; best on large screens | Expressive fonts welcome (even handwritten/script); set optical size |
| Headline | 32 / 28 / 24 | Short, high-emphasis text on smaller screens; marks primary passages/regions | Expressive faces OK with proper line height and tracking |
| Title | 22 / 16 / 14 | Medium-emphasis, relatively short text; divides secondary passages/regions; app bar and card titles | **Caution** with expressive/display/script fonts |
| Body | 16 / 14 / 12 | Longer passages | Readable workhorse faces; **no** expressive or decorative fonts |
| Label | 14 / 12 / 11 | Utilitarian: text inside components, captions. Buttons use `labelLarge` | — |

**Material components don't use emphasized styles by default** — swap the baseline token for its emphasized twin (`md.sys.typescale.emphasized.display-large`). Official guidance: use them for selection, actions, headlines and editorial treatments; in components to mark active/selected state, unread messages, or key interactions. Listed as working well with: **badges, buttons for primary actions, extended FAB, selected list items, selected menu items**. Also: hero headlines and key numbers (Aura: "18", "3min"). Apply them to text that already carries weight in the hierarchy.

## Hierarchy

- Type separates levels of information: the largest, most legible text can signal the primary action; smaller text signals secondary/tertiary ones. Group similar content with the same style.
- Build hierarchy with size + weight contrast **between** roles, not five weights inside one role.
- One family is normal; at most two (a display face for Display/Headline, a workhorse for the rest).
- Keep text spacing consistent while other things animate. Aura caution: letter-spacing shifting (condensed "inhale" → expanded "exhale") at the same time as moving arrows and a morphing flower competes for attention.
- A custom `Typography(...)` must also define the emphasized styles, or they silently fall back to the default font.
- Customize a typeface by adjusting line height and letter spacing; **avoid changing type sizes** — components render and reflow from them. Heavier fonts may need wider tracking; long ascenders/descenders need different line heights.
- Roboto (static) is the component default; Roboto Flex is variable but not yet part of the M3 type scale. With Roboto Flex, set font fallback Roboto Flex → Roboto → Noto Sans (Noto covers 150+ scripts).

## Typesetting

- **Android: specify vertical spacing relative to baselines** (baseline-to-baseline line height; reference point to baseline spacing). Compose: `Modifier.paddingFromBaseline(top, bottom)` / `alignByBaseline()`.
- **Web/iOS**: line height = bounding box; equal top/bottom padding (CSS half-leading); spacing from box edges.
- Material's line heights are tuned per token — don't override them casually. Guide ratios: ~1.2× type size for title/headline/display, ~1.5× for body/label.
- Line heights can adapt to the script's **language height** category (small, medium, large, extra large); components with fixed heights may not adapt by default — prefer min heights.

## Readability

- **Line length**: 40–60 characters for long body text; 20–40 for short lines. Constrain width on large screens.
- **Tabular figures** for values that change or align (clocks, timers, tables): `fontFeatureSettings = "tnum"`.
- **Contrast**: ≥ 4.5:1 small text, ≥ 3:1 large text. Default text color `onSurface`; `onSurfaceVariant` for lower emphasis.
- **Links**: `primary` on surface (`tertiary` for less prominent), always underlined.
- **Font scale**: text sizes are `sp` on Android (never `dp`); test at 200%; let containers grow (`heightIn(min = …)`). Spacing stays the same when text scales.

## Scaling across devices

- Watch → phone: increase headline, display, and body.
- Phone/tablet → desktop: increase display and headline.
- Desktop → TV: increase headline and body.
- In multi-column layouts, keep focus on the most prominent card by increasing its type size as the layout widens.

## Variable font axes

| Axis | Effect | Guidance |
|---|---|---|
| Weight | Overall stroke thickness | Very light weights at small body sizes break on low-res displays (save them for display sizes); excessive weight at small sizes hurts legibility |
| Grade | Fine thickness without changing width or line breaks | Use to emphasize without reflow; match grade with icons |
| Width | Horizontal space per glyph | Narrow fits more in labels; avoid wide styles in tight spaces like top app bars |
| Optical size | Glyph design tuned for a size | Match optical size to rendered size; don't use large optical sizes at small sizes |

Compose: `FontVariation.Settings(FontVariation.weight(…), FontVariation.grade(…), FontVariation.width(…), FontVariation.opticalSizing(…))` on a variable `Font`.

## Editorial treatments

Stand-alone, type-driven showcase moments (custom large sizes, blockier looks) that depart from functional layouts. Combine with motion, shape, and color for product-wide hero moments.

- **Use for**: celebrating content (a memory, an achievement, a preference), the voice of the user (let users personalize type or reflect their input), or bespoke functionality.
- Match the emotion: narrow/thin for serenity, bold/italic for liveliness.
- Guard rails: tokenize each treatment and reuse it for similar moments; don't mix clashing treatments in one layout; don't mimic personalization theming; **never** use editorial treatments for labels or plain information.

Sources: m3.material.io/styles/typography/{applying-type, editorial-treatments, type-scale-tokens, fonts}.
