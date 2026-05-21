# Apply For Job - Codex Workflow

Use this when the user asks Codex to apply for a job, tailor a resume/CV, generate a cover letter, or runs `/apply-for-job`.

The canonical workflow is `.claude/commands/apply-for-job.md`. Follow it exactly, adapting tool names, interaction style, and the output root for Codex.

Claude Code writes generated applications under `output/Claude Code/`. Codex must write generated applications under `output/Codex/`.

## Summary

Generate one tailored CV and one tailored cover letter per configured language from a job description.

Inputs:

- `input/profile.md`
- `input/resume.tex`
- all Markdown files in `proj_refs/`
- `lang_rules/{code}.md` for every configured language

Outputs:

- `output/Codex/{company-role}/resume.cls`
- `output/Codex/{company-role}/Resume_{file_slug}_{code}.tex`
- `output/Codex/{company-role}/CoverLetter_{file_slug}_{code}.tex`

## Codex Procedure

1. Read `.claude/commands/apply-for-job.md`.
2. Check that `input/profile.md`, `input/resume.tex`, and `resources/resume.cls` exist.
3. Parse the YAML front matter in `input/profile.md` for:
   - `identity.full_name`
   - `identity.file_slug`
   - `languages[]`
   - `primary_language`
4. Read every `proj_refs/*.md` file that exists.
5. Ensure every configured language has a `lang_rules/{code}.md`; create a missing rules file from `lang_rules/_template.md` only when needed.
6. Get the job description from the user prompt. If it was not provided, ask the user to paste it.
7. Print a concise gap analysis to chat only.
8. Derive the output slug `{company-role}`.
9. Create `output/Codex/{slug}/`, copy `resources/resume.cls` into it as `resume.cls`, and write all generated `.tex` files there.
10. Confirm the output folder and file list, including `resume.cls`.

## Hard Rules

- Never modify `input/profile.md` or `input/resume.tex` during this workflow.
- Never write Codex-generated output outside `output/Codex/{slug}/`.
- Copy `resources/resume.cls` into each generated output folder as `resume.cls` so the CV can compile next to its class file.
- Preserve the CV LaTeX template structure from `input/resume.tex`.
- Generate the primary language first; translate/adapt non-primary languages from the primary output.
- Be truthful. Emphasize and reorder, but do not invent.
- Escape all LaTeX special characters.
- Use ATS-safe headings and conventions from `lang_rules/{code}.md`.
- Keep each CV to one A4 page.
- Use plain ASCII hyphens. Do not use em dashes.
