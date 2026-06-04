# Examples

Reference playbooks produced by this skill. Open each in a browser to see the canonical visual output. Use them as the gold standard when judging whether a new playbook looks right.

| File | Blueprint | What to study |
|------|-----------|---------------|
| `breakpoint-example.html` | Breakpoints | Hero with 3-statement thesis, UX-laws grid, tier reference, interactive playground (width slider + capped/fluid toggle + horizontal/vertical nav), step-down ladder with WCAG floor, rationale callout (type-scale exception), anti-patterns, anatomy of the scroll-spy side-nav |
| `accessibility-table-example.html` | Accessibility | Hero with **half-donut gauge + stat list** (the BillDesk Collection Overview pattern), 9-axis findings grid, playground quick-guide strip + 5 demos (SR sim with Web Speech API, keyboard nav overlay, focus ring, contrast checker, target-size overlay), code-finding cards with current vs fix |
| `accessibility-pagination-example.html` | Accessibility | Same hero pattern as the table audit + the **3-card Overview cascade** (Semantics / Keyboard / Announcements) showing how one root cause splits into three failure modes, root-cause callout, current vs fix, no manual checklist (skipped path) |

## Spec blueprint

No example yet. The first Specs playbook produced should land here as `spec-example.html` so future runs have a reference.

## What "looks right" means

When you generate a new playbook and want to check if it matches the system, open these in a separate tab and compare:

- **Chrome** — sticky top header, BillDesk wordmark left, no inline nav
- **Side-nav** — floating left, white card, scroll-spy with orange-tinted active state
- **Hero** — eyebrow pill + 3-statement h1 + lead + meta strip + (audit) gauge card + stat list
- **Sections** — 96px vertical padding, 48px section-head margin-bottom, 760px max-width on lead text
- **Cards** — 16px or 12px radius, 1px border, var(--surface) background, 24px padding
- **Footer** — cream gradient, 3-column grid, mono-font meta line at bottom

If the new playbook drifts visually from these three, the scaffold copy step was skipped or the chat re-derived CSS from the SKILL.md recipes instead of inheriting them.
