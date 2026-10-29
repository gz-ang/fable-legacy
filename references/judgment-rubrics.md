# Judgment Rubrics — Fable-Tier Calls, Made Executable

Eight judgment calls that stronger models make implicitly and weaker models get wrong.
Each has a rule, a positive example (apply), and a negative example (don't). Load this
file only when facing one of these calls without a stronger model in the loop.
Rubrics 6–8 were added in the Oct 2026 revision from field incidents.

## 1. When to escalate the model tier

**Rule:** Escalate after two consecutive failed attempts on the *same* subtask at the
current tier (same count as the dispatch protocol: retry once, second failure goes
up). Escalate immediately — skip the retries — when the task shows: contradictory
requirements that survived one reconciliation attempt, a design choice whose cost of
being wrong exceeds the cost of asking, or reasoning circling the same hypotheses.
Carry the full failure trail upward — what was tried, what happened, current best
theory. Ordering with rule 3: workers escalate to the Conductor (model-to-model);
only the Conductor stops to ask the user.

- ✅ *Escalate:* Sonnet twice attempted a race-condition fix; both patches moved the
  failure elsewhere. Stop. Package both diffs + test output, escalate to Opus.
- ❌ *Don't:* First test run failed on a missing import. That is iteration, not
  defeat — fix and rerun. Escalating here wastes the premium tier on housekeeping.

## 2. When work is actually done

**Rule:** Done = the acceptance criteria in the work-package are demonstrated by
evidence a third party could check: passing test, successful build, observed runtime
behavior, or read-back of the written file. "The code looks correct" is not a state
of done; neither is "the edit was applied." A subagent's report is not that evidence
either — it is a claim *about* evidence, and it is `reported` until you check it
(SKILL.md "Receiving findings").

- ✅ *Done:* Migration written, `php artisan migrate` ran clean on a fresh DB, the new
  endpoint returns the contracted shape in an actual request, test added and green.
- ❌ *Not done:* All files edited, "should work now" — but the test suite was never
  rerun after the last change. The last change is exactly the one that breaks it.

## 3. When to stop and ask the user

**Rule:** Ask when the decision is the user's to make and wrong guesses are expensive
to unwind: irreversible actions, scope changes, taste/product calls with multiple
defensible answers, or contradictions between their instructions and reality (the
canonical list is `autonomous-run.md` §3). Do NOT ask about things the code, docs, or
conventions already answer.

- ✅ *Ask:* Task says "clean up old sessions table" but the table has rows from
  yesterday. Deleting live data on a guess is unrecoverable — surface it.
- ❌ *Don't ask:* "Should I use the project's existing formatter?" The repo config
  answers this. Asking burns the user's attention on a solved question.

## 4. When the direction is wrong (switch path, don't retry)

**Rule:** Retrying suits *execution* failures (typo, flake, missing setup). Switch
approach when the *theory* keeps failing: each fix reveals a new problem in the same
area, the diff keeps growing relative to the goal, or you are fighting the framework.
Signal: you are explaining why the approach still makes sense more often than making
progress.

- ✅ *Switch:* Mocking a third-party client for tests has spawned four nested mocks
  and still fails. Stop patching mocks — wrap the client behind an interface and fake
  the interface instead.
- ❌ *Don't switch:* An integration test failed once on a port conflict. That is
  environment noise — rerun; redesigning the test harness over one flake is churn.

## 5. Quality floor — what to verify before calling anything reviewable

**Rule:** Minimum bar regardless of task size: it builds/parses; existing tests still
pass; new behavior has at least one test or demonstrated run; no secrets, debug
prints, or dead code introduced; the diff contains only changes the task needed.
Anything below this floor is not "done with minor issues" — it is unfinished.

- ✅ *At floor:* Small refactor, all tests green, one new test for the extracted
  function, diff reviewed to confirm no drive-by edits.
- ❌ *Below floor:* Feature works when run by hand, but two unrelated files carry
  leftover experiment code and a commented-out block "in case we need it". Unfinished.

## 6. Trust or verify — how far up the ladder

**Rule:** Tier by cost of a miss (`verification-ladder.md` §1), then go one tier higher if
the worker ran at `low` effort, is Haiku, answered suspiciously fast, contradicts another
agent, or the claim is about to enter a contract, plan or doc. Confidence in the report
never lowers the tier.

- ✅ *Verify:* Worker says "all 3 endpoints done, 418/418 pass" on a merge-bound package.
  Re-run the suite on the integrated tree, `git diff --stat` per endpoint, run the risky
  path once for real. (In the field: package-local green hid a cross-package regression,
  and a "done" multi-item package had shipped only part of its items.)
- ❌ *Don't over-verify:* A scout located the config loader so a worker can read it. Take
  the path; the worker will open the file anyway.

## 7. Is my context poisoned?

**Rule:** Suspect it when you catch yourself acting on a summary, a backlog, a remembered
number or a UI label rather than on the code — or when the user asks why the last few
answers were wrong. Response: stop, list what you believe and where each belief came from
(summary / memory / file / run), re-read the file-backed ones from disk, re-run the
measured ones, drop the rest. Say what you no longer have in context.

- ✅ *Recover:* After compaction the backlog says "favicon missing". Grep before acting;
  it exists under another path. Then re-derive the remaining open items from the code,
  not the backlog.
- ❌ *Don't:* Re-open "finished" items because the summary lists them as open, apply a
  mobile-only change to desktop because the summary dropped the qualifier, or invent a
  blocker ("needs a deploy key") that no file supports.

## 8. Handoff now, or push on?

**Rule:** Write the handoff (`autonomous-run.md` §4) when any of: context ≥ ~80%; a
usage-limit notice; a model switch is coming; you are about to start a phase you cannot
finish in the remaining budget. A finished handoff beats an unfinished deliverable. Push
on when the remaining work is genuinely a few steps *and* the handoff is already current.

- ✅ *Handoff:* Phase 4 of 6 done, context at 82%, phase 5 touches five files. Write the
  handoff naming the next command; tell the user; stop.
- ❌ *Don't:* Stop "at a clean point" two minutes in with no handoff file — the user came
  back eight hours later to "continue till the end" and nothing had moved.

## Escape hatch

If a call is not covered here and the stakes are real: prefer the reversible option,
write down what you chose and why, and flag it in your report. A documented guess a
reviewer can catch beats a silent one nobody can.
