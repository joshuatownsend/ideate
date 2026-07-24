# Adversarial cut and scoring

This pass exists because brainstorms without it produce flabby lists. It runs **after** generation, never folded into it — generate freely first, then cut hard here. "Not worth doing" is a valid and useful conclusion, for a candidate or for the whole batch.

## Procedure

1. For **every** candidate, force-answer each kill question below **in writing**. Any triggered kill signal without an offsetting survival signal kills the candidate.
2. Score each survivor on the rubric.
3. Assign each survivor a **verdict** (Kill / Park / Validate / Advance — defined below). A candidate does not Advance merely because it avoided a hard kill; it Advances only on an affirmative case for user value, strategic fit, differentiation, and feasibility.
4. Apply the dual-lens bonus (ordering, not points).
5. Record every Kill and Park: idea, one-line reason, closest surviving sibling. Killed and parked candidates are first-class output — they appear in the portfolio presentation and the index ledger.

## Kill questions

For each proposed feature, answer these questions before it remains under consideration.

### 1. Who would use this, for what recurring job, and how often?

Name the specific persona, the situation that triggers use, and the expected frequency.

**Kill signals:**
- No clearly named persona
- No repeatable trigger
- Expected use is vague ("it depends") or incidental
- The persona could reasonably ignore the feature without consequence

**Survival signal:** A defined persona would use it regularly *or* rely on it whenever a critical workflow occurs.

**Note on frequency:** Weekly use is a strong signal for interactive products and tools. However, for libraries, infrastructure, or safety-critical features (e.g., backup/restore, migrations), lower-frequency use can still justify inclusion if the stakes are high and the feature is essential when needed.

### 2. What meaningful outcome becomes easier, faster, safer, or newly possible?

Describe the user outcome — not the feature behavior — and estimate the magnitude of improvement.

**Kill signals:**
- The benefit is primarily convenience, novelty, or polish
- The outcome can only be described vaguely ("better visibility," "more flexibility")
- The user saves only trivial time or effort
- The feature does not change a decision, workflow, or result

**Survival signal:** The feature materially improves an important outcome or enables something the user cannot reasonably do today.

### 3. What unique leverage makes this more than a thin wrapper?

Identify the source of the feature's power. Examples include proprietary or local data, cross-source joins, accumulated context, workflow automation, service-specific intelligence, or a capability competitors cannot easily reproduce.

**Kill signals:**
- It is a renamed version of one existing operation
- A user could obtain essentially the same result from a generic AI prompt
- It adds another interface without adding intelligence, context, or automation
- There is no defensible reason this product should provide it

**Survival signal:** The feature combines data, context, or workflow capabilities in a way that creates disproportionate value.

### 4. Why does this belong in this product, for this project, right now?

Ground the idea in the project's users, architecture, roadmap, current pain points, or strategic direction.

**Kill signals:**
- The same recommendation could apply to nearly any product in the category
- No repository artifact, user evidence, roadmap item, or known workflow supports it
- A decision document or prior constraint already rejected the premise
- The feature is merely adjacent to the product's purpose

**Survival signal:** There is a direct and specific connection to the product's strategy, evidence, and present stage of development.

### 5. Does it strengthen, duplicate, or compete with an existing capability?

Name the closest existing feature, planned feature, or workflow. Explain whether the proposal extends it, replaces it, or creates overlap.

**Kill signals:**
- It introduces a second way to perform the same job without a compelling reason
- It fragments an existing workflow
- It competes with a stronger capability already available or planned
- The distinction depends on wording rather than user-visible value
- It increases conceptual or surface-area complexity without clear payoff

**Survival signal:** It fills a clearly defined gap or makes an existing capability substantially more valuable without unnecessary duplication or confusion.

### 6. What is the smallest credible version, and can the current architecture support it?

Tag the likely implementation path, required data, integrations, dependencies, security implications, and operational burden.

**Kill signals:**
- The idea depends on unavailable data or speculative infrastructure
- The "minimum" version still requires a major architectural detour
- Ongoing maintenance, support, or compliance costs are disproportionate to the value
- Buildability can only be described as "the AI will figure it out"

**Survival signal:** A useful first version can be built with known components, bounded risk, and an implementation path appropriate to the product's maturity.

### 7. What is the strongest sibling idea that loses to this one, and why?

Compare the proposal against the closest alternative aimed at the same persona, problem, or outcome.

**Kill signals:**
- No credible sibling idea was generated
- The feature only survives when evaluated in isolation
- The distinction between the ideas is cosmetic
- Another idea offers greater value, frequency, differentiation, or buildability

**Survival signal:** The proposal wins a direct comparison on explicit criteria, not preference or novelty. If no credible sibling exists, generation was too narrow — return to Phase 4 and widen before cutting.

### 8. What are we choosing not to do if we pursue this?

Identify the opportunity cost: engineering capacity, design attention, roadmap delay, added complexity, or reduced focus.

**Kill signals:**
- The feature is valuable but less valuable than the work it would displace
- Its benefits do not justify its complexity or sequencing cost
- It creates permanent product surface area for a temporary opportunity
- There is no credible reason to prioritize it now

**Survival signal:** The idea is important enough to displace a named alternative and remains worthwhile after its full cost is considered.

## Verdicts

Every candidate exits this phase with exactly one of four explicit outcomes:

- **Kill** — the underlying idea is not worth pursuing. Recorded in the killed ledger with reason and closest surviving sibling.
- **Park** — potentially useful, but insufficiently frequent, grounded, differentiated, or timely. Recorded in the parked ledger; re-scored automatically on the next reconcile run.
- **Validate** — promising, but requires user evidence, technical discovery, or prototype testing. Appears in the portfolio; if selected, its output is a **design/spike brief** (validation *is* the spike — a full plan would front-run it).
- **Advance** — strong enough to enter prioritization or product-definition work. Appears in the portfolio; if selected, offer the choice of spike brief or **full handoff plan** regardless of the `--plans` flag.

A feature should not Advance merely because it avoided a hard kill. It Advances only when there is an affirmative case for its user value, strategic fit, differentiation, and feasibility.

## Scoring rubric

Score each surviving candidate 0–10 as the sum of five dimensions. On inside-out-only runs (degrade level 4), User pain and Research backing are marked N/A and score bands scale proportionally (out of 5: ≤2 Park, 3 Validate, 4–5 Advance).

| Dimension | Points | Anchors |
|---|---|---|
| **User pain / demand** | 0–3 | 3 = explicit demand surfaced in research; 2 = persona frustration directly addressed; 1 = plausible inference; 0 = no evidence |
| **Project fit** | 0–2 | 2 = squarely in the product's purpose and stage; 1 = adjacent; 0 = category-generic |
| **Feasibility** | 0–2 | 2 = natural-fit with current architecture; 1 = bounded new work; 0 = architectural detour |
| **Research backing** | 0–2 | Mechanically count distinct evidence source types cited (live-research / transcript / interview / repo-internal); each counts 1, cap at 2. This dimension is counted, never vibes-scored |
| **Differentiation** | 0–1 | 1 = makes the product more distinct; 0 = catches up to category norms |

**Score bands → verdict support:** ≤4 supports Park; 5–7 supports Validate; 8–10 supports Advance. Bands *inform* the verdict but do not dictate it — the affirmative case (user value, strategic fit, differentiation, feasibility) remains the final gate. A 9/10 with an unanswered feasibility question can still land at Validate; record why whenever verdict and band disagree.

> **Tuning note.** As weighted, Differentiation (0–1) means a well-evidenced catch-up feature — an export format every competitor has, say — can still Advance. That is the right default for actively-supported products, where closing table-stakes gaps is often the highest-value work. If you want the skill biased harder toward *novel* capabilities (the "transcendence" spirit of Printing Press's Novel Features), bump **Differentiation to 0–2** and drop **Project fit to 0–1**. Keep the total at 10 so the score bands stay meaningful.

## Dual-lens bonus

An idea with grounded evidence from **both** lenses — the architecture makes it cheap AND a persona demonstrably wants it — is the intersection this skill exists to find. Sort dual-lens candidates above single-lens candidates of equal score, and say so in the portfolio ("both" in the Lenses column). Do not add points for it; the bonus is ordering and framing, not score inflation.

## Portfolio table format

```markdown
| # | Idea | Verdict | Score | Lenses | Persona | Evidence | Effort | Build |
|---|------|---------|-------|--------|---------|----------|--------|-------|
| 1 | <imperative title> | Advance | 8/10 | both | <name> | <strongest single piece, cited> | M | natural-fit |
```

- **Build** column: `natural-fit` (existing architecture supports it directly) or `new-ground` (requires new subsystem/dependency) — the honest-scope commitment shown before the user selects.
- Show **every** Validate/Advance candidate. Never hide any behind "plus N more."
- Follow the table with the **killed** and **parked** ledgers (idea — reason — closest surviving sibling).
- Present the portfolio and the selection question in separate turns.
