# Onboarding - Codex Workflow

Use this when the user asks Codex to set up, initialize, onboard, or configure the Resume Builder project.

The canonical workflow is `.claude/skills/onboarding/SKILL.md`. Follow it exactly, adapting only tool names and interaction style for Codex.

## Goal

Create the user's private working files from the tracked templates:

- `input/profile.md`
- `input/resume.tex`
- `output/Claude Code/`
- `output/Codex/`
- `proj_refs/`
- any missing `lang_rules/{code}.md` files for chosen languages

## Procedure

1. Read `.claude/skills/onboarding/SKILL.md`.
2. Detect whether onboarding is already complete:
   - If `input/profile.md` exists and contains a real `identity.full_name`, tell the user it is already configured.
   - If files are missing or still contain placeholders, continue.
3. Create `input/`, `output/Claude Code/`, `output/Codex/`, and `proj_refs/`.
4. Copy:
   - `input.example/profile.template.md` to `input/profile.md`
   - `input.example/resume.template.tex` to `input/resume.tex`
5. Ask the user to choose a data collection route:
   - Interview: collect identity, framing, career history, education, skills, spoken languages, constraints, and goals.
   - Import resume: extract those fields from a pasted/provided resume, then ask follow-up questions for gaps or conflicts.
6. Apply project description discipline while collecting role/project detail: ask for stack, methodology, key decisions, and outcomes; push back at most twice when descriptions are thin, then move on.
7. Before writing files, pause for project references: let the user describe projects, drop files into `proj_refs/`, or skip. Any projects provided during onboarding must be synthesized directly into `input/profile.md`.
8. Write `input/profile.md` last, after all profile and project data is collected. Include a `## Notable Projects` section and a `<!-- proj_refs_ingested: ... -->` stamp.
9. Replace placeholders in `input/resume.tex`.
10. Generate missing language rules when needed.
11. Summarize created files and next steps, including that future project additions should go through `proj_refs/` plus the update-profile workflow.

## Hard Rules

- Never modify `input.example/`.
- Never commit or expose private data from `input/`, `output/`, or `proj_refs/`.
- Treat `input/profile.md` as the single source of truth for future CV generation; project summaries in `proj_refs/` are an update feed, not runtime context.
- Write `input/profile.md` only after the interview/import route and project-reference step are complete.
- Be truthful and preserve uncertainty rather than filling gaps with invented details.
- Keep the same LaTeX class, packages, and section structure from the template.
- Use plain ASCII hyphens. Do not use em dashes.
