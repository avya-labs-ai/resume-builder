# AGENTS.md - Resume Builder

Project instructions for Codex sessions in this repo.

This repo is shared with Claude Code. Keep the existing Claude workflow intact:

- Claude Code reads `CLAUDE.md` and `.claude/`.
- Codex reads this `AGENTS.md`.
- The actual product behavior should stay aligned in both tools, except generated application files are separated by tool:
  - Claude Code writes to `output/Claude Code/{company-role}/`.
  - Codex writes to `output/Codex/{company-role}/`.

## What This Project Is

A single-purpose agent workflow: take a job description, then produce a tailored CV and cover letter in every configured language as compilable `.tex` files under the tool-specific output folder.

There is no application server, API, package manager, or runtime to install. The agent is the engine.

## Canonical Workflow Files

Use these files as the source of truth:

- `CLAUDE.md` - full project contract and working rules.
- `.claude/commands/apply-for-job.md` - canonical generation workflow.
- `.claude/commands/project-summary.md` - canonical project summary workflow.
- `.claude/commands/update-profile.md` - canonical profile update workflow.
- `.claude/skills/onboarding/SKILL.md` - canonical onboarding workflow.
- `.codex/commands/apply-for-job.md` - Codex-facing wrapper for the apply workflow.
- `.codex/commands/project-summary.md` - Codex-facing wrapper for project summaries.
- `.codex/commands/update-profile.md` - Codex-facing wrapper for profile updates.
- `.codex/commands/onboarding.md` - Codex-facing wrapper for onboarding.

When the user asks to apply for a job in Codex, follow `.codex/commands/apply-for-job.md`. If the user mentions the Claude slash command name `/apply-for-job`, treat it as the same request.

When the user asks to set up, initialize, onboard, or configure the project in Codex, follow `.codex/commands/onboarding.md`.

When the user asks for a project summary in Codex, follow `.codex/commands/project-summary.md`.

When the user asks to update, refresh, sync, or ingest project references into their profile in Codex, follow `.codex/commands/update-profile.md`. If the user mentions `/update-profile`, treat it as the same request.

## Inputs

Personal source files are intentionally gitignored:

- `input/profile.md` - profile, identity, language config, and career source material.
- `input/resume.tex` - LaTeX CV template to preserve.
- `proj_refs/*.md` - project summary update feed for `input/profile.md`; never runtime input for CV generation.

Shared templates and rules are tracked:

- `input.example/`
- `proj_refs.example/`
- `output.example/`
- `lang_rules/`
- `resources/resume.cls`

## Operating Rules

- Do not modify `input/profile.md` or `input/resume.tex` during a CV generation run.
- Do not modify `input.example/` for a specific user.
- Do not modify existing files in `lang_rules/` for a specific user. You may create a missing `lang_rules/{code}.md` if a configured language needs it.
- During apply-for-job, use `input/profile.md` as the single source of truth and do not read `proj_refs/`.
- To incorporate new project summaries, run the update-profile workflow before apply-for-job.
- Write Codex-generated CVs and cover letters only under `output/Codex/{slug}/`.
- Copy `resources/resume.cls` into each Codex-generated output folder as `resume.cls`.
- Before generation, ask for a company URL or description and use it to write a company-specific "why us" paragraph in the cover letter. If skipped, use the generic paragraph plus the required LaTeX TODO comment from the apply workflow.
- Preserve the LaTeX document class, packages, and section structure from `input/resume.tex`.
- Professional Experience must be generated in strict reverse chronological order. Do not reorder roles by JD relevance; use bullet selection and compression for relevance.
- For AI-focused JDs, keep automotive roles in chronology but compress each automotive role to a maximum of 2 lines total unless the JD explicitly asks for automotive, autonomous driving, ADAS, safety-critical validation, or V&V. Use the saved space for current AI/automation experience and completed AI projects from `input/profile.md`.
- Escape LaTeX special characters in all generated text, especially `_`, `&`, `%`, `$`, `#`, `{`, and `}`.
- Never invent experience, metrics, tools, certifications, languages, or employment history.
- Use plain ASCII hyphens in generated prose and dates. Do not use em dashes.
- CVs must be ATS-friendly and fit on a single A4 page.
- During any `/apply-for-job` or `/update-profile` conversation, watch every user message for feedback that could improve future runs — not just voice or style, but anything: framing, identity positioning, section naming, structure, ordering, formatting conventions, tone, language-specific phrasing, project selection rules, etc. The test is: *could this same correction usefully recur on a future application?* If yes, apply the change to the current files AND present a candidate-learning block (rule, scope, why) asking `Lock as learning for future runs? [y/n/edit]`. On `y`, append a new `R###` block to `input/feedback.md`. Read `input/feedback.md` at Step 1 of both commands and apply applicable rules (`[global]` plus matching language tag) to all generated content. Skip the prompt for one-off corrections that cannot recur (a specific date typo, a name misspelling, a LaTeX compile fix) — apply those silently. Detect conflicts against existing rules at read-time and at lock-time; surface and let the user resolve. Full mechanics live in `.codex/commands/apply-for-job.md` and `.codex/commands/update-profile.md`.
- Whenever you modify the structure of the agent itself — `CLAUDE.md`, `AGENTS.md`, any file under `.claude/commands/`, `.claude/skills/`, `.codex/commands/`, `lang_rules/_template.md`, `resources/resume.cls`, or `.gitignore` — append a new entry to `/CHANGELOG.md` describing what was Added, Changed, Deprecated, Removed, or Fixed (Keep-a-Changelog format). Group related edits made in one session under a single dated heading. Do not log edits to user data files (`input/profile.md`, `input/resume.tex`, `input/feedback.md`, `proj_refs/`, `output/`) or to files under `docs/`. If `CHANGELOG.md` does not exist, create it.

## Expected User Phrases

Map common Codex prompts to the workflow:

- "apply for this job", "generate my CV", "tailor my resume", or `/apply-for-job` -> run the apply workflow.
- "help me set this up", "onboard me", "initialize this project", "configure my profile" -> run onboarding.
- "summarize this project for my CV", `/project-summary`, or "make a project summary" -> run the project summary workflow.
- "update my profile", "sync project refs", "ingest proj_refs", or `/update-profile` -> run the update-profile workflow.

## Verification

This project usually cannot be fully tested with an automated command because the output is agent-generated writing plus LaTeX. After generation, verify by inspection that:

- Output files were written to the expected `output/Codex/{slug}/` folder.
- `resources/resume.cls` was copied into that folder as `resume.cls`.
- Every configured language has one CV and one cover letter.
- Output filenames use `identity.file_slug` from `input/profile.md`.
- Project detail came from `input/profile.md` / `## Notable Projects`, not direct `proj_refs/` reads.
- The LaTeX source does not contain obvious unescaped text-mode special characters.
- The CV follows the one-page compression rules from `CLAUDE.md`.
