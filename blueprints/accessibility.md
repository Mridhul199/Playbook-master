# Accessibility blueprint

For WCAG 2.1 AA audits with code-level findings + manual checks.

**Prerequisite:** Run `/accessibility-check` first on the source. Reuse findings here. Don't re-derive WCAG analysis inline.

---

## Design change required tag — MANDATORY rule

Playbooks are read by both developers and designers. For any finding whose fix requires **design-team action** (token update, spec change, visual pattern mandate) rather than a code change, you must add a `[Design change required]` tag.

**When to apply:**
- Color/contrast failures tied to a design token value (e.g. placeholder color, disabled text token)
- Non-color error cue missing — the decision to mandate an icon/pattern belongs to design
- Touch target size specified as too small in the design spec
- Focus ring visual style (color, width) not matching the design system spec
- Any finding where the developer cannot fix the issue without a new design deliverable

**When NOT to apply:**
- Missing ARIA attributes (`aria-invalid`, `aria-describedby`, etc.) — pure code fix
- Missing `id` or `role` corrections — pure code fix
- Live region additions — pure code fix

**HTML recipe — finding card:**
```html
<article class="finding design-flagged">
  <div class="finding-head">
    <div class="finding-num">05</div>
    <div style="display:flex;flex-direction:column;align-items:flex-end;gap:6px">
      <span class="status partial">Partial</span>
      <span class="design-tag">
        <svg width="12" height="12" viewBox="0 0 12 12" fill="none" stroke="currentColor" stroke-width="1.5" aria-hidden="true"><path d="M2 9.5l5-7.5 5 7.5H2z"/><path d="M6 5.5v2M6 8.5v.5"/></svg>
        Design change required
      </span>
    </div>
  </div>
  ...
</article>
```

**HTML recipe — inline (callouts, playground sections):**
```html
<div style="display:flex;align-items:center;gap:10px;margin-bottom:8px;flex-wrap:wrap">
  <h4 style="margin:0">[Finding title]</h4>
  <span class="design-tag">
    <svg width="12" height="12" viewBox="0 0 12 12" fill="none" stroke="currentColor" stroke-width="1.5" aria-hidden="true"><path d="M2 9.5l5-7.5 5 7.5H2z"/><path d="M6 5.5v2M6 8.5v.5"/></svg>
    Design change required
  </span>
</div>
```

CSS is in `scaffold.html` under `DESIGN-REQUIRED TAG`. Do not redefine it inline.

---

## Section structure

```
1.  Hero — factual frame ("Auditing the X component") + meta strip
1a. Audit summary card (gauge + stat list) — REQUIRED, sits inside .hero, below .hero-meta
2.  Thesis / Overview — semantic markup vs current (root-cause framing)
3.  Findings grid — 9 WCAG axes as cards (semantic / keyboard / names / states / color / target / focus / contrast / ARIA)
4.  Playground quick-guide strip — numbered guide above tabs (REQUIRED when 3+ demos)
5.  Interactive playground — 5 demos:
    a) Screen-reader sim (Web Speech API, failure → fix)
    b) Keyboard nav visualizer
    c) Focus ring (weak vs accessible)
    d) Contrast checker (live ratios)
    e) Target-size overlay (16 / 24 / 44)
6.  Manual verification — chat-driven, render results only if user provided
7.  Code-level findings — F-01, F-02 cards with file:line + current/fix
8.  Footer — sources, severity legend, audit metadata
```

**Common drift:** rendering only `.hero-meta` and skipping `.hero-summary` (gauge). The card is NOT optional — every audit playbook must include it.

---

## Design-required tag

Some WCAG findings require design intervention — a token change, a component spec update, or a new visual pattern — rather than a code fix. Tag these explicitly so developers don't attempt to fix them in code.

**When to apply:**
- A color or contrast issue traces to a design token (e.g. `placeholder-text-light` is too light)
- The fix requires adding a new visual element (e.g. an error icon to avoid color-only signaling)
- The component spec must mandate a prop or pattern that is currently optional (e.g. `errorHelperText` must become required)

**How to apply:**

1. Add `class="design-flagged"` to the parent `.finding` card — turns the card indigo-tinted.
2. Insert the tag element after the `<span class="finding-wcag">` line:

```html
<span class="design-tag">
  <svg width="12" height="12" viewBox="0 0 12 12" fill="none" stroke="currentColor" stroke-width="1.5" aria-hidden="true"><path d="M2 9.5l5-7.5 5 7.5H2z"/><path d="M6 5.5v2M6 8.5v.5"/></svg>
  Design change required
</span>
```

3. In the finding-issues list, add a **Design fix:** bullet describing what the design team must change.
4. In any related callout (e.g. contrast checker), add the tag inline next to the `<h4>` and update the copy to say "this is a token-level change, not a code fix."

**Do NOT apply** to findings that are purely code changes (missing `aria-invalid`, wrong `role`, missing `id`, etc.) — those stay untagged.

**Typical design-required axes:** Color & non-color cues (1.4.1), Contrast tokens (1.4.3), Target size spec (2.5.5 when the design itself is undersized).

---

## Audit summary card — gauge + stat list (REQUIRED)

Sits inside `<section class="hero">` directly below `<dl class="hero-meta">`. CSS already in `scaffold.html`.

```html
<div class="hero-summary">
  <!-- LEFT: half-donut gauge -->
  <div class="gauge-card">
    <span class="gauge-card-title">Audit overview</span>
    <span class="gauge-card-sub">WCAG 2.1 AA · [N] axes checked</span>
    <svg class="gauge-svg" viewBox="0 0 240 140" preserveAspectRatio="xMidYMid meet" aria-label="Audit score [X] out of 10">
      <path d="M 30 120 A 90 90 0 0 1 [end1.x] [end1.y]" fill="none" stroke="#C0392B" stroke-width="22" stroke-dasharray="4 6"/>
      <path d="M [end1.x] [end1.y] A 90 90 0 0 1 [end2.x] [end2.y]" fill="none" stroke="#D97706" stroke-width="22" stroke-dasharray="4 6"/>
      <path d="M [end2.x] [end2.y] A 90 90 0 0 1 210 120" fill="none" stroke="#0A8F5C" stroke-width="22" stroke-dasharray="4 6"/>
      <text x="120" y="100" text-anchor="middle" font-family="Inter" font-size="40" font-weight="600" fill="#0D1526" letter-spacing="-1">[score]</text>
      <text x="120" y="122" text-anchor="middle" font-family="Inter" font-size="11" fill="#8A97AC" letter-spacing="0.04em">OUT OF 10</text>
    </svg>
    <div class="gauge-legend">
      <div><b style="color:#C0392B">[N]</b>Critical / Fail</div>
      <div><b style="color:#D97706">[N]</b>High / Partial</div>
      <div><b style="color:#0A8F5C">[N]</b>Pass</div>
    </div>
    <div class="gauge-trend">
      <span>[↘ Fails WCAG 2.1 AA — fix critical first]</span>
      <span class="arrow">›</span>
    </div>
  </div>
  <!-- RIGHT: stat list -->
  <div class="stat-list">
    <div class="stat-row crit">
      <div class="stat-info">
        <h5>Critical</h5>
        <div class="stat-big">[N]</div>
        <div class="stat-sub">↓ Blocks AA conformance</div>
      </div>
      <div class="stat-icon" style="background:var(--error-tint);color:var(--error)">
        <svg width="20" height="20" viewBox="0 0 20 20" fill="none" stroke="currentColor" stroke-width="1.6"><circle cx="10" cy="10" r="7.5"/><path d="M10 6v4.5M10 13.5v.5"/></svg>
      </div>
    </div>
    <div class="stat-row warn">...</div>
    <div class="stat-row pass">...</div>
  </div>
</div>
```

### Arc math (3 segments)

Gauge is a semicircle: `cx=120, cy=120, r=90`. Start `(30, 120)` at left. Total arc = 180°.

For segment occupying **P%** of full arc:
- Span° = P × 1.8
- End angle (CW from previous) = `prev_angle - span`. Start = 180°, end = 0°.
- End.x = `120 + 90 × cos(angle°)`
- End.y = `120 - 90 × sin(angle°)`

**Pre-computed end-points:**

| Split | Seg 1 ends | Seg 2 ends | Seg 3 ends |
|---|---|---|---|
| 33 / 33 / 33 | (75, 35.4) | (165, 35.4) | (210, 120) |
| 50 / 25 / 25 | (120, 30) | (197.9, 75) | (210, 120) |
| 67 / 22 / 11 | (165, 42.1) | (204.6, 89.2) | (210, 120) |
| 40 / 45 / 15 | (92.2, 34.4) | (200.2, 79.1) | (210, 120) |
| 30 / 40 / 30 | (67.1, 47.2) | (172.9, 47.2) | (210, 120) |

### Score
`passing_axes / total_axes × 10` (one decimal). e.g., 3/9 = 3.3, 8/9 = 8.9.

---

## Playground quick-guide strip (REQUIRED if 3+ demos)

Numbered guide above the tab bar, explaining each demo.

```html
<div class="pg-guide">
  <div class="pg-guide-row">
    <div class="pg-guide-num">01</div>
    <div class="pg-guide-info">
      <b>[Tab name]</b>
      <span>[What to click, what to look for, the takeaway.]</span>
    </div>
  </div>
  <!-- one per tab -->
</div>
```

```css
.pg-guide{background:var(--orange-tint-2);border:1px solid var(--orange-tint);border-radius:var(--r-xl);padding:16px 24px;margin-bottom:24px;display:grid;grid-template-columns:repeat([N],1fr);gap:16px}
.pg-guide-row{display:flex;gap:12px;align-items:flex-start}
.pg-guide-num{flex-shrink:0;width:24px;height:24px;border-radius:50%;background:var(--orange);color:#fff;display:grid;place-items:center;font-size:11px;font-weight:600;font-family:var(--mono)}
.pg-guide-info b{display:block;font-size:13px;color:var(--text-1);margin-bottom:4px}
.pg-guide-info span{font-size:12px;color:var(--text-2);line-height:1.5}
@media (max-width:980px){.pg-guide{grid-template-columns:1fr}}
```

---

## Failure → fix playground demo

Every demo follows wrong → right.

```html
<div class="ff-demo">
  <div class="ff-side ff-bad">
    <span class="ff-tag">✕ Failure</span>
    <div class="ff-render"><!-- broken example live --></div>
    <div class="ff-readout">
      <b>What SR reports:</b>
      <span>"image"</span>
    </div>
    <pre class="ff-code"><code>&lt;img src="..." alt="image" /&gt;</code></pre>
  </div>
  <button class="ff-toggle" data-toggle="ff-1">Try the fix →</button>
  <div class="ff-side ff-good">
    <span class="ff-tag">✓ Fix</span>
    <div class="ff-render"><!-- corrected example live --></div>
    <div class="ff-readout">
      <b>What SR reports:</b>
      <span>"Girl holding a credit card"</span>
    </div>
    <pre class="ff-code"><code>&lt;img src="..." alt="Girl holding a credit card" /&gt;</code></pre>
  </div>
</div>
```

Per-axis readout:
- **SR**: Web Speech API `speechSynthesis.speak(new SpeechSynthesisUtterance(text))` + caption
- **Keyboard**: numbered overlay on focusable elements (Tab order)
- **Contrast**: live ratio reading
- **Focus**: outline visibility toggle
- **Target size**: 24×24 / 44×44 overlay grid

---

## Code-finding card

For file:line violations.

```html
<article class="code-finding">
  <header class="code-finding-head">
    <div>
      <h3>F-01 · [Short name]</h3>
      <div class="meta">[File]:[lines] · WCAG [criteria] · [Severity]</div>
    </div>
    <span class="status fail">Fail</span>
  </header>
  <div class="code-body">
    <div>
      <h5>Current</h5>
<pre class="code">[current code with .err on broken bits]</pre>
    </div>
    <div>
      <h5>Fix</h5>
<pre class="code">[fixed code with .ok on changes]</pre>
    </div>
  </div>
  <div class="code-foot"><b>Bonus:</b> [one-line follow-up]</div>
</article>
```

```css
.code-finding{background:var(--surface);border:1px solid var(--border);border-radius:var(--r-xl);overflow:hidden;margin-bottom:16px}
.code-finding-head{padding:20px 24px;display:flex;gap:16px;align-items:center;justify-content:space-between;border-bottom:1px solid var(--border)}
.code-body{padding:24px;display:grid;grid-template-columns:1fr 1fr;gap:16px}
.code-body h5{font-size:11px;font-weight:500;letter-spacing:.06em;text-transform:uppercase;color:var(--text-3);margin-bottom:8px}
.code-foot{padding:16px 24px;background:var(--surface-2);font-size:12px;color:var(--text-2);border-top:1px solid var(--border)}
pre.code{margin:0;background:#0D1526;color:#CBD5E0;padding:14px 16px;border-radius:var(--r-l);font-size:12px;line-height:1.6;overflow:auto}
pre .err{color:#FF8B8B;background:rgba(192,57,43,.15);padding:0 2px;border-radius:2px}
pre .ok{color:#7CD9B6;background:rgba(10,143,92,.15);padding:0 2px;border-radius:2px}
```

Use `<span class="err">` on broken lines in "Current", `<span class="ok">` on changed lines in "Fix".

---

## Manual verification — chat workflow (Step 5)

Manual checks are performed **in chat**, not as HTML forms.

**Ask the user:**

> ~60% of WCAG requires human verification (keyboard sweep, screen-reader walkthrough, focus, zoom, touch).
>
> - **Skip** — ship without manual section
> - **Provide findings now** — I'll walk you through each check, you reply PASS/FAIL + notes

**If SKIP** — omit the manual section entirely. No TBD, no TOC link.

**If PROVIDE** — run checks one at a time:

> **Manual check 1 of 6 — Keyboard sweep** (WCAG 2.1.1)
> Tool: Keyboard only.
> Steps: [numbered list]
> Look for: [criteria]
>
> Reply: **PASS** / **FAIL** — notes: [...]

Wait for reply, then next. Don't fire all 6 at once.

### Render results (HTML)

After all checks done, render this section. **No form fields.**

```html
<section id="manual" class="tight">
  <div class="shell">
    <div class="section-head">
      <span class="eyebrow"><span class="dot"></span>Manual verification</span>
      <h2>Verified by [name] on [YYYY-MM-DD].</h2>
      <p class="lead">[N] human-required checks. Results below.</p>
    </div>
    <div class="manual-results">
      <article class="manual-result fail">
        <header>
          <div>
            <h3>1 · Keyboard sweep</h3>
            <span class="manual-meta">WCAG 2.1.1 · 2.4.3</span>
          </div>
          <span class="status fail">Verified fail</span>
        </header>
        <p class="manual-notes">[user's notes verbatim]</p>
      </article>
      <!-- one per completed check -->
    </div>
  </div>
</section>
```

```css
.manual-results{display:flex;flex-direction:column;gap:12px}
.manual-result{background:var(--surface);border:1px solid var(--border);border-radius:var(--r-xl);padding:20px 24px;border-left-width:4px}
.manual-result.pass{border-left-color:var(--success)}
.manual-result.fail{border-left-color:var(--error)}
.manual-result.partial{border-left-color:var(--warning)}
.manual-result header{display:flex;justify-content:space-between;align-items:flex-start;gap:16px;margin-bottom:8px}
.manual-meta{font-size:11px;color:var(--text-3);font-family:var(--mono);display:block;margin-top:4px}
.manual-notes{font-size:13px;color:var(--text-2);line-height:1.5;margin:0}
```

**Skipped checks** are not rendered. If user only ran 3 of 6, show 3 — not 6 with "skipped" placeholders.
