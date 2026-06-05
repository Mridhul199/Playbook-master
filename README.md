# Playbook Master

A Claude Code skill for creating premium, single-file HTML playbooks documenting design rules, system specs, audits, or pattern references.

Combines BillDesk DLS tokens with a battle-tested playbook architecture (hero → rationale → tier reference → interactive playground → reference table → anti-patterns → footnote). Output is consistent in voice, structure, and visual treatment across every playbook produced.

**Three blueprints:** Breakpoints · Accessibility · Specs. Pick one per run.

**Examples included** — open `examples/` to see three canonical playbooks produced by this skill. Use them as the visual gold standard when judging new output.

---

## Install — Git (recommended)

One-time clone, future updates with one command.

```bash
cd ~/.claude/skills
git clone <repo-url> playbook-master
```

Restart Claude Code. New chat → say "Create a playbook for X" or type `/playbook-master`.

### Update to latest

```bash
cd ~/.claude/skills/playbook-master
git pull
```

Restart Claude Code. Done.

### Check installed version

```bash
cat ~/.claude/skills/playbook-master/VERSION
```

See `CHANGELOG.md` for what changed.

---

## Install — Zip (fallback if no Git)

1. Download `playbook-master.zip` from the shared link.
2. Unzip into your skills folder:
   ```bash
   rm -rf ~/.claude/skills/playbook-master
   unzip ~/Downloads/playbook-master.zip -d ~/.claude/skills/
   ```
3. Restart Claude Code.

For updates: repeat the download + unzip step.

### Windows

1. Open File Explorer, navigate to `%USERPROFILE%\.claude\skills\`
2. Delete the existing `playbook-master` folder (if updating)
3. Extract the new `playbook-master` folder there
4. Restart Claude Code

---

## Usage

In Claude Code, say any of:
- "Create a playbook for [topic]"
- "Document the rule/spec for [thing]"
- "Build a playbook from this audit"
- "Same format as the breakpoint playbook"

Claude will:
1. Ask **which blueprint** — Breakpoints, Accessibility, or Specs
2. Ask **which component + path to your DLS `src/` folder** — skill auto-resolves source, story, tests, and variants
3. Ask **2 scope questions** (exclusions, interactive elements)
4. Propose a **section structure** for approval
5. Build the playbook as a single HTML file at `~/Documents/playbooks/<Component>-playbook/`
6. (Accessibility) Ask if you want to run manual checks inline in chat — your answers render into the published HTML

The skill recreates components visually from your source code as inline HTML/CSS (using real DLS tokens). No screenshots needed for handoff.

---

## What you get

Every playbook produced has:
- BillDesk DLS tokens (orange, dark text, cream surfaces, Inter font)
- Sticky header with BillDesk wordmark
- Floating left scroll-spy nav
- Hero with 3-statement headline + meta strip
- (Audit) gauge + stat-list summary card (BillDesk Collection Overview style)
- Rationale cards, reference tables, anti-pattern cards
- Optional interactive playground (drag handle, tier buttons, failure→fix demos, Web Speech screen-reader sim)
- Cream-gradient footer

Single HTML file. Self-contained. No external assets. Opens in any browser, hosts on GitHub Pages.

---

## Customizing for a different design system

1. Open `SKILL.md` → find the **"BillDesk DLS tokens"** section
2. Open `scaffold.html` → find the `:root { ... }` block at the top
3. Replace the orange + neutral palette with your design system's tokens
4. Update the type scale, spacing rule, radii to match
5. Replace the BillDesk wordmark SVG in `scaffold.html` chrome with your own

The architecture (hero, rationale, anti-patterns, tier reference, gauge) works for any design system. Only the tokens and logo are BillDesk-specific.

---

## Maintenance

When you discover a new pattern or constraint worth encoding, add it to `SKILL.md` and (if visual) to `scaffold.html`. Commit with a clear message. Bump `VERSION` and add a `CHANGELOG.md` entry.

Treat `SKILL.md` + `scaffold.html` as living docs.

---

## Credit

Refined over ~50 iterations across five production playbooks. The patterns in here represent days of iterative design feedback compressed into a reusable spec.
