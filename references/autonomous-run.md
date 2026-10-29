# Autonomous Runs — Phase Loop, Stop Set, Handoff and Report Templates

Load when the user says some form of "gather context, plan, iterate through all phases,
review after each, stop only when blocked". This is the most common request shape the
doctrine met in the field; the protocol below is what the sessions the user praised did.

## 1. Phase 0 — before the first dispatch

1. Read before acting: project rules, existing plan/docs location, the code the phase
   touches. Spike inline where a fact feeds the contract (real data, a real request).
2. Write the plan to the project's named location (user-named > project `CLAUDE.md` >
   global rule > skill default; state which). Plan = phases, each with: goal, packages,
   owned paths per package, acceptance commands, review depth, what the phase report must
   contain.
3. Write the contract: schemas, API shapes, shared types, ownership map, acceptance rows.
   Commit it (if commits are pre-granted) so workers can cite a sha.
4. Get one independent read of the plan (fresh agent, stronger-or-equal, or the user).
   You do not approve your own plan.
5. Confirm the stop set with the user once (below) — then do not ask again per phase.

## 2. Phase loop (repeat per phase)

```
plan slice → preflight envelopes → dispatch (disjoint first; dependents after)
→ "N workers running; nothing independent left" status line
→ use wait time for read-only scouts / the e2e script
→ receive each report → gate (verification-ladder.md) → integrate (apply hub patches,
  diff ownership, re-run on integrated tree)
→ commit per verified batch, with hashes in the log (if commits are granted; else cite
  the contract and files by path)
→ independent review per the review chain: a different agent instance; Opus at `high`
  with fresh context and runtime probes when the phase is merge-bound multi-file,
  security, payments or deploy; the conductor's own read for trivia → fix wave → re-verify
→ phase report to file → one status line to the user → next phase
```

Rules inside the loop:

- Serialize phases that share files; parallelize only file-disjoint packages.
- One status line per milestone. Silence across three phases is a defect; so is a status
  line per tool call.
- Batch questions: collect non-blocking questions into the phase report's "Your calls";
  ask blocking ones together while unblocked work continues.
- Side requests ("also note X, don't stop") → backlog file, acknowledged in one line.
- Decisions made mid-phase → written to plan/contract the same turn, cited in the report.
- Never restart a server, rebuild an in-use binary, or run installs while another job
  (yours, a worker's, the user's) depends on it. Check first.

## 3. Stop set (canonical — SKILL.md and the rubrics point here)

Stop and ask when one of these is true. Otherwise continue.

1. A commit, push, deploy or PR that was not pre-granted.
2. A package install or a tool the user has not approved.
3. A product, design or **scope** call with more than one defensible answer and real cost
   to undo (the kind of call process cannot buy). Foreseeable ones are asked in phase 0;
   ones that surface mid-run go to "Your calls" if non-blocking, or are asked — batched —
   if blocking.
4. An action only the human can perform (permission dialog, physical device, account).
5. Destructive or irreversible steps: deleting data, rewriting published history, touching
   production.
6. A contradiction between the user's instruction and reality that survived one
   reconciliation attempt.
7. The failure chain is exhausted on a package: attempt → sharpened retry → escalated and
   halved → still failing.
8. A usage-limit notice: finish only what is genuinely a few steps, write the handoff, say
   that background agents keep running.
9. Context at ~80%: this is a pause, not a question — write the handoff (§4), tell the
   user, stop.

Not a stop: a question the code or docs already answer; a style choice with a repo
convention; a flaky test (re-run); a single worker failure (chain); a side request (backlog).

## 4. Handoff file (write before stopping early, and at ~80% context)

```
# Handoff — <date time> — <phase N of M>
Standing constraints (user's words, verbatim): …
State: what is merged (shas), what is in flight (agent, package, last known status),
       what is verified vs reported.
Where things are: plan, contract, backlog, phase reports, scratch (paths).
Not in context any more: <list what you know you lost>.
Next command: <the exact next step a fresh session runs>.
Open calls for the user: numbered.
```

After a compaction or a model switch, the *first* action is to read this file and the
plan from disk, then say what is no longer in context. The Fable→Opus handoff that worked
without re-explanation had exactly this: constraints verbatim, contract and backlog in the
repo, next command named.

## 5. Phase report template (to file; one-line pointer to the user)

```
**<Phase N: headline in one line — what is now true>**
What exists: <paths, one line each>
Real run: <numbers on the user's own data / the actual command and its observed output>
Built / tested / deployed / verified-against-real: <state each separately>
Not exercised yet: <explicit list>
Decisions written: <where>
Your calls:
  1. <what you saw → what happens → why it matters → fix → size → recommendation>
  2. …
Next: <phase N+1 in one line, or "blocked on 1 above">
```

Keep it terse; the praised reports were a screen long. Decision items are self-contained
(no bare ids); one numbering scheme for the whole run.

## 6. Consolidating N agent reports

1. Table every claim: claim · source agent · tag (verified / reported / inferred) · oracle
   run · result.
2. Dedupe by claim, not by wording.
3. Disagreement between agents = a finding. Resolve with an oracle, not by preference or
   by averaging; record which side was overstated and why.
4. Sample every audit: 5–8 "met" rows against the code; count rows vs the scorecard; run
   the mechanical checker if one exists. One failed sample → re-dispatch the audit with the
   miss appended.
5. Pin the commit each report was produced against. If the tree moved, re-verify citations
   — never offset line numbers.
6. Promote to the plan or doc only `verified` rows; carry `reported` rows with their tag.

## 7. Final report (to the user)

- Outcome first, one line.
- What could not be verified, and why (blocked oracle, no access, out of time).
- Paths: plan, contract, phase reports, handoff.
- What was left out of scope, and whose call it is.
- Open questions, numbered, self-contained.
- No restatement of the work; no closing offers.
