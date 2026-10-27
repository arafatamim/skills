# Color

The color system is paint-by-number: UI elements carry a **role** (the number); a scheme assigns a color to every role. Design and code against roles, never hex values — the final colors are generated per device, theme, and contrast level.

## How the system works

1. **Source color** — from the user's wallpaper (user-generated), from in-app content like album art (content-based, via quantization), or hand-picked (the baseline scheme is hand-picked).
2. **Algorithm** (Material Color Utilities, MCU) derives five **key colors**: primary, secondary, tertiary, neutral, neutral variant.
3. Each key color becomes a **tonal palette**: tones 0–100 in steps of 10, plus 95, 98, 99.
4. Tones are assigned to the 26+ roles for light *and* dark theme using accessible relationships (e.g. `primary` = primary40, `onPrimary` = primary100 in light).
5. Changing the source re-colors the whole UI; dark theme comes for free.

**HCT** (hue 0–360, chroma 0–~120, tone 0–100) is the color space. **Tone determines contrast**: bigger tone delta = more contrast. HCT tone ≠ HSL lightness — never inspect or adjust Material colors in HSL/RGB and expect predictable contrast. Some hue/tone combos cannot reach high chroma (no bright light blue), so chroma varies along a palette.

### Contrast levels

Every role supports **standard** (default), **medium**, and **high** contrast, in both themes. Pairs are ≥ 3:1; for more contrast the algorithm moves tones further apart (up to 7:1). Custom components inherit this *only* if they use correct role pairs (e.g. `primaryContainer` + `onPrimaryContainer`). The guidelines say contrast settings apply automatically to roles; in code, a static brand scheme can't adapt by itself — export medium/high variants from Material Theme Builder and pick one from `UiModeManager.getContrast()` (API 34+). Whether `dynamic*ColorScheme` reflects the setting depends on the OS version — verify on device (implementation inference, not on the guideline pages).

## Roles

Naming: **Surface** = backgrounds/large low-emphasis areas. **Primary/Secondary/Tertiary** = accents by emphasis. **Container** = fill for foreground elements (buttons etc.), never text/icon color. **On…** = text/icons on the paired color. **…Variant** = lower-emphasis alternative.

| Group | Roles | Use |
|---|---|---|
| Primary | `primary`, `onPrimary`, `primaryContainer`, `onPrimaryContainer` | Most prominent components: FAB, high-emphasis buttons, active states. `primaryContainer` = standout fill (FAB) |
| Secondary | `secondary`, `onSecondary`, `secondaryContainer`, `onSecondaryContainer` | Less prominent: filter chips, tonal buttons, selected nav icon, dismissive button |
| Tertiary | `tertiary`, `onTertiary`, `tertiaryContainer`, `onTertiaryContainer` | Contrasting accents that balance primary/secondary or heighten attention (badge, notification, an input field). At designer's discretion |
| Error | `error`, `onError`, `errorContainer`, `onErrorContainer` | Error states only. **Static**: not re-hued by dynamic color, still adapts light/dark |
| Surface | `surface`, `onSurface`, `onSurfaceVariant` | Backgrounds; text/icons on any surface or surface container |
| Surface containers | `surfaceContainerLowest`, `…Low`, `surfaceContainer` (default), `…High`, `…Highest` | Containers (cards, sheets, dialogs, nav areas); hierarchy and nesting, especially on expanded screens |
| Inverse | `inverseSurface`, `inverseOnSurface`, `inversePrimary` | Elements contrasting with surrounding UI (snackbar); `inversePrimary` for actions (text button) on `inverseSurface` |
| Outline | `outline`, `outlineVariant` | `outline`: important boundaries (text field). `outlineVariant`: decorative (dividers) |
| Scrim | `scrim` | Behind modals and expanded navigation, at 32% opacity |

### Pairing rules (official do/don't)

- Apply colors only in their intended pairs/layers. Any `on*` role is guaranteed contrast with its partner; other combos may fail 4.5:1 (small text) / 3:1 (large text) and break when contrast level changes. **Don't**: `primaryContainer` text on `primary`, or `onSurface` on `secondaryContainer`.
- **Don't** use `outline` for dividers or for borders of multi-element components (cards) → `outlineVariant`.
- **Don't** use `outlineVariant` to create hierarchy or to mark the boundary of a target → `outline` (or anything 3:1 against surface). **Exception**: `outlineVariant` borders on chips/buttons are fine when their icon/text already meet 4.5:1.
- Text defaults to `onSurface`; `onSurfaceVariant` is the lower-emphasis alternative. Don't fade text with alpha (`onSurface.copy(alpha = 0.6f)`) — it breaks contrast levels.
- Links on surface: `primary` (or `tertiary` for less prominent), and **always underlined**.
- Disabled: content 38% `onSurface`, container 12% `onSurface` (component spec values; components handle this). Disabled states don't need to meet contrast requirements. Don't show a disabled FAB — hide it when its action is unavailable.

### Surface mapping

- Most common combo: `surface` for the body, `surfaceContainer` for the navigation area.
- **Keep region mappings the same across breakpoints** (body = `surface`, nav = `surfaceContainer` on phone *and* tablet). Larger windows may add surface roles if applied consistently.
- Overlapping containment areas must use *different* roles to show separation.
- Component defaults (nav bar, menu, dialog → specific surface containers) may be remapped to suit the product.

### Add-on roles (most products don't need these)

- **Fixed** (`primaryFixed`, `primaryFixedDim`, `onPrimaryFixed`, `onPrimaryFixedVariant`, same for secondary/tertiary): same tone in light and dark. `*FixedDim` is a deeper version. **Don't** use where contrast matters — they don't flip with theme; use the normal accent roles instead.
- **`surfaceDim` / `surfaceBright`**: keep relative brightness across themes (`surfaceBright` is the brightest area in both light and dark), unlike `surface`, which inverts.

## Choosing a dynamic color source

| Source | Choose when | Result |
|---|---|---|
| **User-generated** (wallpaper) | You want a personal, well-tested experience and the newest Material features | App and system UI match the wallpaper; "personalized" |
| **Content-based** (image, logo, video preview) | Content is front-and-center; it helps a feature (media player); team can do advanced customization | UI matches on-screen imagery; "smart". Best on contained elements next to the source image |
| **Multiple** | Both apply | Start from user-generated, then layer content color |

Compose: `dynamicLightColorScheme(context)` / `dynamicDarkColorScheme(context)` on API 31+. Always ship a custom (brand or baseline) scheme as the fallback for devices/users without dynamic color. Content-based schemes need MCU (`SchemeContent` / `DynamicScheme`) — in Compose a common route is a seed-color library such as MaterialKolor (`rememberDynamicColorScheme(seedColor, isDark, style = PaletteStyle.Content)`); verify before adding a dependency.

Dynamic color can be applied **selectively**: a brand scheme app-wide, with the user's scheme in a personal space like a profile screen.

## Advanced customization — apply, define, adjust

### Apply

- **Combine schemes**: baseline or user-generated as the foundation; content-based roles mapped onto *contained* areas (the now-playing card, the purchase flow).
  - Use content color to build hierarchy, to associate related items in repeated lists (item + its action), or for full-screen immersive moments.
  - **Keep the source visible** where its color is used; don't tint areas where the content isn't shown.
  - **Max two schemes from different source types per screen** (e.g. user-generated + album art).
  - **Don't replace semantic colors** (red error, green success) with content color.
- **Map / remap**: pick the role by usage, not look. Custom components must be mapped to roles too. Test under light/dark and several hues (red, yellow, green, blue seeds). Never use static values or raw tonal-palette values — they break theme, contrast and dynamic color.

### Define new colors

- **Static colors** (formerly "custom colors"): input a reference color, get four roles back (`color`, `onColor`, `colorContainer`, `onColorContainer`) following accent conventions. Use for brand expression or semantics (success green). Don't define a red — `error` already exists.
- **Harmonize** static colors toward the scheme's primary (MCU `Blend.harmonize`; MDC `MaterialColors.harmonizeWithPrimary`) so they shift slightly warmer/cooler without changing meaning. Check under many schemes. **Don't harmonize brand colors** that must stay exact.
- **Custom roles** (last resort): specify for each role a palette (primary/secondary/tertiary/neutral/neutralVariant/error) and reference tone for light *and* dark, its foreground/background pairings or tone deltas, and verify contrast minimums. Generate values through MCU so contrast levels keep working.

### Adjust existing colors

- **Own baseline (static) scheme**: input primary, secondary, tertiary, neutral, neutral variant in Material Theme Builder. Keep conventions: primary and tertiary most prominent (tertiary = shifted hue); secondary, neutral variant, neutral share primary's hue with progressively less chroma. Don't hand-pick the 26+ roles.
- **Own dynamic scheme**: supply hue/chroma rules per color group (e.g. "more vibrant than default") as a custom MCU scheme variant.
- **Color fidelity** ("match color" in Theme Builder; on by default for custom baseline and static colors): shifts tones so outputs look like the inputs (e.g. a light input that would otherwise look washed out). Toggle and compare; still pair roles with their `on*` colors.

## Brand + dynamic color (customization)

- Put the primary brand color in as the source; the system produces complementary tones and handles contrast, legibility, and state colors. Add a second/third input only if needed.
- Use Material Theme Builder to generate the scheme and export tokens (Compose `Color.kt`/`Theme.kt`, DSP); treat the export as the source of truth.
- Dynamic color is both a user setting and a developer choice — offer a toggle if brand identity matters.

## Emphasis with color (Expressive)

- Use eye-catching primary/secondary roles for hierarchy; contrasting hues (purple vs green) beat similar ones.
- One `primary` fill per region. Different roles for **actions** vs **data** (Aura: primary button; secondary on selected settings; secondaryContainer on dates). Using primary + primaryContainer for everything flattens hierarchy.
- State layers (hover 8%, focus 10%, pressed 10%, dragged 16%) use the element's **content (`on*`) color** — e.g. `onSecondaryContainer` over a `secondaryContainer` button. One state layer at a time. Components do this; reproduce with `ripple()` on custom clickables.
- **Activated** (current destination in tabs, nav drawer, nav list) isn't a state layer: container → `secondaryContainer`, content → `onSecondaryContainer`. Only for navigation, never action buttons; one activated item per set.
- Surfaces are no longer tinted by elevation (+1…+5 overlays were replaced by surface-container roles); themes exported before Feb 2023 / Aug 2024 have stale surface and `on*Container` values — regenerate.

Sources: m3.material.io/styles/color/{roles, system/how-the-system-works, dynamic/choosing-a-source, dynamic/user-generated-source, dynamic/content-based-source, advanced/apply-colors, advanced/define-new-colors, advanced/adjust-existing-colors}, /foundations/customization, /foundations/interaction/states, /foundations/usability.
