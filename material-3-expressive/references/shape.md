# Shape

Shape is the most recognisable Expressive signal. Used well it directs attention and shows state; used everywhere it is noise.

## Principles (official)

- **Shape and type in harmony.** Material shapes echo the visual attributes of M3 type; pair them so the product feels cohesive.
- **Morph to connect function and feeling** — communicate interaction states (selected), actions in progress (someone typing, page loading), and environment changes (sound, temperature, time of day). Consider tap, swipe, scroll, release, long-press.
- **Be bold; embrace tension.** Contrast square/sharp with rounded, or conventional with unconventional shapes, to convey state, draw attention, or make a design memorable. Material is no longer rounded-only.
- **Shape is versatile, not semantic.** Never give one shape a fixed meaning (a wave is not "progress"; waves can be button containers, progress can be a rotating shape).
- **Abstract shapes sparingly.** Never trade clarity for decoration; know *why* a shape is there and how it balances the composition. Different shapes with no clear reason = clutter.
- **Decorative moments get the most freedom**: graphics, photo cropping, avatar masks, non-interactive elements.
- **2.5D**: shape plus motion can suggest depth and volume.

## Corner scale

| Token | Radius | Compose | Typical use |
|---|---|---|---|
| None | 0 | `RectangleShape` | App bars, docked toolbars, full-bleed media |
| Extra small | 4dp | `shapes.extraSmall` | Snackbars, plain tooltips, menus, text field top corners |
| Small | 8dp | `shapes.small` | Chips |
| Medium | 12dp | `shapes.medium` | Cards, small FAB, rich tooltips |
| Large | 16dp | `shapes.large` | FAB, list items, nav drawer (end corners) |
| Large increased | 20dp | `shapes.largeIncreased` | Medium FAB |
| Extra large | 28dp | `shapes.extraLarge` | Dialogs, bottom sheets (top corners), large FAB |
| Extra large increased | 32dp | `shapes.extraLargeIncreased` | No component default — custom hero containers |
| Extra extra large | 48dp | `shapes.extraExtraLarge` | No component default — very large hero surfaces |
| Full | fully rounded (token `full`, no longer "50%") | `CircleShape` | Buttons, icon buttons, badges, search bar |

(Component mappings from `material3` 1.5.0-beta01 tokens.)

Rules:
- **Optical roundness — nested corners shrink:** inner radius = outer radius − padding (official example: 48dp − 14dp = 34dp; a 28dp sheet with 16dp padding holds 12dp cards). Don't reuse the outer radius inside.
- **Bigger element → bigger radius**, roughly. A 4dp radius on a full-screen card looks like a mistake.
- **Don't put large or full corners on information-dense components** such as text-heavy cards; rounder shapes are for visual variety elsewhere.
- **Asymmetric corners signal connection.** Closely grouped items (menus, split buttons, connected button groups, segmented lists) round the outer corners and keep **inner corners** that scale with size — connected button groups: XS 4, S 8, M 8, L 16, XL 20dp, with 2dp gaps (`ButtonGroupDefaults.connectedLeadingButtonShapes()` etc.). Asymmetric shapes use the same 10-step scale; inner-corner tokens map to the individual-corner tokens.
- Switching between square and fully rounded is a legitimate way to create an unexpected moment.
- Never hand-roll `RoundedCornerShape(13.dp)`; pick the nearest token.

### Customizing

Stay on the scale by default; customize for hero moments or custom components.
- **Style level**: change what a style means (e.g. `medium` = 16dp) — affects every component mapped to it unless that component has an override. Compose: `Shapes(medium = RoundedCornerShape(16.dp))`.
- **Component level**: remap one component to another style (buttons default to `full`; remap to `small`/`medium` for a squarer brand) via the component's `shape`/`shapes` parameter.
- **Cut corners** (`CutCornerShape`) instead of rounded: a straight-line corner clips more content than a rounded one of the same size — add padding in dense components.

## Shape as state (morphing)

Expressive components change shape on interaction:
- **Buttons**: round → squarer when pressed (`ButtonDefaults.shapes()` returns `ButtonShapes(shape, pressedShape)`; sized variants via `ButtonDefaults.shapesFor(height)`).
- **Toggle buttons / icon toggles**: round when unselected → square-ish when selected (or reverse) — selection is visible from shape alone, which also helps color-blind users.
- **Standard button groups**: the pressed/selected button changes shape and width and its neighbours adjust (`Modifier.animateWidth(interactionSource)` inside `ButtonGroup`). **Connected** groups change shape only — selecting a button does not affect its neighbours. Reserve shape differences within a group for key interactions.
- Round and square icon buttons share the same pressed shape radius.
- **FAB menu**: the FAB morphs into a close button; items spring out.
- **Loading indicator**: cycles through `MaterialShapes` polygons.

Use the component's built-in shapes param first. Only build custom morphs for a hero element. Morphs use the **expressive** motion scheme by default; switch to standard where bounce is wrong. Morphs are decorative motion: disable them under reduced motion.

## Material shapes library

`MaterialShapes` (35 normalized `RoundedPolygon`s, `@ExperimentalMaterial3ExpressiveApi`):

`Circle Square Slanted Arch Fan Arrow SemiCircle Oval Pill Triangle Diamond ClamShell Pentagon Gem Sunny VerySunny Cookie4Sided Cookie6Sided Cookie7Sided Cookie9Sided Cookie12Sided Ghostish Clover4Leaf Clover8Leaf Burst SoftBurst Boom SoftBoom Flower Puffy PuffyDiamond PixelCircle PixelTriangle Bun Heart`

Official uses: masking imagery or filling empty space — photo cropping, personalized avatar masks, graphics and other non-interactive decoration, containers for a few key metrics (official "Aura" report: stats inside uniform flower shapes); the loading indicator. Reasonable extensions: an empty-state illustration frame, a selected-state background behind an icon.
Avoid: text-heavy containers (corners clip text), dense lists (visual noise), mixing many shape forms/sizes on one screen, overlapping shapes. When several shapes appear together keep them uniform and evenly spaced.

```kotlin
@OptIn(ExperimentalMaterial3ExpressiveApi::class)
Image(
    painter = avatar,
    contentDescription = name,
    modifier = Modifier.size(96.dp).clip(MaterialShapes.Cookie9Sided.toShape()),
    contentScale = ContentScale.Crop,
)
```

## Custom morph

`Morph` comes from `androidx.graphics:graphics-shapes`; `Morph.toPath(progress)` is the Material3 extension. Polygons are normalized (0..1), so scale to the size.

```kotlin
@OptIn(ExperimentalMaterial3ExpressiveApi::class)
// A new instance per progress value: graphicsLayer caches the outline until the shape changes.
class MorphShape(private val morph: Morph, private val progress: Float) : Shape {
    override fun createOutline(size: Size, layoutDirection: LayoutDirection, density: Density): Outline {
        val path = morph.toPath(progress)
        path.transform(Matrix().apply { scale(size.width, size.height) })
        return Outline.Generic(path)
    }
}

@OptIn(ExperimentalMaterial3ExpressiveApi::class)
@Composable
fun MorphingSelection(selected: Boolean, modifier: Modifier = Modifier) {
    val morph = remember { Morph(MaterialShapes.Circle, MaterialShapes.Cookie9Sided) }
    val progress by animateFloatAsState(
        if (selected) 1f else 0f,
        MaterialTheme.motionScheme.defaultSpatialSpec(), // shape = spatial
    )
    Box(
        modifier
            .size(56.dp)
            // Reading progress inside the layer block re-runs only the layer, not composition
            .graphicsLayer { shape = MorphShape(morph, progress); clip = true }
            .background(MaterialTheme.colorScheme.primaryContainer),
    )
}
```

- Officially every shape in the set can morph into any other; similar vertex counts still give the smoothest in-between frames.
- Expressive spatial springs overshoot: progress briefly goes past 1 — that's the intended "pop". Clamp if your shape can't extrapolate cleanly.
- A morph is a spatial animation: respect reduced motion by snapping.

## Web

The shape library and shape morph are officially **not available on web**. `@material/web` exposes corner tokens as `--md-sys-shape-corner-*`; abstract shapes need an exported SVG path (`clip-path: path(...)`/`mask-image`) and morphs need SVG path interpolation (equal point counts) or a library such as flubber.

Sources: m3.material.io/styles/shape/{overview-principles, corner-radius-scale, shape-morph}.
