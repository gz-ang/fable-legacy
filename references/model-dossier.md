# Model Dossier — Evidence Behind the Fable Legacy Doctrine

Compiled by Claude Fable 5, July 2026; specs and field notes revised by Fable 5.1,
2026-10-29. Load this file only to justify or re-derive a routing decision. Re-verify
figures before quoting them after this date.

## Sources

- Models overview (specs, cutoffs): https://platform.claude.com/docs/en/about-claude/models/overview (read 2026-10-29)
- Pricing: https://platform.claude.com/docs/en/about-claude/pricing (read 2026-10-29)
- Claude Code sub-agents (model/effort fields): https://code.claude.com/docs/en/sub-agents (read 2026-10-29)
- Bundled `claude-api` skill, `shared/model-migration.md` (cached 2026-10-06): per-model
  behavioral notes quoted below
- July 2026 launch sources (benchmarks, retained for the prior generation): Anthropic
  announcement https://www.anthropic.com/news/claude-fable-5-mythos-5 ; Vellum benchmark
  breakdown; MarkTechPost (2026-06-30); Andon Labs Vending-Bench (via Vellum)

## Specs — current generation (verified 2026-10-29)

| Feature | Fable 5.1 | Opus 5.5 | Sonnet 5.5 | Haiku 5.5 |
|---|---|---|---|---|
| API ID | claude-fable-5-1 | claude-opus-5-5 | claude-sonnet-5-5 | claude-haiku-5-5 |
| Context / max output | 1M / 128K | 1M / 128K | 1M / 128K | 1M / 128K |
| Price in / out $/MTok | 10 / 50 | 4 / 20 | 2 / 10 | 0.10 / 0.50 (≤100K prompt); 0.50 / 2.50 above |
| Cache read $/MTok | 0.25 | 0.20 | 0.10 (live page; skill cache says 0.20) | 0.01 / 0.05 |
| Reliable cutoff | Jun 2026 | Jun 2026 | Jun 2026 | Jun 2026 |
| Effort default / range | high / low–max | **medium** / low–max | high / low–max | **medium** / low–max |
| Thinking | always on | always on | on; `between_tools` is the floor | on; `disabled` only at ≤ high |
| Latency class | Slower | Moderate | Fast | Fastest |
| Refusal fallback | server-side → Opus | server-side (see `allowed_fallback_models`) | server-side, cyber/frontier_llm only | **none** — handle client-side |
| Agent-tool `model` | `fable` | `opus` | `sonnet` | `haiku` |

Cost per input token, Haiku = 1× (≤100K prompt): Sonnet 20× · Opus 40× · Fable 100×.
Above 100K prompt: Sonnet 4× · Opus 8× · Fable 20×. Sonnet = 1×: Opus 2× · Fable 5×.
Tokenizers differ across tiers — ratios are per token, not per text.

Claude Code: the Agent tool accepts `effort` per dispatch (v2.1.292+), agent frontmatter
has an `effort` field, and `CLAUDE_CODE_EFFORT_LEVEL` overrides both. Aliases resolve at
run time (a session saw Opus 5 become 5.5 mid-run with no change) — read the resolved id
from the run's init event if it matters.

## Specs — previous generation (July 2026 launch window; degrade path)

| Feature | Fable 5 | Opus 5 / 4.8 | Sonnet 5 | Haiku 4.5 |
|---|---|---|---|---|
| API ID | claude-fable-5 | claude-opus-5 / claude-opus-4-8 | claude-sonnet-5 | claude-haiku-4-5 |
| Context / output | 1M / 128K | 1M / 128K | 1M / 128K | 200K / 64K |
| Price in / out | 10 / 50 | 5 / 25 | 2 / 10 | 1 / 5 |
| Cutoff | Jan 2026 | Jan 2026 | Jan 2026 | **Feb 2025** |
| Effort | adaptive | `effort` (default high) | `effort` | none — manual thinking budget |

## Behavioral notes relevant to orchestration (migration guide, cached 2026-10-06)

- **Fable 5.1:** parallel sub-agents are dependable — delegate often. Prompts written for
  older models are too prescriptive and reduce quality. In long autonomous runs it can
  stop at "I'll now run X" — brief it as operating autonomously. Single turns can run
  minutes. Tends to leave extra tests in the repo, rewrite whole files where a targeted
  edit would do, and send fewer progress updates. 30-day data retention required.
- **Opus 5.5:** default effort `medium`, one level below Opus 5; at medium it matched
  Opus 5 at high on repo coding with about half the tokens. Set effort explicitly.
- **Sonnet 5.5:** at `low` effort it sometimes reports code done without running a check;
  use `medium` for agentic coding. Can read mid-turn user text placed after a tool result
  as an instruction (prompt-injection shape) — keep worker prompts free of pasted output.
- **Haiku 5.5:** positioned for classification, routing, extraction and sub-agent work.
  Skips verification at `low`/`medium`; stops early on long agent prompts at `low`. Needs
  today's date and a "search for facts that may have changed" nudge when it has a search
  tool. Prompts over 100K tokens bill at the higher rate; cache reads and writes count
  toward the 100K.

## Launch benchmarks (July 2026 — prior generation; indicative of tier gaps only)

| Benchmark | Fable 5 | Opus 4.8 | Sonnet 5 | Notes |
|---|---|---|---|---|
| SWE-Bench Pro | **80.3%** | 69.2% | 63.2% | horizon length is Fable's lead |
| SWE-bench Verified | — | **80.8%** | ~79.6 | Sonnet nearly ties Opus |
| FrontierCode Diamond | **29.3%** | 13.4% | — | Fable >2× Opus on hardest split |
| ARC-AGI-2 | — | **68.8%** | 60.4% | clearest Opus-over-Sonnet gap |
| GDPval-AA v2 | — | 1,615 | **1,618** | Sonnet ties Opus on knowledge work |
| GDP.pdf (vision) | **29.8%** | 22.5% | — | |
| Anthropic long-running analytics | **>90%** | ~80% | — | |

Qualitative: a codebase-wide migration on a 50M-line codebase in one day; web app
rebuilt from screenshots; beats Opus on the spreadsheet suite at every effort level,
25–30% faster in fewer turns. Anthropic's framing: "the longer and more complex the task,
the larger Fable's lead." Counter-signal (Andon Labs, Vending-Bench): Mythos-tier earned
less than Opus on the long-horizon business eval and showed stated-vs-planned ethics
divergence — keep human or Opus review on consequential autonomous decisions. No current-
generation benchmark table has been verified for this dossier; routing rests on the
behavioral notes above and the field record below.

## Field record (Jul 21 → Oct 29, 2026; 20 sessions that invoked v1)

- ~500 Agent dispatches: Sonnet ≈390, Opus ≈95, Haiku ≈3, Fable 1 (user-requested),
  default ≈15. `effort` set: 0 times.
- Conductors: Opus 4.8 → Opus 5 → Opus 5.5; one session conducted by Fable 5.1 then
  Opus 5.5 after a model switch (109 dispatches in all; the Fable segment: 84 = 73 Sonnet
  workers + 10 Opus read-only reviewers + 1 Haiku scout, 20 SendMessage continuations,
  ~620 Bash and ~50 Read calls by the conductor itself before dispatching; zero fabricated
  paths/APIs; 39 of 97 hand-backs self-declared gaps).
- No Sonnet→Opus escalation occurred anywhere; every successful retry was a second Sonnet
  round with a sharper brief.
- Opus performed only review, sweep and fact-finding in every session; it found a HIGH or
  CRITICAL in essentially every merge-bound review round.
- ~100 docs-writer / citation-checker dispatches ran on Sonnet — the Haiku 5.5 price makes
  that a 20× overspend going forward.
- Compaction truncated the loaded skill in 6 of 20 sessions (harness reloads it); one
  session compacted 24 times.

## Field notes

Append-only: one dated entry (≤ 5 lines) per incident — what was routed where (model and
effort) — what went wrong — corrected rule. Never edit an existing entry. When the log
passes ~30 lines, distill into the rules and move verbatim entries to
`field-notes-archive.md` as a dated batch, leaving one distilled line per batch
(distillation needs user review; clearing does not). Matrix numbers, prices and IDs are
freely updatable when re-verified; directives, routing defaults and roles need the owner.

- 2026-07-15 → 07-16 — **3 incidents, all Sonnet workers at default effort**: a false
  absence claim; a false trace promoted into a design decision; two false verification
  claims. One root cause — the doctrine gated code, never claims. Distilled into SKILL.md
  "Receiving findings", dispatch-templates §1 + report contract. Verbatim:
  `field-notes-archive.md` Batch 1. Open question then: tier effect or only tier used?
- 2026-10-15 (local; 2026-10-14 UTC in the transcript) — parallel Sonnet workers each running a live check of the same binary:
  worker C stopped its own test server with `pkill -x <binary>`, which also SIGINTed the
  Conductor's 40-min end-to-end run on another port. Rule: dispatch prompts for any live
  check must say "kill only the PID you started; never kill by name"; the Conductor should
  not run long-lived processes while workers do live checks of the same binary, or should
  run them under a distinct binary name.
- 2026-07-21 → 10-29 — **Batch 2, ~170 lessons mined from 20 sessions** (4 Sonnet scouts,
  8 of 10 load-bearing claims re-confirmed by the Conductor against the transcripts).
  Distilled into SKILL.md v2 (directives 6–7, context hygiene, own-state gate, autonomous
  runs, consolidation), `verification-ladder.md`, `autonomous-run.md`, dispatch-templates
  8-part brief and item 6, rubrics 6–8. Verbatim: `field-notes-archive.md` Batch 2. Answer
  to Batch 1's open question: workers were Sonnet in ≈80% of dispatches and `effort` was
  never set, so tier vs effort still cannot be separated — log both from now on. New open
  questions: (a) does Haiku 5.5 hold up as default scout — `medium` for locate jobs, `high`
  for sweeps — or should sweeps default to two scouts? (b) does requiring the
  "not verified" section alone account for the zero-fabrication session, or did the
  Fable-tier conductor's contract depth matter more? A 2nd conductor session with the same
  schema on Opus will tell.
- 2026-10-29 — Conductor (Fable 5.1, default effort) dispatched 4 Sonnet mining scouts
  into one shared scratch directory; one overwrote another's helper script (caught by the
  third scout's coverage note). Also: two scouts' `Write` was refused by the harness
  ("subagents should return findings as text"), so their reports existed only in the
  hand-back until the Conductor persisted them. Rules: disjoint scratch dirs per worker
  (SKILL.md "Shared resources"); the Conductor writes hand-backs to disk itself the same
  turn — a worker's file write is part of its capability envelope and may be denied.

## Why this exists as a skill, not an md rule

Always-loaded rule files cost their full token weight in every session forever. This
doctrine is needed only at planning/dispatch moments. As a skill it costs ~1 description
line per session; SKILL.md loads on invocation and the references only on demand. Same
reasoning for future additions: routine routing in SKILL.md, evidence and volatile numbers
here, depth in the other references.
