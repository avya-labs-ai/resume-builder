# proj_refs/

This folder holds **active project summaries** — one Markdown file per project you want the CV agent to draw from when tailoring your CV and cover letter.

Project refs are **optional but high-value**: without them, the agent works only from `input/profile.md`. With them, it can pull specific stack details, methodology names, metrics, and outcome language that profile.md summarises too broadly.

## How to add a project ref

If you use the [Avya AIOS](https://github.com/avya-labs/aios) or a similar Claude Code setup that has the `/project-summary` skill, run it inside your project session:

```
/project-summary
```

Then drop the output file into `proj_refs/`. The `/apply-for-job` command reads all `.md` files here automatically.

If you don't have that skill, write a free-form Markdown file yourself — the shape below is what works best.

## Expected shape (see sample_project_summary.md for a full example)

```markdown
# Project: [Project Name]
**Status:** [Active / Shipped / Archived]
**Stack:** [language, framework, key libraries]
**What it does:** [1–2 sentences]

## Architecture decisions
- [Key ADR or design choice with rationale]

## Key outcomes / metrics
- [Specific, quantified result]

## Methodology
- [How you approached it — TDD, ADRs, WAT framework, etc.]
```

The agent selects the 1–2 most JD-relevant projects and uses the specific language from these files (not the high-level summaries in profile.md).

## Gitignore note

`proj_refs/` is gitignored — your project summaries stay on your machine only. This folder (`proj_refs.example/`) is what ships with the repo.
