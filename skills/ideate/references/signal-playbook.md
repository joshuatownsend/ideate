# Signal playbook — inside-out lens

What the codebase itself reveals about what it wants to become. Five signal families, each mined by reading the repo — never by speculating about the category it belongs to.

**Grounding rule (repeat in every subagent prompt):** every finding must cite evidence from the repo itself. A suggestion that could apply to any project in the category is noise, not a finding.

## Signal 1 — Unfinished intent

Work someone started and meant to finish.

- TODO/FIXME/HACK clusters that share a theme (three TODOs about caching are a signal; one is not)
- Feature flags that were added but never rolled out or removed
- Stubbed functions, empty modules, interfaces with one trivial implementation
- Commented-out feature code
- Abandoned branches and mid-feature work visible in `git log` / `git branch -a`

## Signal 2 — Stated-but-undelivered

Promises the project made that the code hasn't kept.

- README/docs sections describing behavior with no corresponding code
- Roadmap items, "coming soon" notes, CHANGELOG "planned" entries
- CLI flags or config options that are accepted but no-ops
- Issue templates for feature categories that don't exist
- **A PRD or PRODUCT.md that names users, use cases, or direction the code hasn't caught up to is the strongest grounding signal there is.** Prefer it over inferred intent — and never propose something a decision doc already rejected; note the contradiction instead.

## Signal 3 — Surface asymmetries

One-directional pairs where the missing half is conspicuous.

- Export without import; backup without restore; create without bulk-create
- Webhooks out but not in; read API without write API
- Entities with CRUD minus one operation
- A public API that internal code clearly hand-rolls around instead of using
- Symmetric UI affordances present on one entity type but not its siblings

## Signal 4 — The adjacent possible

Capabilities the existing architecture makes disproportionately cheap.

- A plugin/extension system that is one interface-extraction away
- A public API one route file from the existing service layer
- An integration the data model already supports (the tables/types exist; only the glue is missing)
- Data already collected but never surfaced (logs, metrics, history tables with no UI/command)
- A generalization the code has hand-rolled three times in specific forms

## Signal 5 — Friction worth productizing

Things users evidently do by hand *around* the project that it could absorb.

- Setup steps in the README that could be a command
- Scripts in `scripts/`, `examples/`, or gists that wrap the project's own interface
- Multi-step workflows documented in docs/issues that could be one operation
- Copy-paste rituals visible in issue reproductions or discussions

## Finding format

Return each finding as:

```markdown
### [SIGNAL-N] Short imperative title

- **Evidence**: `path/file.ts:123` — one-sentence description. (2–5 strongest
  locations; "and ~N similar sites" if widespread.)
- **Who wants this and why now**: product/user value, concrete — not vibes.
- **Effort**: S (hours) / M (a day-ish) / L (multi-day) — coarse is fine.
- **Confidence**: HIGH/MED/LOW — how grounded the evidence is, not certainty
  that building it is the right call.
- **Sketch**: 1–3 sentences on what the capability would look like.
```

Findings only — no fixes, no implementations, no file dumps.
