# AGENTS.md - Resume Builder

Project instructions for Codex sessions in this repo.

This repo is shared with Claude Code. Keep the existing Claude workflow intact:

- Claude Code reads `CLAUDE.md` and `.claude/`.
- Codex reads this `AGENTS.md`.
- The actual product behavior should stay the same in both tools.

## What This Project Is

A single-purpose agent workflow: take a job description, then produce a tailored CV and cover letter in every configured language as compilable `.tex` files under `output/{company-role}/`.

There is no application server, API, package manager, or runtime to install. The agent is the engine.

## Canonical Workflow Files

Use these files as the source of truth:

- `CLAUDE.md` - full project contract and working rules.
- `.claude/commands/apply-for-job.md` - canonical generation workflow.
- `.claude/commands/project-summary.md` - canonical project summary workflow.
- `.claude/skills/onboarding/SKILL.md` - canonical onboarding workflow.
- `.codex/commands/apply-for-job.md` - Codex-facing wrapper for the apply workflow.
- `.codex/commands/project-summary.md` - Codex-facing wrapper for project summaries.
- `.codex/commands/onboarding.md` - Codex-facing wrapper for onboarding.

When the user asks to apply for a job in Codex, follow `.codex/commands/apply-for-job.md`. If the user mentions the Claude slash command name `/apply-for-job`, treat it as the same request.

When the user asks to set up, initialize, onboard, or configure the project in Codex, follow `.codex/commands/onboarding.md`.

When the user asks for a project summary in Codex, follow `.codex/commands/project-summary.md`.

## Inputs

Personal source files are intentionally gitignored:

- `input/profile.md` - profile, identity, language config, and career source material.
- `input/resume.tex` - LaTeX CV template to preserve.
- `proj_refs/*.md` - active project summaries.

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
- Write generated CVs and cover letters only under `output/{slug}/`.
- Preserve the LaTeX document class, packages, and section structure from `input/resume.tex`.
- Escape LaTeX special characters in all generated text, especially `_`, `&`, `%`, `$`, `#`, `{`, and `}`.
- Never invent experience, metrics, tools, certifications, languages, or employment history.
- Use plain ASCII hyphens in generated prose and dates. Do not use em dashes.
- CVs must be ATS-friendly and fit on a single A4 page.

## Expected User Phrases

Map common Codex prompts to the workflow:

- "apply for this job", "generate my CV", "tailor my resume", or `/apply-for-job` -> run the apply workflow.
- "help me set this up", "onboard me", "initialize this project", "configure my profile" -> run onboarding.
- "summarize this project for my CV", `/project-summary`, or "make a project summary" -> run the project summary workflow.

## Verification

This project usually cannot be fully tested with an automated command because the output is agent-generated writing plus LaTeX. After generation, verify by inspection that:

- Output files were written to the expected `output/{slug}/` folder.
- Every configured language has one CV and one cover letter.
- Output filenames use `identity.file_slug` from `input/profile.md`.
- The LaTeX source does not contain obvious unescaped text-mode special characters.
- The CV follows the one-page compression rules from `CLAUDE.md`.

