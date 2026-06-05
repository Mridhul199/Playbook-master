---
name: playbook-master
description: |
  Use this skill to create premium, single-file HTML playbooks documenting design rules, system specs, audits, or pattern references. Combines BillDesk DLS tokens with a proven playbook architecture (hero → rationale → tier reference → interactive playground → reference table → anti-patterns → footnote). Output is consistent in voice, structure, and visual treatment across every playbook produced.

  TRIGGER when the user says:
  - "Create a playbook for X"
  - "Document the rule/spec for Y"
  - "Make a reference doc for Z"
  - "Build a playbook from this audit"
  - "Same format as the breakpoint playbook"
  - Provides a folder/screenshots and asks for a playbook from it
---

# Playbook Master

A production-grade authoring tool for BillDesk DLS-branded HTML playbooks. Combines the visual design system (tokens, typography, spacing) with a battle-tested playbook architecture refined over ~30 iterations on the breakpoint playbook.

This skill is for **structure and consistency**. It does NOT do the underlying analysis (e.g., accessibility audits). For analysis, run that work first (or invoke a separate audit skill), then use this skill to render the output as a polished playbook.

---

## Canonical scaffold — MANDATORY FIRST STEP

This skill ships with **`scaffold.html`** alongside this file. It is the canonical visual skin every playbook must inherit — pixel-perfect chrome, hero shell, floating left scroll-spy nav, footer, all reusable section CSS, playground JS.

**Reference examples** are in `examples/`. If you're unsure how a section should look, open the matching example:
- `examples/breakpoint-example.html` — for Breakpoints blueprint
- `examples/accessibility-table-example.html` — for Accessibility blueprint (audit with full manual checklist skipped scenario)
- `examples/accessibility-pagination-example.html` — for Accessibility blueprint (audit with Overview cascade pattern)

When in doubt about visual treatment, copy from an example rather than re-derive.

**The hard rule:**

> Before writing any HTML, **READ `scaffold.html` in full**, then **COPY it verbatim** to the user's output path. Fill ONLY the `{{PLACEHOLDER}}` slots and add sections inside the marked `<main>` zone. **Never** re-derive chrome / hero / footer / nav CSS from prose — that's how drift happens.

### Workflow

1. **Read** `~/.claude/skills/playbook-master/scaffold.html` end-to-end before writing anything.

2. **Copy** it to the target path:
   ```bash
   cp ~/.claude/skills/playbook-master/scaffold.html [user-folder]/[name]-playbook.html
   ```

3. **Fill placeholders** (do NOT change the structure around them):
   - `{{TITLE}}` — page title
   - `{{BRAND_SUB}}` — subtitle in chrome ("DLS · Accessibility Audit", etc.)
   - `{{NAV_LINKS}}` — one `<a href="#sec-id">Label</a>` per section, inside `.side-nav`
   - `{{EYEBROW}}` — eyebrow tag text (Topic · vN · Month YYYY)
   - `{{H1}}` — 3-statement headline OR short article title
   - `{{LEAD}}` — 2–3 sentence lead
   - `{{META_ITEMS}}` — 4–6 `<div><dt>Label</dt><dd>Value</dd></div>` in `.hero-meta`
   - `{{FOOT_COL1_*}}`, `{{FOOT_COL2_*}}`, `{{FOOT_COL3_*}}` — three footer columns
   - `{{FOOT_META_TITLE}}`, `{{DATE}}`, `{{FOOT_META_AUDIENCE}}` — footer meta

4. **Add sections** into the `<main>` zone, AFTER the hero. Use the reusable CSS classes already in the scaffold (`.findings-grid`, `.ff`, `.pg-guide`, `.anti-card`, `.code-finding`, `.step-table`, `.step-ladder`, `.step-callout`, `.thesis`, `.checklist`). Don't redefine them.

### Do NOT

- Redesign the chrome (height, color, blur)
- Redesign the hero (eyebrow → h1 → lead → meta rhythm)
- Redesign the footer (cream gradient, foot-grid 2fr/1fr/1fr)
- Swap the left side-nav for top tabs
- Change the BillDesk SVG inside `.brand`
- Introduce a dark theme, alternate fonts, or new tokens
- Generate CSS from the recipes section below if `scaffold.html` exists

### When the scaffold is missing

If `~/.claude/skills/playbook-master/scaffold.html` is genuinely absent (e.g., user installed only `SKILL.md`), **stop and tell the user** the install is incomplete. Recommend they reinstall the full folder. As a fallback, you may derive CSS from the recipes below — but warn the user the output will drift from teammates' playbooks.

The recipes below remain as reference for extending the scaffold (e.g., a new card variant), not for re-deriving what's already there.

---

## Workflow (be fast, not iterative)

The original playbook took ~30 rounds because we figured it out as we went. With this skill, you have the answers. Execute in **5 steps**, in this order.

### Step 0 — Type selection (FIRST, before anything else)

When invoked, before asking for the folder or anything else, ask:

> **Which type of playbook?**
>
> 1. **Breakpoints** — responsive scaling, viewport tiers, component density rules, the step-down ladder
> 2. **Accessibility** — WCAG audit (automated findings, code-level fixes, manual checklist, failure→fix playground)
> 3. **Specs** — component spec (anatomy, states, variants, props/API, tokens used)
>
> Pick one. Each type has its own section blueprint (see "Type blueprints" below). You can merge multiple later into a master per-component doc — but produce one playbook per type for now.

Lock in the choice before Step 1. All subsequent steps (folder structure, scope questions, proposed sections) are tailored to the chosen type.

### Step 1 — Component + source resolution (after type is chosen)

The user is a **designer** working against the DLS source tree. They already have the `src/` folder cloned somewhere on disk. Don't ask them to create folders or copy files — read straight from their source.

Send this prompt:

> Which DLS component? And paste the absolute path to your `src/` folder.
>
> Example: `Component: Pagination` · `Source: ~/Downloads/src 2/`
>
> If your story files live somewhere non-standard, paste that path too. Otherwise I'll look at `<src>/../stories/`.

#### Auto-resolve

Given `Component` (e.g., `Pagination`) and `sourceRoot` (e.g., `~/Downloads/src 2/`):

1. **Verify source exists** — check `<sourceRoot>/<Component>/`
   - If missing → fuzzy-match against `<sourceRoot>/*` folder names and suggest 3 nearest:
     > Couldn't find `Pgination` in your src. Did you mean: **Pagination**, **PageHeader**, **PriceSlider**?
   - Ask user to confirm or repaste the correct name.
2. **List source files** — every `.tsx`, `.ts`, `.css` under `<sourceRoot>/<Component>/` (and `__tests__/`).
3. **Resolve story file** — try in order:
   - `<sourceRoot>/../stories/<Component>.stories.tsx`
   - `<sourceRoot>/../stories/<Component>/<Component>.stories.tsx`
   - `<sourceRoot>/<Component>/<Component>.stories.tsx`
   - If none → ask user.
4. **Extract variants from the story** — parse `export const X = ...` declarations (excluding `default`). Present the list:
   > Found 5 variants in `Button.stories.tsx`: Primary, Secondary, Tertiary, Destructive, Text.
   > Audit all, or pick a subset?
5. **Create output folder**:
   ```bash
   mkdir -p ~/Documents/playbooks/<Component>-playbook
   cp ~/.claude/skills/playbook-master/scaffold.html ~/Documents/playbooks/<Component>-playbook/<component-name>-playbook.html
   ```
   If the user has a different preferred location, ask once and use it.

#### Optional extra inputs

The designer can paste inline at any point:
- Live URL / Storybook URL
- Figma frame links
- Existing playbooks for style match
- Screenshots (drag into chat)

No required `screenshots/`, `dev/`, `references/` folders — those are gone. Read what the user gives you. Note what's missing but proceed unless the gap is structural.

#### Visual clone (Breakpoints + Specs blueprints)

When the playbook needs to show the component visually (mock dashboard at multiple viewports, variant grid, anatomy diagram):

- **Read the TSX source** and identify the visual structure (JSX tree + className strings).
- **Recreate it inline** in plain HTML/CSS using the same DLS tokens and Tailwind classes.
- **DO NOT** ship a React runtime, Babel CDN, or any bundler output inside the playbook. The output is a single self-contained HTML file.
- **DO NOT** screenshot the component — recreate it. The clone is editable, scalable, accessible to inspection.

The clone is a **snapshot** of the source at the moment the playbook was generated. Note this in the footer:
> Audited against `<Component>` @ commit `<short-sha>` on `<YYYY-MM-DD>`.

If no Git available, just use the date.

### Step 2 — Scope (one round, exactly 2 questions)

The component is known from Step 1, so "Topic" is implicit. Ask only:

1. **Exclusions** — Anything explicitly OUT of scope? (e.g., "no step-down behavior — that's the breakpoint playbook", "skip the Destructive variant — not in production yet")
2. **Interactive elements** — Propose 2–3 based on the blueprint, ask which to keep.

DO NOT ask about:
- **Component name** — already from Step 1
- **Audience** — always **Dev + stakeholders**. Write for both.
- **WCAG level** — default **AA**, mention AAA as upgrade path.
- **Mobile** — default **no** (desktop-first, 720+ range) unless explicit.
- Section count (propose it)
- Color palette (always DLS)
- Font (always Inter)
- File format (always single self-contained HTML)

### Step 3 — Propose structure

Send the section outline in one message. Format:

```
## Proposed structure
1. **Hero** — [thesis statement]
2. **[Section name]** — [one-line purpose]
...

Approve / cut / reorder?
```

Wait for approval. Don't build until they say go.

### Step 4 — Build in batches

1. Read the reference HTML to extract scaffold (if available)
2. Write the new file in one go (or scaffold + Edit per section)
3. Announce each batch in one short line
4. Tell user the file path when done

### Step 5 — Manual checks (chat workflow, NOT inside the HTML)

For audit playbooks, manual checks are performed **in the conversation**, not inside the rendered HTML. The HTML displays verified outcomes; the chat is the workflow. Do NOT add interactive form fields (Pass/Fail radios, note inputs) to the HTML.

Before completing the playbook, ASK the user:

> The automated audit is rendered. About 60% of WCAG requires human verification (keyboard sweep, screen-reader walkthrough, focus visibility, zoom, touch, color-only state).
>
> Two paths:
> - **Skip** — Ship without the manual section. Clean automated-only playbook.
> - **Provide findings now** — I'll walk you through each check inline in chat. You run them in the browser, reply with PASS / FAIL + notes. I'll render the verified results in the HTML.

**If the user says SKIP** — omit the manual section entirely. No TBD placeholder, no TOC link, no mention in the footer.

**If the user says PROVIDE FINDINGS** — run the checks one at a time, inline in chat:

> **Manual check 1 of 6 — Keyboard sweep** (WCAG 2.1.1)
> Tool: Keyboard only. Disable mouse / cover trackpad.
> Steps: [numbered list]
> Look for: [observable criteria]
> 
> Run it, then reply: **PASS** / **FAIL** — notes: [anything you saw]

Wait for the user's reply, then move to the next check. Don't fire all 6 at once — one at a time keeps the loop tight.

Once all checks are answered (or the user says "that's all I'm running"), render a **Manual verification results** section in the HTML using the recipe below. The section shows the verdict for each check — no form, just the recorded outcome.

**Why this approach:**
- The chat is the conversation; the HTML is the deliverable.
- Designers run tests at their pace; the audit record assembles itself.
- Skipped audits don't get half-baked TBD sections.
- The HTML stays a clean published document, not a worksheet.

---

## Standard playbook architecture

Every playbook has these zones. Omit only when irrelevant to the topic.

| # | Zone | Purpose | Default? |
|---|---|---|---|
| 1 | Sticky chrome header | BillDesk logo + TOC | Always |
| 2 | Hero | 3-statement thesis + lead + meta strip | Always |
| 3 | Thesis comparison | Good vs naive side-by-side | If applicable |
| 4 | Rationale (3–9 cards) | Why the rule exists | Always |
| 5 | Tier reference | Matrix of tiers × components | If multi-tier |
| 6 | Interactive playground | Live scaled mock | If interactive |
| 7 | Visual ladder | 3-tile comparison | If multi-state |
| 8 | Reference table | Definitive spec | Always |
| 9 | Component behavior | One section per component | If component-focused |
| 10 | Anti-patterns | Red-tinted "don't" cards + fix | Always |
| 11 | Rationale callout | Exceptions explained | If exceptions exist |
| 12 | Footnote | Floors and asterisks | Always |

---

## Type blueprints

The standard architecture above gives you the zones. The blueprint tells you which zones to include and what each section's content focuses on, per playbook type.

### Breakpoints blueprint

**Prerequisite — run `/breakpoint-check` first.** When the user picks Breakpoints, before proposing structure (Step 3), invoke the `breakpoint-check` skill on the `dev/` folder + `screenshots/`. It produces scored findings across 6 axes (cap & center, step-down, fluid/fixed mix, touch-target floor, type scale, overflow) with code refs and a step-down ladder. Feed those findings into the blueprint below. Do NOT do the responsive analysis inline — `breakpoint-check` is the audit tool, `playbook-master` is the renderer.

If `breakpoint-check` has already been run in this session (or the user supplies prior findings), skip the invocation and reuse.

```
1.  Hero — 3-statement thesis ("Capped. Stepped. Floored.") + meta strip (range, baseline, floor)
2.  Thesis — Capped + centered vs naive fluid stretch (side-by-side)
3.  Rationale — UX laws applied (Fitts, Hick, Proximity, reading length, foveal, Miller, Jakob, etc.)
4.  Tier reference — 5 breakpoint tiles (e.g., 720 / 1024 / 1280 / 1440 baseline / 1920 / 2560+)
5.  Interactive playground — viewport scrubber + capped/fluid toggle + horizontal/vertical-nav layouts
6.  Step-down ladder — component scaling rules (button heights, row heights, paddings) + WCAG floor
7.  Anti-patterns — stretched tables, fluid forms, edge-pinned CTAs, tiny islands
8.  Footnote — sources, exceptions, floor reminders
```

Key tokens used: tier-reference table, visual ladder, rationale callout, playground with width slider.

### Accessibility blueprint

**Prerequisite — run `/accessibility-check` first.** When the user picks Accessibility, before proposing structure (Step 3), invoke the `accessibility-check` skill on the `dev/` folder. It produces scored findings with code references. Feed those findings into the blueprint below. Do NOT do the WCAG analysis inline — `accessibility-check` is the audit tool, `playbook-master` is the renderer.

If `accessibility-check` has already been run in this session (or the user supplies prior findings), skip the invocation and reuse.

```
1.  Hero — factual frame ("Auditing the X component") + eyebrow + h1 + lead + hero-meta strip
1a. Audit summary card (gauge + stat list) — REQUIRED, sits inside .hero, immediately below .hero-meta. See "Audit summary card" recipe.
2.  Thesis / Overview — semantic markup vs current approach (root-cause framing, not blame)
3.  Findings grid — 9 WCAG axes as cards (semantic / keyboard / names / states / color / target / focus / contrast / ARIA), with pass/partial/fail badges and specific issues
4.  Playground quick-guide strip — numbered guide above the tabs (REQUIRED)
5.  Interactive playground — 5 demos:
    a) Screen-reader simulation (Web Speech API, failure → fix)
    b) Keyboard nav visualizer (Tab order overlay)
    c) Focus ring (weak vs accessible side-by-side)
    d) Contrast checker (live ratios on real token pairs)
    e) Target-size overlay (16 / 24 / 44 px comparison)
6.  Manual verification — chat-driven (Step 5 workflow). If user provided findings, render results section. If skipped, omit entirely.
7.  Code-level findings — F-01, F-02, ... cards with file:line, current vs fix code, rationale
8.  Footer — sources (W3C WAI, axe rules, APG), severity legend, audit metadata
```

Key tokens used: **gauge + stat list (REQUIRED hero card)**, status badges, failure→fix cards, code-finding cards, Web Speech API for SR playback.

**Common mistake:** rendering only the `.hero-meta` strip and skipping the `.hero-summary` (gauge + stat list) card. The teammate-Button-audit drift came from this. The card is NOT optional — every audit playbook must include it.

### Specs blueprint

```
1.  Hero — what the component is, who uses it, what it's good for (one-liner)
2.  Anatomy — labelled diagram of the component parts (header / body / footer / actions / states)
3.  Variants — visual grid of all variants (size: sm/md/lg, intent: primary/secondary/ghost, etc.)
4.  States — interactive demo of every state (default, hover, active, focus, disabled, loading, error)
5.  Props / API table — every prop with type, default, description, example value
6.  Tokens used — design tokens referenced (color, spacing, type, radii) with token name + raw value
7.  Usage examples — 3–5 real composition examples with code snippet
8.  Anti-patterns — wrong usage with the fix
9.  Footnote — links to Figma, Storybook, related components
```

Key tokens used: variant grid, props table (with type column color-coded), state demos, code blocks.

---

## BillDesk DLS tokens (always use these)

```css
:root {
  /* Brand */
  --orange: #F26522;
  --orange-hover: #D9541A;
  --orange-tint: #FEF0E8;
  --orange-tint-2: #FFF7F1;

  /* Text */
  --text-1: #0D1526;     /* primary */
  --text-2: #4A5568;     /* secondary */
  --text-3: #8A97AC;     /* tertiary / muted */

  /* Surfaces */
  --canvas: #F7F8FA;
  --surface: #FFFFFF;
  --surface-2: #FAFBFC;

  /* Borders */
  --border: #E2E8F0;
  --border-strong: #CBD5E0;

  /* Semantic */
  --success: #0A8F5C;
  --warning: #D97706;
  --error: #C0392B;
  --success-tint: #E6F4EE;
  --warning-tint: #FEF3E2;
  --error-tint: #FBE9E7;

  /* Type */
  --f: 'Inter', -apple-system, BlinkMacSystemFont, sans-serif;
  --mono: 'JetBrains Mono', ui-monospace, monospace;

  /* Radii */
  --r-s: 4px; --r-m: 6px; --r-l: 8px; --r-xl: 12px; --r-2xl: 16px; --r-pill: 999px;

  /* Shadows */
  --sh-1: 0 1px 2px rgba(13,21,38,.04), 0 0 0 1px rgba(13,21,38,.04);
  --sh-2: 0 4px 12px rgba(13,21,38,.06), 0 0 0 1px rgba(13,21,38,.05);
  --sh-3: 0 12px 32px rgba(13,21,38,.08), 0 0 0 1px rgba(13,21,38,.06);

  /* Layout */
  --container: 1280px;
  --gutter: 32px;
}
```

### Spacing rule (strict)
- **Padding, margin, gap**: ONLY multiples of 8 (0, 8, 16, 24, 32, 40, 48, 64, 80, 96, 128)
- **NEVER** use 4, 6, 10, 12, 14, 18, 22, 28 for layout spacing
- **Type sizes**: NOT strict 8 — use the type scale below

### Type scale (perceptual, not arithmetic)
10 · 11 · 12 · 13 · 14 · 16 · 20 · 24 · 28 · 32 · 36 · 48

**Why typography breaks the 8-rule**: spacing is perceived linearly (additive), but type size is perceived logarithmically (ratio). A flat 8px delta would crush small text and leave large text indistinguishable. The type scale uses ~1.2–1.33 ratios so each step feels equivalent.

### Icon sizes
14, 16, 18, 20, 24, 32 — usually 16 in body, 20 in cards, 24 in nav.

### Border radii by use
- Inputs: 4
- Buttons: 6 or 8
- Cards (small): 8
- Cards (large): 12 or 16
- Hero cards / modals: 16 or 24
- Pills: 999

### Logo SVGs (paste inline, no external assets)
The BillDesk wordmark SVGs are in the reference playbook. Three variants:
- **Light wordmark** (dark text fills) — for light backgrounds
- **Dark wordmark** (white text fills) — for dark backgrounds
- **Logomark** (40×40 circle icon) — for compact rails / icon-only contexts

When you build a side-nav with collapse behavior, include BOTH the wordmark and logomark; CSS swaps which is visible.

---

## Hero pattern

```html
<section class="hero">
  <div class="shell">
    <span class="eyebrow"><span class="dot"></span>[Topic] · v[1] · [Month Year]</span>
    <h1>[3-statement thesis. Each sentence under 5 words. End with periods.]</h1>
    <p class="lead">
      [One paragraph: principle + scope + range. 2–3 sentences.]
    </p>
    <dl class="hero-meta">
      <div><dt>[Key]</dt><dd>[Value]</dd></div>
      <!-- 4–6 of these -->
    </dl>
  </div>
</section>
```

### H1 patterns that work
- "Capped. Stepped. Floored."
- "A playbook for responsive scale."
- "Content centered. Chrome full-bleed. Capped at 1440."
- "Layouts hold. Components step. Floors don't move."

**Rule**: 1–3 short declaratives OR one short article-style statement. Never longer than 80 chars total.

### Tone calibration — frame as state, not verdict

For **audit / quality playbooks**, the hero must describe what's being analyzed, not pronounce judgment. Stakeholders read the hero first; an accusatory opening puts them on the defensive before they see the data.

| ❌ Blunt verdict | ✓ Factual frame |
|---|---|
| "This table is not accessible." | "Auditing the Table component." |
| "The current code is broken." | "Current state of the Table — and the path to AA." |
| "17 violations." (alone) | "17 findings, each with a code-ready fix." |
| "We failed accessibility." | "Where the system stands against WCAG 2.1 AA." |

In the lead, prefer:
- "A WCAG 2.1 AA audit of …" over "Failures of …"
- "Findings, by axis" over "Where it fails"
- "Highest priority — block AA conformance" over "Blocks AA. Fix before release."
- "Address in the next sprint" over "Plan a sprint. Degrades experience."

Status badges (Fail / Partial / Pass) inside finding cards are still diagnostic and stay sharp — they're function, not editorial. The framing softens, the diagnosis doesn't.

### Lead patterns
2–3 sentences max:
1. The principle
2. What's inside / scope
3. The range

Example: "Layouts that cap and center. Components that step down in 8s. An accessibility floor that holds. Inside: the rationale behind every rule, and a live playground from 720 to 4K."

---

## Section recipes (copy-paste these)

### Eyebrow + section head
```html
<div class="section-head">
  <span class="eyebrow"><span class="dot"></span>[Tag]</span>
  <h2>[Title.]</h2>
  <p class="lead">[1–2 sentence intro.]</p>
</div>
```

### Rule card (3-grid intro)
```html
<article class="step-rule">
  <div class="ico"><svg width="20" height="20" viewBox="0 0 20 20" fill="none" stroke="currentColor" stroke-width="1.8" stroke-linecap="round"><path d="M3 10h14M10 3v14"/></svg></div>
  <h3>[Rule name]</h3>
  <p>[2–3 sentences. State the rule, give a concrete example.]</p>
</article>
```

Add `.a11y` modifier for green-tinted accessibility floor cards.

### Tier reference table
```html
<div class="step-table">
  <div class="step-table-row head">
    <span>Component</span>
    <span>[Tier 1] <small>label</small></span>
    <span>[Tier 2] <small>label</small></span>
    <span>WCAG floor <small>do not cross</small></span>
  </div>
  <div class="step-table-row">
    <span class="label">[Component name]</span>
    <span class="base">[X]px</span>
    <span>[Y]px <span style="color:var(--text-3);font-size:11px">(−8)</span></span>
    <span class="floor">≥ [N] [reason]</span>
  </div>
</div>
```

- Mark deltas in parens with grey small font
- Mark floor values in green via `.floor` class
- Mark baseline in orange via `.base` class

### Rationale callout (orange-tinted)
```html
<div class="step-callout">
  <div class="step-callout-ico"><svg>...</svg></div>
  <div>
    <h4>[Why this exception exists]</h4>
    <p>[Plain language explanation.]</p>
    <p>[Technical/scientific backing.]</p>
    <p><b>Accessibility consequence:</b> [WCAG criterion + impact.]</p>
  </div>
</div>
```

Use this for: type-scale exceptions, contrast ratios, motion preferences, ANY rule that breaks the strict pattern. Always include WCAG citation if applicable.

### Anti-pattern card (red-tinted)
```html
<article class="anti-card">
  <span class="anti-tag">✕ Anti-pattern</span>
  <h3>[Failure name]</h3>
  <p>[Why it's bad — concrete impact, not abstract.]</p>
  <div class="anti-viz">[mini visualization or screenshot]</div>
  <div class="anti-fix"><b>Fix —</b> [What to do instead.]</div>
</article>
```

### Visual ladder (3-tile comparison)
```html
<div class="step-ladder">
  <article class="step-tier is-base">
    <div class="step-tier-meta">
      <span class="step-tier-tier">[Tier label]</span>
      <span class="step-tier-vw">[Value]</span>
      <span class="step-tier-range">[Description]</span>
    </div>
    <div class="step-demo">[Component at this tier]</div>
    <div class="step-demo-spec">
      <b>[Spec summary]</b>
      <span>[Secondary spec]</span>
    </div>
  </article>
  <!-- repeat 2x more, drop .is-base from non-base tiers -->
</div>
```

### Interactive playground (scaled mock)
Pattern:
- Stage container with overflow:hidden, fixed height
- Frame inside positioned absolute, `transform-origin: top center`
- JS: `scale = min(stageW/frameW, stageH/frameH, 1)`
- Tier buttons → set `currentWidth`, re-render
- Mode toggle (e.g., capped vs fluid) → apply class to stage
- Layout toggle (e.g., top vs side nav) → show/hide mocks by `data-layout`

### Failure → fix playground (for audit-style playbooks)

For accessibility / quality audit playbooks, every playground demo follows the **wrong → right** pattern. Never show just "here's how it works" — always show the failure observable AND the fix.

```html
<div class="ff-demo">
  <div class="ff-side ff-bad">
    <span class="ff-tag">✕ Failure</span>
    <div class="ff-render"><!-- the broken example, rendered live --></div>
    <div class="ff-readout">
      <b>What [SR / keyboard / contrast tool] reports:</b>
      <span>"image"</span>
    </div>
    <pre class="ff-code"><code>&lt;img src="..." alt="image" /&gt;</code></pre>
  </div>
  <button class="ff-toggle" data-toggle="ff-1">Try the fix →</button>
  <div class="ff-side ff-good">
    <span class="ff-tag">✓ Fix</span>
    <div class="ff-render"><!-- the corrected example, rendered live --></div>
    <div class="ff-readout">
      <b>What [SR / keyboard / contrast tool] reports:</b>
      <span>"Girl holding a credit card"</span>
    </div>
    <pre class="ff-code"><code>&lt;img src="..." alt="Girl holding a credit card" /&gt;</code></pre>
  </div>
</div>
```

Each axis has its own readout:
- **Screen reader**: use Web Speech API (`speechSynthesis.speak(new SpeechSynthesisUtterance(text))`) so a "Listen" button plays the announcement aloud. Also show the text caption.
- **Keyboard**: numbered overlay on focusable elements showing Tab order
- **Contrast**: live ratio reading (e.g., "3.2:1 fail" → "4.6:1 pass")
- **Focus**: outline visibility toggle (none → 2px orange ring)
- **Target size**: 24×24 / 44×44 overlay grid

### Audit summary card — gauge + stat list (REQUIRED for Accessibility blueprint)

Inspired by the BillDesk Collection Overview pattern. Sits inside `<section class="hero">` directly below `<dl class="hero-meta">`. CSS is already in `scaffold.html` — never re-derive it.

```html
<div class="hero-summary">
  <!-- LEFT: half-donut gauge -->
  <div class="gauge-card">
    <span class="gauge-card-title">Audit overview</span>
    <span class="gauge-card-sub">WCAG 2.1 AA · [N] axes checked</span>
    <svg class="gauge-svg" viewBox="0 0 240 140" preserveAspectRatio="xMidYMid meet" aria-label="Audit score [X] out of 10">
      <!-- 3 arc segments — see arc math below -->
      <path d="..." fill="none" stroke="#C0392B" stroke-width="22" stroke-dasharray="4 6" stroke-linecap="butt"/>
      <path d="..." fill="none" stroke="#D97706" stroke-width="22" stroke-dasharray="4 6" stroke-linecap="butt"/>
      <path d="..." fill="none" stroke="#0A8F5C" stroke-width="22" stroke-dasharray="4 6" stroke-linecap="butt"/>
      <text x="120" y="100" text-anchor="middle" font-family="Inter" font-size="40" font-weight="600" fill="#0D1526" letter-spacing="-1">[score]</text>
      <text x="120" y="122" text-anchor="middle" font-family="Inter" font-size="11" fill="#8A97AC" letter-spacing="0.04em">OUT OF 10</text>
    </svg>
    <div class="gauge-legend">
      <div><b style="color:#C0392B">[N]</b>Critical / Fail</div>
      <div><b style="color:#D97706">[N]</b>High / Partial</div>
      <div><b style="color:#0A8F5C">[N]</b>Pass</div>
    </div>
    <div class="gauge-trend">
      <span>[↘ Fails WCAG 2.1 AA — fix critical first] OR [↗ N of M axes pass — ...]</span>
      <span class="arrow">›</span>
    </div>
  </div>
  <!-- RIGHT: stat list (3 or 4 rows depending on severity buckets) -->
  <div class="stat-list">
    <div class="stat-row crit">
      <div class="stat-info">
        <h5>Critical</h5>
        <div class="stat-big">[N]</div>
        <div class="stat-sub">↓ Blocks AA conformance</div>
      </div>
      <div class="stat-icon" style="background:var(--error-tint);color:var(--error)">
        <svg width="20" height="20" viewBox="0 0 20 20" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round"><circle cx="10" cy="10" r="7.5"/><path d="M10 6v4.5M10 13.5v.5"/></svg>
      </div>
    </div>
    <div class="stat-row warn">...</div>
    <div class="stat-row pass">...</div>
  </div>
</div>
```

#### Arc math (for the 3 gauge segments)

The gauge is a semicircle: `cx=120, cy=120, r=90`. Start point is `(30, 120)` at the left. Total arc = 180°.

For a segment that occupies **P%** of the full arc:
- Span in degrees = P × 1.8
- End-angle (clockwise from the previous end) = `previous_angle - span` (start = 180°, end = 0°)
- End-point x = `120 + 90 * cos(end_angle°)`
- End-point y = `120 - 90 * sin(end_angle°)`

Each arc command is: `M [start.x] [start.y] A 90 90 0 0 1 [end.x] [end.y]`.

**Pre-computed end-points for common splits:**

| Distribution | Seg 1 ends at | Seg 2 ends at | Seg 3 ends at |
|---|---|---|---|
| 33 / 33 / 33 | (75, 35.4) | (165, 35.4) | (210, 120) |
| 50 / 25 / 25 | (120, 30) | (197.9, 75) | (210, 120) |
| 67 / 22 / 11 | (165, 42.1) | (204.6, 89.2) | (210, 120) |
| 40 / 45 / 15 | (92.2, 34.4) | (200.2, 79.1) | (210, 120) |
| 30 / 40 / 30 | (67.1, 47.2) | (172.9, 47.2) | (210, 120) |

If your distribution isn't listed, compute fresh. Round to 1 decimal place.

#### Score

Use `passing_axes / total_axes × 10` (one decimal place). Examples: 3 pass / 20 axes = 1.5. 2 / 9 = 2.2. 8 / 9 = 8.9.

#### Stat-list rows

Match the gauge segments. Typically 3 rows (Critical / High-or-Partial / Pass). Add a 4th if the audit uses Critical/High/Medium tiers (then either combine into the gauge or add a separate "Axes passing" row).

### Playground quick-guide strip (REQUIRED when playground has 3+ tabs)

If the interactive playground has three or more demos / tabs, render a numbered guide strip directly above the tab bar explaining what each demo does and how to use it. Without it, users see tab labels with no idea what's inside.

```html
<div class="pg-guide">
  <div class="pg-guide-row">
    <div class="pg-guide-num">01</div>
    <div class="pg-guide-info">
      <b>[Tab name]</b>
      <span>[1–2 sentences: what to click, what to look for, the takeaway.]</span>
    </div>
  </div>
  <!-- repeat per tab -->
</div>
```

CSS:
```css
.pg-guide{
  background:var(--orange-tint-2);border:1px solid var(--orange-tint);
  border-radius:var(--r-xl);padding:16px 24px;margin-bottom:24px;
  display:grid;grid-template-columns:repeat([N],1fr);gap:16px;
}
.pg-guide-row{display:flex;gap:12px;align-items:flex-start}
.pg-guide-num{
  flex-shrink:0;width:24px;height:24px;border-radius:50%;
  background:var(--orange);color:#fff;display:grid;place-items:center;
  font-size:11px;font-weight:600;font-family:var(--mono);
}
.pg-guide-info b{display:block;font-size:13px;color:var(--text-1);margin-bottom:4px}
.pg-guide-info span{font-size:12px;color:var(--text-2);line-height:1.5}
.pg-guide-info code{font-family:var(--mono);font-size:11px;padding:1px 6px;background:var(--surface);border-radius:4px;border:1px solid var(--border)}
@media (max-width:980px){.pg-guide{grid-template-columns:1fr}}
```

The guide is the *user manual* for the playground. Without it, users click around blindly.

### Code-finding card (for audit playbooks — line-level fixes)

When an audit yields specific file:line violations, render each as a code-finding card with:
- Header: finding ID, file path with line range, WCAG criterion, severity badge
- Body: current code (left) vs fix (right), side-by-side, syntax-highlighted
- Foot: short rationale or "Bonus:" / "Better:" note

```html
<article class="code-finding">
  <header class="code-finding-head">
    <div>
      <h3>F-01 · [Short finding name]</h3>
      <div class="meta">[File]:[lines] · WCAG [criteria] · [Severity]</div>
    </div>
    <span class="status fail">Fail</span>
  </header>
  <div class="code-body">
    <div>
      <h5>Current</h5>
<pre class="code">[current code with .err spans on the broken bits]</pre>
    </div>
    <div>
      <h5>Fix</h5>
<pre class="code">[fixed code with .ok spans on the changes]</pre>
    </div>
  </div>
  <div class="code-foot">
    <b>Bonus:</b> [one-line follow-up, e.g., "wrap in &lt;button&gt; for free keyboard handling"]
  </div>
</article>
```

CSS:
```css
.code-finding{background:var(--surface);border:1px solid var(--border);border-radius:var(--r-xl);overflow:hidden;margin-bottom:16px}
.code-finding-head{padding:20px 24px;display:flex;gap:16px;align-items:center;justify-content:space-between;border-bottom:1px solid var(--border)}
.code-finding-head h3{font-size:15px}
.code-finding-head .meta{font-size:12px;color:var(--text-3);font-family:var(--mono);margin-top:6px}
.code-body{padding:24px;display:grid;grid-template-columns:1fr 1fr;gap:16px}
.code-body h5{font-size:11px;font-weight:500;letter-spacing:.06em;text-transform:uppercase;color:var(--text-3);margin-bottom:8px}
.code-foot{padding:16px 24px;background:var(--surface-2);font-size:12px;color:var(--text-2);border-top:1px solid var(--border);line-height:1.55}
.code-foot b{color:var(--text-1);font-weight:500}
pre.code{margin:0;background:#0D1526;color:#CBD5E0;padding:14px 16px;border-radius:var(--r-l);font-size:12px;line-height:1.6;overflow:auto}
pre .kw{color:#FFA76B}
pre .val{color:#7CD9B6}
pre .com{color:#5A6478;font-style:italic}
pre .err{color:#FF8B8B;background:rgba(192,57,43,.15);padding:0 2px;border-radius:2px}
pre .ok{color:#7CD9B6;background:rgba(10,143,92,.15);padding:0 2px;border-radius:2px}
```

Use `<span class="err">` on the broken lines in "Current" and `<span class="ok">` on the changed lines in "Fix" — visual diff highlighting. Devs scan to the highlighted bits first.

### Manual verification results (rendered AFTER chat workflow completes)

When the user has answered the manual checks inline in chat, render a **Manual verification** section showing the verified outcomes. **No form fields.** The HTML displays the verdict and the user's notes — read-only, published.

```html
<section id="manual" class="tight">
  <div class="shell">
    <div class="section-head">
      <span class="eyebrow"><span class="dot"></span>Manual verification</span>
      <h2>Verified by [name] on [YYYY-MM-DD].</h2>
      <p class="lead">Six human-required checks, performed in the browser against the rendered component. Results below.</p>
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
        <p class="manual-notes">Pin button focusable but invisible. Row Enter does nothing. Sort works only on Enter, not Space.</p>
      </article>

      <article class="manual-result pass">
        <header>
          <div>
            <h3>3 · Focus visibility</h3>
            <span class="manual-meta">WCAG 2.4.7</span>
          </div>
          <span class="status pass">Verified pass</span>
        </header>
        <p class="manual-notes">2px orange focus ring on every interactive element. Distinguishable at 200% zoom.</p>
      </article>

      <!-- ... one per completed check -->
    </div>
  </div>
</section>
```

CSS:
```css
.manual-results{display:flex;flex-direction:column;gap:12px}
.manual-result{background:var(--surface);border:1px solid var(--border);border-radius:var(--r-xl);padding:20px 24px;border-left-width:4px}
.manual-result.pass{border-left-color:var(--success)}
.manual-result.fail{border-left-color:var(--error)}
.manual-result.partial{border-left-color:var(--warning)}
.manual-result header{display:flex;justify-content:space-between;align-items:flex-start;gap:16px;margin-bottom:8px}
.manual-result h3{font-size:15px;margin:0}
.manual-meta{font-size:11px;color:var(--text-3);font-family:var(--mono);display:block;margin-top:4px}
.manual-notes{font-size:13px;color:var(--text-2);line-height:1.5;margin:0}
.manual-notes:empty{display:none}
```

**Skipped checks** are not rendered. If the user only ran 3 of 6, the section shows 3 results — not 6 with "skipped" placeholders.

**If the user skipped manual verification entirely** — do not render this section at all.

---

## Voice and copy

the user's style:
- **Brief, direct.** No pleasantries. No "Great question!"
- **Specific over general.** Use numbers (1440px, not "large").
- **Authority through restraint.** Don't oversell.
- **Three-statement rhythm** for headlines.
- **Eyebrow tags** with version + date.
- **Footnotes (\*)** for exceptions, with explanation at section end.
- **Active voice.** "Components step" not "components are stepped."
- **Em-dashes** for asides. Not commas-as-em-dashes.
- **Sentence-case headings**, not Title Case (except H1).

### Words to avoid
- "Best practice" — use "the rule"
- "Should" — use "must" or restate as imperative
- "Try to" — drop and state directly
- "Various" — name the specific things
- "Things" — name them
- "Etc." — name them

### Words that work
- "Holds", "drops", "steps", "caps", "floors"
- "Strict 8s", "the floor"
- "By design", "intentional"
- "Across the system"

---

## Anti-patterns in playbook construction (do NOT do these)

- ❌ Asking 10 setup questions. Ask 5 max.
- ❌ Building before structure approval.
- ❌ Using non-8 spacing values for layout (12, 14, 18, 22, 28).
- ❌ Using strict 8 for type sizes (use the type scale).
- ❌ Forgetting WCAG floors in reference tables.
- ❌ Long headers (>3 statements, >80 chars).
- ❌ Decorative SVGs without `aria-hidden="true"`.
- ❌ Using icon fonts. Always inline SVG.
- ❌ External CSS/JS files. Always single HTML.
- ❌ React runtime / Babel CDN inside the playbook. Visual clones are hand-coded HTML/CSS using DLS tokens — never live React.
- ❌ Screenshots of components as the primary visual. Always recreate from source as inline HTML.
- ❌ Auditing components in bulk. One component per playbook run.
- ❌ Asking user to create folders + drop files. Read straight from their DLS `src/` path.
- ❌ Placeholder Lorem text in final output.
- ❌ Side-nav widths other than 56 (icon-only), 200 (medium), 240/264 (full open).
- ❌ "TBD" sections in delivered output (unless explicitly approved as placeholder).
- ❌ Apologizing or hedging in copy ("we tried", "hopefully").
- ❌ Code blocks without language hints.
- ❌ Long bullet lists where a table would work.

---

## Recovery patterns

**User says "it's broken"** →
1. Ask for screenshot (don't guess).
2. Identify the failing rule (CSS specificity, JS state, structure).
3. Fix with the smallest possible edit.
4. Tell user file is updated, refresh.

**User says "too long" / "make it shorter"** →
- Hero lead: cut to 2 sentences
- Section heads: drop subtitle
- Cards: 2 paragraphs max
- Drop secondary callouts

**User says "make it more generic"** →
- Drop product-specific names (Smart Collect, Ravi Menon, ₹ amounts)
- Use generic personas ("ops users", "admin")
- Frame as DLS / system, not single product
- Generic data in mocks (Item 1, ₹1,234, etc.)

**User shares improved version of the file** →
- Read their version
- Carry forward their changes
- Apply your new edits on top
- Don't overwrite their improvements

---

## Output

Default location: `~/Documents/playbooks/<Component>-playbook/<component-name>-playbook.html`

The skill creates this folder during Step 1. If the user has a different preferred location, ask once and use it.

After writing, tell user:
1. The file path
2. Section count / what's inside
3. Variants audited (if accessibility/specs blueprint)
4. Commit SHA + date noted in footer (snapshot reference)
5. Anything that needs vetting (mismatches, assumptions, missing inputs)

Don't oversell. Don't add emojis. Don't add "Hope this helps!"

---

## When to NOT use this skill

- Quick one-pager / cheatsheet (use a markdown file)
- Internal-only notes (no need for polish)
- Prototype iteration (use sketches first)
- Documentation that lives in code (use JSDoc / TSDoc / similar)

This skill is for **shareable, durable reference docs** that stakeholders will return to. If it's not that, don't bother with the polish.

---

## Demo prompt for first run

After installing, run a demo:
1. Pick a small topic the user cares about (e.g., "Button states", "Empty states", "Loading states", "Error messages")
2. Use this skill's scoping questions
3. Build a minimal version (hero + 1 rationale + 1 reference table + 1 anti-pattern)
4. Show output, get feedback, iterate
5. Confirm the skill produced a doc consistent with the breakpoint playbook
