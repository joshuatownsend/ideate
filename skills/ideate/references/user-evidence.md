# User evidence — outside-in lens

Real users are the evidence base. Three sources, then persona synthesis. Every piece of evidence gets a **source-type tag** — `live-research`, `transcript`, `interview`, or `repo-internal` — because Research Backing scoring counts distinct source types.

## Source 1 — Live research (deep mode)

Real signal from where users of this project (or its competitors) already talk.

1. **This repo's issue tracker**: `gh issue list` / `gh search issues` for the most-reacted open feature requests; grep issues and discussions for pain keywords — "workaround", "how do I", "would be great", "manually", "wish", "instead I".
2. **Competitor/adjacent projects** (find 1–2): their most-requested features, their issue-tracker pain, capabilities they have that this project lacks — and, just as useful, capabilities users complain about there that this project could do better.
3. **Web search**: blog posts, Reddit/HN/Stack Overflow threads mentioning the project or its problem space. What do people hand-roll? What do reviews and comparisons say is missing?

Record verbatim quotes with links. A feature request with 40 reactions is stronger evidence than any inference.

## Source 2 — Session-transcript mining

If the maintainer has been working in the target repo with Claude Code, recent transcripts are real usage evidence.

1. Locate `~/.claude/projects/<repo-slug>/*.jsonl`, most recently modified first. **Confirm the file selection with the user before reading** — wrong-project transcripts poison the evidence.
2. Walk for friction signals: hand-rolled workarounds (scripts or curl commands wrapping what should be a first-class capability), repeated multi-step rituals, agent commentary like "X doesn't exist" or "there's no way to Y", retry-after-failure patterns, questions the user asked that the project couldn't answer.
3. Record each signal with timestamp, category, and a short verbatim excerpt. A workaround the user actually built during a session outranks a theoretical need.

## Source 3 — Maintainer interview

Offered once per run (skippable; forced by `--interview`). The maintainer is a primary source, not a fallback. Ask exactly three questions, then stop:

1. Beyond what the research surfaced, what workflows do YOU (or your users) run around this project that it doesn't support?
2. What frustrates YOU about it that no issue tracker would show?
3. What's YOUR killer feature — the thing only you would think of?

Each substantive answer becomes evidence with source-type `interview` and feeds candidate generation directly. Present the questions in their own turn — don't bundle them into another decision.

## Persona synthesis

From the gathered evidence, write **2–4 named personas**. NOT "developers" or "users" — concrete people with concrete habits. Good: "a platform engineer who re-runs the same three commands before every deploy." Bad: "API users."

For each persona, three short paragraphs:

- **Today (without this feature-set)**: what tabs are open, what scripts get re-run, what they can't answer today.
- **Weekly ritual**: what they actually do with the project, how often.
- **Frustration**: the single most tedious or impossible part.

Every persona statement must trace to evidence gathered above. If you find yourself inventing details to make a persona feel complete, stop — that persona isn't grounded.

## Degrade ladder (no fabrication, no hard halt)

Work down until a level is honestly supportable:

1. **Full outside-in**: 2–4 personas from live research + transcripts + interview.
2. **Thin evidence**: 1–2 personas from whatever sources exist (repo-internal evidence like issue templates and doc friction counts, tagged `repo-internal`). Mark each persona `LOW-CONFIDENCE`.
3. **Interview-only**: no external evidence, but the maintainer answered the interview — personas built from their answers alone, disclosed as such.
4. **Inside-out only**: no supportable persona. Disclose plainly in the portfolio: *"No user evidence was available — this run is inside-out only, and outside-in scoring dimensions are marked N/A."* Then proceed. A disclosed degrade is a valid run; an invented persona is not.
