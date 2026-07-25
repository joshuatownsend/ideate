# User evidence — outside-in lens

Real users are the evidence base. Three sources, then persona synthesis. Every piece of evidence gets a **source-type tag** — `live-research`, `transcript`, `interview`, or `repo-internal` — because Research Backing scoring counts distinct source types.

## Source 1 — Live research (deep mode)

Real signal from where users of this project (or its competitors) already talk.

1. **This repo's issue tracker**: `gh issue list` / `gh search issues` for the most-reacted open feature requests; grep issues and discussions for pain keywords — "workaround", "how do I", "would be great", "manually", "wish", "instead I".
2. **Competitor/adjacent projects** (find 1–2): their most-requested features, their issue-tracker pain, capabilities they have that this project lacks — and, just as useful, capabilities users complain about there that this project could do better.
3. **Web search**: blog posts, Reddit/HN/Stack Overflow threads mentioning the project or its problem space. What do people hand-roll? What do reviews and comparisons say is missing?

Record verbatim quotes with links. A feature request with 40 reactions is stronger evidence than any inference.

## Source 2 — Session-transcript mining

If the maintainer has been working in the target repo with Claude Code, recent transcripts are real usage evidence — the richest outside-in source there is, and the one most likely to return nothing for reasons that have nothing to do with the evidence.

**Assume this environment** (measured on a real run): transcripts run to tens of MB, a single JSON line can exceed 1,000,000 characters, and **`jq` may not be installed**. Two consequences, and they are the actual reason mining "silently doesn't happen":

- **Never `Read` a transcript**, and never grep one in content mode without `-o`. One matched line can be a megabyte; it will consume the miner's entire context before it reports anything.
- **Extract by streaming**, emitting only short capped excerpts. Bounded output is what makes this deliverable at all.

1. **Locate the files.** `~/.claude/projects/<repo-slug>/*.jsonl`, most recently modified first. Also sweep sibling worktree dirs — `<repo-slug>--claude-worktrees-agent-*` — which hold real sessions against the same repo. **Confirm the file selection with the user before reading**; wrong-project transcripts poison the evidence. Once confirmed, mining is a commitment: if it cannot be completed, say so **at the moment it fails**, not in a closing disclosure.
2. **Extract with a streaming pass** — node or python, never `jq`-dependent. Scan assistant/user text *and* `tool_use` Bash commands (a workaround the user actually built outranks any theoretical need), and print one capped line per hit:

```js
// node mine.js <file.jsonl> …  → a few short ranked lines per category, nothing else
const fs=require('fs'),readline=require('readline');
const NOISE=/^<(local-command|command-name|command-message|command-args|system-reminder|task-notification)/;
// [pattern, category, roles that count] — keep patterns word-boundaried:
// unbounded /again/ matches "against" and floods the output with prose.
const PATTERNS=[
 [/\b(doesn'?t|does not) exist\b|\bthere'?s no way\b|\bnot supported\b|\bno built-?in\b|\bno such (command|flag|option)\b/i,'missing-capability',['assistant','user']],
 [/\bworkaround\b|\bhand-?roll|\bhad to (write|build)\b|\bfor now I'?ll\b|\bmanually\b|\bby hand\b/i,'workaround',['assistant','user']],
 [/\bevery time\b|\beach time\b|\bas always\b|\blike (last|every) time\b|\bkeeps? (failing|breaking)\b/i,'repeated-ritual',['user']],
 [/\bstill (failing|broken|not working)\b|\bdidn'?t work\b|\bfailed again\b/i,'retry-after-failure',['user']],
];
const PER_CAT=12;
(async()=>{
 const buckets=new Map(PATTERNS.map(p=>[p[1],[]]));
 for(const f of process.argv.slice(2)){ let n=0;
  const rl=readline.createInterface({input:fs.createReadStream(f),crlfDelay:Infinity});
  for await(const line of rl){ n++;
   let o; try{o=JSON.parse(line)}catch{continue}
   const role=o?.message?.role; let c=o?.message?.content;
   if(typeof c==='string') c=[{type:'text',text:c}];  // plain human turns — NEVER skip these
   if(!Array.isArray(c)) continue;
   for(const b of c){
    const isCmd = b.type==='tool_use'&&b.name==='Bash';
    const text = b.type==='text' ? b.text : isCmd ? b.input?.command : null;
    if(!text||NOISE.test(text.trim())) continue;
    for(const [re,cat,roles] of PATTERNS){
     if(!isCmd&&!roles.includes(role)) continue;
     const bucket=buckets.get(cat); if(bucket.length>=PER_CAT) continue;
     const m=text.match(re); if(!m) continue;
     const i=Math.max(0,text.indexOf(m[0])-90);
     bucket.push(`${(o.timestamp||'').slice(0,16)} L${n} ${isCmd?'BASH':role}: …${text.slice(i,i+200).replace(/\s+/g,' ')}…`);
     break; } } } }
 for(const [cat,hits] of buckets) console.log(`\n[${cat}] ${hits.length}\n`+(hits.join('\n')||'  (none)'));
})();
```

Two traps this script exists to avoid, both of which silently produce a useless run:

- **A record's `content` is sometimes a plain string, not a block array** — and those are the maintainer's own typed turns, the highest-value evidence in the file. One real transcript held 179 of them against 4,090 block-array records; an array-only walk discards every human word and reports "no user friction found."
- **A per-file hit cap filled by a sloppy pattern truncates coverage.** With `/again/` matching "against", the cap was reached at line 4,519 of 13,488 — two-thirds of the session never examined, with no indication anything was missed. Cap per category, not globally, and scan the whole file.

Calibration: a full scan of two transcripts totalling 51 MB completes in about 1.2 s at ~250 MB RSS. If mining feels expensive, the extraction is wrong, not the transcripts.

3. **Categories to mine**: hand-rolled workarounds (scripts or curl commands wrapping what should be a first-class capability), repeated multi-step rituals, agent commentary like "X doesn't exist" or "there's no way to Y", retry-after-failure patterns, questions the user asked that the project couldn't answer.
4. **Report** each signal as timestamp, category, and a short verbatim excerpt — capped at roughly 40 excerpts across all files, strongest first. Widen the patterns and re-run rather than raising the cap; the deliverable is a ranked handful of real friction, not a dump.
5. **Report even when nothing is found.** "Scanned 4 transcripts (51 MB, 2026-07-18→07-24); 7 missing-capability, 12 workaround, 0 ritual hits" is a delivered result. Findings of zero are evidence about the project; silence is only evidence about the pipeline. State which files were scanned and over what date range so the next reconcile run knows what ground is already covered.

## Source 3 — Maintainer interview

Offered once per run (skippable; forced by `--interview`). The maintainer is a primary source, not a fallback. Ask exactly three questions, then stop:

1. Beyond what the research surfaced, what workflows do YOU (or your users) run around this project that it doesn't support?
2. What frustrates YOU about it that no issue tracker would show?
3. What's YOUR killer feature — the thing only you would think of?

**Ask via the AskUserQuestion tool — never as streaming text.** Interview questions printed inline in a busy turn (evidence subagents streaming updates around them) scroll past unread and unanswered. The tool blocks until the user responds and renders each question as an explicit interaction. Mechanics:

- **Opting out of the whole interview happens at the offer gate, not inside it.** The offer ("Do you want the 3-question maintainer interview?") is its own decision, made before any interview question is asked. Do not put a "skip the interview" option on the questions themselves — all questions in an AskUserQuestion call are always presented to the user; there is no early exit mid-call, so such an option would promise a termination it cannot deliver.
- Once the user opts in: one AskUserQuestion call carrying all three questions (adapt the wording to the project; keep the intent). Each question's options: **"Skip this question"** and **"Nothing comes to mind"**. Real answers arrive through the tool's built-in "Other" free-text field; say so in each question's text: *"Choose an option, or type your answer under Other."* A user who changes their mind mid-interview just picks Skip on the remaining questions.
- Best timing: offer the interview, then issue the question call immediately after launching the Phase 2 evidence subagents — the blocking wait then overlaps their runtime instead of adding to it.
- If AskUserQuestion is unavailable in the environment, ask the three questions in a dedicated turn containing nothing else, and wait for the reply before proceeding.

Each substantive answer becomes evidence with source-type `interview` and feeds candidate generation directly.

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
