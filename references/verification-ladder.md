# Verification Ladder — Trust Tiers, Hallucination Tells, Oracle Catalogue

Load when a report has arrived and you must decide how much to check before acting on it.
Every rule here was paid for: incidents are in `field-notes-archive.md`.

## 1. Pick the tier by cost of a miss, not by how confident the report sounds

| Tier | When | What you do |
|---|---|---|
| **0 Take the word** | throwaway lookup, doc tweak, a scout locating one file for context (the worker will open it anyway) | Read the report. Done. |
| **1 Spot-check** | any sweep, any absence claim, any audit verdict, any number | One refuting oracle you run yourself (≤ 30 s). Sample 5–8 rows of an audit. |
| **2 Re-run** | merge-bound code, "tests pass", "build green", data repair | Re-run the worker's own check on the *integrated* tree; read what the suite covers; run the risky path once for real. Run twice before calling a disagreement real. Then the review chain applies (SKILL.md): a different agent instance for routine work, Opus for multi-file merge-bound. |
| **3 Independent re-derivation** | security, payments, deploy/infra, schema changes, anything irreversible | Tier 2 **plus** a fresh-context stronger-or-equal reviewer with runtime probes; you verify its top findings in code. For a recommendation you cannot ground, consult a stronger model (up to Fable via the user's `/model`) and adopt only what you then confirm. |
| **4 Human** | destructive on live data, outward-facing, production credentials, user's taste or product calls | Stop with the evidence laid out and the exact action named. |

From Tier 1 upward, go one tier higher when: the worker ran at `low` effort; the worker is
Haiku; the report arrived suspiciously fast; the claim contradicts another agent; or you
are about to write the claim into a contract, plan or doc. Tier 0 pointer results are
exempt — a located file is checked the moment someone opens it.

## 2. Hallucination tells — reject or re-dispatch on sight

- **Specifics without provenance.** `file:line` for a file the worker never opened (its tool
  log shows no Read/grep there); API names or flags that do not appear in the repo or docs.
- **Claims outside the tool set.** "I ran the tests" from an agent type without Bash; "the
  screenshot shows…" from an agent that had no Read access to the image folder; "pushed"
  from a worker with no network. Check the capability envelope you dispatched with.
- **Effort mismatch.** A two-repo "find every X" answered in six tool calls and 79 seconds;
  a security audit that "looked in" one directory.
- **In-flight narration.** "Build is running", "should pass", "will confirm", "I'll pause
  here" — with `status: completed`. Not done.
- **Absence stated as fact.** "No such directory", "already migrated", "no stall detector"
  — without the commands that earned it. Both directories existed; the detector existed
  under another name.
- **Partial reported as whole.** "Item A done" for a three-item package; "completed" from a
  worker that hit its usage limit and committed nothing (inferred from the conductor's
  narration; archive Batch 2).
- **Report–disk mismatch.** Reported diff vs actual diff differ (a formatter rewrote 3,300
  lines nobody mentioned); reported commit sha not on the remote; empty branch merged.
- **Agreement without evidence.** A reviewer that confirms the implementer's framing word
  for word; a verifier that reverses under one pushback.
- **Round numbers and tidy audits.** "15 tables" (17); 80/80 states clean on first pass —
  check the audit before you believe the subject.
- **Questions instead of facts.** A context worker returning "open questions" that the
  brief already answered: it did not read the brief; treat the rest of its report the same.

## 3. Oracle catalogue — the one command that would refute the claim

| Claim class | Refuting oracle |
|---|---|
| "No occurrences of X" | `grep -rn` for X **and two synonyms**; `ls` the directory it says is absent |
| "Tests pass" | Re-run the exact command; read the test names against the risky path (the destructive flag, the false branch); check exit code, not grep output (`test \| grep; git commit` committed on a failing suite) |
| "Build green" | Build yourself on the integrated tree; check the artifact exists and is newer than the source |
| "Done" (multi-item) | Per-item table against the original list; `git diff --stat` non-empty for each owned path |
| "Committed / pushed" | `git log origin/<branch> -1`; sha present on the remote; diff not empty |
| "Fixed" (UI) | Render it: headless browser or the real one; tests and review both passed a defect "nobody ever looked at rendered" |
| "Fixed" (runtime) | Reproduce the failing input once before and once after; a fix proven by a test that fails without it |
| Data repair | Invariant that must close: counts before/after, sums that must match |
| Gate / predicate | Execute against the false case; never reason about truthiness |
| Live-system check | Prove it fails when it should: a busy-check that read errored output as "idle" would have killed a running job |
| Reviewer finding | Reproduce with the real tool; mark confirmed / overstated / false — a "missing security headers HIGH" was a file one directory up |
| Audit of N rows | Sample 5–8 "met" rows against the code; count rows vs the stated scorecard (caught a `\|` column shift); run the citation checker |
| "Agent is stuck / alive" | Notification `status` + body; file mtimes; logs. Never a UI label or the TODO list |
| Any number | Re-measure or label *recalled* |

**Validate the probe first.** An oracle that cannot observe the property is evidence of
nothing: a missing `timeout` on macOS misread as empty output; a lint capped at 200 files
showing zero warnings with 27 hidden; CI red on a branch built to fail; a mock of the unit
under test "proving" the vendor integration. Ask: does this check cap, truncate, mock, or
run against the wrong target?

**Blocked oracle = unverified.** When the browser, device, remote or credential is
unavailable, the result is "unverified — next oracle: …", not "definitive". A session that
shipped reasoned fixes as definitive while its browser tool was blocked got "tested
locally, not fixed" back; the real cause (an element at opacity 0 under reduced-motion)
only appeared in a headless run.

## 4. Recording provenance

Every claim that enters a plan, contract, doc or decision carries a tag:

- `verified` — you saw the evidence (ran it, opened it, counted it).
- `reported (by <agent>, <date>)` — a worker said so; oracle not yet run.
- `inferred` — plausible from other verified facts; no direct evidence.
- `measured` / `published` / `recalled` — for numbers.

A wrong `verified` compounds: every later decision cites it and nobody reopens the file.
When in doubt, downgrade the tag; it costs a word.

## 5. What to do with a failed gate

- Oracle refutes the report → the report is unread, not "mostly right". Redispatch with the
  oracle's hit appended, or do it yourself.
- Partial done → split the package; re-dispatch the missing items with the per-item table
  required.
- Out-of-envelope claim → reject the whole report; fix the envelope (grant access or
  change the task) before re-dispatch.
- Reviewer and implementer disagree → reproduce it yourself; the one with the runnable
  evidence wins; record the other as overstated or false with the reason.
- Two scouts disagree → that is the finding; dispatch a third with both results appended,
  or resolve it yourself with the oracle.
