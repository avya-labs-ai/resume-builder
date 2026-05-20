# Onboarding - Codex Workflow

Use this when the user asks Codex to set up, initialize, onboard, or configure the Resume Builder project.

The canonical workflow is `.claude/skills/onboarding/SKILL.md`. Follow it exactly, adapting only tool names and interaction style for Codex.

## Goal

Create the user's private working files from the tracked templates:

- `input/profile.md`
- `input/resume.tex`
- `output/`
- `proj_refs/`
- any missing `lang_rules/{code}.md` files for chosen languages

## Procedure

1. Read `.claude/skills/onboarding/SKILL.md`.
2. Detect whether onboarding is already complete:
   - If `input/profile.md` exists and contains a real `identity.full_name`, tell the user it is already configured.
   - If files are missing or still contain placeholders, continue.
3. Create `input/`, `output/`, and `proj_refs/`.
4. Copy:
   - `input.example/profile.template.md` to `input/profile.md`
   - `input.example/resume.template.tex` to `input/resume.tex`
5. Interview the user for languages, identity, framing, career history, education, skills, spoken languages, constraints, and optional project references.
6. Replace placeholders in `input/profile.md` and `input/resume.tex`.
7. Generate missing language rules when needed.
8. Summarize created files and next steps.

## Hard Rules

- Never modify `input.example/`.
- Never commit or expose private data from `input/`, `output/`, or `proj_refs/`.
- Be truthful and preserve uncertainty rather than filling gaps with invented details.
- Keep the same LaTeX class, packages, and section structure from the template.
- Use plain ASCII hyphens. Do not use em dashes.

