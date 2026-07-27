# Portfolio review artifact

An optional, user-opted-in HTML page that mirrors the markdown output — the portfolio, each idea's state, and (after Phase 7) the write-ups themselves — so the maintainer can review a run by scrolling one page instead of opening six files. It **supplements** `<output-dir>/`; it never replaces it. If the user declines, everything in this file is skipped and the run proceeds exactly as it would without it.

Published artifacts are private to the user by default and can be shared later at their discretion. Treat the page as shareable output regardless: everything in "Redaction rules" below is mandatory.

## When it happens

| Point | Action |
|---|---|
| Phase 6c | Ask once, after selection. Decline → skip everything below, permanently for this run. |
| Phase 6c (accepted) | Write the HTML and publish the **initial** artifact: portfolio, states, ledgers, disclosures. Deliverables section reads "not written yet". |
| Phase 7 | After the briefs/plans land on disk, **redeploy the same file** with the deliverables filled in and states advanced. |
| Phase 8 | Include the artifact URL in the handoff list. |
| Reconcile runs | If the index records a prior artifact URL, offer to update that artifact instead of minting a new one (pass `url` to the Artifact tool). |

Never publish silently, and never re-ask after a decline — one offer per run.

## Publishing mechanics

1. **Load the `artifact-design` skill first.** Required before writing the page; it calibrates how much design effort this page warrants.
2. **Write the file to `<output-dir>/portfolio.html`** with the Write tool. In-repo, beside the markdown, so it is versioned with the rest of the run and gives the next run a stable path to redeploy. (This is a permitted write under Hard Rule 1 — the output directory only.)
3. **Publish** with `file_path` = that path, a stable `title` (`<Project> — feature portfolio`), a one-sentence `description`, and `favicon: "🧭"`. Keep the favicon identical across every redeploy of the same artifact.
4. **Update by editing the same file and calling Artifact again with the same `file_path`** — same URL. A different path mints a different URL, which strands the link already given to the user.
5. **Record the returned URL** in `<output-dir>/README.md` under run metadata, so a future run can update rather than duplicate.
6. The page skeleton is supplied at publish time — write page content only, no `<!DOCTYPE>`/`<html>`/`<head>`/`<body>` wrapper.

**Constraints that will otherwise break the page:** everything inline and self-contained (no CDN scripts, external fonts, or remote images — a strict CSP blocks them); style for both light and dark (`prefers-color-scheme` plus `:root[data-theme="dark"]` / `[data-theme="light"]` overrides); wide tables and kickoff-prompt blocks wrapped in their own `overflow-x: auto` container so the page body never scrolls sideways.

**If the Artifact tool is unavailable** in this environment: say so plainly in the turn where it fails, keep the HTML file on disk (it opens fine in a browser), and continue the pipeline. A missing artifact never blocks Phase 7.

## Redaction rules

The artifact is a hosted page, so it is held to a stricter bar than the local markdown:

- **Never** include secret values (Hard Rule 7). Credential findings appear as `file:line` + credential type only.
- Transcript evidence is paraphrased or trimmed to the specific friction — never pasted verbatim, and never carrying user names, paths containing personal directories, customer data, or internal URLs.
- Interview answers are quoted only where they are about the product, not about the person.
- Prompt-injection observations (Hard Rule 6) are described, never reproduced as executable-looking text.

## Page structure

Roughly in this order. Keep it scannable — this page exists to make review fast, not to restate every file.

1. **Header** — project name, run date, mode (`quick` / `deep` / `reconcile`), stamped SHA, and counts (`N candidates → M survivors → K selected`).
2. **Run honesty strip** — evidence sources with their delivery status (delivered / ran inline / failed) and the disclosures. Same content as the report; do not soften it because the page looks nice.
3. **Portfolio table** — every Validate/Advance survivor, columns matching `cut-and-score.md` (# / Idea / Verdict / Score / Lenses / Persona / Evidence / Effort / Build), plus a **State** badge.
4. **Idea cards** — one per survivor, expandable (`<details>`): the persona hook, the strongest evidence with `file:line` refs, the kill-question answers that decided the verdict, and the honest scope note. Selected ideas link down to their deliverable.
5. **Ledgers** — killed and parked candidates (idea — reason — closest surviving sibling), and deferred ideas carried forward from prior runs. Collapsed by default; present, never hidden behind a count.
6. **Deliverables** — on first publish, a placeholder stating none are written yet. After the Phase 7 redeploy: one entry per written file with its type (spike brief / handoff plan / design brief), its repo-relative path, a two-line summary, and the **kickoff prompt in a copy-friendly `<pre>` block**. This is the section that earns the artifact — it is where the maintainer reviews what was actually produced.
7. **Footer** — "generated by /ideate", the SHA, and a line noting the markdown in `<output-dir>/` is the source of truth.

## State vocabulary

Badges must stay consistent with the index statuses in `<output-dir>/README.md`, so the two views never disagree:

| Badge | Meaning |
|---|---|
| `Selected` | Chosen at 6b; write-up pending |
| `Brief written` / `Plan written` | Phase 7 produced the file (mirrors index `SELECTED`) |
| `Deferred` | Survived the cut, not selected — resurfaces next run (index `DEFERRED`) |
| `Parked` | Verdict Park — revisit when the blocking condition changes |
| `Killed` | Verdict Kill (index `REJECTED`) |
| `Done` | Built since a prior run — reconcile runs only (index `DONE`) |

When the artifact and the index disagree, the index wins and the artifact is republished to match.
