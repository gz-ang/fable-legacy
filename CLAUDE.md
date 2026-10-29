# fable-legacy — contributor notes

These notes load only when Claude Code runs from this repository. They are not part of
the skill: the skill is `SKILL.md` plus `references/`.

## Skill editing

- Work on this repo first, then sync to the installed copies. There are two, and neither
  is a symlink to this repo:
  - `~/dotfiles/claude/skills/fable-legacy/` — the shared store; `~/.claude-me` and
    `~/.claude-work` symlink their `skills/fable-legacy` here.
  - `~/.claude/skills/fable-legacy/` — a real directory used by the bare profile and
    Claude Desktop.
- Sync = copy `SKILL.md` and `references/` into both, then `diff -rq` all three. Field
  notes appended in an installed copy during a session must be merged back here before
  the next edit (the store ran ahead of the repo once).
- `*.bak` files are git-ignored; the maintenance protocol asks for one before any
  wholesale rewrite.
- Keep the first ~40 lines of `SKILL.md` self-sufficient: compaction truncates loaded
  skills and the quick card is what survives.
