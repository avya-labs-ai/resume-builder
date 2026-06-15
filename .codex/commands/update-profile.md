# Update Profile - Codex Workflow

Use this when the user asks Codex to update their profile, sync project references, ingest `proj_refs/`, or run `/update-profile`.

The canonical workflow is `.claude/commands/update-profile.md`. Follow it exactly, adapting only tool names and interaction style for Codex.

## Goal

Detect new Markdown files in `proj_refs/`, synthesize them into the `## Notable Projects` section of `input/profile.md`, and update the `<!-- proj_refs_ingested: ... -->` stamp so already-ingested files are skipped later.

## Procedure

1. Read `.claude/commands/update-profile.md`.
2. Read `input/profile.md` and parse the `<!-- proj_refs_ingested: ... -->` block. If it is missing, treat the ingested list as empty. Also read `input/feedback.md` if it exists and parse active `[global]`-tagged rules; if the file is absent, treat the rule set as empty. Surface any internal conflicts among the active rules using the Step 1.5 block format from `.claude/commands/apply-for-job.md` and wait for user resolution before continuing.
3. List `proj_refs/*.md` and compare basenames against the ingested list.
4. If there are no new files, tell the user that `input/profile.md` is already up to date and stop.
5. For each new file, read it in full and synthesize a project entry under `## Notable Projects` in `input/profile.md`. Apply the recruiter-lens extraction defined in the canonical command (Step 3): extract impact with quantification, engineering decisions that signal seniority, methodology/discipline signals, scope/ownership, and stack — not just a neutral summary of what the project does. Every bullet must be a recruiter-pause-worthy point; cut anything generic. **Honor `[global]`-tagged rules** read from `input/feedback.md` in Step 2 when phrasing each entry — vocabulary bans, framing patterns, identity positioning, section naming conventions. Feedback rules override recruiter-lens defaults if they conflict.
6. Update the ingestion stamp with all newly ingested filenames, preserving existing entries.
7. Confirm the files ingested, project names added, total ingested count, and that `/apply-for-job` can now use the updated profile.
8. **Watch follow-up turns for improvable feedback.** After ingestion, the user may correct phrasing, framing, or identity positioning in the newly added entries. Apply the candidate-learning watch logic defined in `.claude/commands/apply-for-job.md` Step 6.5 (canonical spec). Apply the requested change to the relevant `## Notable Projects` entry first, then prompt with the candidate-learning block. Scope defaults to `[global]` since the profile is language-neutral. On `y`, append a new `R###` block to `input/feedback.md` (create the file from the template in Step 6.5 if it does not exist). On `n`, discard. On `edit`, let the user rephrase.

## Hard Rules

- Never modify files in `proj_refs/`; they are read-only inputs.
- Never modify `input/resume.tex`, `lang_rules/`, or `input.example/`.
- Only update `input/profile.md`.
- Do not rewrite existing profile sections unnecessarily; append new project entries and update the stamp.
- Escape LaTeX special characters in project content that may appear in generated CVs.
