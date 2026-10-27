# Motion

M3 Expressive replaces easing+duration with a **motion physics system** (springs) for component motion. A spring = stiffness + damping + initial velocity. Springs have no fixed duration, keep velocity when interrupted, retarget smoothly, and handle gestures — one spring can serve transitions, button effects, and gestures, so motion feels consistent. Easing+duration survives as a fallback and still drives **screen transitions** (officially slated to move to springs later).

## Motion schemes

`MaterialTheme.motionScheme` is `MotionScheme.expressive()` (default under `MaterialExpressiveTheme`, with overshoot) or `MotionScheme.standard()` (minimal bounce). Official guidance: **expressive for most situations, especially hero moments and key interactions; standard for utilitarian products**. Use one scheme for most of a product.

Customization levels:
1. Use a default scheme (enough for almost everything; Compose components — 21 of them — use it automatically).
2. Custom scheme: implement `MotionScheme` returning your own `AnimationSpec` per spec.
3. Swap per element: override locally, e.g. `MaterialTheme(motionScheme = MotionScheme.standard()) { … }` around a dense subtree or one hero element.

Tokens never name the scheme (`md.sys.motion.spring.fast.spatial`): the scheme is chosen at product level, so swapping it never touches call sites. Exact values differ per device class (watch, phone, tablet) so "fast" always feels fast in context.

Each scheme exposes six specs — **speed** × **type**:

| Spec | Expressive (damping / stiffness) | Standard (damping / stiffness) |
|---|---|---|
| `fastSpatialSpec()` | 0.6 / 800 | 0.9 / 1400 |
| `defaultSpatialSpec()` | 0.8 / 380 | 0.9 / 700 |
| `slowSpatialSpec()` | 0.8 / 200 | 0.9 / 300 |
| `fastEffectsSpec()` | 1.0 / 3800 | 1.0 / 3800 |
| `defaultEffectsSpec()` | 1.0 / 1600 | 1.0 / 1600 |
| `slowEffectsSpec()` | 1.0 / 800 | 1.0 / 800 |

(Values from `androidx.compose.material3.tokens.{Expressive,Standard}MotionTokens`.)

### Spatial vs effects — the rule people break

- **Spatial** (overshoots and bounces into place): x/y position, size, scale, rotation, corner radius, shape morph progress.
- **Effects** (never overshoot, damping 1.0): color, alpha/opacity, blur, elevation tint. An overshooting alpha goes >1 or <0 and flickers; an overshooting color shifts hue.

### Speed

Most motion uses **default**; smaller elements fast, larger slow.

| Speed | Spatial example | Effects example |
|---|---|---|
| Fast | Small components: switches, buttons, icon buttons, press states, dismissals | Color change of a switch handle |
| Default | Partial-screen: bottom sheet, expanding nav rail, menus, FAB menu items | Opacity of content inside a nav rail |
| Slow | Full-screen animations | Full-screen content refresh |

## Compose patterns

```kotlin
// State-driven, interruptible, theme-aware
val motion = MaterialTheme.motionScheme
val width by animateDpAsState(if (expanded) 240.dp else 56.dp, motion.defaultSpatialSpec())
val tint by animateColorAsState(
    if (selected) MaterialTheme.colorScheme.primaryContainer else MaterialTheme.colorScheme.surfaceContainer,
    motion.defaultEffectsSpec(),
)

// In-screen enter/exit: expand along one axis away from the nearest edge (not scale/z), short fade, faster exit
AnimatedVisibility(
    visible = show,
    enter = expandVertically(motion.defaultSpatialSpec(), expandFrom = Alignment.Top) + fadeIn(motion.fastEffectsSpec()),
    exit = shrinkVertically(motion.fastSpatialSpec(), shrinkTowards = Alignment.Top) + fadeOut(motion.fastEffectsSpec()),
)

// Custom press feedback on a non-Material clickable
val pressed by interaction.collectIsPressedAsState()
val scale by animateFloatAsState(if (pressed) 0.96f else 1f, motion.fastSpatialSpec())
Modifier.graphicsLayer { scaleX = scale; scaleY = scale }
```

- Read `motionScheme` at the call site; don't hardcode `spring(dampingRatio = 0.6f, ...)` — it won't follow a scheme switch.
- Animate in `graphicsLayer {}` / draw lambdas where possible to skip recomposition and layout.
- Don't animate layout size of big subtrees every frame; prefer `animateContentSize(motion.defaultSpatialSpec())` on a small container or a `graphicsLayer` scale.
- Material components already use the scheme internally — a custom wrapper should pass the same spec, not invent one.

## Transitions

Transitions connect elements or full screens and are the **top priority** of a motion implementation — they teach users how the app is organized. They currently use easing+duration.

| Pattern | Use for | Motion | Common with |
|---|---|---|---|
| **Container transform** | An element expands to show more detail; hero moments; shallow hierarchies (expand, then collapse) | A persistent container (or hero image) morphs between start and end — strongest relationship, most expressive | Cards, lists, galleries, search, sheets, FABs, chips |
| **Forward and backward** | Consecutive hierarchy levels (inbox → thread) | Horizontal slide; Android fades while sliding (iOS: parallax). **Use the platform default** | Lists, cards, buttons, links |
| **Lateral** | Peers at the same level (tabs in a library) | Grouped elements slide in unison, **no fade or parallax** — hints at swipe | Tabs, carousels, galleries |
| **Top level** | Navigation bar/rail/drawer destinations | Quick fade out, then fade in; deliberately no shared elements | Nav bar, rail, drawer |
| **Enter and exit** | Introduce/remove a component | Within bounds: expand/collapse along x or y, **away from the nearest edge** (top menu expands down, snackbar expands up); avoid scale and z-axis (imply elevation change). Beyond bounds: slide on/off while expanding along the axis | Within: FABs, dialogs, menus, snackbars, time pickers, tooltips. Beyond: app bars, banners, nav bar/rail/drawer, sheets |
| **Skeleton loaders** | Loading → loaded | Subtle pulse from top-left to bottom-right; content quickly fades in over it | Any content-shaped load |

Entry location builds the spatial model: notifications from the top (shade pulls down), nav drawer from the start edge, bottom sheets and keyboard from the bottom. Coplanar side sheets shrink the content area instead of overlaying. Bars may enter/exit with scroll to free space.

### Choosing — do / don't

- Container transform for hero moments (opening a photo memory), **not** forward/backward. **Don't** use it in deep hierarchies or frequent utility navigation — excessive; it also needs custom implementation.
- **Don't** use lateral for hierarchical navigation (full-width slide is too much for a frequent transition and implies peers) or for top-level destinations (swipe conflicts with carousels and swipeable list items). Don't fade lateral content as it slides.
- **Don't** use enter/exit (full-height slides) to navigate between hierarchical screens.

### What makes a good transition

- **Follows accessibility settings**: under reduced motion, subtle fades instead of sliding/scaling; disable parallax and shape morph.
- **Consistent** pattern for the same kind of navigation.
- **Stable layouts**: skeleton loaders; no content popping in or shifting.
- **No jump cuts** by default (disorienting) — except where pure efficiency matters, e.g. opening a menu in a productivity app.
- **Coherent spatial model**: don't switch axes for the same content (horizontal ↔ vertical carousel).
- **Unified direction**: group elements and move along one primary axis; only hero elements persist. Don't animate many persistent elements independently.
- **Clean fades**: fully fade out before fading in; if a cross-fade is unavoidable keep it short, hidden in the fastest part. Don't slowly fade components over content; **don't fade a bottom sheet** as it enters/exits; a dialog fade must be short.
- **Simple style**: transitions are frequent and large — **no bouncy springs** or overt stylization on common transitions.

Compose: Navigation 3 `NavDisplay(transitionSpec = …, popTransitionSpec = …, predictivePopTransitionSpec = …)` (or `NavHost`'s enter/exit transition lambdas); `SharedTransitionLayout` + `Modifier.sharedBounds` for container transform. Material3 ships no ready-made transition objects — build patterns from `slideInHorizontally`/`fadeIn`/etc. Always support **predictive back**: the outgoing screen tracks the gesture instead of jumping at release.

### Legacy easing and duration (transitions only)

Officially **no longer maintained**: still used for transitions and by teams not yet on Expressive; everything else should migrate to springs.

| Token | Curve | Default duration | Use |
|---|---|---|---|
| Emphasized | Path (Android only). **No CSS/iOS form** — use Standard as the fallback | 500ms | Begin and end on screen; exit *temporarily* (drawer: ends at rest just off screen, so it reads as retrievable) |
| Emphasized decelerate | `cubic-bezier(0.05, 0.7, 0.1, 1)` | 400ms | Enter the screen |
| Emphasized accelerate | `cubic-bezier(0.3, 0, 0.8, 0.15)` | 200ms | Exit *permanently* (ends at peak velocity, reads as gone) |
| Standard | `cubic-bezier(0.2, 0, 0, 1)` | 300ms | Small, quick utility transitions; fallback for Emphasized on web/iOS |
| Standard decelerate | `cubic-bezier(0, 0, 0, 1)` | 250ms | Enter (utility) |
| Standard accelerate | `cubic-bezier(0.3, 0, 1, 1)` | 200ms | Exit (utility) |

Duration tokens: short 50–200ms, medium 250–400ms, long 450–600ms, extra-long 700–1000ms (rare). Scale duration with the **area covered** (small area ~200ms, large ~500ms); **exits, dismissals and collapses are shorter** than enters. Neither jarringly fast nor slow enough to feel like waiting. Example: a bottom sheet enters with Emphasized decelerate and exits permanently with Emphasized accelerate.

Design-engineering overlay (Emil, not official): for UI opened many times a day, lean to the short end (a menu at 150–200ms beats 300ms). Never use an accelerate (ease-in) curve for something *entering*.

## Web

Springs are compatible on the web; use a JS spring when interruption or gestures matter (Motion `{ type: "spring", stiffness, damping }` — web damping is a coefficient: `c = 2ζ√(k·m)`). Otherwise use the **official spring-to-curve conversions**:

| Spring | `cubic-bezier` | Duration |
|---|---|---|
| Expressive fast spatial | 0.42, 1.67, 0.21, 0.90 | 350ms |
| Expressive default spatial | 0.38, 1.21, 0.22, 1.00 | 500ms |
| Expressive slow spatial | 0.39, 1.29, 0.35, 0.98 | 650ms |
| Standard fast / default / slow spatial | 0.27, 1.06, 0.18, 1.00 | 350 / 500 / 750ms |
| Fast effects (both) | 0.31, 0.94, 0.34, 1.00 | 150ms |
| Default effects (both) | 0.34, 0.80, 0.34, 1.00 | 200ms |
| Slow effects (both) | 0.34, 0.88, 0.34, 1.00 | 300ms |

Animate only `transform`/`opacity`; gate hover motion behind `@media (hover: hover) and (pointer: fine)`.

## Reduced motion

Official: use subtle fades instead of sliding/scaling, and disable decorative effects (parallax, shape morph). Android: if `Settings.Global.getFloat(resolver, ANIMATOR_DURATION_SCALE, 1f) == 0f`, Compose animations already snap; for custom infinite or decorative motion (loading morphs, parallax) also check it and fall back to static or fade-only. Keep color/opacity cues that carry meaning.

## Decision checklist

1. Is it seen often? → shrink or remove. Motion is for key moments; it distracts when overused.
2. Moving/resizing/morphing? → spatial spec. Color/alpha? → effects spec.
3. Size of moving area → fast / default / slow.
4. Exit → one speed step faster than enter.
5. Screen change → pick the transition pattern from the table + predictive back; no bounce.
6. Only one thing animates for attention at a time.
7. Expressive scheme by default (it's meant for most situations); no bounce on common screen transitions, scroll-linked or high-frequency UI; whole utilitarian products use `MotionScheme.standard()`.

Sources: m3.material.io/styles/motion/{overview/how-it-works, overview/specs, transitions/transition-patterns, transitions/applying-transitions, easing-and-duration}.
