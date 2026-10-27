# Designing like an M3 Expressive expert

The specs in the other references say *what is allowed*. This file is *how to decide*. An expert's screen differs from a generic one in three ways: one unmistakable **hero**, a decisive **emphasis ladder** below it, and **calm** everywhere else. Expression is a budget spent on the hero and on state changes, never spread evenly.

Leading words used throughout: **hero** (the one thing the screen exists for), **ladder** (the emphasis ranks below it), **budget** (how many expressive accents a screen may spend), **squint test** (hierarchy read with blurred eyes), **calm** (everything not on the ladder's top rungs).

Tags: *official* = m3.material.io guidance; *craft* = house design practice, not on the guideline pages.

## 1. The emphasis ladder

Every element on a screen sits on exactly one rung. Rungs map to Material channels; exact tokens live in `color.md`, `typography.md`, `shape.md`, `components.md`, `motion.md`.

| Rung | Count | Color role | Size / type | Shape | Containment / elevation | Motion |
|---|---|---|---|---|---|---|
| **Hero** | 1 per screen | `primary` / `primaryContainer` fill, or the content itself (image, number) | Largest on screen: L/XL button, display/headline *emphasized* | May carry the screen's one distinctive shape (MaterialShapes mask, morph) | Own space; most whitespace around it | Expressive spatial spring on its interaction |
| **Primary action** | 1 per region | `primary` (filled button) | M/S button, `labelLarge` | Component default | — | Component default |
| **Secondary** | few | `secondaryContainer` (tonal) / outline | S buttons, title styles | Default | Grouped (button group, list) | Default |
| **Accent** | ≤ 1 per screen | `tertiary` / `tertiaryContainer` | Small: badge, highlight, one special chip | Default or a small shape | — | Effects only |
| **Tertiary** | any | text/icon buttons, `onSurfaceVariant` | Body/label | Default | Inside parent's container | Minimal |
| **Ambient** | rest | `surface` ladder, `onSurface` | Body | Corner scale only | Implicit grouping | None |

Craft rules for using the ladder:
- **Adjacent rungs differ on at least two channels** (size + color, or color + containment). A one-channel difference reads as "almost the same", which is the signature of generic UI.
- **Make the size jump decisive**: the hero is clearly larger than anything else (often 1.5–2× the next element), not one type step up.
- **Placement is a channel.** On compact, the hero action sits low, as the last element in the reading flow (official Aura pattern); content heroes sit top/center.
- Rank by **goal** (official: primary/secondary/tertiary goals), not by what the stakeholder wants loudest.

## 2. The process

Work the steps in order; each ends on its completion check. Write the outputs down (ASCII wireframes are fine) — designing in your head skips steps.

1. **Brief.** User, context (one-handed? glanceable? long session?), how often the screen is seen (daily-100× vs rare), product personality (§4). *Done when* the screen's job fits one sentence and its frequency band is named.
2. **Inventory and rank.** List every element the screen could show; give each a goal rank (primary/secondary/tertiary). Delete what serves no goal; move tertiary goals behind a menu, sheet, or another screen. *Done when* every remaining element has a rank and exactly one primary goal remains.
3. **Skeleton.** Pick the breakpoint behavior and canonical layout (`layout.md`): panes, bars, rails, where the hero sits, where the thumb reaches. Draw compact and expanded. *Done when* both skeletons exist and the hero's position is fixed in each.
4. **Hierarchy in grayscale.** Using only size, placement, spacing and grouping — no color — make the ranking visible. *Done when* the **squint test** passes: blurred, the eye lands on the hero first and then the primary action, in that order.
5. **Containment.** For each group decide implicit (space) or explicit (container tone, outline, divider); choose surface roles by nesting (`color.md`). *Done when* every group has one containment decision and overlapping regions use different surface roles.
6. **Ladder.** Assign each element a rung (§1). *Done when* every element has a rung and the counts fit the table (one hero, one primary per region, ≤ one accent).
7. **Expression pass.** Spend the **budget**: at most two accents per screen from this menu — a size jump, emphasized type on the hero or key numbers, one MaterialShapes mask or morph, a tertiary accent, an editorial type treatment, an expressive spring on the hero interaction, content-based color around hero imagery. Fewer on secondary/tertiary screens (official). *Done when* each accent has a one-line reason tied to the hero or a state change.
8. **Motion pass.** For each change, choose purpose, transition pattern, spec, and reduced-motion fallback (`motion.md`). Frequent paths get little or none. *Done when* every animation has all four written down.
9. **State matrix.** Design every cell: empty, loading (< 200ms / 200ms–5s / > 5s), error, offline, 1 vs many vs huge counts, long names, 200% font, RTL, dark, high contrast, four seed hues (red, yellow, green, blue). *Done when* no cell is "TBD".
10. **Critique.** Run §7. *Done when* every test passes or each exception is written with its reason.

## 3. Composition craft

- **Focal point and flow.** Design the path the eye travels: entry point → supporting info → hero action. Aura's start screen reads top (daily message, soft) → middle (settings, secondary) → bottom (XL Start button). *official example*
- **Whitespace is emphasis.** The hero gets the most space around it; generous spacing does the separating that lines would otherwise do. *official*
- **Rhythm.** Repeated items are identical in size, spacing and alignment; vary only what carries meaning. Uniform shapes, even spacing, aligned flow produce calm. *official*
- **Alignment.** Snap to the grid and rulers; leading-align text and lists. Center only short, standalone moments (hero numbers, empty states, celebrations). *craft*
- **Tension, once.** One contrasting move per screen — a square element among rounds, a sharp corner, an oversized numeral — draws the eye. Two contrasts compete. *official principle, craft limit*
- **Color calm.** At any moment the screen shows surfaces + content color + the primary accent; secondary and tertiary appear only where the ladder puts them. *craft*
- **Type contrast over type variety.** Two or three styles per screen with clear size gaps beat six close ones. *craft*
- **One moving thing.** When the hero animates, everything else holds still (official Aura caution).

## 4. Brand personality → Material choices

| Personality | Motion scheme | Color scheme variant (MCU) | Shape | Type | Budget |
|---|---|---|---|---|---|
| Utility / pro tool | Standard | Tonal spot or Neutral | Corner scale; squarer buttons (remap to `medium`) | Roboto / one workhorse; few emphasized | Low: size + type only |
| Everyday consumer | Expressive | Tonal spot (dynamic) | Defaults; one shape mask on hero | Emphasized headlines | Medium |
| Bold / playful | Expressive | Vibrant or Expressive | Abstract shapes as decoration, morph on selection | Variable font, expressive display | High on hero screens only |
| Editorial / premium | Expressive, restrained | Neutral or Fidelity (brand-true) | Large radii or deliberate sharp tension | Serif or expressive display + editorial treatments | Medium, type-led |
| Media / content-first | Expressive | Content (from artwork) near the media | Defaults; image crops with shapes | Emphasized titles | Medium |

MCU variant names (from Material Color Utilities; not verified against a local artifact — confirm before coding): `SchemeTonalSpot` (Android default), `SchemeNeutral`, `SchemeVibrant`, `SchemeExpressive`, `SchemeFidelity`, `SchemeContent`, `SchemeMonochrome`, `SchemeRainbow`, `SchemeFruitSalad`. Keep personality consistent across screens; vary the **budget** by goal rank, not the personality.

## 5. Screen recipes

Starting points, not templates — run §2 on each.

**Home / dashboard.** Hero: the one number or action the user came for (today's progress, "Start"). Skeleton: hero block top or hero action bottom; supporting metrics as an evenly spaced cluster; secondary destinations in nav. Accents: emphasized display numerals, one shape container for the key metric. Trap: a grid of equal cards — pick the winner and enlarge it.

**List → detail (inbox, chats, files).** Hero on the list: scanning speed — the content, not chrome. Rows: leading avatar/icon, title, supporting text, trailing meta; unread/selected via emphasized type and `secondaryContainer` (activated). Primary action: FAB (or extended FAB on wider windows). Expanded: `ListDetailPaneScaffold`, detail pane gets the hero. Motion: container transform only if the hierarchy is shallow; else platform forward/back. Trap: cards around every row; use segmented list rows or plain rows with space.

**Conversation / thread.** Hero: the newest content. Primary action: send, attached to the composer (IME-aware, bottom). Secondary: attachments/modes in a split button or a compact button group. Accents: none in the message stream (it's seen constantly); spend them on empty state and on the live "working" state (loading indicator / morph). Motion: new messages enter calmly (effects + short spatial); no bounce on scroll.

**Detail / media.** Hero: the artwork or content. Content-based color on contained surfaces next to it (≤ 2 color sources per screen, keep semantic colors static). Large flexible app bar collapsing on scroll; floating toolbar for contextual actions. Accent: shape-cropped imagery.

**Form / settings.** Hero: completing the task — the primary action (filled, bottom or end). Group fields under headings with space; one field style per form; labels not placeholders. Accent budget near zero. Errors inline at the field with `error` roles and text, not only color.

**Onboarding / empty state.** Rare → room for delight. Hero: one sentence of value + one XL/L action. A MaterialShapes-framed illustration, editorial type, an expressive spring on entry. One idea per page.

**Completion / celebration.** The best place to spend the budget: editorial type, shape morph, color-rich container, expressive spring. Then hand back to calm with one clear next action.

**Search.** Hero: the query field (search bar, full roundness). Results ranked by relevance with emphasized matches; suggestions as chips in rows. Full-screen expansion on compact, docked on expanded.

**Editor / tool.** Hero: the canvas. Floating toolbar for contextual tools (hides on scroll), standard motion scheme locally, density only if the user chooses it. Accents only on mode changes.

## 6. Tells of a generic Material screen → the expert move

| Tell | Expert move |
|---|---|
| Every section wrapped in a card | Implicit grouping with space; reserve containers for groups that need a boundary |
| All elements roughly the same size | Pick the hero; make the size jump decisive |
| `primary` on many things | Walk the ladder: one primary, tonal/outlined below, tertiary as a single accent |
| Everything centered | Leading-aligned grid; center only standalone moments |
| An icon on every row and heading | Icons where they speed scanning or carry state; labels carry meaning |
| Gradients, glass, drop shadows for depth | Surface-container tones; shadows only over imagery or for interaction lift |
| Random abstract shapes as decoration | One shape tied to the hero or a state change |
| Bounce on every interaction | Frequency rule: springs on hero and occasional moments; calm on frequent paths |
| Hardcoded brand purple | Brand as a seed color through color roles |
| Walls of text edge to edge | 40–60 characters per line; constrain width or split panes |
| Settings sprinkled on the main screen | Group them as list items above or behind the primary action (Aura v1 → v2) |

## 7. Critique rubric

Run each test and record pass/fail with the fix (report format in `review.md`).

1. **Squint test** — blurred, the hero reads first, primary action second.
2. **Grayscale test** — hierarchy survives without color; state survives without color (shape, icon, label).
3. **Three-second test** — a newcomer can say what the screen is for and what to tap.
4. **Thumb test** — on compact, the primary action is in comfortable reach; nothing essential on the very bottom edge.
5. **Budget count** — accents ≤ 2 (fewer on secondary/tertiary screens), each with a reason.
6. **Seed stress** — four seed hues, light and dark, medium/high contrast: every pairing still legible.
7. **Content stress** — empty, one, many; longest realistic strings; 200% font; RTL.
8. **Motion in slow motion** — 5× animator scale: one moving focal element, exits faster than enters, reduced-motion path present.
9. **Resize test** — compact → expanded → back: state survives, layout reflows by the skeleton from step 3.

## 8. Specifying for build

For each element on the ladder, hand off: rung, component, color roles (container + on), shape token, type token (baseline or emphasized), spacing tokens (padding, gaps), elevation level, motion spec (spatial/effects, speed), and states (enabled, hover, focus, pressed, disabled, activated, error). Anything not expressible as a token is a flagged exception with its reason.
