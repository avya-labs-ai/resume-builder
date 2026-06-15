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
- `input/feedback.md` (optional) — learned style, framing, identity, structural, and formatting rules from prior conversations; consulted at Step 1 and applied during generation in Step 12

Outputs:

- `output/Codex/{company-role}/resume.cls`
- `output/Codex/{company-role}/Resume_{file_slug}_{code}.tex`
- `output/Codex/{company-role}/CoverLetter_{file_slug}_{code}.tex`

## Codex Procedure

1. Read `.claude/commands/apply-for-job.md`.
2. Check that `input/profile.md`, `input/resume.tex`, and `resources/resume.cls` exist. Also read `input/feedback.md` if it exists and parse all active (non-superseded) rule blocks. If the file is absent, treat the rule set as empty and continue silently. After reading, surface any internal conflicts among active rules using the Step 1.5 block format from `.claude/commands/apply-for-job.md`; wait for user resolution before proceeding.
3. Parse the YAML front matter in `input/profile.md` for:
   - `identity.full_name`
   - `identity.file_slug`
   - `languages[]`
   - `primary_language`
4. Use the `## Notable Projects` section in `input/profile.md` as the project context. Include all AI-related projects — do not cap at 1–2. Compress each to minimum bullets. Do not read `proj_refs/` during this workflow.
5. Ensure every configured language has a `lang_rules/{code}.md`; create a missing rules file from `lang_rules/_template.md` only when needed.
6. Get the job description from the user prompt. If it was not provided, ask the user to paste it.
7. Extract the company name from the JD. Ask the user for a company website URL or short company description; allow `skip`.
8. If the user provides a URL, research it before generation. If the user provides a description, use it as company research. Use concrete details from that research in the cover letter's "why us" paragraph.
9. If research is skipped, write a role-targeted but company-generic "why us" paragraph and add the LaTeX TODO comment required by `.claude/commands/apply-for-job.md`.
10. Print a concise gap analysis to chat only.
10.4. Compute and print a suitability score (chat only, never saved) using the weighted rubric and harsh, evidence-based grading rules defined in `.claude/commands/apply-for-job.md` (Step 3.4) — that file is the canonical rubric definition; do not duplicate or re-weight it here.
10.5. Print a pre-generation sign-off plan to chat (projects selected with rationale, identity framing, key emphasis, languages). Branch on the score: if the score is >= 85%, auto-generate without waiting; if the score is < 85%, wait for the user to confirm ("go") before writing any files. **While waiting on a sub-85% gate**, treat any plan-level pushback ("drop the IBS role", "lead with Avya OS", etc.) as a candidate learning per the watch logic in Step 14 below — apply the change to the plan, then prompt to lock the rule.
11. Derive the output slug `{company-role}`.
12. Create `output/Codex/{slug}/`, copy `resources/resume.cls` into it as `resume.cls`, and write all generated `.tex` files there. **Apply learned feedback rules during generation:** filter the rules read from `input/feedback.md` to those tagged `[global]` plus `[{code}]` for the language being generated, and apply them as hard constraints on phrasing, vocabulary, section naming, framing, ordering, and structure.
13. Confirm the output folder and file list, including `resume.cls`.
14. **Watch follow-up turns for improvable feedback.** After files are generated, the conversation continues. Apply the candidate-learning watch logic defined in `.claude/commands/apply-for-job.md` Step 6.5 (canonical spec) — same classifier (*could this same correction usefully recur on a FUTURE application?*), same candidate-learning block format, same `[y/n/edit]` semantics, same conflict-check-at-lock-time behaviour. On `y`, append a new `R###` block to `input/feedback.md` (create the file using the template in Step 6.5 if it does not exist). On `n`, discard; on `edit`, let the user rephrase before re-prompting. Identity-level facts (official company name, role title) get applied AND a suggestion to run `/update-profile`. One-off corrections (specific date typos, name misspellings, LaTeX compile fixes, one-time bullet reorders for this CV) are applied silently with no prompt.

## Codex-Specific Emphasis Rules

- Professional Experience must always be in strict reverse chronological order. Never reorder roles by JD relevance; use bullet selection and compression to show relevance.
- For AI-focused JDs, keep automotive roles in chronology but compress each automotive role to a maximum of 2 lines total unless the JD explicitly asks for automotive, autonomous driving, ADAS, safety-critical validation, or V&V.
- For AI-focused JDs, spend the recovered space on the current AI/automation professional experience and all AI-related projects from `input/profile.md` / `## Notable Projects`. Target one A4 page; spill to a maximum of two pages only after all compression is exhausted.

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
- Target one A4 page; maximum two A4 pages. Never exceed two pages.
- Use plain ASCII hyphens. Do not use em dashes.
