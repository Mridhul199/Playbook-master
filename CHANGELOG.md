# Changelog

All notable changes to playbook-master.

## [1.3.0] — 2026-06-09

Design-tag enforcement upgrade. The `[Design change required]` tag was already in the blueprint but applied inconsistently — only to obvious color/contrast findings. This release makes it a hard checklist step that fires on every finding across all three blueprint types.

### Changed
- **SKILL.md** — Added `GLOBAL RULE — Design change required tag` section at the top of the router (above workflow steps). Applies to ALL blueprint types, not just accessibility. Includes a mandatory post-write scan: after all findings are written, rescan for any "Design fix:" bullet missing its tag.
- **blueprints/accessibility.md** — `Design change required tag` section rewritten as a hard checklist with a YES/NO decision gate per finding. Added "Enforcement" paragraph mandating a post-write scan. Expanded the "when to apply" list to include: component prop mandated as required, any new visual pattern not yet in the DLS.
- **Version bump** SKILL.md `v1.2.0` → `v1.3.0`.

### Scope
Findings that now reliably receive the tag (previously missed):
- Focus ring spec undefined in DLS → design must define color/width/offset
- Touch target size in design spec below 44px minimum
- `alt` prop not enforced as required in component API → design spec must mandate it
- Any finding whose "Design fix:" bullet would otherwise silently go to a developer who cannot action it

### Unchanged
- HTML output structure, scaffold, all section recipes
- DLS tokens, type scale, 8px grid
- Audit summary gauge, playground demos, code-finding cards
- Manual checks chat workflow

---

## [1.2.0] — 2026-06-05

Token-budget refactor. New sessions starting a playbook were burning ~50% of context on skill load alone. Split SKILL.md by blueprint so only the relevant one loads per run.

### Added
- **`blueprints/breakpoints.md`** — Breakpoints-specific instructions + screen-mock CSS recipes (`.mock-*` classes for whole-screen Breakpoints) + the canonical step-down table (Base/Medium/Compact values for every token).
- **`blueprints/accessibility.md`** — Accessibility-specific instructions, gauge math, code-finding card, manual-check chat workflow.
- **`blueprints/specs.md`** — Specs-specific instructions, variant grid, props table, tokens-used table.
- **Token-discipline rules in SKILL.md** — "don't re-read after Edit", "use `limit`/`offset` on TSX", "don't load examples unless asked", "read scaffold.html once per session", "load ONE blueprint, not three".
- **Screen-level Breakpoints** — Step 1 now accepts a screen name (e.g., `Orders screen`) as the "component" target.
- **Canonical step-down table** — single source of truth for every token's Base/Medium/Compact value. Reuse verbatim instead of re-deriving from prose.

### Changed
- **SKILL.md trimmed from 934 → ~250 lines.** Contains only the router (workflow, tokens, universal recipes, voice, anti-patterns). Blueprint-specific content moved out.
- **Step 0** — locks blueprint choice and tells Claude to read ONLY the matching `blueprints/*.md` file.

### Removed (from SKILL.md, moved to blueprint files)
- Audit summary card recipe + arc math → `blueprints/accessibility.md`
- Playground quick-guide + failure→fix → `blueprints/accessibility.md`
- Code-finding card → `blueprints/accessibility.md`
- Manual verification render → `blueprints/accessibility.md`
- Tier reference table + visual ladder → `blueprints/breakpoints.md`
- Variant grid + props table → `blueprints/specs.md`

### Token impact
On a fresh playbook session: ~50% reduction on initial skill load. Single-blueprint sessions read ~600 lines of skill content vs. ~1,400 before.

### Unchanged
- HTML output structure (same scaffold, same section recipes)
- DLS tokens, type scale, 8px grid
- Scroll-spy side-nav
- Audit summary card requirement
- Manual checks chat workflow
- All section recipes (just relocated by blueprint)

Existing playbooks (table, pagination, button, selection-control, breakpoint, orders-screen) remain visually compatible.

---

## [1.1.0] — 2026-06-05

Source-aware workflow. Designers point at their DLS `src/` folder; the skill auto-resolves source, story, tests, and variants. No more manual folder creation or file copying.

### Added
- **Component resolution from source tree** — Step 1 now asks `"Which component? + path to src/"`. Skill walks `<src>/<Component>/`, finds the matching story file, parses variants from `export const X = ...` declarations.
- **Fuzzy match fallback** — if the component name doesn't exist as a subfolder, skill suggests 3 nearest matches.
- **Output folder convention** — skill creates `~/Documents/playbooks/<Component>-playbook/` and copies `scaffold.html` into it as the working file. User can override the location.
- **Visual clone guidance** — explicit rule that visuals are hand-coded HTML/CSS using DLS tokens, read from TSX source. No React runtime, no Babel CDN, no screenshots-as-primary-visual.
- **Snapshot reference** — footer notes "Audited against `<Component>` @ commit `<sha>` on `<date>`" so reviewers know which source version the playbook reflects.

### Changed
- **Step 1** rewritten — replaced "create folder + drop files into screenshots/dev/references" with the source-aware flow.
- **Step 2** trimmed from 3 questions to 2 — component name is implicit from Step 1, so "Topic" is dropped.

### Removed
- Manual folder creation prompt
- `screenshots/`, `dev/`, `references/` subfolder requirement
- Type-specific "drop your inputs" prompts (no longer needed)

### Anti-patterns added
- Don't ship a React runtime / Babel CDN inside the playbook
- Don't use screenshots as primary visuals — recreate from source
- Don't audit components in bulk — one per run
- Don't ask the user to create folders — read from their DLS `src/`

### Unchanged
- HTML output structure (same scaffold, same blueprints, same recipes)
- DLS tokens, type scale, 8px grid
- Scroll-spy side-nav
- Audit summary card (gauge + stat list)
- Manual checks chat workflow
- All section recipes

Existing playbooks (table, pagination, button, selection-control, breakpoint) remain visually compatible with v1.1.0 output.

---

## [1.0.0] — 2026-06-04

Initial release. Refined over ~50 iterations on three production playbooks (Smart Collect breakpoint, Table accessibility audit, Pagination accessibility audit, Selection-control accessibility audit, Button accessibility audit).

### Skill structure
- `SKILL.md` — workflow, blueprints (Breakpoints / Accessibility / Specs), tokens, section recipes, voice/copy rules, anti-patterns
- `scaffold.html` — canonical visual skin (chrome, hero, footer, all reusable section CSS, smooth-scroll JS, scroll-spy JS, Web Speech API for screen-reader sims)
- `examples/` — three reference playbooks teammates can compare against
- `README.md` — install (Git + zip) + share
- `VERSION` — semantic version
- `CHANGELOG.md` — this file

### Workflow (5 steps)
1. **Type selection** — Breakpoints / Accessibility / Specs
2. **Project folder** — create `screenshots/`, `dev/`, `references/` substructure
3. **Scope** — exactly 3 questions (topic, scope/exclusions, interactive elements)
4. **Build** — copy `scaffold.html`, fill placeholders, add sections per blueprint
5. **Manual checks** (audit only) — chat-driven, skip or provide findings; HTML displays results, no forms

### Visual system
- BillDesk DLS tokens (orange #F26522, dark text, Inter, JetBrains Mono)
- Strict 8px grid for spacing; type scale (10/11/12/13/14/16/20/24/28/32/36/48) for typography
- Sticky chrome header (BillDesk wordmark left, no inline nav)
- Floating left **scroll-spy nav** (IntersectionObserver-based)
- Three-statement hero h1, eyebrow + meta strip
- Cream-gradient footer

### Required patterns
- **Tone calibration** — factual framing for audits ("Auditing the X component"), never blunt verdicts
- **Audit hero summary card** — gauge + stat list pattern (BillDesk Collection Overview style) — mandatory for Accessibility blueprint
- **Playground quick-guide strip** — orange-tinted numbered strip above tabs when 3+ demos
- **Failure → fix** pattern in playground demos (never just "here's how it works")
- **Code-finding cards** — file:line + WCAG + severity + current/fix side-by-side
- **Manual checks** — chat workflow, results-only in HTML (no Pass/Fail forms)

### Distribution
- Git: clone for install, `git pull` for updates
- Zip: fallback for non-Git users
