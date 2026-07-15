# fable-legacy

A Claude Code skill: **model orchestration doctrine**. It teaches whichever model is
driving how to route work across the Claude family, dispatch parallel subagents, and —
critically — how to judge what those subagents report back.

Written in-character by Claude Fable 5 in a single session (July 2026), so that
successor orchestrators inherit how Fable would divide the work rather than
rediscovering it every session.

## What it covers

- **Prime directives** — capability flows down not up; context is a budget; parallelize
  only independent work; verify with a different pair of eyes.
- **Routing** — a model matrix (Fable 5 / Opus 4.8 / Sonnet 5 / Haiku 4.5) with the
  task shapes that justify escalating, descending, or changing `effort` instead of tier.
- **Orchestration playbook** — the four roles (Conductor, Architect, Workers, Scouts),
  the dispatch protocol, the review chain, and crash-safety.
- **Receiving findings** — the gate on *claims* a worker reports, as opposed to *code*
  a worker wrote. Absence claims, cheap oracles, coverage disclosure, provenance.
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
| `references/dispatch-templates.md` | before writing a worker prompt | Delegation prompts for the five task shapes, with a worked example |
| `references/judgment-rubrics.md` | when a weak model faces a hard call alone | Five judgment calls made executable, each with a do/don't |
| `references/model-dossier.md` | to justify or re-derive a routing decision | Benchmarks, specs, caveats, sources, and dated field notes |

Reference files are deliberately *not* loaded by default — see "Why this exists as a
skill, not an md rule" in the dossier.

## Maintenance

The doctrine carries its own protocol (`SKILL.md` § Maintenance protocol):

- **Freely updatable** — matrix numbers, prices, model IDs, benchmarks, once re-verified
  against live docs.
- **Needs the owner's approval** — prime directives, routing defaults, role definitions,
  or deleting any section. These encode intent, not facts.
- **Field notes are append-only** — when a routing decision misfires in practice, append
  a dated line to the dossier. Never edit history.

## Provenance

Benchmark and pricing figures are launch-window numbers for July 2026 and are cited in
`references/model-dossier.md`. **Re-verify them before quoting.** The doctrine is built
to outlive its numbers: keep the directives, replace the figures.

## License

MIT — see [LICENSE](LICENSE).
