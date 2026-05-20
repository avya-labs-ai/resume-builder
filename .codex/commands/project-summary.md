# Project Summary - Codex Workflow

Use this when the user asks Codex to summarize a project for the Resume Builder, create a CV project reference, or runs `/project-summary`.

The canonical workflow is `.claude/commands/project-summary.md`. Follow it exactly, adapting only tool names and interaction style for Codex.

## Goal

Read a separate project repo, understand it, and write a concise plain-English summary to:

`summaries/{slug}_summary_DD_MM_YYYY.md`

The resulting file can be copied into this Resume Builder repo's `proj_refs/` folder.

## Procedure

1. Read `CLAUDE.md` or `AGENTS.md` in the target project when present.
2. Scan for project context in root Markdown files and folders such as `context/`, `docs/`, `decisions/`, `references/`, and `context_handoff/`.
3. Derive a short lowercase hyphenated project slug.
4. Write the summary to `summaries/{slug}_summary_DD_MM_YYYY.md`.
5. Report only the file path and a short completion note.

## Summary Shape

Use these sections when enough information exists:

- Who is behind this
- The project
- How it is set up
- Decisions made
- Current state
- Priorities and next steps
- Goals and deadlines

Keep it plain English, useful to a third party, and free of filler.

