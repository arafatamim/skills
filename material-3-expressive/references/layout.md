# Layout

## Parts of layout

- **Window** — frames the app. Expect multi-window (split-screen, bubbles, free-form desktop windows); every layout must adapt to arbitrary resizing.
- **Grid** — groups related info in columns, applies spacing consistently, creates focal points for primary actions, aligns bars, rails and panes.
- **Bars** — frame the main content; hold an app bar or navigation bar; may span one or several panes.
- **Rails** — fill the perimeter around panes or float above them; hold the nav rail, toolbars, chat input, FABs, other primary controls.
- **Panes** — **all content lives in a pane**; a layout has **1–3 panes** whose widths adapt to the breakpoint and to language (RTL puts navigation on the right). A pane is a single destination (message list, thread).
  - *Implicit grouping*: panes blend into the background, implying relationship. *Explicit grouping*: distinct container colors or outlines separate them. In XR, panes always use a container color.
- **Drag handles** — resize flexible panes, or fully collapse/expand a fixed pane to switch between one and two panes.
- **Rulers** — global alignment lines across all layers (Compose has a rulers API).

## Breakpoints (formerly window size classes)

| Class | Width | Typical | Panes | Navigation | Margins / spacer |
|---|---|---|---|---|---|
| Compact | < 600dp | Phone portrait | 1 | Navigation bar, or modal expanded rail | 16dp |
| Medium | 600–839dp | Tablet portrait, foldable open | **1 (recommended)** or 2 | Single pane: collapsed rail. Two panes: navigation bar. Modal expanded rail | 24dp / 24dp |
| Expanded | 840–1199dp | Tablet landscape, desktop | 1 or **2 (recommended)** | Rail, collapsed or expanded (modal or standard) | 24dp / 24dp |
| Large | 1200–1599dp | Desktop | 1 or **2 (recommended)** | Rail, collapsed or expanded | 24dp / 24dp |
| Extra-large | ≥ 1600dp | Ultra-wide | 1 to **3 (recommended)**; 3rd = standard side sheet | Expanded rail best suited here | 24dp / 24dp |

- The **navigation drawer is no longer recommended** in M3 Expressive — use the expanded navigation rail. Swap equivalent components across breakpoints (bar → collapsed rail → expanded rail; full-screen dialog/bottom sheet → dialog/menu); never swap non-equivalent ones (a button for a menu).
- Medium two-pane: only for low-density content (settings), panes 50/50 by default — avoid custom widths. Don't use two panes at medium with high information density.
- Fixed-and-flexible panes: fixed pane 360dp (expanded), 412dp (large/XL); side sheets max 400dp. Split-pane layouts keep the spacer visually centered.
- The rail can hide on secondary destinations if the primary destination stays reachable via back.
- Across breakpoints, adjust margins and type to keep text at 40–60 characters per line.

Compose: `currentWindowAdaptiveInfo().windowSizeClass` (material3-adaptive). Branch on `isWidthAtLeastBreakpoint(WIDTH_DP_MEDIUM_LOWER_BOUND)` etc., never on device type or orientation. Height breakpoints exist, but layouts rarely need to adapt to height since most content scrolls.

## Adaptive design

Responsive design scales one layout; **adaptive design changes structure, components and layouts** per context — the person (preferences, settings), the device (watch → phone → foldable → tablet → desktop → XR), and usage (resizing, rotating, switching devices).

- **Start with mobile, scale up** through desktop to spatial. Mobile window modes: full-screen, split-screen, bubbles. Desktop: free-form windows; a tablet with keyboard + mouse, or a phone on an external monitor, becomes a desktop experience.
- **Design for every input** — touch, pointer, physical keyboard — on every device.
- **Pane adaptive strategies**: *show and hide* (panes enter/exit), *levitate* (a pane floats over another), *reflow* (panes reorder/restack). Compose: Navigation 3 can show several destinations at once.
- **Component strategies**:
  - *Resize* with content and placement (buttons stretch with the container or hug content with start/end alignment).
  - *Show and hide* information (list items reveal supporting text as their pane widens).
  - *Presentation change* — orientation, color, type, shape, or configuration (FAB → extended FAB on wider windows; nav rail auto-expands).
- Keep **surface color mappings per region identical across breakpoints** (body `surface`, nav `surfaceContainer`).

### Canonical layouts

- **List-detail** — inbox, chats, settings. `ListDetailPaneScaffold` + `rememberListDetailPaneScaffoldNavigator()`. One pane on compact (navigates), two on expanded. Back from detail returns to list on compact only.
- **Supporting pane** — main content + related tools/info. `SupportingPaneScaffold`. Supporting pane becomes a bottom sheet on compact.
- **Feed** — grid of cards. `LazyVerticalGrid(GridCells.Adaptive(minSize = 160.dp))`, not fixed column counts.

## Spacing

### System

- **8dp scale**: `space100` = 8dp; token number = multiplier × 100 (`space50` = 4dp, `space150` = 12dp, `space200` = 16dp, `space300` = 24dp). Nested units like 2, 4, 6, 10dp exist inside components. Extend with the same pattern (`space225` = 18dp) rather than inventing raw values. Define once (a `Spacing` object or CompositionLocal) and use names.
- **Three kinds**: *padding* (inside an element), *gap* (between elements in a container/grid), *margin* (outside an element). Positions: top, bottom, vertical, horizontal, **leading/trailing** (flip in RTL — use `start`/`end`, never `left`/`right`).
- **Prefer padding and gaps on the parent over margins on children.** Compose: `Modifier.padding(…)` on the container + `Arrangement.spacedBy(…)`; avoid per-child `padding` acting as margins. Use margins only beyond the parent's padding, or at layout level.
- Simple components: one horizontal and one vertical gap. Complex components name gaps by their neighbours (icon–label gap).
- **Spacing stays the same when text scales to 200%.**
- Customize per product: remap component spacing (taller default buttons), tokenize repeated patterns ("surface content horizontal padding"), map to different tokens per device type, and adapt vertical padding for density.
- Example (search bar): 8dp vertical padding, 8dp horizontal gaps, 24dp horizontal margins (12dp focused).

### Typical values

| Context | Value | Source |
|---|---|---|
| Window margin compact | 16dp | official |
| Window margin medium+ | 24dp | official |
| Spacer between panes | 24dp | official |
| Card internal padding | 16dp (12dp dense) | house default |
| Between related items | 8dp | house default |
| Between groups | 16–24dp | house default |
| Between sections | 32–48dp | house default |

Spacing creates grouping. **Implicit grouping** uses proximity and open space; **explicit grouping** uses outlines, dividers, and shadows (and can signal interactivity, e.g. list items between dividers). Prefer implicit first; use dividers when the content needs explicit boundaries. Generous space around the hero focuses attention.

Max content width (house default, not official): ~600dp for forms/reading, ~840dp mixed, ~1040dp grids; center or pane-split beyond that. Officially, constrain by line length (40–60 characters).

## Edge-to-edge and insets

- `enableEdgeToEdge()` in the Activity (mandatory from targetSdk 35).
- Use `Scaffold`'s `innerPadding`; for custom layouts apply `WindowInsets.safeDrawing` / `systemBars` / `ime` via `windowInsetsPadding` or `contentPadding` on lazy lists (so content scrolls *under* bars but the last item stays reachable).
- `Modifier.imePadding()` on the composer/input container; don't also pad the whole screen.
- Display cutouts: `WindowInsets.displayCutout` in landscape.

## Foldables and large screens

- Respect hinges: `currentWindowAdaptiveInfo().windowPosture.hingeList` — never place a tap target or text line across a separating hinge.
- Tabletop posture (half-folded horizontal): content top, controls bottom (house default).
- Keyboard + mouse: hover states, focus rings (`focusable` + visible indicator), right-click context menus, keyboard shortcuts — and no animation on keyboard actions (design-engineering rule, not official).
- State survives resize/fold: keep it in ViewModel / `rememberSaveable`, not layout-local.

## Touch targets and density

- **Minimum 48×48dp target** even when the visual element is smaller (`minimumInteractiveComponentSize()`); don't scale components below it by default. Keep some space between targets (8dp is a house default).
- Reach on compact: primary actions toward the bottom (FAB, bottom toolbar), but keep essential controls off the very bottom edge (hard to reach for larger hands). On landscape tablets and unfolded foldables, limit interactions in the top 25% of the screen. Destructive actions away from frequent ones (house default).
- **Density is a user choice**: it shouldn't change automatically across breakpoints or orientation. Offer it where it helps (data tables, pro tools on desktop); settings for reverting density must keep default 48dp targets; never densify components that alert people (snackbars, dialogs). Density adapts vertical padding.

Sources: m3.material.io/foundations/layout/layout-overview/{parts-of-layout, adaptive-design, density}, /foundations/layout/{breakpoints, grids-spacing}, /styles/spacing/{overview, applying-spacing, tokens}.
