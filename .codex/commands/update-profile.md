# Update Profile - Codex Workflow

Use this when the user asks Codex to update their profile, sync project references, ingest `proj_refs/`, or run `/update-profile`.

The canonical workflow is `.claude/commands/update-profile.md`. Follow it exactly, adapting only tool names and interaction style for Codex.

## Goal

Detect new Markdown files in `proj_refs/`, synthesize them into the `## Notable Projects` section of `input/profile.md`, and update the `<!-- proj_refs_ingested: ... -->` stamp so already-ingested files are skipped later.

## Procedure

1. Read `.claude/commands/update-profile.md`.
2. Read `input/profile.md` and parse the `<!-- proj_refs_ingested: ... -->` block. If it is missing, treat the ingested list as empty.
3. List `proj_refs/*.md` and compare basenames against the ingested list.
4. If there are no new files, tell the user that `input/profile.md` is already up to date and stop.
5. For each new file, read it in full and synthesize a project entry under `## Notable Projects` in `input/profile.md`.
6. Update the ingestion stamp with all newly ingested filenames, preserving existing entries.
7. Confirm the files ingested, project names added, total ingested count, and that `/apply-for-job` can now use the updated profile.

## Hard Rules

- Never modify files in `proj_refs/`; they are read-only inputs.
- Never modify `input/resume.tex`, `lang_rules/`, or `input.example/`.
- Only update `input/profile.md`.
- Do not rewrite existing profile sections unnecessarily; append new project entries and update the stamp.
- Escape LaTeX special characters in project content that may appear in generated CVs.
