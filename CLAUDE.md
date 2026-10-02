# CLAUDE.md - Resume Builder

@AGENTS.md

## Claude-only

- **Single source of truth:** all project rules live in `AGENTS.md`, imported above. Do not add or duplicate rules here. Edit `AGENTS.md`, and keep this file to the import plus Claude-specific notes.
- **Commands and skills:** run the workflows with the slash commands `/apply-for-job`, `/gap-analysis`, `/update-profile`, `/project-summary`. The `onboarding` skill auto-triggers on a fresh clone or on "help me set this project up". The `red-team-review` skill is invoked by `/apply-for-job` at the end of generation. Their canonical definitions are in `.claude/commands/` and `.claude/skills/`.
- **Output folder:** Claude Code writes generated applications to `output/Claude Code/{slug}/`. Codex writes to `output/Codex/{slug}/`.
- **Context management and delegation:** the global `~/.claude/CLAUDE.md` rules (context-mode tools for large reads, subagent delegation for isolated tasks) apply in this repo.
- **Guard check after editing instruction files:** `grep -q '^@AGENTS.md' CLAUDE.md && [ "$(wc -l < CLAUDE.md)" -le 40 ] && echo OK`
