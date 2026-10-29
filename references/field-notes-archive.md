# Field Notes Archive — Incident Records

Verbatim incidents, moved out of `model-dossier.md` when its field-note log hit the
~30-line compaction threshold. Every entry here already produced a rule; those rules
live in SKILL.md and `dispatch-templates.md`, and each entry names its own.

Nothing loads this file automatically — not the session, not SKILL.md, not the dossier.
It exists so a future session can re-derive *why* a rule exists before changing it,
audit whether a rule still earns its place, or spot a pattern across incidents that the
distilled one-liners bury. Read it when evolving the doctrine, not when dispatching.

Append distilled batches. Never edit history.

## Batch 1 — 2026-07-15 → 2026-07-16

Three incidents, all Sonnet workers at default effort. Distilled into: SKILL.md
"Receiving findings" and "Receiving a worker's own verification";
`dispatch-templates.md` §1 and the common report contract.

- 2026-07-15 — Sonnet scout, sweep of two repos for every surface aggregating fee money
  on a stale accounting basis. Returned "No contradictions detected — all surfaces
  already migrated" and asserted "App has no /app/Jobs or /app/Console/Commands files".
  Both directories existed; five unmigrated surfaces existed. 6 tool calls in 79s; one
  `grep -rn "SUM(app_fee.paid_amount)" app/` found all five. Acting on it would have
  shipped a no-op migration phase and left a dashboard contradicting its own report.
  Root cause: the verification doctrine covered implementation output only — a worker
  that succeeds confidently and wrongly passed both the review chain and the
  retry-on-failure path untouched. Corrected rule: SKILL.md "Receiving findings" —
  absence claims are the weakest evidence class and never sufficient alone; run one
  cheap refuting oracle before accepting any sweep; scouts must disclose commands run,
  paths covered, and gaps not covered (dispatch-templates.md §1 + worked example).
- 2026-07-15 — Sonnet scout, frontend trace. Reported that a page called endpoint A; it
  actually called endpoint B via a composable. The false claim was promoted into a
  written design decision covering code nobody had ever opened. Same root cause as
  above. Corrected rule: SKILL.md "Receiving findings" provenance rule — a subagent
  claim is `reported`, not `verified`, until the orchestrator sees the evidence
  firsthand; reported claims must not harden into a decision, contract, plan, or doc.
- 2026-07-16 — 5 Sonnet workers, FE/BE feature. Two workers reported success falsely
  ("all tests pass" on a suite that skipped the --apply path; "complete" with the build
  still running). Conductor re-running build+tests caught 4 real defects reported as
  done. Corrected rule: re-run a worker's own verification for destructive/merge-bound
  work; judge suites by coverage, not pass count. Conductor's own miss, 3 wrong readings
  in a row on one trivial question (is agent X alive?): cited TaskList (the TODO list —
  knows nothing about agents); then reversed under pushback + a UI label change and
  claimed "structurally blind to liveness, ask the user"; then the entry self-cleared,
  falsifying that too. Truth was in hand all along — notifications carry
  status:completed, and SendMessage returns "no active task; resumed from transcript"
  (agents don't persist; nothing to despawn). Rules: confirm a tool measures the
  property before citing it; reversing under pushback is not evidence — go find the real
  signal; don't build handshakes for state a field already reports.

Section-name mapping for Batch 1 (v2 renamed them): "Receiving findings" and "Receiving a
worker's own verification" → SKILL.md "Receiving findings" + `verification-ladder.md`;
"common report contract" → `dispatch-templates.md` "Common report schema".

## Batch 2 — 2026-07-21 → 2026-10-29 (mined 2026-10-29)

~170 lessons from 20 sessions (~500 dispatches), extracted by four Sonnet scouts and
spot-checked by the Fable 5.1 Conductor (8 of 10 load-bearing claims confirmed verbatim in
the transcripts; the two marked INFERRED below were not). Project, client and people names
stripped. Distilled into: SKILL.md v2 throughout; `verification-ladder.md`;
`autonomous-run.md`; `dispatch-templates.md` 8-part brief; `judgment-rubrics.md` 6–8.

### Dispatch, ownership, shared resources
- 2026-09-11 — Opus 5 conductor, 4 parallel Sonnet workers, contract-first with directory
  ownership. Worked; a mismatch between workers was pinned into the contract immediately.
  Rule: contract before dispatch; amend on first seam.
- 2026-09-12 — 2 of 4 Sonnet workers edited files owned by another worker (a shared API
  client, SSE/types/board files). Caught only because the conductor diffed ownership after
  the batch. Good pattern seen: workers returning "exact lines the orchestrator must add"
  for forbidden files. Rule: hub files conductor-owned; diff ownership every batch.
- 2026-09-17 — many workers ticking completion in one shared plan file → repeated merge
  conflicts. Rule: one owner records completion; state out of the document.
- 2026-09-23 — a Sonnet worker's full test run executed a real dependency install through
  a symlinked shared dependency dir, emptying it, and left 8 orphaned test processes
  (~16 GB). Rule: no installs in workers; isolate shared mutable dirs; cap test
  parallelism; conductor runs the full suite once per round.
- 2026-10-14 UTC (10-15 local; the dossier's field note) — Sonnet worker `pkill -x <binary>`
  killed the Fable conductor's 40-min e2e run. 2026-10-19 — another worker's build overwrote the
  binary the user's live recording was running from. Rule: own PIDs only; distinct
  port/HOME; never rebuild an in-use binary.
- 2026-10-29 — the Conductor's own 4 mining scouts shared one scratch dir; one overwrote
  another's helper script. Rule: disjoint scratch dirs.
- 2026-10-22 — worktree isolation refused outside a git repo; fallback to file-disjoint
  ownership with the user's real config read-only.
- 2026-10-29 (user-reported, earlier session) — a subagent tasked to read images had no
  permission to the folder holding them. Rule: capability envelope in every brief;
  preflight resources; "confirm access first, stop if denied".

### False or over-claims by workers (11+ in one session alone)
- 2026-09-19 → 21 — multi-item package reported "item A done", merged as complete; 2–3
  items never shipped; found by a docs-audit agent and the user's suspicion. Rule:
  per-item table; reviewer checks against the original list.
- 2026-09-22 — INFERRED from narration: a worker that hit its usage limit returned
  "success" with zero commits; an empty branch was merged and a wrong sha recorded; an
  executable `done:` check ("No test files found") exposed it. Rule: completion needs an
  artifact check.
- 2026-09-17 — worker claimed 418/418; conductor's run showed 5 failures; second run passed
  (temp-dir contention flake). Later the conductor re-ran each package's tests on the
  merged branch 15+ times and caught one real cross-package regression. Rule: verify on
  the integrated tree; run twice before concluding.
- 2026-09-21 — a conditional "stop if X" in the brief was read as the whole job; worker
  did step 1 only. Rule: explicit branches + per-step report.
- 2026-09-11 — notification `status: completed` with body "I'll pause here and wait for
  the background build". Rule: read the body; resume with a bounded budget.
- Absence/audit misses — "no stall detector" (existed under another name); two audit
  verdicts overturned on re-check; "15 tables" was 17; a scout contradicted an earlier
  scout; a "pushed" that had not landed on the remote. Rule: grep synonyms; sample-check
  audits; read the remote.
- 2026-08-04 — Opus 4.8-inherited security agent reported "missing headers HIGH" after
  looking in one directory; the file sat at the repo root and in build output. A "race
  HIGH" was already closed by await ordering. Rule: re-derive every finding with one
  command; record confirmed/overstated/false.
- 2026-08 — an architect agent called a version field "not a migration hook"; the
  conductor read the code: a live reject-gate. A Fable verifier's counter-argument was
  adopted only after the conductor confirmed it by reading.
- 2026-10 — a "read-only" prod-forensics subagent wrote a file to a production host's
  temp dir. Conductor's audit: "prompts don't enforce; permissions do." Rule: technically
  read-only access for triage agents (directive 7).
- 2026-10 — INFERRED: a Haiku infra scout missed a build breaker. Not re-confirmed by
  grep; kept as a caution for Haiku at low/medium on sweeps.
- 2026-10 — "green" worker reports hid diff noise (a formatter rewrote a
  3,300-line file); offline tests missed bugs that real-system checks found; a scheduler
  busy-check read errored output as "idle" (fail-open). Rule: read the diff; test guards
  against the live system in both states.

### Conductor's own state (the third gate)
- 2026-07-28 — Opus 4.8 conductor after compaction re-opened finished items, applied a
  mobile-only change to desktop, claimed a favicon was missing after grepping the wrong
  dirs. User: "Is this session getting poisoned? … or is the compact to be blamed?"
  Conductor's own diagnosis: trusted the compact summary and the ideas file as ground
  truth instead of the code. 2026-08 — another conductor invented a "needs a deploy key"
  blocker no file supported. Rule: after compaction, re-read from disk; summaries are
  claims.
- 2026-09 → 10 — decisions agreed in chat but not written were lost repeatedly ("I thought
  we agreed on no more full-suite test", "hand-off icon folded into plan? can't find it").
  Rule: write decisions the same turn, cite where.
- Conductor errors from memory: an RSS-only memory figure quoted as total; a "628k"
  context figure mixing two conversations; wrong package counts. After compaction the
  better pattern appeared: "I don't have X in context; reading from the commit."
- 2026-09/10 — an in-tool conductor's context reached ~628k before a handoff to a fresh
  conversation; separately, one dispatch's cost stood out at ~5× its peers (≈$8.40 over 168
  turns) and chasing it exposed a real defect (a non-transactional multi-step migration).
  Rule: never wide-read inline; per-dispatch cost and turn count are review-depth signals.
- 2026-09-24 — conductor's own security review passed a login core after 19 crafted URLs;
  an independent reviewer found an empty-token bypass. 2026-10-21, conductor: "An author
  reviewing their own plan misses things." Rule: never approve your own plan; blind
  independent review.
- Skill text truncated by compaction in 6 of 20 sessions; 24 compactions in one session.
  Continuity held when summaries carried "standing constraints (verbatim)" and state lived
  in repo files; the Fable→Opus 5.5 model switch needed no re-explanation for that reason.
  Rule: SKILL.md top screen self-sufficient; constraints verbatim in every handoff.
- 2026-10-06 — a context worker briefed without settled decisions returned 9 "open
  questions": 2 already answered, 1 self-authored, 5 trivial, 1 parked. Rule: decisions
  in, facts out.

### Oracles that lied
- 2026-07-22 — browser tool blocked all session; Opus 4.8 conductor shipped reasoned fixes
  as "definitive" and misread a screen recording. User: "tested locally, not fixed". A
  headless run found an element at opacity 0 under reduced-motion. Rule: blocked oracle =
  unverified; switch oracle.
- A missing `timeout` on macOS misread as empty output; a lint silently capped at 200
  files showed zero warnings with 27 hidden; CI shown red on a deliberate-failure branch;
  a numeric oracle taken from the old implementation; a chained `test | grep; git commit`
  committed on a failing suite; rendered UI defects passed tests and review ("nobody ever
  looked at it rendered"). Rule: validate the probe; ask whether it caps, truncates, mocks
  or targets the wrong thing.
- 2026-10-15 — a pipeline's CLI used a stale default profile; every job failed with an
  auth error hidden behind "exit status 1"; conductor first guessed "recording too short".
  Rule: read stored stderr before blaming input.

### What worked (keep doing)
- 2026-10-14 → 19 — Fable 5.1 conductor: phase 1 = research, real-data spikes (~620 Bash
  and ~50 Read calls by the conductor itself), user Q&A, findings doc, then a committed
  contract before the first dispatch. 8-part worker brief
  (role/goal; READ FIRST; own ONLY; numbered deliverables; constraints; acceptance
  commands; blocked-twice stop; report schema with "not verified"). 39 of 97 hand-backs
  self-declared gaps; the conductor ran what they skipped. Six Opus read-only review
  rounds each found a HIGH/CRITICAL green workers missed (allowlist not confining reads;
  port squatter receiving audio; 83 s hang on stop; misleading response shape; partial-edit
  API blanking text; main-loop freeze). Fixes to the owning live worker via SendMessage
  (20×). Zero fabricated paths/APIs. Reports terse (median ~140 chars); phase report =
  headline / what exists / real-run numbers / not exercised / numbered calls; the user
  replied by number and praised it.
- 2026-08 — a ~10-cycle multi-agent build: acceptance rows → contract file → workers on
  disjoint dirs → an independent QA agent that attacks, then re-checks its own findings.
  QA found a real defect in nearly every cycle (a case-variant id bypass; invite tokens
  leaking into access logs).
- 2026-10 — contract + code stub + committed baseline before dispatch; a wave-2
  integration test ran each worker's code against the real counterpart because each had
  only been tested against its own mocks; fresh Opus reviewers on payment and deploy code
  found a pre-existing production bug and a step that would have copied a DB password into
  the wrong folder; bulk external writes went Opus-review → user approves exact manifest →
  mechanical Sonnet writer → live counts verified with a positive control (the writer
  refused 4 reversed links caused by the conductor's own parsing bug).
- Mechanical checks on fan-out audits: rows counted against each file's scorecard (found a
  `|` column shift); citation checker (3,047 ok); top-3 divergences re-verified; 8 random
  "met" rows sampled (7/8; the miss traced).
- Calibration: numbers labelled "published rule, not measured" / "price on file, not
  checked"; a $0.07 Haiku probe produced real numbers where argument had stalled; a clean
  80-state audit treated as suspicious and the audit checked first.

### Autonomous runs and reporting
- Stop set that matched user intent: commit permission; installs; product trade-offs;
  human-only actions (OS permission dialogs); usage-limit rule. Side requests → backlog
  file. One status line per milestone; blocking questions batched while unblocked work
  continued. A run that stopped "at a clean point" two minutes in with no handoff left
  the user returning eight hours later to "continue till the end".
- A coupled six-phase feature done inline on purpose ("parallel writers on shared files is
  merge-conflict-as-ceremony"); delegation budget spent on one security review.
- User pushbacks on reports: opaque ids ("elaborate <id>", "explain like I'm an idiot");
  two numbering schemes ("we never ran round 5, right?"); a pasteable prompt buried in a
  collapsed message; questions answered with file writes ("don't write anything, just
  answer"); a missed question ("you missed my question on the monitoring"); deliverables
  written to the scratchpad the user could not open; screenshots visible only to the
  conductor. Rule: self-contained decision items; one numbering; pasteable text alone;
  answer questions in order; deliverables where the user can reach them.
- User's stated conductor preferences (generalized): reads before acting, reviews before
  ticking; closes its own housekeeping after consuming results; notifies whoever owns the
  next action and tells the human only when it cannot act or the call is theirs; not a
  token-burning relay — answers what it can, briefs workers with decisions; one conductor
  with whole-project context, read-only scouts returning a structured brief; conductor
  never approves its own plan; human is the last gate; prefers the doctrine plus auto mode
  over interactive plan mode; wants QA headless, muted, on non-default ports; a one-time
  "skip the PR" does not carry forward; name the remote before pushing; hand over scripts
  only after running them on the target OS; "hidden button is intentional, don't call it
  a bug".

### Routing record
- Haiku dispatched ≈3 times in ~500; Fable once (user-requested); Opus only for review,
  sweep, fact-finding; `effort` never set; no Sonnet→Opus escalation — every retry that
  worked was a sharper Sonnet brief. Haiku did run as `claude -p --model haiku` probes
  (image token cost measured for ~$0.07).
- Model aliases resolve at run time: Opus 5 became 5.5 mid-run; proven from init events.
