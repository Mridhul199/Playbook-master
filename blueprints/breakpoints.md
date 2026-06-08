# Breakpoints blueprint

For responsive scaling playbooks. Two modes:
- **Component-level** — single component across viewports (Button, Table, Pagination)
- **Screen-level** — whole-screen mock across viewports (Orders, Dashboard)

**Prerequisite:** Run `/breakpoint-check` first on the source. Reuse findings here. Don't re-derive responsive analysis inline.

---

## Section structure

```
1. Hero — 3-statement thesis ("Capped. Stepped. Floored.") + meta strip
2. Thesis — Capped + centered vs naive fluid stretch (side-by-side)
3. Rationale — UX laws (Fitts, Hick, Proximity, reading length, foveal)
4. Tier reference — 3–5 breakpoint tiles (720 / 1024 / 1280 baseline / 1920)
5. Interactive playground — viewport scrubber + tier buttons
6. Step-down ladder — canonical token table (see below)
7. Anti-patterns — stretched tables, fluid forms, edge-pinned CTAs
8. Footnote — sources, exceptions, WCAG floors
```

---

## Canonical step-down table

The system has 3 tiers: **Base (≥1280)**, **Medium (1024)**, **Compact (720)**. Every token below steps once or twice across these tiers. Reuse these values verbatim — don't invent new ones.

| Token | Base (≥1280) | Medium (1024) | Compact (720) | Floor / reason |
|---|---|---|---|---|
| **Layout** | | | | |
| Layout padding (outer) | 24 | 16 | 16 | — |
| Content spacing (between cards) | 40 | 32 | 24 | — |
| Container padding (inside card) | 24 | 16 | 16 | — |
| Container corner radius | 32 | 16 | 12 | — |
| **Side nav** | | | | |
| Side nav width | 264 / 280 | 240 | 56 / 64 (icon rail) | — |
| Side nav padding | 16 | 12 | 8 | — |
| Side nav item | full row | full row | 36×36 square pill | — |
| Side nav icon | 18 | 16 | 18–20 | — |
| **Typography** | | | | |
| Header H1 | 36 | 24 | 22 | — |
| Subtext | 16 | 14 | 12 | ≥12 readable |
| **Buttons** | | | | |
| Button height (lg) | 48 | 40 | 40 | ≥40 touch |
| Button padding x | 24 | 20 | 16 | — |
| **Inputs** | | | | |
| Input height | 48 | 40 | 40 | ≥40 touch |
| **Table** | | | | |
| Table row padding | 24 | 12 × 16 | 12 × 16 | — |
| Table row height | 72 | 56 | 56 | — |
| Table header height | — | — | — | use th padding 16×24 / 12×16 |
| **Pagination** | | | | |
| Pagination padding | 24 | 16 | 16 | — |
| Pagination height | 72 | 56 | 56 | — |
| Pagination button | 40 | 32 | 32 | ≥32 (compact only) |

**Floor reminders (WCAG):**
- Touch targets: 44×44 ideal, 24×24 floor (WCAG 2.5.8).
- Body text: ≥12px (legibility).
- Font weight: ≥400 on body, ≥500 on dense UI.

---

## Screen-mock CSS (for whole-screen Breakpoints)

When the playbook shows a whole screen at multiple tiers, use these `.mock-*` classes. Drop into the playbook's `<style>` block.

```css
/* Outer dark shell (matches BillDesk product chrome) */
.mock-side{display:flex;height:100%;background:#3D2418;overflow:hidden}

/* Sidenav with brand-gradient */
.mock-sidenav{
  width:280px;
  background:linear-gradient(180deg,#4A2D1E 0%,#3D2418 45%,#2E1812 100%);
  color:#fff;padding:16px
}
.msn-head{display:flex;align-items:center;gap:12px;margin-bottom:24px}
.msn-head .mark{width:40px;height:40px;border-radius:8px;background:#fff;display:grid;place-items:center}
.msn-item{
  display:flex;align-items:center;gap:12px;padding:10px 12px;
  color:rgba(255,255,255,.78);border-radius:8px;font-size:14px;
  font-weight:500;cursor:pointer;white-space:nowrap;margin-bottom:2px
}
.msn-item.active{background:rgba(255,255,255,.08);color:#fff}

/* Main content panel — white card with rounded corners */
.mock-main{
  flex:1;margin:16px;background:#fff;border-radius:32px;
  overflow:hidden;display:flex;flex-direction:column;
  box-shadow:0 1px 2px rgba(0,0,0,.04)
}
.mock-main .mock-body{padding:32px;flex:1;overflow:auto}
.mock-h1{font-size:30px;font-weight:600;letter-spacing:-.025em;margin:0 0 4px;line-height:1.15;color:var(--text-1)}
.mock-sub{font-size:13px;color:var(--text-3)}

/* Filter row */
.mock-filters{
  display:grid;grid-template-columns:1.4fr 1fr 1fr auto;
  gap:16px;align-items:end;background:var(--surface);
  border:1px solid var(--border);border-radius:16px;
  padding:24px;margin-bottom:24px
}
.filt-btn{
  height:48px;padding:0 24px;background:var(--orange);color:#fff;
  border:none;border-radius:8px;font-size:14px;font-weight:600;
  cursor:pointer;box-shadow:0 1px 2px rgba(242,101,34,.25)
}

/* Table */
.mock-table-wrap{background:var(--surface);border:1px solid var(--border);border-radius:16px;overflow-x:auto}
.mock-table{width:100%;border-collapse:collapse;font-size:13px}
.mock-table th{
  padding:16px 24px;text-align:left;font-size:11px;font-weight:500;
  color:var(--text-3);letter-spacing:.04em;text-transform:uppercase;
  border-bottom:1px solid var(--border);white-space:nowrap
}
.mock-table td{
  padding:24px;border-bottom:1px solid var(--border);
  color:var(--text-1);white-space:nowrap;height:72px;box-sizing:border-box
}
.mock-table .t-sub{color:var(--text-3);font-size:11px;line-height:1.3;margin-top:2px}

/* MEDIUM tier (1024) overrides */
.mock-side.is-medium .mock-sidenav{width:240px;padding:12px}
.mock-side.is-medium .msn-item{font-size:13px;padding:8px}
.mock-side.is-medium .mock-main{margin:12px;border-radius:16px}
.mock-side.is-medium .mock-main .mock-body{padding:24px}
.mock-side.is-medium .mock-h1{font-size:24px}
.mock-side.is-medium .mock-sub{font-size:13px}
.mock-side.is-medium .mock-filters{padding:16px;gap:12px;border-radius:12px}
.mock-side.is-medium .filt-btn{height:40px;padding:0 20px}
.mock-side.is-medium .mock-table-wrap{border-radius:12px}
.mock-side.is-medium .mock-table th{padding:12px 16px}
.mock-side.is-medium .mock-table td{padding:12px 16px;height:56px;line-height:1.2}
.mock-side.is-medium .mock-table .t-sub{font-size:10px;margin-top:1px}

/* COMPACT tier (720) overrides — collapsed icon rail */
.mock-side.is-compact .mock-sidenav{width:64px;padding:8px}
.mock-side.is-compact .brand-wordmark{display:none}
.mock-side.is-compact .brand-logomark{display:block}
.mock-side.is-compact .msn-item{
  justify-content:center;padding:0;gap:0;
  width:36px;height:36px;margin:0 auto 2px;border-radius:8px
}
.mock-side.is-compact .msn-item svg{width:18px;height:18px}
.mock-side.is-compact .msn-item .lbl,
.mock-side.is-compact .msn-item .chev,
.mock-side.is-compact .msn-user{display:none}
.mock-side.is-compact .mock-main{margin:8px;border-radius:12px}
.mock-side.is-compact .mock-main .mock-body{padding:16px}
.mock-side.is-compact .mock-h1{font-size:22px}
.mock-side.is-compact .mock-sub{font-size:12px}
.mock-side.is-compact .mock-filters{padding:12px;gap:8px;margin-bottom:16px}
.mock-side.is-compact .filt-btn{height:40px;padding:0 16px}
.mock-side.is-compact .mock-table-wrap{border-radius:12px}
.mock-side.is-compact .mock-table{font-size:12px}
.mock-side.is-compact .mock-table th{padding:12px 16px}
.mock-side.is-compact .mock-table td{padding:12px 16px;height:56px;line-height:1.2}
.mock-side.is-compact .mock-table .t-sub{font-size:10px;margin-top:1px}
```

**Tier toggling:** the playground JS adds `.is-medium` at viewport ≤1100 and `.is-compact` at viewport ≤900 on `.mock-side`.

---

## Tier reference table recipe

```html
<div class="step-table">
  <div class="step-table-row head">
    <span>Component</span>
    <span>Base <small>≥1280</small></span>
    <span>Medium <small>1024</small></span>
    <span>Compact <small>720</small></span>
    <span>WCAG floor</span>
  </div>
  <div class="step-table-row">
    <span class="label">Button</span>
    <span class="base">48</span>
    <span>40 <span style="color:var(--text-3);font-size:11px">(−8)</span></span>
    <span>40</span>
    <span class="floor">≥40 touch</span>
  </div>
</div>
```

- `.base` → orange (baseline tier value)
- `.floor` → green (WCAG floor)
- Mark deltas like `(−8)` in grey small font

---

## Interactive playground

Pattern:
- Stage with `overflow:hidden`, fixed height (760px works)
- Frame inside `position:absolute`, `transform-origin: top center`
- JS: `scale = min(stageW/frameW, 1)`
- Tier buttons → set `currentWidth`, re-apply class to `.mock-side`
- Drag handle on bottom for fine scrub

Tier buttons: `720` · `1024` · `1280` · `1440` · `1920`. Apply `.is-compact` ≤900, `.is-medium` 901–1100, base above.

---

## Visual ladder (3-tile comparison)

```html
<div class="step-ladder">
  <article class="step-tier is-base">
    <div class="step-tier-meta">
      <span class="step-tier-tier">Base</span>
      <span class="step-tier-vw">≥1280</span>
    </div>
    <div class="step-demo">[mock at this tier]</div>
    <div class="step-demo-spec"><b>row 72px</b><span>pad 24</span></div>
  </article>
  <!-- repeat for medium / compact, drop .is-base -->
</div>
```

---

## Anti-patterns specific to Breakpoints

- Stretched table on 1920+ (no cap) → reading line >100ch
- Fluid form with capped label column → mismatched gutters
- Edge-pinned CTAs → user reaches across whole screen
- Side-nav at full width on 720 → eats half the canvas
- Step-down by media query alone (no class toggling) → component-level overrides break
