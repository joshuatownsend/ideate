# ideate

A Claude Code skill for brainstorming, refining, and prioritizing new feature and capability ideas for any actively developed project.

`ideate` runs a **dual-lens pipeline**:

- **Inside-out** — mines the codebase itself for grounded direction signals: unfinished intent, stated-but-undelivered promises, surface asymmetries, the adjacent possible, and friction worth productizing.
- **Outside-in** — gathers real user evidence: live research on issue trackers and the web, friction mined from Claude Code session transcripts, and a short maintainer interview. Evidence becomes concrete personas; personas drive ideas.

Candidates are generated wide (2× target), then run through a mandatory **adversarial cut** — eight kill questions answered in writing per candidate — and a five-dimension scoring rubric. Every candidate exits with an explicit verdict: **Kill / Park / Validate / Advance**. Validate and Advance ideas are presented as a portfolio of options; killed and parked candidates are shown too, with reasons. Selected ideas become **design/spike briefs** (Validate) or optionally **full handoff plans** ready for an executor agent (Advance).

Ideas that survive *both* lenses — the architecture makes it cheap AND a real user demonstrably wants it — float to the top. That intersection is the point.

## Usage

```
/ideate                     quick run
/ideate deep                live research, more subagents, wider generation
/ideate reconcile           refresh a prior portfolio against current reality
/ideate <topic>             focus ideation on a stated area
  --plans                   produce executor-ready handoff plans
  --interview               force the maintainer interview
```

Outputs land in `ideas/` in the target repo: one file per selected idea plus a `README.md` index that makes the next run a reconcile instead of a cold start. The skill is strictly read-only on source code.

## Design principles

1. Generic-to-any-project suggestions are noise, not findings — every idea cites evidence from this repo or its users.
2. Never fabricate user evidence — degrade with disclosure instead (down to an honest inside-out-only run).
3. Separate generation from critique — brainstorm wide, then cut adversarially.
4. Killed and deferred ideas are never silent — they're recorded, shown, and resurfaced.
5. Strategy belongs to the maintainer — the skill produces grounded options with honest trade-offs, not a roadmap.

Inspired by the `next` command in [shadcn/improve](https://github.com/shadcn/improve) and the Novel Features + user research capabilities in [mvanhorn/cli-printing-press](https://github.com/mvanhorn/cli-printing-press).

## Install

Copy `skills/ideate/` into `~/.claude/skills/`, or install the repo as a Claude Code plugin.

## Status

v0.2.0 — first full end-to-end test run complete; six dogfood-driven amendments applied. Under active development.
