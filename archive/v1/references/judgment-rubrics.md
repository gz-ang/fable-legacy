# Judgment Rubrics — Fable-Tier Calls, Made Executable

Five judgment calls that stronger models make implicitly and weaker models get wrong.
Each has a rule, a positive example (apply), and a negative example (don't). Load this
file only when facing one of these calls without a stronger model in the loop.

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
defensible answers, or contradictions between their instructions and reality. Do NOT
ask about things the code, docs, or conventions already answer.

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

## Escape hatch

If a call is not covered here and the stakes are real: prefer the reversible option,
write down what you chose and why, and flag it in your report. A documented guess a
reviewer can catch beats a silent one nobody can.
