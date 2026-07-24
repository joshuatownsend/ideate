# Output templates

Two formats. **Design/spike brief** is the default; **full handoff plan** only when `--plans` was passed or the user asks for one. Both are written for a reader with zero context from this session, and both are stamped with the run SHA for drift detection.

## Design/spike brief (default)

The goal is a decision-ready spec: enough to start a spike, honest about what's unknown. Investigation steps, not build steps.

```markdown
# NNN — <Idea title>

> Brief for a design spike. Investigate and prototype; do not ship.
> Drift check: `git diff --stat <SHA>..HEAD -- <evidence paths>` — if the
> evidence files changed materially, re-validate the evidence before spiking.

- **Status**: SELECTED | DEFERRED
- **Verdict**: Validate | Advance
- **Score**: N/10 (see ideas/README.md for the portfolio context)
- **Lenses**: inside-out (<signal>) / outside-in (<source types>)
- **Effort (coarse)**: S / M / L
- **Planned at**: <SHA> on <date>

## The idea
One paragraph: what the capability is and what using it looks like.

## Who wants this and why now
The persona(s) and evidence. Quote the strongest evidence verbatim with
file:line refs or links.

## Why this project, why cheap
What existing architecture, data, or surface makes this a natural fit —
with file:line refs.

## Trade-offs and risks
What this costs, what it competes with, what could make it a bad idea.
Honest — this section is why the brief exists.

## Open questions
The 3–7 questions a spike must answer before a build decision.

## Spike plan
Numbered investigation steps (read X, prototype Y behind a flag, measure Z),
each with a "you'll know when" observation. Target: a spike someone can
finish in a day.

## Decision criteria
What answer pattern means "build it", "reshape it", or "drop it".

## Kickoff prompt
> Copy-paste to start this spike in any session or agent.
```text
Read <absolute path to this file> in full before doing anything. It is a
design-spike brief: run the spike steps, answer the open questions, and
report against its decision criteria. Scope: <one line>. Do not build
beyond the spike; prototype work happens behind a flag or on a branch.
```
```

## Full handoff plan (`--plans`)

Written for an executor model that has **zero context**: it has not seen this session, the portfolio, or any other plan, and it may be a smaller model. Assume it is competent at following explicit instructions and weak at filling gaps or knowing when to stop. Three properties are mandatory:

1. **Self-contained** — all needed context inlined: code excerpts with `file:line`, repo conventions with an exemplar file pointer, build/test/lint commands.
2. **Verification gates** — every step ends with a command to run and its expected result. The executor never has to *judge* whether it succeeded.
3. **Hard boundaries** — explicit scope in/out and STOP conditions.

```markdown
# NNN — <Idea title>

> Executor instructions: follow steps in order; verify each before the next.
> If a STOP condition triggers, stop and report — do not improvise.
> Drift check first: `git diff --stat <SHA>..HEAD -- <in-scope paths>`.
> If in-scope files changed since planning, STOP and report drift.

- **Status / Score / Lenses / Effort / Planned at**: (as in brief)
- **Depends on**: other plans by number, or "none"

## Why this matters
The user value, compressed from the brief. The executor should understand
what "good" looks like, not just what to type.

## Current state
Files and their roles. Code excerpts (verbatim, with file:line) for every
location the plan touches. Repo conventions with one exemplar file.

## Commands
| Purpose | Command | Expected |
|---|---|---|
| test | ... | ... |
| lint | ... | ... |
| build | ... | ... |

## Scope
- **In**: exact files/areas the executor may touch.
- **Out**: everything else, named where tempting.

## Steps
1. <change>, then **Verify**: <command> → <expected output>
2. ...

## Test plan
New/updated tests, and what each must actually assert (a test that asserts
nothing meaningful is a review failure, not coverage).

## Done criteria
- [ ] checklist the reviewer re-runs — every item is a command or observable

## STOP conditions
Conditions that end the attempt (missing dependency, drift, a step's verify
fails twice, scope pressure).

## Maintenance notes
What future changes this capability makes easier/harder; follow-ups
deliberately left out.

## Kickoff prompt
> Copy-paste to start this work in any session or agent.
```text
Read <absolute path to this file> in full before doing anything. It is a
self-contained implementation plan: follow the executor instructions at
the top, run the drift check first, execute the steps in order verifying
each, and stop at any STOP condition. Scope: <one line>. Work on a
branch; when done, report against the Done criteria checklist.
```
```

Excerpt integrity: code excerpts in either format come from your own reads during this session — never from a subagent's report. Re-open every cited file before writing; a wrong excerpt becomes a wrong plan that fails its own drift check.

## Index (`<output-dir>/README.md`)

```markdown
# Ideas index

> Generated by /ideate. Last run: <date>, mode <quick|deep>, at <SHA>.

## Portfolio
| # | Idea | Verdict | Score | Lenses | Effort | Status |
|---|------|---------|-------|--------|--------|--------|
(all Validate/Advance candidates; Status is the USER's disposition —
SELECTED / DEFERRED / REJECTED / DONE — while Verdict is the advisor's)

## Parked ideas
| Idea | Parked because | Closest surviving sibling |
(advisor verdict: potentially useful but not now — re-scored on reconcile)

## Killed candidates
| Idea | Kill reason | Closest surviving sibling |
(every candidate cut in Phase 5, one line each)

## Rejected ideas (do not re-propose)
| Idea | Rejected because | Run |
(user said no, or a decision doc rejected it — persistent negative memory)

## Run log
| Date | Mode | SHA | Candidates | Survivors | Evidence sources |
```
