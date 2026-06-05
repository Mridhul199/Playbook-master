# Changelog

All notable changes to playbook-master.

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
