---
name: material-3-expressive
description: Use when designing, building, or reviewing UI with Material Design 3 or M3 Expressive — Jetpack Compose MaterialTheme/MaterialExpressiveTheme, MotionScheme springs, MaterialShapes and shape morphing, button groups, split buttons, FAB menus, floating toolbars, loading indicators, flexible app bars, adaptive layouts, dynamic color, color roles, elevation, Material Symbols icons, spacing, typography, transitions — or when a Material screen looks flat, generic, cramped, or "AI-generated" and needs to feel polished.
version: 1.0.0
---

# Material 3 Expressive

## Overview

M3 Expressive is Material 3 plus a deliberate **emphasis system**: shape, color, type, size, containment and spring motion are used to make the *one thing that matters* on a screen obvious, and everything else calm. Google's research behind it (46 studies, 18k+ participants) found expressive layouts let people find the key action up to 4× faster — the point is hierarchy, not decoration.

Two rules govern every decision here:

1. **Tokens over values.** Every color, corner, type style, spacing step and motion spec comes from the theme. A raw `Color(0xFF…)`, `RoundedCornerShape(13.dp)` or `tween(300)` is a bug until proven otherwise.
2. **Expression is a budget.** One or two hero moments per screen. If everything is bold, nothing is. Frequent, repetitive interactions stay quiet and fast (see Emil's frequency rule below).

Compose is the primary target (Expressive ships first and fully there). Flutter has partial support; `@material/web` is maintenance-only and has **no** Expressive components — on the web, implement with tokens + CSS and approximate springs.

## Reference files — load the one you need

| Task | Read |
|---|---|
| Designing or redesigning a screen: the full process, emphasis ladder, composition, brand personality, screen recipes, critique rubric | `references/design-guide.md` |
| Goal ranking and the official "Aura" worked example | `references/usability.md` |
| Animating anything, springs vs easing, choosing a transition pattern | `references/motion.md` |
| Corners, shape principles, `MaterialShapes`, morphing, shape as emphasis | `references/shape.md` |
| Color roles and pairing, dynamic color sources, contrast levels, brand/static/custom colors, harmonization | `references/color.md` |
| Type scale, emphasized type, typesetting, readability, editorial treatments | `references/typography.md` |
| Elevation levels, tonal vs shadow separation, scrims | `references/elevation.md` |
| Material Symbols style/axes, icon sizing and targets, custom icon design | `references/icons.md` |
| Picking or wiring a component (buttons, groups, toolbars, FAB menu, nav, lists, search, progress) | `references/components.md` |
| Parts of layout, breakpoints, adaptive strategies, spacing system, insets, foldables | `references/layout.md` |
| Reviewing / auditing an existing screen | `references/review.md` |

## Design workflow

Any new or redesigned screen runs the ten-step process in `references/design-guide.md` §2, each step ending on its completion check. In short: brief → rank goals and cut → skeleton per breakpoint → hierarchy in grayscale (**squint test**) → containment → **emphasis ladder** → spend the expression **budget** (≤ 2 accents, each tied to the hero or a state change) → motion → state matrix → critique. Most bad Material UIs skip the ranking and decorate instead.

For a small change to an existing screen, place the touched elements on the ladder and run the critique rubric (§7) on the result.

## Expressive tactics (what actually creates the "expressive" feel)

| Tactic | Do | Don't |
|---|---|---|
| **Shape contrast** | Mix rounded containers with one abstract shape (Cookie, Clover, Sunny) for the hero element; let pressed/selected states morph shape | Put abstract shapes on every avatar, chip and card |
| **Size contrast** | Use `ButtonDefaults.LargeContainerHeight`/`MediumContainerHeight` for the primary action, medium/large flexible app bars for top-level screens | Make every button the same size |
| **Color richness** | Use `tertiary`/`tertiaryContainer` for a single accent that should pop; use the full surface-container ladder for depth | Hardcode a brand hex; use `primary` for everything |
| **Emphasized type** | `*Emphasized` styles for headlines, selected labels, key numbers | Bold every title; mix emphasized and plain at the same level |
| **Containment** | Group related controls in a `ButtonGroup`, `FloatingToolbar`, segmented list | Free-floating buttons scattered on a surface |
| **Spring motion** | Bouncy spatial spring on hero interactions (FAB menu opening, selection morph) | Bounce on scroll-linked, keyboard or high-frequency UI |

## Design-engineering overlay (from emil-design-eng, translated to Material)

Material tells you *what* to animate with; these rules decide *whether* and *how much*. They override Material defaults where they conflict with frequency.

- **Frequency decides motion.** 100+/day (keyboard shortcuts, list item taps, tab switches in a work tool) → no or near-instant motion. Occasional (sheets, dialogs, FAB menu) → the scheme's default-speed springs. Rare (onboarding, completion) → room for delight. Never animate a keyboard-initiated action.
- **Every animation needs a purpose:** spatial continuity, state change, feedback, or preventing a jarring jump. "Looks cool" on a frequent path → delete.
- **Press feedback must be instant.** Material ripple + Expressive's pressed-shape morph (`ButtonDefaults.shapes()`) already give this — don't remove it. On custom clickables, add a 0.95–0.98 scale on press via `fastSpatialSpec`.
- **Nothing appears from nothing.** On Android, Material components enter by expanding along x or y away from the nearest edge/anchor (a top menu expands down, a snackbar up) with a short fade — not scale or z-motion, which imply an elevation change. Never `scale(0f)`.
- **Exit faster than enter.** Use `fastEffectsSpec` / `fastSpatialSpec` for dismissals; the user has already decided.
- **Springs are interruptible; keep them that way.** Use `animate*AsState`/`Animatable` driven by state so a reversed gesture retargets from current velocity. Don't chain `tween` keyframes on rapidly toggled UI.
- **Gestures:** dismiss on velocity, not only distance; apply resistance past bounds instead of hard stops.
- **Reduced motion means gentler, not none.** Keep fades/color; drop translation, bounce and morph. On Android check `Settings.Global.ANIMATOR_DURATION_SCALE == 0f`, on web `prefers-reduced-motion`.
- **Stagger lists 30–60ms apart,** never block input during it, and only on first appearance.
- **Review in slow motion** (Android: Developer options → Animator duration scale 5×; Compose: Animation Preview).

## Quick start (Compose)

```kotlin
@OptIn(ExperimentalMaterial3ExpressiveApi::class)
@Composable
fun AppTheme(darkTheme: Boolean = isSystemInDarkTheme(), content: @Composable () -> Unit) {
    val context = LocalContext.current
    val colorScheme = when {
        Build.VERSION.SDK_INT >= Build.VERSION_CODES.S ->
            if (darkTheme) dynamicDarkColorScheme(context) else dynamicLightColorScheme(context)
        darkTheme -> darkColorScheme(/* generated from Material Theme Builder */)
        else -> expressiveLightColorScheme()
    }
    MaterialExpressiveTheme(
        colorScheme = colorScheme,
        motionScheme = MotionScheme.expressive(),
        shapes = Shapes(), // includes largeIncreased / extraLargeIncreased / extraExtraLarge
        typography = AppTypography, // define *Emphasized styles if you use a custom font
        content = content,
    )
}
```

Most Expressive APIs require `@OptIn(ExperimentalMaterial3ExpressiveApi::class)`. API names here were checked against `material3` 1.5.0-beta01 sources (Oct 2026); confirm against the project's Compose BOM before relying on a new one (grep the material3 jar or check the release notes).

## Common mistakes

| Mistake | Fix |
|---|---|
| `Color(0xFF6750A4)` / `Color.White` in a composable | `MaterialTheme.colorScheme.primary` / `onPrimary` — pair every container with its `on*` role |
| Cards with `elevation = 8.dp` shadows to separate content | Tonal step: `surfaceContainerLow` → `surfaceContainerHigh`; shadows only over busy backgrounds/imagery or as a temporary lift on interaction |
| Dividers between every list row by default | Implicit grouping first (spacing, `SegmentedListItem`); dividers (`outlineVariant`) when content needs explicit boundaries |
| `tween(300, easing = FastOutSlowInEasing)` for a component | `MaterialTheme.motionScheme.defaultSpatialSpec()` |
| Spatial spring on a color/alpha change (overshoots → flicker) | Effects spec for color/alpha; spatial only for position, size, scale, shape |
| Lateral slide between bottom-nav tabs, or between list and detail | Top-level fade for nav destinations; platform forward/backward for hierarchy; container transform only for hero moments |
| `outline` for dividers / `outlineVariant` as a button's only boundary | `outlineVariant` for dividers and card borders; `outline` (≥ 3:1) for target boundaries |
| Content-derived color painted away from its image, or replacing red error / green success | Keep content color next to its visible source; keep semantic colors static |
| Mixed icon styles/weights, icons sized unlike adjacent text | One Material Symbols style + weight; match text size; filled variant for selected |
| Bouncy motion on scroll-linked or high-frequency UI | `MotionScheme.standard()` locally, or no animation |
| Abstract shapes everywhere | One hero shape per screen; everything else uses the corner scale |
| Two filled primary buttons side by side | Filled only for the most important action; the other tonal/outlined/text — or a connected `ButtonGroup` |
| Phone layout stretched on tablets | Breakpoint → nav rail / list-detail; keep text at 40–60 characters per line |
| Navigation drawer on large screens | Expanded navigation rail (drawer is no longer recommended in Expressive) |
| Icon-only button without `contentDescription`, or <48dp target | Add label; keep `minimumInteractiveComponentSize()` |
| Custom font without emphasized variants | Define all 15 `*Emphasized` styles in `Typography(...)` or they fall back to defaults |
