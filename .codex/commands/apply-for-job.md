# Apply For Job - Codex Workflow

Use this when the user asks Codex to apply for a job, tailor a resume/CV, generate a cover letter, or runs `/apply-for-job`.

The canonical workflow is `.claude/commands/apply-for-job.md`. Follow it exactly, adapting tool names, interaction style, and the output root for Codex.

Claude Code writes generated applications under `output/Claude Code/`. Codex must write generated applications under `output/Codex/`.

## Summary

Generate one tailored CV and one tailored cover letter per configured language from a job description.

Inputs:

- `input/profile.md`
- `input/resume.tex`
- `resources/resume.cls`
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
4. Use the `## Notable Projects` section in `input/profile.md` as the project context. Do not read `proj_refs/` during this workflow.
5. Ensure every configured language has a `lang_rules/{code}.md`; create a missing rules file from `lang_rules/_template.md` only when needed.
6. Get the job description from the user prompt. If it was not provided, ask the user to paste it.
7. Extract the company name from the JD. Ask the user for a company website URL or short company description; allow `skip`.
8. If the user provides a URL, research it before generation. If the user provides a description, use it as company research. Use concrete details from that research in the cover letter's "why us" paragraph.
9. If research is skipped, write a role-targeted but company-generic "why us" paragraph and add the LaTeX TODO comment required by `.claude/commands/apply-for-job.md`.
10. Print a concise gap analysis to chat only.
11. Derive the output slug `{company-role}`.
12. Create `output/Codex/{slug}/`, copy `resources/resume.cls` into it as `resume.cls`, and write all generated `.tex` files there.
13. Confirm the output folder and file list, including `resume.cls`.

## Hard Rules

- Never modify `input/profile.md` or `input/resume.tex` during this workflow.
- Never write Codex-generated output outside `output/Codex/{slug}/`.
- Never read `proj_refs/` during CV generation. `input/profile.md` is the single runtime source of truth for profile and project narratives.
- Copy `resources/resume.cls` into each generated output folder as `resume.cls` so the CV can compile next to its class file.
- Ask for company URL or description before generation and use it for a company-specific "why us" cover letter paragraph. If skipped, include the required LaTeX TODO comment above the generic paragraph.
- Preserve the CV LaTeX template structure from `input/resume.tex`.
- Generate the primary language first; translate/adapt non-primary languages from the primary output.
- Be truthful. Emphasize and reorder, but do not invent.
- Escape all LaTeX special characters.
- Use ATS-safe headings and conventions from `lang_rules/{code}.md`.
- Keep each CV to one A4 page.
- Use plain ASCII hyphens. Do not use em dashes.
