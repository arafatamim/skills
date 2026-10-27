# Components

Compose names below come from `androidx.compose.material3` (`androidx-main`). Expressive ones need `@OptIn(ExperimentalMaterial3ExpressiveApi::class)`. Confirm availability against the project's BOM.

Always start from a Material component — it already carries the right color roles, shape, elevation level, motion scheme, states and accessibility. Customize through its parameters (remap a role, a shape style, a spacing token) before building custom; a custom component must be mapped to the same roles and tokens.

## Official catalog (m3.material.io/components)

| Category | Components |
|---|---|
| Buttons | Button groups, buttons, extended FAB, FAB menu, FAB, icon buttons, segmented buttons, split button |
| Pickers | Date pickers, time pickers |
| Loading & progress | Loading indicator (short waits), progress indicators (real-time status) |
| Navigation | Navigation bar (small windows), navigation rail (collapsed/expanded), navigation drawer (still listed, but no longer recommended in Expressive) |
| Sheets | Bottom sheets, side sheets |
| Other | App bars, badges, cards, carousel, checkbox, chips, dialogs, divider, lists, menus, radio button, search, sliders, snackbar, switch, tabs, text fields, toolbars, tooltips |

Resting elevation per component: see `elevation.md`.

## Choosing an action component

| Need | Component | Notes |
|---|---|---|
| The screen's main action, always reachable | `FloatingActionButton` / `MediumFloatingActionButton` / `LargeFloatingActionButton`, or `ExtendedFloatingActionButton` | Medium/Large for hero screens; extended when the label adds clarity |
| 2–6 related actions from one entry point | `FloatingActionButtonMenu` + `ToggleFloatingActionButton` + `FloatingActionButtonMenuItem` | Replaces speed dial and stacked small FABs. FAB morphs to close. Color set (primary/secondary/tertiary) matches the FAB's color style. Never with one item, from an extended FAB or other component, or alongside a toolbar or nav rail |
| Main action in a form/dialog | `Button` (filled) | One per region |
| Secondary action | `FilledTonalButton`, `OutlinedButton` | Tonal for "important but not primary" |
| Low-emphasis action | `TextButton`, `IconButton` | |
| Primary action + variants (Send ▾, Save ▾) | `SplitButtonLayout` with `SplitButtonDefaults.LeadingButton` / `TrailingButton` | Trailing opens a menu; trailing icon rotates when open |
| Binary on/off action that looks like a button | `ToggleButton`, `FilledTonalToggleButton`, `OutlinedToggleButton`, `ElevatedToggleButton`, `*IconToggleButton` | Shape changes with checked state |
| A set of related actions or a single/multi selection | `ButtonGroup` (standard: buttons react to neighbours; connected: segmented look) | Prefer connected `ButtonGroup` of toggle buttons over legacy `SegmentedButton` for new Expressive UI |
| Contextual actions on the page's content (editor, media, map) | `HorizontalFloatingToolbar` / `VerticalFloatingToolbar` (optionally with an integrated FAB) | Can hide on scroll (`FloatingToolbarDefaults.exitAlwaysScrollBehavior`) |
| Global actions that stay the same across pages | Docked toolbar (`FlexibleBottomAppBar`) | Full width, straight corners |

### Button sizes

Five sizes: XS 32dp, S 40dp (default), M 56dp, L 96dp, XL 136dp heights (`ButtonDefaults.ExtraSmallContainerHeight` … `ExtraLargeContainerHeight`). Size the content with `ButtonDefaults.contentPaddingFor(h)`, `iconSizeFor(h)`, `textStyleFor(h)`, `shapesFor(h)`. Icon buttons have matching size variants and **narrow / default / wide** widths.

Use L/XL for one hero action on sparse screens (onboarding, a camera shutter, "Start" — the official Aura example puts an XL "Start" button low on the screen as the last element in the flow). Secondary screens step down to M. Mixed sizes inside one button group only for hero moments (official); one XL per screen (house rule). Visual size can be smaller than 48dp; the touch target cannot.

### Button group sketch

```kotlin
@OptIn(ExperimentalMaterial3ExpressiveApi::class)
@Composable
fun ViewModeSelector(selected: Int, onSelect: (Int) -> Unit) {
    val options = listOf("Day", "Week", "Month")
    Row(horizontalArrangement = Arrangement.spacedBy(ButtonGroupDefaults.ConnectedSpaceBetween)) {
        options.forEachIndexed { i, label ->
            ToggleButton(
                checked = selected == i,
                onCheckedChange = { onSelect(i) },
                shapes = when (i) {
                    0 -> ButtonGroupDefaults.connectedLeadingButtonShapes()
                    options.lastIndex -> ButtonGroupDefaults.connectedTrailingButtonShapes()
                    else -> ButtonGroupDefaults.connectedMiddleButtonShapes()
                },
                modifier = Modifier.weight(1f).semantics { role = Role.RadioButton },
            ) { Text(label) }
        }
    }
}
```

For a *standard* (non-connected) group with the neighbour-squish effect, use `ButtonGroup(overflowIndicator = …) { clickableItem(...) / toggleableItem(...) }` or `customItem` with `Modifier.animateWidth(interactionSource)`.

## Communication

| Need | Component |
|---|---|
| Indeterminate wait 200ms–5s, replacing an indeterminate circular spinner | `LoadingIndicator` (morphing shapes). Not for processes that turn determinate |
| Same, over content or in pull-to-refresh | `ContainedLoadingIndicator` |
| Known progress, or long task | `LinearWavyProgressIndicator` / `CircularWavyProgressIndicator` for expressive emphasis; flat `LinearProgressIndicator` for dense or low-emphasis contexts. The wave is styling, not a required "progress" symbol (shape is not semantic) |
| Brief confirmation with optional undo | `Snackbar` (one action; never for errors needing a decision). Info in an auto-dismissing snackbar must also be available inline or near the triggering action |
| Decision required | `AlertDialog`: dismissive action left of confirming; confirming disabled until a choice is made, dismissive never disabled; stacked buttons put confirming above dismissive. Short (1–3 word) labels |
| Counts/status on icons | `Badge` |
| Label for an icon | `TooltipBox` with `PlainTooltip`; `RichTooltip` for a short explanation with action (both are `TooltipScope` extensions) |

Under 200ms: show content immediately, no indicator. Over 5s: a progress indicator. Don't switch from a loading indicator to a determinate one mid-process. For content-shaped loads during transitions, skeleton loaders (see `motion.md`).

## Navigation

| Window class | Primary navigation |
|---|---|
| Compact | `ShortNavigationBar` / `NavigationBar` (3–5 destinations), or `ModalWideNavigationRail` |
| Medium | Collapsed `WideNavigationRail` for single-pane; navigation bar for two-pane; or modal expanded rail |
| Expanded+ | `WideNavigationRail` collapsed or expanded (modal or standard); expanded suits large/XL |

The **navigation drawer is no longer recommended** in M3 Expressive — the expanded navigation rail replaces it. `NavigationSuiteScaffold` (material3-adaptive-navigation-suite) picks bar vs rail automatically — prefer it over hand-switching. Selected item: `secondaryContainer` indicator, active label in `secondary`.

### App bars

| Screen | App bar |
|---|---|
| Top-level, content-first | `TopAppBar` (small) or `TopSearchBar`/`AppBarWithSearch` |
| Top-level with a strong title / hero screen | `MediumFlexibleTopAppBar` / `LargeFlexibleTopAppBar` (title + optional subtitle, collapses on scroll) |
| Detail screen | Small `TopAppBar` with back; title optional |
| Bottom actions | `FlexibleBottomAppBar` or a floating toolbar |

Flexible app bars collapse with `TopAppBarDefaults.exitUntilCollapsedScrollBehavior()`; keep the title readable when collapsed.

## Containment and lists

- `ListItem` for rows; `SegmentedListItem` for grouped, card-like rows with shared outer corners (Expressive lists).
- Cards: `Card` (filled, `surfaceContainerHighest`), `OutlinedCard`, `ElevatedCard` — officially equal in legibility and function, so pick by style (elevation guidance still favors shadows mainly over busy backgrounds). A card can be one large touch target opening a detail view; avoid many small targets inside it (house rule).
- Carousel: `HorizontalMultiBrowseCarousel` (browse), `HorizontalUncontainedCarousel` (custom widths), hero layouts for featured content.
- Sheets: `ModalBottomSheet` for short tasks on compact; side sheets / supporting pane on expanded.
- Menus: `DropdownMenu` with `DropdownMenuItem`; Expressive adds `DropdownMenuGroup`, `SelectableDropdownMenuItem`, `CheckableDropdownMenuItem`. Group with gaps (max 1–2 gaps, not in scrolling menus); vibrant (tertiary) menus sparingly.
- Search: `SearchBar` / `TopSearchBar` / `DockedSearchBar` / `ExpandedFullScreenSearchBar`; full-screen expansion on compact, docked on expanded.

## Inputs

- Text fields: `OutlinedTextField` has less emphasis, which simplifies forms with many fields; `TextField` (filled) has more emphasis. Don't mix styles in one form. Labels, not placeholders, carry meaning. `SecureTextField` for passwords.
- `Slider` (Expressive: taller track, inset icon support, `VerticalSlider`), `RangeSlider`.
- `Switch` with icon for clearer state; `Checkbox` for lists, `RadioButton` for exclusive choices of ≤ 5 (else a menu).
- Chips: assist (smart action), filter (toggle), input (entered value), suggestion. Place chips inline in rows, not vertical lists; overflowing chips wrap to the next line — beyond two rows of filter chips, use horizontal scrolling.
- Date pickers: modal on compact; docked (input field + dropdown calendar) on medium/expanded. Don't use a modal calendar picker for distant dates like a birth date — use modal input or docked. `DatePicker`/`DateRangePicker`/`TimePicker`.

## Web mapping

`@material/web` covers buttons, icon buttons, FAB, chips, text fields, checkbox/radio/switch/slider, menus, lists, tabs, dialogs, progress. Everything Expressive (button groups, split button, FAB menu, toolbars, loading indicator, wavy progress, flexible app bars) must be built from tokens.
