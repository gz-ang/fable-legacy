# Dispatch Templates — Worker Prompts for the Six Task Shapes

Fill-in delegation prompts for the Conductor. Every template enforces the same contract:
goal + why, owned scope, capability envelope, acceptance, report schema. Workers return
conclusions and `file:line` pointers — long artifacts go to files, never into the report.
State `model` **and** `effort` explicitly at dispatch (Opus and Haiku default to `medium`).
Never run a worker at `low`: Sonnet skips checks at `low`, Haiku at `low` and `medium` —
so Haiku gets `medium` only for locate jobs the conductor gates anyway, and `high` when
its output is itself the verification (sweeps, audits, citation checks).

**Light brief** for Tier-0 locate jobs (one file, one symbol, one config key): goal ·
envelope · "report file:line + the commands you ran" · nothing else. The full 8-part brief
below is for anything whose answer will be acted on.

## The 8-part brief (every shape)

1. **Role + goal + why** — the motivation lets the worker resolve ambiguity the right way.
2. **READ FIRST** — contract section numbers, the reference implementation to copy, the
   plan slice. Pointers, not pasted transcripts.
3. **You own ONLY these paths** — and "do NOT touch" for the hub files. For a change a
   forbidden file needs, return the exact patch; the Conductor applies it.
4. **Numbered deliverables** with exact signatures / shapes.
5. **Constraints you cannot infer** — toolchain path, conventions, permissions already
   granted, runtime caps, decisions already made, items out of scope. "Return facts, not
   questions."
6. **Capability envelope** — what you can reach: tools, directories inside scope, network,
   credentials. First action for any resource outside the working dir: confirm access; if
   denied, stop and report — never infer contents. Shared-resource rules: own scratch dir,
   own port/HOME, kill only PIDs you started (never by name), no dependency installs, do not
   rebuild a binary something else is running.
7. **Numbered acceptance commands** — exact, runnable. "If blocked twice on the same point,
   stop and report the failure trail — do not improvise around the contract."
8. **Report schema** (below).

Common report schema (all templates end with this):

> Report back with: (1) outcome in ≤5 sentences, (2) file paths created/modified with line
> refs for key changes, (3) evidence per acceptance item: the exact command and its observed
> output — name which paths that evidence exercises, including the risky one (the
> destructive flag, the false branch); a pass count is not coverage, (4) **Not verified:**
> everything you did not run, open, or exercise, and why, (5) anything you could not do.
> For multi-item packages: a table item · evidence · done/partial/not. Do not include full
> file contents. Never report a check that was still running when you wrote this: a result
> you have not seen is "not verified", not "passing".

## 1. Search / scout   (model: haiku, effort: medium — keep < 100K prompt tokens)

> Goal: locate [what] so that [who] can [do what] without scanning the repo.
> Scope: search only — do not modify files. Envelope: [dirs you may read].
> Look in: [dirs/patterns if known, else "entire repo"]. Also search synonyms: [list].
> Acceptance: every hit as file:line + one-line role; explicit "none found" per item.
> Coverage (mandatory): commands and globs run, verbatim, with hit counts; paths covered;
> what you could NOT cover and why. A "none found" without the commands that earned it
> will be rejected. Never infer absence from a listing or from files you did not open.
> + report schema.

For "find ALL X" where a miss is expensive: two scouts, different strategies (grep vs
directory walk), one message. Agreement is evidence; divergence is a finding.

### Worked example — a sweep report the Conductor can judge

Dispatch: *"Find every surface that aggregates `<table>.<stale_column>` across both
repos, so phase 3 migrates all of them."*

❌ Rejected — a verdict with no coverage:

> No contradictions detected — all surfaces already migrated. The app has no
> `<jobs dir>` or `<console commands dir>`.

Both directories existed; so did five unmigrated surfaces. One Conductor oracle
(`grep -rn "SUM(<table>.<stale_column>)" <app dir>/`) refutes it in seconds.

✅ Accepted shape — same conclusion would at least be checkable:

> Ran: `grep -rn "<table>\.<stale_column>" <app dir>/ <frontend dir>/` → 41 hits;
> `grep -rln "SUM(<table>" <app dir>/` → 5 files; `ls <jobs dir> <console dir>` → exist.
> Covered: repo A `<app dir>/`, `<frontend dir>/`. NOT covered: repo B (no access);
> raw SQL in `<migrations dir>` (out of scope — confirm?).
> Hits: `<jobs dir>/<Job>.php:88` — SUMs the stale column, unmigrated. […4 more]
> Not checked: `<views dir>/`; template-embedded queries may be missed.

## 2. Implement   (model: sonnet, effort: medium; high if merge-bound)

> Goal: [feature/change] because [motivation].
> READ FIRST: [contract §n, reference file].
> You own ONLY: [paths]. Do NOT touch: [hub files] — return patches for those.
> Interface you must honor: [contract/schema]. Decisions already made: [list].
> Constraints you cannot infer: [conventions, toolchain, permissions granted].
> Envelope: [tools, dirs, network]. Shared-resource rules apply (8-part brief, item 6).
> Acceptance: 1. [command → expected] 2. […]. Demonstrated, not claimed.
> If blocked twice on the same point, stop and report the failure trail.
> + report schema.

## 3. Refactor   (model: sonnet, effort: medium; opus if it changes architecture)

> Goal: restructure [what] toward [shape] because [why]. Behavior must not change.
> You own: [files]. Do not touch: [load-bearing files/APIs].
> Acceptance: full existing suite green before AND after (paste both commands + output);
> no public interface changes unless listed: [list or "none"]; `git diff --stat` free of
> unrelated edits (a formatter rewriting 3,300 lines is unrelated).
> + report schema.

## 4. Research / fact-find   (model: sonnet, effort: medium; haiku for pure doc-fetch with sources supplied)

> Question: [precise question]. Decision it feeds: [what will be done with the answer].
> Sources to prefer: [docs/repos/registries]. Today's date: [date].
> Acceptance: answer with cited sources; each claim tagged verified (saw evidence) /
> reported (source asserts it) / inferred; explicit "unknown" where sources conflict or
> are silent — never fill gaps with plausible guesses. Numbers tagged measured / published.
> + report schema.

## 5. Review   (model: opus, effort: high for architecture/security/deploy; sonnet medium for routine. Must differ from the implementer. Merge-bound / multi-file / security-touching work only — one-line fixes get the Conductor's own read)

> Review [commit range / files] implemented against this contract: [acceptance criteria].
> You have fresh context by design — do not ask the implementer; if the code needs
> explaining, that is a finding. Read-only: do not modify files.
> Run the build, tests and these probes yourself: [commands]. State what you verified
> by running vs by reading.
> Report findings ranked by severity, each as file:line + scenario (input/state → wrong
> outcome) + concrete fix. An empty category must say what was checked to earn it.
> + report schema.

## 6. Verify / QA attack   (model: sonnet, effort: high; opus when the surface is security or money)

> Target: [feature] against these acceptance rows: [list]. Implementers' declared
> deviations and known gaps: [list] — attack those first.
> Attack, do not confirm: crafted inputs, the false branch, the destructive flag,
> concurrency, the rendered UI (headless browser), the real counterpart (not a mock).
> Each finding: reproduction command, observed vs expected, severity. Then re-check your
> own findings once before reporting (false positives cost a fix wave).
> + report schema.

## Anti-patterns (all shapes)

- Dispatching without acceptance criteria — you get confidence, not evidence.
- Dispatching without an envelope check — a worker asked to read images it had no
  permission to open either stalls or describes images it never saw.
- Two workers owning the same file, port, binary, dependency dir or scratch folder.
- "Also take a quick look at…" outside scope — scope creep at dispatch is a merge conflict
  at integration.
- A conditional "stop if X" without explicit branches — one worker read it as the whole
  job and did step 1 only.
- Re-dispatching a failed package unchanged — append the failure trail, sharpen, halve.
- Accepting "none found", "tests pass", or "done" on a worker's word (SKILL.md "Receiving
  findings"; `verification-ladder.md`).
- Promoting a worker's claim into a decision doc without opening the file yourself.
- Leaving `effort` unset — in ~500 field dispatches it was never set once.
