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
