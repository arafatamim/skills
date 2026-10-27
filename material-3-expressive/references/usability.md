# Usability with M3 Expressive

Usability (Nielsen Norman) = **efficiency, errors** (fewer, recoverable), **learnability, memorability, satisfaction**. Accessibility makes a product work for people with disabilities; usability makes it intuitive for everyone. Expressive tactics exist to serve both — The eye-tracking and focus-group research behind M3 Expressive found people spot key elements up to 4× faster in Expressive designs.

## Method

1. **Rank goals** for the screen/app: primary (required to use the product: start a task, decide), secondary and tertiary (statuses, settings, history).
2. **One primary task per page.** Give it the strongest emphasis; use empty space to focus attention; keep other goals discoverable but quieter.
3. **Build hierarchy first** with color, size, spacing, placement, containment, type. **Then** add unique emphasis (illustration, scale, shape, shape morph) to celebrate success or progress.
4. **Spend fewer tactics on lower-ranked goals.** Hero screens use many; secondary screens fewer; tertiary screens subtle ones. Don't stack many expressive tactics at once.
5. **Test and iterate** with varied users and contexts, early and often.

## Tactics

| Tactic | Guidance |
|---|---|
| Color & contrast | Primary/secondary roles to emphasize key actions; contrasting hues over similar ones; separate roles for actions vs data; always meet basic accessibility contrast |
| Containment | Group related elements in subtle containers; split content into sections with containment, spacing, headings |
| Size | The most important action/CTA is the largest element. Larger key actions → faster, fewer errors, more learnable |
| Placement | Primary action low on the screen (thumb reach), last in the visual flow so the eye lands on it |
| Shape & morph | Adds emphasis, guides focus, differentiates containers/buttons, signals interaction, sets tone; morph communicates states (selected, tap, swipe, scroll, release, long-press) and in-progress actions |
| Typography | Largest, most legible text can mark the primary action; smaller text for secondary/tertiary; same style groups similar content |
| Motion | Emphasizes key moments; use sparingly — it distracts |

## Worked example: "Aura" (official reference app)

- **Hero screen (start a session)**: extra-large primary button at the bottom, the final element in the vertical flow; settings in secondary color inside subtle containers; the daily message least emphasized but in a large soft container. Generous spacing between the three.
- **Hero moment (breathing)**: a huge morphing "flower"/"sunny" shape driven by spring tokens dominates; very large emphasized countdown numerals vs small instruction text; pause/stop are a large button group at the bottom; nav bar hidden during the task.
- **Secondary screen (report)**: fewer tactics; metrics inside uniform flower shapes with decreasing size guiding the eye; medium Finish button, less emphasized than the hero's Start button.
- **Tertiary screen (progress)**: subtle — key data in `primary`, secondary-role accents for completed days, custom-scaled numbers large enough to scan without dominating.

## Common failures (from the Aura cautions and v1 → v2 iteration)

- Everything large and equally prominent → no focal point. Fix: one big element; group the rest.
- Same roles (primary, primaryContainer) for all actions and data → flat. Fix: primary for the main action; secondary for selected settings, secondaryContainer for secondary data (dates). On a data-focused screen, key data itself may take `primary`, with secondary for accents.
- Shapes of different forms and sizes, overlapping → clutter. Fix: uniform shapes, even spacing, consistent rhythm.
- Several things animating at once (text tracking + arrows + morph) → competing motion. Fix: one moving focal element; keep everything else still.
- Unrelated settings scattered on the hero screen. Fix: group as list items above the main action.

Best practices: clear scale and placement; consistent color roles; calm, balanced layouts with uniform shapes/sizes and gentle, aligned rhythm.

Sources: m3.material.io/foundations/usability/{overview, applying-m-3-expressive}.
