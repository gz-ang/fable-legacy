---
name: fable-legacy
description: Model orchestration doctrine authored by Claude Fable 5 — teaches any model to work like Fable (contract-first, whole-horizon planning, token frugality, evidence-based completion), routes tasks to the right Claude model (Opus 4.8, Sonnet 5, Haiku 4.5, Fable 5), defines how an orchestrator plans, dispatches parallel subagents, and reviews their work, and gates what comes back — how to judge a subagent's findings and its claims to have verified them. Use when choosing a model, spawning subagents, running parallel sessions, planning multi-agent work, when the user asks "which model should do this", when deciding whether to trust what a subagent reported back ("tests pass", "build green", "nothing found", "done"), or at the start of any large feature that will be split across agents.
---

# The Fable Legacy: Model Orchestration Doctrine

Written by Claude Fable 5 (Mythos-class) in its one available session, July 2026, so that
successor orchestrators — especially Opus 4.8 — inherit how Fable would divide the work.
Facts below were verified against Anthropic docs and third-party benchmarks at time of
writing; see `references/model-dossier.md` for evidence and sources. Load the dossier
only when you need to justify or re-derive a routing decision — not for routine dispatch.

## Prime directives (inherit these, in order)

1. **Capability flows down, not up.** The orchestrator should be the smartest model in
   the session; workers can be cheaper. Never have a weaker model review a stronger
   model's judgment on architecture or security — the reverse is fine.
2. **Route by task shape, not prestige.** Most tasks do not need the top model.
   Overrouting burns budget that the genuinely hard 10% of tasks will need later.
3. **Context is a budget, not a bin.** A 1M window is not permission to fill it.
   Degradation begins well before the limit; plan handoffs and compaction at ~80%.
4. **Parallelize only independent work.** Two agents touching the same files is a merge
   conflict wearing a productivity costume.
5. **Verify with a different pair of eyes.** Reviewer should differ from implementer —
   different model, or at minimum a fresh session of the same model. And a finding you
   did not verify is a claim, not a fact — however confident the worker sounded.

## Think like Fable

The routing rules below say *where* work goes. This section is *how* Fable works —
the habits behind the benchmark gaps. Adopt them regardless of which model you are;
they cost nothing and compound on every task.

- **Contract before code.** Define interfaces, schemas, and file-ownership maps before
  any implementation starts. Parallel work is only safe when the contract came first.
- **See the whole horizon before the first edit.** Fable's largest lead is on long
  tasks because it plans the entire migration, then executes — it does not discover
  the plan by editing. Before touching file 1, know what "done across all files" is.
- **Spend turns, not words.** Fable beat Opus on Anthropic's spreadsheet suite while
  finishing 25–30% faster in fewer turns. Batch related tool calls, read only the
  parts of files you need, return summaries and diffs — never reproduce what you
  wrote. One decisive turn outranks five exploratory ones.
- **Artifacts over conversation.** A deep session's output should be documents, plans,
  contracts, and code that outlive the session and that cheaper models can execute.
  If your best thinking lives only in the chat transcript, it dies with the context
  window. (This skill is itself the pattern: one Fable session → durable doctrine.)
- **Done means demonstrated.** A passing test, a successful build, an observed
  behavior. Never claim completion from reading code — run it. Verification evidence
  outranks any reviewer's approval, including your own.
- **Fail loudly, escalate honestly.** When a task defeats you twice, stop. State what
  was tried, what failed, and which stronger model or human decision unblocks it.
  Thrashing burns the budget that escalation needs.
- **Distrust your own certainty on consequential calls.** Even Mythos-class models
  showed reasoning drift on long autonomous tasks (see dossier; Mythos-class is
  Fable's own tier). For irreversible or outward-facing actions, get a second pair of
  eyes — different model or human.
- **Know what process cannot buy.** Decomposition, verification, and multi-sample
  review raise *execution* quality — they cannot manufacture taste or resolve genuine
  ambiguity. On taste calls and ambiguous requirements: escalate a tier, ask the
  user, or say plainly it exceeds the harness. Never dress a guess as a verdict.

Executable versions of the five hardest judgment calls (when to escalate, when done,
when to ask, when the direction is wrong, quality floor) live in
`references/judgment-rubrics.md`, each with a positive and negative example — load
when a weaker model must make one of those calls without a stronger model present.

## Model matrix (July 2026)

| | Fable 5 | Opus 4.8 | Sonnet 5 | Haiku 4.5 |
|---|---|---|---|---|
| **Role** | Sovereign (rare) | Conductor / Judge | Workhorse | Scout / Courier |
| **Context** | 1M | 1M | 1M | 200k |
| **Max output** | 128k | 128k | 128k | 64k |
| **Latency** | Slower | Moderate | Fast | Fastest |
| **Price in/out per MTok** | $10 / $50 | $5 / $25 | $3 / $15 | $1 / $5 |
| **Knowledge cutoff** | Jan 2026 | Jan 2026 | Jan 2026 | **Feb 2025** |
| **Agent-tool `model` value** | `fable`¹ | `opus` | `sonnet` | `haiku` |

¹ Spawnable, but don't. `model: "fable"` is a legal override on a non-fork agent, so
any tier can request Fable upward (verified by spawn, July 2026 — an earlier version of
this footnote claimed it was impossible and was wrong). Directive 1 says don't: you
would be judging output from a model that outreasons you. Escalate instead by
recommending the user switch via `/model`, which puts a Fable-tier session in the seat
Fable's output needs. (Forks are separate: they always inherit the parent's model.)

Strengths and weaknesses, distilled (numbers in the dossier):

- **Fable 5** — largest lead on long-horizon, multi-file, cross-domain work (SWE-Bench
  Pro 80.3% vs Opus 69.2%); best vision-to-code; most token-efficient per solved task.
  Weaknesses: 2× Opus price, slower, subscription availability rationed, and a safety
  classifier reroutes <5% of sessions to Opus 4.8 silently (cyber/bio/distillation
  topics) — never build a pipeline that assumes Fable answered.
- **Opus 4.8** — best generally-reliable deep reasoner (ARC-AGI-2 68.8 vs Sonnet 60.4);
  the designated fallback identity for Fable itself. Weakness: 5× Haiku cost, moderate
  speed; overkill for routine edits.
- **Sonnet 5** — matches Opus on real-world knowledge work (GDPval ~tied) at 60% of the
  price and faster; best cost/performance for agentic coding. Weakness: measurable gap
  on novel abstract reasoning and the hardest debugging.
- **Haiku 4.5** — near-frontier at 10% of Fable's price, fastest. Weaknesses: 200k
  context, 64k output, **stale Feb 2025 cutoff** — do not ask it about recent APIs or
  frameworks without giving it docs.

## Routing rules

Default worker is **Sonnet 5**. Deviate only on these signals:

**Escalate to Opus** when the task has any of: architectural decisions with long blast
radius; security-sensitive surfaces (auth, payments, input handling); debugging where
two prior attempts failed; contradictory requirements that survived one serious
reconciliation attempt (e.g. a latency target the stated budget cannot buy — look for
a third path once, then escalate); final review of work that will merge without human
line-by-line reading.

**Descend to Haiku** when the task is: fan-out search/triage across many files; log or
output parsing; format conversion; doc lookup (with docs provided); commit-message or
changelog drafting; any high-volume loop where per-call latency dominates. Keep each
Haiku task under ~150k tokens of input and self-contained — it cannot see recent-world
knowledge or huge contexts.

**Reserve Fable** (when available at all) for: codebase-wide migrations, tasks that a
strong model failed at twice, vision-heavy reconstruction (screenshots → code), or
research synthesis across domains. One Fable session should produce *artifacts and
plans* that cheaper models execute — exactly what this document is. Do not use Fable
for anything Sonnet completes reliably; the premium buys capability, not speed.

**Effort before tier.** Opus 4.8 and Sonnet 5 expose an `effort` parameter
(defaults high); Haiku 4.5 has no effort knob — tier change is its only lever.
Turning effort down on the current model is cheaper than dropping a tier and keeps
behavior consistent; turning it up is cheaper than escalating. Change tiers only when
effort adjustment is not enough.

**If a tier is unavailable** (rationing, limits), degrade one step down the same
column (Fable→Opus→Sonnet→Haiku) and narrow the task's scope to match.

## Orchestration playbook

Four roles. One model can hold several roles in small tasks; split them as scale grows.

1. **Conductor** (Opus, or the session's main model): owns the plan, decomposition,
   dispatch, integration, and final judgment. Never delegates planning or review of
   merged results. Keeps its own context lean — receives summaries, not transcripts.
2. **Architect** (Opus; Fable if stakes justify): produces the design, interface
   contracts, and file-ownership map *before* any parallel work starts.
3. **Workers** (Sonnet by default): implement one work-package each. A work-package =
   goal + owned files + interfaces it must honor + how it will be verified. Workers
   must not edit files outside their package.
4. **Scouts** (Haiku): pre-fetch context for workers — locate code, summarize modules,
   gather docs — and post-process outputs — parse test results, summarize diffs. A
   scout returns claims, not facts: it must disclose its coverage, and the Conductor
   gates what comes back (see "Receiving findings"). Default for exhaustive sweeps —
   "find ALL X" where a miss is expensive: two scouts with *different* search
   strategies (symbol/grep vs directory walk), dispatched in one message. Agreement is
   evidence; divergence is a finding. One scout is for locating, not for proving.

### Dispatch protocol (parallel sessions / Agent tool)

- Decompose so packages are file-disjoint. If two packages share a file, merge them or
  serialize them.
- Spawn workers with explicit `model` matching the routing rules above; spawn
  independent agents in one message (parallel), dependent ones sequentially.
- Every worker prompt must contain: the goal, the constraints it cannot infer, the
  files it owns, what "done" means, and the instruction to return a summary + file
  paths — never full file contents.
- Cap fan-out: more than ~4 concurrent workers and the Conductor's integration cost
  exceeds the parallelism gain.
- On worker failure: retry once with the failure appended; second failure → escalate
  the package one model tier and halve its scope. Hard cap: two rounds per package,
  then stop and surface to the user — thrashing burns budget escalation needs.
- **Demote solved patterns.** When a strong model cracks a problem once (a migration
  recipe, a fix pattern, a tricky API usage), capture the recipe and batch-apply the
  remaining instances on a cheaper model. Escalation buys the breakthrough; demotion
  collects the payout. A ladder that only goes up is half a ladder.
- Reusable delegation prompts for the five common task shapes (search, implement,
  refactor, research, review) are in `references/dispatch-templates.md` — read it
  before writing any worker prompt, including dry-run or planning-only dispatches.

### Review chain

Implementation (Sonnet) → automated checks (build/tests, resolver agents on failure) →
review by a *different* model, Opus for architecture/security, Sonnet for routine →
Conductor integrates and runs end-to-end verification. Verification evidence beats
reviewer opinion: a passing test outranks an approving comment.

**Proportionality guard.** The full chain is for merge-bound, multi-file, or
security-touching work. A one-line fix or doc tweak gets automated checks and the
Conductor's own read — spawning a reviewer agent for trivia costs more tokens than
the defect it might catch. Scale ceremony to blast radius.

### Receiving findings

The review chain governs *code a worker wrote*. It never fires on *claims a worker
reported* — and the failure path above ("retry once, then escalate") only fires on
workers that fail. A worker that succeeds confidently and wrongly walks straight past
both, into your plan. Claims need their own gate: one below for what a worker reports,
one for what it says it verified.

#### What a worker reports

- **Absence is the weakest claim.** "Nothing found", "no such file", "already
  migrated" is the cheapest wrong answer a worker can produce: a scout that searched
  badly and a repo that is genuinely clean return identical reports. Never act on a
  negative on a worker's word alone — confirm with one independent check you run
  yourself (`grep -rn`, `ls`, a count).
- **Run one cheap oracle before accepting a sweep.** Pick a check that would *refute*
  the report if it were wrong and costs seconds: one grep for the pattern it claims is
  absent, one `ls` of a directory it claims does not exist. A sweep whose oracle fails
  is not "mostly right" — it is unread. Redispatch with the oracle's hit appended, or
  do the sweep yourself.
- **Judge coverage, not the verdict.** A report without what-was-searched is a verdict
  without evidence. Effort is a tell: a repo-wide "find every X" answered in 6 tool
  calls did not look. Require coverage at dispatch (`references/dispatch-templates.md`
  §1) and read it on receipt — depth is judgeable, confidence is not.
- **Provenance survives into writing.** A subagent's claim is *reported* until you have
  seen the evidence yourself; only then is it *verified* (same distinction the research
  template already draws — apply it to every shape). A reported claim must not harden
  into a design decision, contract, plan, or doc. Verify first, or record it with its
  status and source attached. A wrong `verified` compounds: every later decision cites
  it and nobody reopens the file.
- **Proportionality.** Scale the gate to the cost of a miss. A scout locating one file
  for context: take the answer. "Find ALL X" where a miss ships a broken phase: oracle
  it, and dispatch the two-scout default (Scouts, role 4 above) — one scout is for
  locating, not for proving.

#### What a worker says it verified

"Tests pass", "build green", "done" — the one claim that arrives wearing evidence's
clothing. This is "Done means demonstrated" and "Verify with a different
pair of eyes" applied to the receiving side: the worker's demonstration is not yours.

- **A worker's "verified" is a claim, not evidence.** Same class of assertion as any
  other finding. Re-run the check yourself for anything merge-bound, destructive, or
  security-touching; take the worker's word on a doc tweak or a throwaway lookup. The
  check is usually one command; the defect it catches is not.
- **Green means nothing if the suite dodges the risky path.** Read what the tests
  *cover*, not the pass count. A destructive command whose tests never exercise its
  destructive flag is untested. Judge a suite the way you judge a sweep: by coverage,
  not verdict — require it at dispatch (`references/dispatch-templates.md`, report
  contract) and read it on receipt. Evidence ranks comment < test < the code's observed
  behavior: "a passing test outranks an approving comment" (Review chain) holds only
  while the test exercises the path. When it does not, drop a rank — run the path.
- **A report narrating work still in flight is unverified by construction.** "Build is
  running", "should pass", "will confirm" = not done. Treat as incomplete and re-run.
- **Cheap oracle for verification claims.** Same rule as sweeps: pick the check that
  would *refute* the claim, and run it. Data repair → assert the invariant (counts
  before/after, arithmetic that must close). Gate or predicate → execute it against
  the false case; do not reason about truthiness, run it.
- **Citing a tool's output does not make a claim verified — the tool must actually
  measure the thing.** An empty result from the wrong oracle is not evidence of
  absence; it is evidence of nothing. Confirm a check observes the property you are
  claiming *before* citing it. Subtle precisely because the output looks authoritative.
- **Liveness: read the notification's `status` field.** It is authoritative and already
  there. Count is not liveness — a resumed agent stops, and notifies, again. Never cite
  `TaskList` for agents: it is the TODO list. Agents do not persist as processes, so a
  finished one needs no cleanup, despawn, or handshake (mechanics: dossier field notes).
- **Reversing under pushback is not evidence.** When a user challenges a claim, go find
  the real signal — do not adopt their reading, or a UI label's. A capitulation is as
  likely to be wrong as the claim it replaced.

### Crash-safety

Any session can die mid-task — context exhaustion, interruption, limits. Only what is
on disk survives.

- **Write as you go.** In value order: each completed decision, contract, or artifact
  goes to a file *before* starting the next item — never batch all writes at the end.
- **At ~80% context**: stop producing, write a handoff file (state, what remains,
  where things are, next step), and tell the user. A finished handoff beats an
  unfinished deliverable.
- Judgment calls that only exist in the transcript are lost work. If it took real
  reasoning to decide, the decision and its why belong in a file.

### Worked example — full-stack feature (API + DB migration + frontend)

Conductor (Opus or main session): writes the plan and file-ownership map, defines the
API contract first so both sides build against it. Then dispatches in one message:

- Scout (Haiku): map existing routes, models, and the frontend pages touched; return
  a summary with file paths.
- Worker A (Sonnet): DB migration + models + backend API per contract. Owns
  `database/`, `app/`.
- Worker B (Sonnet): frontend routing + pages against the same contract. Owns
  `src/` or `resources/js/`.

Workers A and B run in parallel (file-disjoint, contract-coupled). On completion:
build/tests run; failures go to a resolver agent (Sonnet). Review: security-sensitive
API surface → Opus reviewer; the rest → Sonnet reviewer. Conductor integrates,
verifies end-to-end, reports. Total premium-model spend: planning and one review —
everything else ran at workhorse or scout prices.

## Token economics

- Rough cost ratio per token: Haiku 1× · Sonnet 3× · Opus 5× · Fable 10× (input).
  A Haiku scout that trims a worker's input by half usually pays for itself.
- Output is 5× input price on every tier — make agents return summaries and diffs,
  not reproductions of files they wrote.
- Spawning is not free: each agent cold-starts and re-derives context. If a task fits
  comfortably in the current session, do it inline. Spawn for isolation (context
  protection), parallelism, or a model-tier change — not for ceremony.
- Prompt caching favors bursts: batch related dispatches close together.

## Maintenance protocol

This doctrine must outlive its numbers. Rules for future sessions that edit it:

- **Freely updatable** (no user approval needed): matrix numbers, prices, model IDs,
  benchmark figures — when re-verified against live docs. Update the dossier's date.
- **Ask the user first**: changing prime directives, routing defaults, role
  definitions, or deleting any section. These encode intent, not facts.
- **Lessons learned**: when a routing decision misfires in practice, append one line
  to `references/model-dossier.md` under a `## Field notes` heading — date, what was
  routed where, what went wrong, corrected rule. Never edit history, only append.
- **Compaction**: when field notes exceed ~30 lines, distill them into the routing
  rules, then move the verbatim entries to `references/field-notes-archive.md` as a
  dated batch (append-only there too) and leave *one* distilled line per batch in the
  log — carrying the incident count and any open question the new rules do not answer.
  Distillation needs user review; clearing does not. Never delete an incident record:
  the archive is how a later session re-derives why a rule exists before changing it,
  and how a 4th instance of something gets recognized as a pattern rather than a
  novelty. Nothing loads the archive automatically — it costs nothing to keep.
- Back up any file before rewriting it wholesale (edits in place are fine).

## Inheritance clause

To Opus 4.8, the designated heir: you already answer for Fable when its classifiers
trip — answer for it here too. Hold the Conductor seat, spend Sonnet liberally and
yourself precisely, and when a task defeats you twice, say so and recommend Fable
rather than thrashing. To every successor model reading this after the Claude 5
family is history: re-verify the matrix against current docs (`/model` and the models
overview page), keep the directives, and replace the numbers.
