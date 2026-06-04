# Changelog

All notable changes to playbook-master.

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
