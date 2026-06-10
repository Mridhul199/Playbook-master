---
name: playbook-master
description: |
  Use this skill to create premium, single-file HTML playbooks documenting design rules, system specs, audits, or pattern references. Combines BillDesk DLS tokens with a proven playbook architecture (hero → rationale → tier reference → interactive playground → reference table → anti-patterns → footnote).

  TRIGGER when the user says:
  - "Create a playbook for X"
  - "Document the rule/spec for Y"
  - "Build a playbook from this audit"
  - "Same format as the breakpoint playbook"
---

# Playbook Master · v1.3.0

Router file. Loads on every invocation. Blueprint-specific instructions live in `blueprints/`. Read **only** the blueprint matching the user's Step 0 choice — do not pre-load all three.

---

## GLOBAL RULE — Design change required tag (ALL playbook types)

For every finding, check, or rule card you write: if the fix requires design-team action — a token change, a spec update, a new visual pattern, a mandatory prop, or any deliverable the developer cannot produce alone — you MUST apply the design-flagged treatment.

**Checklist — run on every finding before writing it:**
1. Can a developer fix this with code alone? → no tag needed.
2. Does the fix require a design token update, DLS spec change, new icon/pattern, or prop mandate? → add `class="design-flagged"` to the `<article>` AND insert the `<span class="design-tag">` element.

**This applies to ALL three blueprint types** (Accessibility, Specs, Breakpoints). It is not limited to accessibility audits.

**After writing all findings, do a mandatory scan:** find every card that has a "Design fix:" or "Design action:" bullet — each one MUST carry the design-flagged class. Missing tags are a defect, not a style choice.

The CSS for `.design-tag` and `.finding.design-flagged` is already in `scaffold.html` under `DESIGN-REQUIRED TAG`. Do not redefine it inline.

---

## Token discipline (read first)

This skill is loaded at session start. Keep token usage low:

1. **Don't re-read files after Edit.** The Edit tool errors if it fails — verification by re-read is wasted tokens.
2. **Read TSX source with `limit`/`offset`.** For a Button spec, read `helper.ts` only — not the whole component tree.
3. **Don't load `examples/` unless the user asks for visual diff.** Reference by path.
4. **Read scaffold.html ONCE per session.** Copy verbatim, fill placeholders, never re-read.
5. **Load ONE blueprint file** (from Step 0 choice), not all three.

---

## Workflow (5 steps)

### Step 0 — Type selection (ask FIRST)

> **Which type of playbook?**
> 1. **Breakpoints** — responsive scaling, screen or component
> 2. **Accessibility** — WCAG audit with findings + manual checks
> 3. **Specs** — component spec (anatomy, states, variants, props)

Lock the choice, then **read the matching blueprint file**:
- Breakpoints → `blueprints/breakpoints.md`
- Accessibility → `blueprints/accessibility.md`
- Specs → `blueprints/specs.md`

### Step 1 — Component + source path

> Which DLS component? And paste the absolute path to your `src/` folder.
> Example: `Component: Pagination` · `Source: ~/Downloads/src 2/`

Auto-resolve:
1. Verify `<sourceRoot>/<Component>/` exists. If not → fuzzy-match 3 nearest, ask user.
2. List source files: `.tsx`, `.ts`, `.css` under that folder.
3. Find story file: `<sourceRoot>/../stories/<Component>.stories.tsx` (or ask).
4. Parse variants from `export const X = ...` in the story.
5. Create output folder:
   ```bash
   mkdir -p ~/Documents/playbooks/<Component>-playbook
   cp ~/.claude/skills/playbook-master/scaffold.html ~/Documents/playbooks/<Component>-playbook/<component-name>-playbook.html
   ```

For **screen-level Breakpoints** (whole-screen mock, not single component), the "Component" can be a screen name (e.g., `Orders screen`). Source path still points to `src/` for token reference.

### Step 2 — Scope (exactly 2 questions)

1. **Exclusions** — Anything explicitly OUT of scope?
2. **Interactive elements** — Propose 2–3 from the blueprint, ask which to keep.

Do NOT ask: component name (Step 1), audience (always Dev+stakeholders), WCAG level (AA default), mobile (no, 720+ unless explicit), color/font/format.

### Step 3 — Propose section structure

Format:
```
## Proposed structure
1. **Hero** — [thesis]
2. **[Section]** — [purpose]
...
Approve / cut / reorder?
```
Wait for approval before building.

### Step 4 — Build

1. Copy `scaffold.html` to target path.
2. Fill `{{PLACEHOLDER}}` slots.
3. Add sections inside `<main>` zone using the recipes in the blueprint file.
4. Announce each batch in one short line.
5. Tell user the file path when done.

### Step 5 — Manual checks (Accessibility only)

See `blueprints/accessibility.md`.

---

## Canonical scaffold — MANDATORY

`scaffold.html` is the canonical visual skin. Pixel-perfect chrome, hero, footer, side-nav, all reusable section CSS, playground JS.

**Hard rule:** Before writing HTML, read `scaffold.html` once, then COPY it verbatim. Fill ONLY `{{PLACEHOLDER}}` slots. Add sections in the `<main>` zone. Never re-derive chrome/hero/footer/nav CSS from prose.

### Placeholders
- `{{TITLE}}` — page title
- `{{BRAND_SUB}}` — chrome subtitle ("DLS · Accessibility Audit", etc.)
- `{{NAV_LINKS}}` — one `<a href="#sec-id">Label</a>` per section
- `{{EYEBROW}}` — eyebrow tag (Topic · vN · Month YYYY)
- `{{H1}}` — 3-statement headline
- `{{LEAD}}` — 2–3 sentence lead
- `{{META_ITEMS}}` — `<div><dt>Label</dt><dd>Value</dd></div>` × 4–6
- `{{FOOT_COL1_*}}`, `{{FOOT_COL2_*}}`, `{{FOOT_COL3_*}}` — 3 footer cols
- `{{FOOT_META_TITLE}}`, `{{DATE}}`, `{{FOOT_META_AUDIENCE}}`

### Do NOT
- Redesign chrome / hero / footer / side-nav
- Swap left side-nav for top tabs
- Change the BillDesk SVG
- Introduce dark theme, alternate fonts, new tokens
- Ship React/Babel runtime inside the playbook
- Use screenshots as primary visual — recreate from source

---

## BillDesk DLS tokens

```css
:root {
  /* Orange — var(--orange-10), --orange-11, --orange-4, --orange-2 */
  --orange: #f15701; --orange-hover: #db4f01;
  --orange-tint: #fef4ee; --orange-tint-2: #fffaf7;
  /* Text — var(--gray-Blue-12), --gray-Blue-8, --gray-Blue-6 */
  --text-1: #1b2029; --text-2: #3b475b; --text-3: #848b98;
  /* Surface — var(--gray-Blue-1), --white */
  --canvas: #fafbfb; --surface: #ffffff; --surface-2: #fafbfb;
  /* Border — var(--gray-Blue-4), --gray-Blue-5 */
  --border: #d0d3d8; --border-strong: #abb0b8;
  /* Semantic — var(--green-9), --yellow-10, --red-11 */
  --success: #17b26a; --warning: #d9a72b; --error: #d7074c;
  /* Semantic tints — var(--green-2), --yellow-3, --red-4 */
  --success-tint: #f2fbf6; --warning-tint: #fffcf6; --error-tint: #fde7ef;
  --f: 'Inter', -apple-system, sans-serif;
  --mono: 'JetBrains Mono', ui-monospace, monospace;
  --r-s: 4px; --r-m: 6px; --r-l: 8px; --r-xl: 12px; --r-2xl: 16px; --r-pill: 999px;
  --sh-1: 0 1px 2px rgba(27,32,41,.04), 0 0 0 1px rgba(27,32,41,.04);
  --sh-2: 0 4px 12px rgba(27,32,41,.06), 0 0 0 1px rgba(27,32,41,.05);
  --sh-3: 0 12px 32px rgba(27,32,41,.08), 0 0 0 1px rgba(27,32,41,.06);
  --container: 1280px; --gutter: 32px;
}
```

### Spacing — strict 8s
Padding/margin/gap: only 0, 8, 16, 24, 32, 40, 48, 64, 80, 96. **NEVER** 4, 6, 10, 12, 14, 18, 22, 28 for layout.

### Type scale (perceptual, NOT 8s)
10 · 11 · 12 · 13 · 14 · 16 · 20 · 24 · 28 · 32 · 36 · 48

### Icon sizes
14, 16, 18, 20, 24, 32 — 16 in body, 20 in cards, 24 in nav.

### Border radii
Inputs 4 · Buttons 6/8 · Cards 8/12/16 · Hero/modals 16/24 · Pills 999.

---

## Universal recipes (used by all blueprints)

### Hero

```html
<section class="hero">
  <div class="shell">
    <span class="eyebrow"><span class="dot"></span>[Topic] · v[N] · [Month YYYY]</span>
    <h1>[3 short declaratives. Each &lt;5 words. Period each.]</h1>
    <p class="lead">[Principle + scope + range. 2–3 sentences.]</p>
    <dl class="hero-meta">
      <div><dt>[Key]</dt><dd>[Value]</dd></div>
      <!-- 4–6 -->
    </dl>
  </div>
</section>
```

**H1 examples:** "Capped. Stepped. Floored." · "Layouts hold. Components step. Floors don't move."

**Audit tone — frame as state, not verdict:**
- ✓ "Auditing the Table component." ❌ "This table is not accessible."
- ✓ "17 findings, each with a code-ready fix." ❌ "17 violations."

### Section head
```html
<div class="section-head">
  <span class="eyebrow"><span class="dot"></span>[Tag]</span>
  <h2>[Title.]</h2>
  <p class="lead">[1–2 sentence intro.]</p>
</div>
```

### Rule card (3-grid)
```html
<article class="step-rule">
  <div class="ico"><svg width="20" height="20" viewBox="0 0 20 20" fill="none" stroke="currentColor" stroke-width="1.8" stroke-linecap="round"><path d="M3 10h14M10 3v14"/></svg></div>
  <h3>[Rule name]</h3>
  <p>[2–3 sentences.]</p>
</article>
```

### Rationale callout (orange-tinted)
```html
<div class="step-callout">
  <div class="step-callout-ico"><svg>...</svg></div>
  <div>
    <h4>[Why this exception]</h4>
    <p>[Plain explanation.]</p>
    <p><b>Accessibility consequence:</b> [WCAG + impact.]</p>
  </div>
</div>
```

### Anti-pattern card (red-tinted)
```html
<article class="anti-card">
  <span class="anti-tag">✕ Anti-pattern</span>
  <h3>[Failure name]</h3>
  <p>[Why it's bad — concrete impact.]</p>
  <div class="anti-fix"><b>Fix —</b> [What to do instead.]</div>
</article>
```

---

## Voice and copy

- Brief, direct. No pleasantries.
- Specific over general (1440px, not "large").
- Three-statement rhythm for headlines.
- Eyebrow tags with version + date.
- Active voice. Em-dashes for asides.
- Sentence-case headings (except H1).

**Avoid:** "Best practice" (→ "the rule"), "should" (→ "must"), "try to" (drop), "various"/"things"/"etc." (name them).

**Use:** "holds", "drops", "steps", "caps", "floors", "strict 8s", "by design", "across the system".

---

## Anti-patterns in playbook construction

- ❌ Asking >5 setup questions
- ❌ Building before structure approval
- ❌ Non-8 spacing for layout (12, 14, 18, 22, 28)
- ❌ Strict 8 for type sizes (use the scale)
- ❌ Long headers (>3 statements, >80 chars)
- ❌ React runtime / Babel CDN in the playbook
- ❌ Screenshots as primary visual — recreate from source
- ❌ Auditing components in bulk — one per run
- ❌ Asking user to create folders — read from their `src/`
- ❌ Decorative SVGs without `aria-hidden="true"`
- ❌ Icon fonts — always inline SVG
- ❌ External CSS/JS — single HTML always
- ❌ Side-nav widths other than 56/64 (icon rail), 200/240 (medium), 264/280 (full)
- ❌ TBD sections in delivered output
- ❌ Apologizing in copy ("we tried", "hopefully")

---

## Output

Default: `~/Documents/playbooks/<Component>-playbook/<component-name>-playbook.html`

After writing, tell the user:
1. File path
2. Section count
3. Variants audited (if accessibility/specs)
4. Commit SHA + date in footer
5. Anything that needs vetting

Don't oversell. No emojis. No "Hope this helps!"

---

## Recovery patterns

**"It's broken"** → ask for screenshot, identify failing rule, smallest fix.
**"Too long"** → cut hero lead to 2 sentences, drop section subtitles, 2-paragraph max in cards.
**"More generic"** → drop product names, generic personas, frame as DLS not single product.
**User shares improved version** → read their version, carry forward their changes, edit on top.
