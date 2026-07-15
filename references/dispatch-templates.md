# Dispatch Templates — Worker Prompts for the Five Task Shapes

Fill-in delegation prompts for the Conductor. Every template enforces the same
contract: goal + why, owned scope, acceptance criteria, report format. Workers return
conclusions and `file:line` pointers — long artifacts go to files, never into the
report. Pick the model per SKILL.md routing rules; state it explicitly at dispatch.

Common report contract (all templates end with this):

> Report back with: (1) outcome in ≤5 sentences, (2) file paths created/modified with
> line refs for key changes, (3) evidence of acceptance criteria (test output, build
> result, or read-back), (4) anything you could not do and why. Do not include full
> file contents.

## 1. Search / scout   (model: haiku — provide docs if task touches post-2025 APIs)

> Goal: locate [what] so that [who] can [do what] without scanning the repo.
> Scope: search only — do not modify files.
> Look in: [dirs/patterns if known, else "entire repo"].
> Acceptance: every hit listed as file:line + one-line description of its role;
> explicit "none found" for each thing not found (absence is a finding).
> Coverage (mandatory, report even if you found everything): the commands and globs
> you ran, verbatim, with hit counts; the paths they actually covered; what you could
> NOT cover and why (no access, too large, ambiguous, ran out of ideas). A "none
> found" without the commands that earned it will be rejected and redispatched.
> Never infer absence from a directory listing or from files you did not open: if you
> did not grep for it, the answer is "not checked", not "not present".
> + report contract.

### Worked example — a sweep report the Conductor can judge

Dispatch: *"Find every surface that aggregates fee money on the stale basis
(`app_fee.paid_amount`) across both repos, so phase 3 migrates all of them."*

❌ Rejected — a verdict with no coverage:

> No contradictions detected — all surfaces already migrated. App has no `/app/Jobs`
> or `/app/Console/Commands` files.

Nothing here is judgeable: no commands, no paths, no gaps — and the absence claim is
stated as fact rather than as a check that was run. Both directories existed; so did
five unmigrated surfaces. One Conductor oracle (`grep -rn "SUM(app_fee.paid_amount)"
app/`) refutes the whole report in seconds.

✅ Accepted shape — same conclusion would at least be checkable:

> Ran: `grep -rn "app_fee\.paid_amount" app/ resources/js/` → 41 hits;
> `grep -rln "SUM(app_fee" app/` → 5 files; `ls app/Jobs app/Console/Commands` → exists.
> Covered: api-repo `app/`, `resources/js/`.
> NOT covered: admin-repo (no access this session); raw SQL inside `database/migrations`
> (judged out of scope — confirm?).
> Hits: `app/Jobs/SettlementJob.php:88` — SUMs stale basis, unmigrated. […4 more]
> Not checked: `resources/views/` not enumerated separately; my `.php` grep may have
> missed Blade-embedded queries.

## 2. Implement   (model: sonnet)

> Goal: [feature/change] because [motivation — lets worker resolve ambiguity in the
> right direction].
> You own ONLY these files: [list]. The interface you must honor: [contract/schema].
> Constraints you cannot infer: [conventions, framework quirks, things decided in
> planning].
> Acceptance: [specific tests pass / endpoint returns shape X / behavior Y observable].
> If blocked twice on the same point, stop and report the failure trail — do not
> improvise around the contract.
> + report contract.

## 3. Refactor   (model: sonnet; opus if it changes architecture)

> Goal: restructure [what] toward [shape] because [why]. Behavior must not change.
> You own: [files]. Do not touch: [files/APIs that are load-bearing].
> Acceptance: full existing test suite green before AND after; no public interface
> changes unless listed here: [list or "none"]; diff free of unrelated edits.
> + report contract.

## 4. Research   (model: sonnet; haiku only for pure doc-fetch with sources supplied)

> Question: [precise question]. Decision it feeds: [what will be done with the answer].
> Sources to prefer: [docs/repos/registries]. 
> Acceptance: answer with cited sources; each claim marked verified (saw evidence) or
> reported (source asserts it); explicit "unknown" where sources conflict or are
> silent — do not fill gaps with plausible guesses.
> + report contract.

## 5. Review   (model: opus for architecture/security, sonnet for routine; must differ
from the implementer. Merge-bound / multi-file / security-touching work only — per
SKILL.md's proportionality guard, one-line fixes and doc tweaks get automated checks
plus the Conductor's own read, no spawned reviewer)

> Review [files/diff] implemented against this contract: [acceptance criteria from the
> original work-package].
> You have fresh context by design — do not ask the implementer to explain; if the
> code needs explaining, that is a finding.
> Report findings ranked by severity, each as file:line + what breaks + concrete fix.
> State explicitly what you verified by running (tests/build) vs by reading.
> An empty findings list must say what was checked to earn it.
> + report contract.

## Anti-patterns (all shapes)

- Dispatching without acceptance criteria — you will get back confidence, not evidence.
- Two workers owning the same file — merge the packages or serialize them.
- Asking a worker to "also take a quick look at" something outside its scope — scope
  creep at dispatch time becomes merge conflicts at integration time.
- Re-dispatching a failed package unchanged — append the failure trail or escalate.
- Accepting "none found" on a worker's word — absence is the cheapest wrong answer a
  worker can produce. Run one check of your own first (SKILL.md "Receiving findings").
- Promoting a worker's claim into a decision doc without opening the file yourself —
  that is how a wrong claim stops being questionable and starts being cited.
