# Elevation

Elevation is z-axis distance in dp. It lets surfaces move in front of/behind each other (content scrolling under an app bar), shows spatial relationships (a FAB separate from a card list), and focuses attention on the highest surface (a dialog). M3 deliberately has few levels — fewer levels = more power to direct attention.

## Levels and component resting elevation

| Level | dp | Resting components |
|---|---|---|
| 5 | 12 | (none; reserved for interaction states such as hover and drag) |
| 4 | 8 | (none; reserved for interaction states such as hover and drag) |
| 3 | 6 | FAB, extended FAB, FAB menu close button, modal dialogs, date/time pickers, search |
| 2 | 3 | App bar (scrolled), menu, navigation bar, rich tooltip, toolbar |
| 1 | 1 | Banner, modal bottom sheet, elevated button/card/chip, modal nav drawer, modal side sheet |
| 0 | 0 | App bar (not scrolled), filled/tonal/outlined buttons, button groups, filled/outlined cards, carousel, chips, full-screen dialog, FAB and extended FAB in a nav rail, FAB menu list items, lists, nav rail, segmented/split buttons, docked side sheet, slider, tabs, icon buttons |

- **Don't change components' default elevation.** Tokens carry no shadow or color; each platform renders them.
- Elevation changes respond to interaction or system events and should be consistent across similar elements: hover/focus raises by **one level** (FAB 3 → 4; all buttons +1 on hover).
- Surface tint is **deprecated** — use the 0–5 level tokens. Surface color roles are **not tied to elevation**; pick them for containment. (Implementation inference: in Compose, set `containerColor = surfaceContainer*` rather than relying on `tonalElevation` to tint.)

## Depicting elevation

A surface reads as elevated when it shows **edges** (contrast with its surroundings), **overlap** (at rest or in motion), and **distance**.

1. **Tonal difference (default in M3).** Different surface roles show where one surface ends — an app bar edge tells users the grid scrolls independently. Overlapping regions must use different roles. For interactive components, edges need accessible contrast to read as separate.
2. **Shadows** — size and softness express distance (small/sharp = close, large/soft = far). Use visible shadows only to:
   - **Protect** elements over patterned/busy backgrounds or imagery where a hairline isn't enough (cards, chips, buttons over a photo or map).
   - **Encourage interaction**: lift temporarily on focus, selection, or drag/swipe; lower when a higher element appears.
3. **Scrims** — beneath modals and expanded navigation menus to boost contrast of the layered surface. `scrim` role at **32%** opacity.

Official do/don't: ensure floating elements have sufficient contrast with surfaces beneath; don't use colors with insufficient contrast — the relationship between surfaces must be clear.

Motion note: on Android, entering components avoid scale and z-axis motion because those imply an elevation change that M3's reduced-elevation model doesn't have (see `motion.md`).

Sources: m3.material.io/styles/elevation/{overview, applying-elevation, tokens}.
