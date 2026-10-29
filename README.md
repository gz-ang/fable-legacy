# fable-legacy

A Claude Code skill: **model orchestration doctrine**. It teaches whichever model is
driving how to route work across the Claude family, dispatch parallel subagents, and —
critically — how to judge what those subagents report back.

Written in-character by Claude Fable 5 in a single session (July 2026), so that
successor orchestrators inherit how Fable would divide the work rather than
rediscovering it every session. Revised (v2) by Claude Fable 5.1 in October 2026 from
20 sessions and ~500 subagent dispatches that ran under v1.

## What it covers

- **Prime directives** — capability flows down not up; context is a budget; parallelize
  only independent work; verify with a different pair of eyes.
- **Routing** — a model matrix (Fable 5.1 / Opus 5.5 / Sonnet 5.5 / Haiku 5.5) with the
  task shapes that justify escalating, descending, or changing `effort` instead of tier.
- **Orchestration playbook** — the four roles (Conductor, Architect, Workers, Scouts),
  what must exist before dispatch (contract, ownership map, capability envelope,
  shared-resource rules), the agent lifecycle, the review chain, and crash-safety.
- **Three gates** — on *code* a worker wrote (review chain), on *claims* a worker made
  (verification ladder: absence claims, oracles, coverage, hallucination tells), and on
  the conductor's *own state* (compaction summaries, remembered numbers, chat-only
  decisions, self-approved plans).
- **Context hygiene** — what goes into a worker prompt, what comes back, and the poison
  sources ranked by how often they bit.
- **Autonomous runs** — the phase loop, the stop set, handoff and report templates,
  consolidation of many agents' reports.
- **Token economics** — what spawning actually costs and when inline beats delegation.

## Install

Clone into your skills directory:

```bash
git clone git@github.com:gz-ang/fable-legacy.git ~/.claude/skills/fable-legacy
```

Claude Code discovers it automatically. The skill loads on demand — at planning and
dispatch moments — so it costs roughly one description line per session until invoked.

## Structure

| File | Loads | Purpose |
|---|---|---|
| `SKILL.md` | on invocation | The doctrine: directives, routing rules, playbook |
| `references/dispatch-templates.md` | before writing a worker prompt | The 8-part brief and delegation prompts for six task shapes, with a worked example |
| `references/verification-ladder.md` | when a report arrives | Trust tiers by cost of a miss, hallucination tells, an oracle catalogue, provenance tags |
| `references/autonomous-run.md` | on "iterate until done" requests | Phase loop, stop set, handoff and phase-report templates, consolidation checklist |
| `references/judgment-rubrics.md` | when a weak model faces a hard call alone | Eight judgment calls made executable, each with a do/don't |
| `references/model-dossier.md` | to justify or re-derive a routing decision | Current and prior-generation specs, behavioral notes, benchmarks, field record, field notes |
| `references/field-notes-archive.md` | never automatically | Verbatim incident records behind the rules — read when evolving the doctrine, not when dispatching |

Reference files are deliberately *not* loaded by default — see "Why this exists as a
skill, not an md rule" in the dossier.

## Maintenance

The doctrine carries its own protocol (`SKILL.md` § Maintenance protocol):

- **Freely updatable** — matrix numbers, prices, model IDs, benchmarks, once re-verified
  against live docs.
- **Needs the owner's approval** — prime directives, routing defaults, role definitions,
  or deleting any section. These encode intent, not facts.
- **Field notes are append-only** — when a rule misfires in practice, append a dated
  entry (model and effort included) to the dossier. Never edit history.
- **Compaction preserves the evidence** — past ~30 lines, notes are distilled into the
  rules and the verbatim entries move to `references/field-notes-archive.md`, leaving
  one line per batch. Records are never deleted: they are how a later session re-derives
  why a rule exists before changing it.

## Provenance

Specs and prices were re-verified against the live model and pricing pages on
2026-10-29; benchmark figures are July 2026 launch numbers for the prior generation. Both
are cited in `references/model-dossier.md`. **Re-verify them before quoting.** The
doctrine is built to outlive its numbers: keep the directives, replace the figures.

Field lessons come from the author's own Claude Code sessions; project, client and
people names are stripped and incidents are generalized.

## License

MIT — see [LICENSE](LICENSE).
