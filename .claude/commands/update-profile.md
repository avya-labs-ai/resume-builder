---
description: Ingest new proj_refs/*.md files into input/profile.md. Run this after dropping new project summaries into proj_refs/. Reads the ingestion stamp in profile.md to detect only new files — already-ingested files are never re-read.
allowed-tools: Read, Write, Bash
---

# Update Profile — Ingest New Project References

You are updating `input/profile.md` to incorporate new project summaries from `proj_refs/`.

## Step 1 — Read the ingestion stamp

Read `input/profile.md` and extract the `<!-- proj_refs_ingested: ... -->` block at the bottom. Parse the filenames listed inside it — these are already ingested and must be skipped.

If no stamp block exists in `profile.md`, treat the ingested list as empty (all proj_refs files are new).

## Step 2 — Detect new files

Run:
```bash
ls proj_refs/*.md 2>/dev/null
```

Compare the listed filenames (basename only, e.g. `avyaLabs_BKA.md`) against the ingested list from Step 1.

If no new files are found, tell the user:
> "`input/profile.md` is already up to date — no new `proj_refs/` files detected."
Then stop.

## Step 3 — Read and synthesize each new file

For each new `proj_refs/*.md` file:
1. Read the file in full.
2. Extract through the lens of a **veteran recruiter or hiring manager with 10+ years of experience** scanning a resume — not just what the project is, but what makes it worth hiring for. Ask yourself: "Would this point make a hiring manager pause and take notice?" Capture only the signal, discard the noise.

   Specifically extract:
   - **Project name and one-line purpose** — what problem it solves, for whom, at what scale
   - **Stack** — technologies, frameworks, models, infra; include versions only if they signal currency or depth
   - **Impact and outcomes** — quantified wherever possible (latency, cost, accuracy, users, revenue, time saved); if not quantified, characterise the magnitude (e.g. "replaced a manual 3-day process", "deployed to 50k users")
   - **Engineering decisions that reveal seniority** — architectural trade-offs made, hard constraints navigated, non-obvious choices and their rationale
   - **Methodology or discipline signals** — eval-driven development, spec-first, TDD, agent orchestration patterns, safety/compliance work; anything that shows *how* the candidate engineers, not just *what* they built
   - **Scope and ownership** — solo vs. team, greenfield vs. legacy, end-to-end ownership vs. component contribution; any cross-functional or stakeholder coordination
   - **Status** — shipped/live, in progress, prototype/POC, or deprecated

3. Write a new project entry under the `## Notable Projects` section of `input/profile.md`, following the same format as the existing entries:
   - Heading: `### N. Project Name` (increment the existing number)
   - One-sentence description that leads with impact or scale, not just function
   - `- **Stack:**` bullet
   - `- **Engineering highlights:**` or `- **Engineering discipline:**` bullet with nested items — each sub-bullet must be a recruiter-pause-worthy point (quantified outcome, non-trivial decision, seniority signal); cut anything generic
   - `- **Status:**` bullet
4. Escape LaTeX special characters in any content that will appear in generated CVs: `_` -> `\_`, `&` -> `\&`, `%` -> `\%`.

## Step 4 — Update the ingestion stamp

At the bottom of `input/profile.md`, update the `<!-- proj_refs_ingested: ... -->` block to include all newly ingested filenames (append to the existing list, preserve the others).

Format:
```
<!-- proj_refs_ingested:
  existing-file.md
  another-existing.md
  newly-ingested-file.md
-->
```

## Step 5 — Confirm

Print:
```
Updated input/profile.md.

Ingested [N] new project(s):
- [filename 1] → [project name]
- [filename 2] → [project name]

Total ingested: [total count] proj_refs files.
Run /apply-for-job when ready to generate a tailored CV.
```

## Constraints

- Never modify any file in `proj_refs/` — they are read-only inputs.
- Never modify `input/resume.tex`, `lang_rules/`, or any file in `input.example/`.
- Only append to `profile.md` — do not restructure or rewrite existing sections.
- If a proj_refs file is malformed or contains very little information, add a minimal entry and note: `<!-- TODO: this entry was sparse — consider updating proj_refs/{filename} with more detail -->`.
