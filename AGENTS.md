# AGENTS.md - Resume Builder

Single source of truth for both Claude Code and Codex sessions in this repo. Context for any agent session working here.

## Single source of truth - how the instruction files stay in sync

- **Edit rules in `AGENTS.md` only.** Codex reads it directly. Claude Code reads it through `CLAUDE.md`, which is a thin file containing `@AGENTS.md` (an import that Claude Code expands at load time) plus a short Claude-only section.
- **Never copy rules into `CLAUDE.md`.** If a rule is not tool-specific, it belongs here. If it is Claude-only (slash-command names, Claude output folder), it goes in the `## Claude-only` section of `CLAUDE.md`.
- **Workflows live once, in `.claude/commands/*.md` and `.claude/skills/*/SKILL.md`.** The files in `.codex/commands/` are thin wrappers that point at them and map phrasing; keep them thin. If a workflow changes, change the canonical file and only adjust a wrapper when Codex needs a tool-specific adaptation.
- **Guard check (run after editing either file):** `CLAUDE.md` must contain `@AGENTS.md` and stay short.
  `grep -q '^@AGENTS.md' CLAUDE.md && [ "$(wc -l < CLAUDE.md)" -le 40 ] && echo OK`

## Tool routing and output folders

- Claude Code reads `CLAUDE.md` (which imports this file) and `.claude/`. Codex reads this `AGENTS.md` and `.codex/commands/`.
- Product behavior must stay aligned in both tools. Only generated application files are separated by tool:
  - Claude Code writes to `output/Claude Code/{company-role}/`.
  - Codex writes to `output/Codex/{company-role}/`.
- Never write one tool's generated CVs or letters into the other tool's folder, or anywhere else.

## What this project is

A single-purpose automation: take a job description, produce a tailored CV and cover letter in **any configured set of languages**, as compilable `.tex` files, organized into a per-application output folder.

There is no application server, no API, no package manager, no Python runtime. The engine is the agent session itself, invoked via the `/apply-for-job` slash command (Claude Code) or the matching Codex wrapper. New users are onboarded via the `onboarding` skill (auto-triggered on first open, or say "help me set this project up").

---

## How it works

### First time (new user)
The **onboarding skill** runs automatically when the repo is freshly cloned or when the user says "Onboard me into the project". It:
1. Creates `input/`, `output/Claude Code/`, `output/Codex/`, and `proj_refs/` from the templates in `input.example/`
2. Interviews the user and writes `input/profile.md` (profile + language config) and `input/resume.tex` (LaTeX template)
3. Generates `lang_rules/{code}.md` for any chosen language that doesn't have one yet

### Every run
Run `/apply-for-job` (Claude Code) or ask Codex to apply for the job. The workflow will:

0. **Load the standing rules first.** Read `input/feedback.md` BEFORE anything else (standing-rules digest first, then all active rules), print `Loaded {N} active rules`, run the conflict check, and apply the rules at every stage of the run, not only at generation. Canonical mechanics: `.claude/commands/apply-for-job.md` Steps 1a, 1.5 and 1.6.
1. Read `input/profile.md` - parse the YAML front-matter for `identity` (name, file_slug) and `languages[]` (ordered list of language configs)
2. Read `input/resume.tex` - the LaTeX CV template
3. Read `lang_rules/{code}.md` for each configured language
4. Take the JD from `$ARGUMENTS` or prompt the user to paste it
5. Print a gap analysis to the chat (terminal-only, never saved)
6. Derive a folder slug `{company}-{role}` from the JD
7. Copy `resources/resume.cls` into the tool's output folder `{slug}/` as `resume.cls`
8. Write `JobDescription.md` and initialize or preserve the append-only `Clarifications.md` in `{slug}/`
9. Write one CV + one cover letter per configured language into `{slug}/`:
   - `Resume_{file_slug}_{code}.tex`
   - `CoverLetter_{file_slug}_{code}.tex`

---

## Input files

### `input/profile.md` - Consolidated profile (gitignored - created by onboarding)

The single source of truth for personal data, career history, skills, framing rules, and constraints. Contains a YAML front-matter block at the top:

```yaml
---
identity:
  full_name: "Jane Doe"
  file_slug: "JaneDoe"         # used in output filenames
languages:
  - code: en
    name: English
  - code: de
    name: German
  # add more here
primary_language: en            # source language; others are translated from this
---
```

The body contains the full profile: identity framing rules, career history, skills, achievements, education, spoken languages, constraints.

**Update this file** when roles, skills, or framing preferences change. To add a language, add an entry to `languages[]` and re-run the apply workflow.

### `input/resume.tex` - LaTeX CV template (gitignored - created by onboarding)

The master CV source. Preserved as a LaTeX template - the workflow keeps its `\documentclass`, packages, and section structure intact, adapting only content. **Do not modify this file during a CV generation run.**

### `input/feedback.md` - Learned rules (gitignored)

Numbered `R###` rules captured from feedback during `/apply-for-job` and `/update-profile` sessions, plus a `## Standing rules` digest of the hard rules at the top. It is the FIRST file read on every run (see Working rules). To remove or edit a rule, edit the file directly; superseded rules are skipped at read time.

### `input/intros.md` - Reusable introductions (gitignored)

Generic and role-specific introduction / referral write-ups (not CVs or cover letters). Not a runtime input for CV generation.

### `input.example/` - Shareable templates (ships with repo)

- `profile.template.md` - profile skeleton with `{{placeholders}}` and YAML front-matter schema
- `resume.template.tex` - LaTeX CV template with `{{placeholders}}`
- `README.md` - explainer

The onboarding skill copies these into `input/` and replaces placeholders with real data. **Never edit the `.example` files on behalf of a specific user** - they are the shared starting point.

### `lang_rules/` - Per-language rules (ships with repo)

One Markdown file per supported language, defining:
- ATS-safe section headings (in that language)
- Date format and month abbreviations
- Cover letter salutation, closing, subject-line label
- Length ratio vs. primary language (for comparable multi-language layout management)
- LaTeX special character escapes (umlauts, accents, etc.)
- Phrasing notes and ATS gotchas

`lang_rules/en.md` and `lang_rules/de.md` ship with the repo. `_template.md` defines the schema. New language files are auto-generated by onboarding or the apply workflow when a configured language has no existing rules file.

### `proj_refs/` - Active project summaries (gitignored - user adds these)

One Markdown file per project. These are the **update feed** for `input/profile.md` - not a runtime input for the apply workflow. When you add a new summary here, run `/update-profile` to synthesize it into `profile.md`.

Preferred format: see `proj_refs.example/sample_project_summary.md`.

**Never read directly during CV generation** - all project detail lives in the "Notable Projects" section of `profile.md` after ingestion.

---

## Layout

```
.
├── .claude/
│   ├── commands/apply-for-job.md      # Canonical apply workflow
│   ├── commands/gap-analysis.md       # Canonical gap-analysis workflow
│   ├── commands/update-profile.md     # Canonical profile-update workflow
│   ├── commands/project-summary.md    # Canonical project-summary workflow
│   ├── skills/onboarding/SKILL.md     # Auto-triggered onboarding skill
│   └── skills/red-team-review/SKILL.md # Adversarial QA - invoked by apply-for-job at end of generation
├── .codex/
│   └── commands/                      # Thin Codex wrappers for the same workflows
│
├── input.example/                     # Templates (ship with repo)
├── proj_refs.example/                 # Example project ref + explainer
├── output.example/                    # Explainer for output structure
│
├── lang_rules/                        # Per-language CV and cover letter rules
│   ├── _template.md
│   ├── en.md
│   ├── de.md
│   └── {code}.md  (auto-generated)
│
├── resources/
│   └── resume.cls                     # Copied into each generated output folder
│
├── input/                             # Gitignored - created by onboarding
│   ├── profile.md
│   ├── resume.tex
│   ├── feedback.md                    # Learned rules (read FIRST on every run)
│   └── intros.md                      # Reusable introductions / referral texts
│
├── proj_refs/                         # Gitignored - user-added project summaries
├── output/                            # Gitignored - generated applications
│   ├── Claude Code/{company}-{role}/  # resume.cls, JobDescription.md, Clarifications.md,
│   └── Codex/{company}-{role}/        #   Resume_{slug}_{code}.tex, CoverLetter_{slug}_{code}.tex
│
├── archive/                           # Backups of past profile versions
├── .gitignore                         # Gitignores input/, output/, proj_refs/
├── AGENTS.md                          # THIS FILE - single source of truth
├── CLAUDE.md                          # Thin wrapper: @AGENTS.md + Claude-only notes
├── CHANGELOG.md                       # Structural changes to the agent itself
└── README.md                          # Human-facing usage docs
```

---

## Canonical workflow files

- `.claude/commands/apply-for-job.md` - canonical generation workflow.
- `.claude/commands/gap-analysis.md` - canonical gap-analysis-only workflow.
- `.claude/commands/project-summary.md` - canonical project summary workflow.
- `.claude/commands/update-profile.md` - canonical profile update workflow.
- `.claude/skills/onboarding/SKILL.md` - canonical onboarding workflow.
- `.claude/skills/red-team-review/SKILL.md` - canonical two-stage red-team QA, invoked by the apply workflow at end of generation.
- `.codex/commands/apply-for-job.md`, `project-summary.md`, `update-profile.md`, `onboarding.md` - Codex-facing wrappers for the matching workflows.

When the user asks to apply for a job in Codex, follow `.codex/commands/apply-for-job.md` (treat the Claude slash name `/apply-for-job` as the same request). Likewise: setup/onboarding -> `.codex/commands/onboarding.md`; project summary -> `.codex/commands/project-summary.md`; update/sync/ingest profile -> `.codex/commands/update-profile.md`.

## Expected user phrases

- "apply for this job", "generate my CV", "tailor my resume", or `/apply-for-job` -> run the apply workflow.
- "gap analysis for this JD" or `/gap-analysis` -> run the gap-analysis workflow (no files written).
- "help me set this up", "onboard me", "initialize this project", "configure my profile" -> run onboarding.
- "summarize this project for my CV", `/project-summary`, or "make a project summary" -> run the project summary workflow.
- "update my profile", "sync project refs", "ingest proj_refs", or `/update-profile` -> run the update-profile workflow.

---

## Working rules

### Standing rules and learning (read first, apply everywhere)
- **Load `input/feedback.md` FIRST on every `/apply-for-job`, `/gap-analysis` and `/update-profile` run**, before the profile, the CV template, or the JD. Read the `## Standing rules` digest first, then all active (non-superseded) rules. Print `Loaded {N} active rules`. Apply them at every stage (research, JD parse, gap analysis, scoring, planned-output block, generation, self-review, red-team), not only at generation. The planned-output block lists the rules that shaped the plan. Full mechanics: `.claude/commands/apply-for-job.md` Steps 1a, 1.5, 1.6.
- **Capture user feedback as learnings.** During any `/apply-for-job` or `/update-profile` conversation, watch every user message for feedback that could improve future runs - not just voice or style, but anything: framing, identity positioning, section naming, structure, ordering, formatting conventions, what to include or omit, tone, language-specific phrasing, project selection rules, etc. The test is: *could this same correction usefully recur on a future application?* If yes, apply the change to the current files AND present a candidate-learning block (rule, scope, why) asking `Lock as learning for future runs? [y/n/edit]`. On `y`, append a new `R###` block to `input/feedback.md` (and add or update its line in the Standing rules digest when it is a hard rule). Skip the prompt for one-off corrections that cannot recur (a specific date typo, a name misspelling, a LaTeX compile fix) - apply those silently. Detect conflicts against existing rules at read-time and at lock-time; surface and let the user resolve. Show the user the exact rule text before writing it. Full mechanics: `.claude/commands/apply-for-job.md` (Steps 1, 1.5, 3.5, 5, 6.5) and `.claude/commands/update-profile.md`.
- **Log structural agent changes to `CHANGELOG.md`.** Whenever you modify the structure of the agent itself - `CLAUDE.md`, `AGENTS.md`, any file under `.claude/commands/`, `.claude/skills/`, `.codex/commands/`, `lang_rules/_template.md`, `resources/resume.cls`, or `.gitignore` - append a new entry to `/CHANGELOG.md` describing what was Added, Changed, Deprecated, Removed, or Fixed (Keep-a-Changelog format). Group related edits made in one session under a single dated heading. Do **not** log edits to user data files (`input/profile.md`, `input/resume.tex`, `input/feedback.md`, `input/intros.md`, `proj_refs/`, `output/`) or to files under `docs/`. If `CHANGELOG.md` does not exist, create it.

### Files and sources
- **Never modify `input/resume.tex` or `input/profile.md`** during a CV run - they are source files.
- **`input/profile.md` is the single source of truth** for all profile data, including full project narratives in its "Notable Projects" section. Do not read `proj_refs/` during a CV generation run.
- **To add a new project to the profile:** drop a summary into `proj_refs/` and run `/update-profile`. Never manually edit the "Notable Projects" section - let `/update-profile` do it.
- **Never modify files in `input.example/` or `lang_rules/`** on behalf of a specific user - those are shared templates and rules. You may create a missing `lang_rules/{code}.md` when a configured language needs it.
- **Generated output belongs under the tool's own folder** (`output/Claude Code/{slug}/` or `output/Codex/{slug}/`).
- **Copy `resources/resume.cls` into every generated output folder** as `resume.cls`, next to the CV and cover letter `.tex` files.
- **One job description = one output subfolder.** Re-running with the same JD overwrites the previous output.
- **Before generation, ask for a company URL or description** and use it to write a company-specific "why us" paragraph in the cover letter. If skipped, use the generic paragraph plus the required LaTeX TODO comment from the apply workflow.
- **Preserve application clarifications for interview preparation.** Every application output folder contains an append-only `Clarifications.md`. During `/apply-for-job` follow-up, log each application-related clarification question plus the substantive answer and reasoning about JD interpretation, wording, claims, project/keyword selection, positioning, trade-offs, or interview implications. Preserve the file across reruns; corrections append a new entry instead of rewriting history. Do not log simple approvals, file operations, compiler issues, token/cost questions, or unrelated meta-conversation. Canonical mechanics and entry format: `.claude/commands/apply-for-job.md` Steps 4.6 and 6.6.

### Content and truthfulness
- **Preserve the LaTeX template** from `input/resume.tex` - same `\documentclass`, same packages, same section structure. Adapt content only.
- **Identity framing is user-defined.** Read the framing rules from the "Identity & Framing Rules for the Agent" section of `input/profile.md`. Follow them exactly.
- **Each language gets its own rules.** Read `lang_rules/{code}.md` before generating content in that language. Section headings, date formats, salutations, and closings come from those files - not from hardcoded defaults.
- **Non-primary languages are translated from the primary-language output.** Do not re-derive from the profile independently for each language. Natural translation, not literal: apply the phrasing notes from `lang_rules/{code}.md`. German is not English with German words.
- **Be truthful.** Adaptation = emphasize, reorder, reframe. Never invent experience, metrics, tools, certifications, languages, or employment history. Never hallucinate content not present in the user's profile - if something is missing and it matters, flag the gap rather than fabricating.
- **Professional Experience chronology is fixed.** Always list roles in strict reverse chronological order. Do not move older roles above newer ones because they match the JD better; adjust bullet content and length instead.
- **AI-focused JDs shift emphasis to AI work.** If the JD focuses on AI, generative AI, machine learning, NLP, chatbots, automation, data science, AI governance, or AI architecture, compress automotive roles to a maximum of 2 lines each unless automotive/safety-critical/V&V experience is explicitly requested. Use the space for current AI/automation experience and the strongest completed AI projects from `input/profile.md`.
- **CV substance outranks a one-page target.** Aim for approximately 1-1.5 A4 pages of readable, relevant content. A second physical page is acceptable when it creates an intentional, well-balanced home for strong project, education, or supporting evidence; never exceed two pages. Summary: 3-4 lines. Skills: 4-6 grouped bullets with `$\diamond$` separators. Most-relevant role: 4 bullets max. Mid-career roles: 2-3 bullets. Older roles (>7 years): 1 line. Give selected projects enough space to show the problem, judgement, architecture, and outcome. No Hobbies section unless the JD signals cultural fit. Font: `\fontsize{10pt}{12pt}\selectfont` - do not shrink it or tighten spacing solely to force one page.
- **ATS-friendly always.** Use `$\diamond$` separators in Skills and Languages lines. Spell out URLs (no hidden `\href` link text). Standard section names from `lang_rules/{code}.md`. Plain ASCII hyphens in date ranges. No tables, multi-column layouts, or images for ATS-critical content. Include exact JD keywords verbatim where truthful. Full ATS rules are in the apply command.
- **Bullet craft: outcome-first, evidence-backed.** Every Experience/Projects bullet leads with the outcome and carries the highest truthful evidence tier from the profile: measured outcome > countable output > characterized magnitude > named specificity. No naked duty statements ("Responsible for", "Worked on"). Harvest metrics from the profile's Headline Summary / Notable Achievements / Notable Projects before drafting. Never invent or estimate numbers. The CV must always read as the profile's consultant-who-ships framing. Full rules: `.claude/commands/apply-for-job.md` (Bullet craft).
- **Cover letter: one story + objection handling.** The experience paragraph tells ONE JD-relevant story in depth, never a list of 3+ projects. When the seniority-fit dimension lost points or an obvious red flag exists (overqualification, pivot, short stint), the letter names and defuses the objection in 1-2 sentences - asking the user for the real motivation if unclear, never fabricating one. Full rules: `.claude/commands/apply-for-job.md` (Cover letter structure).
- **NEVER use em dashes.** Use a plain hyphen `-` in all generated prose and dates.
- **Escape LaTeX special characters** in all generated content (`_`, `&`, `%`, `$`, `#`, `{`, `}`), including underscores in code and file names (`quality\_checker`, `book\_expenses`). An unescaped `_` in text mode causes a hard compile error.

### Process gates and reviews
- **Structured JD parse before gap analysis.** Break the JD into must-haves (only explicitly required items), nice-to-haves, core responsibilities, and signals (tone, seniority, verbatim keywords) and print it to chat (Step 2.7). The parse is the canonical requirement list for the gap analysis, the 40% must-have scoring dimension, and the self-review keyword check - never re-derive requirements downstream.
- **Conditional green light before generating the CV.** After the gap analysis, compute the suitability score (Step 3.4) and present it with the planned-output block. **If the score is < 85%, ALWAYS wait for the user's explicit green light ("go") before generating** the CV and cover letter. **If the score is >= 85%, auto-generate without waiting.** The harsh, evidence-based scoring rubric lives in `.claude/commands/apply-for-job.md` (Step 3.4). The score and gap analysis are terminal-only and never saved to disk.
- **Post-generation self-review is mandatory.** After writing the `.tex` files, run the pass/fail self-review from `.claude/commands/apply-for-job.md` (Step 5.5): must-have coverage, keyword coverage, evidence audit, claim audit, positioning, format, cover letter, rule compliance. Revise and re-check (max 3 iterations), then print the report to chat. Never fabricate a numeric "ATS score" - there is no ground truth for one. The report is terminal-only and never saved.
- **Red-team review is mandatory (runs on top of the Step 5.5 self-review).** After the self-review, the workflow invokes the `red-team-review` skill (`.claude/skills/red-team-review/SKILL.md`, Step 5.6; Codex wrapper step 12.6). Stage 1 scrutinizes the English (primary-language) CV and cover letter as a veteran headhunter reading against the JD parse, running exactly five full passes and editing the `.tex` files in place, then pauses for the user's sign-off before any non-primary language (feedback R031). Stage 2, after the non-primary files are generated from the finalized English, verifies each is a faithful one-to-one translation and edits it in place. Never fabricate to close a JD gap; reports are terminal-only and never saved.

---

## Adding a new language

1. Add a `{code, name}` entry to the `languages` list in `input/profile.md` front-matter.
2. Re-run the apply workflow. If `lang_rules/{code}.md` doesn't exist, it is auto-generated from `lang_rules/_template.md` using the agent's language knowledge.
3. New CV + cover letter files for the added language appear in the output folder.

## Editing the workflow

If the workflow needs to change (ATS rules, generation steps, output filenames), edit `.claude/commands/apply-for-job.md`. That single file defines the entire automation; the Codex wrapper only maps and adapts it.

## Out of scope

- LaTeX compilation (compile in your own editor)
- Job-board scraping / API integration
- Tracking applications, deadlines, status
- Email / cover-letter delivery

This project ends at a complete per-application artifact folder: tailored `.tex` files, `resume.cls`, `JobDescription.md`, and the interview-preparation `Clarifications.md` log.

## Codex adaptations

- Use Codex tools normally while preserving each workflow's intent; for local file edits use `apply_patch`.
- If a Claude workflow says "Read", inspect with shell tools (`sed`, `rg`, `find`); if it says "Write", edit repo files directly and report the path.
- Codex-generated files go under `output/Codex/{slug}/`.

## Verification

This project usually cannot be fully tested with an automated command because the output is agent-generated writing plus LaTeX. After generation, verify by inspection that:

- Output files were written to the expected tool-specific `output/.../{slug}/` folder.
- `resources/resume.cls` was copied into that folder as `resume.cls`.
- `Clarifications.md` exists in the application folder and preserves any prior entries.
- Every configured language has one CV and one cover letter (primary language first, then the rest after sign-off).
- Output filenames use `identity.file_slug` from `input/profile.md`.
- Project detail came from `input/profile.md` / `## Notable Projects`, not direct `proj_refs/` reads.
- The LaTeX source does not contain obvious unescaped text-mode special characters.
- The CV follows the substance-first 1-1.5-page length policy, with no forced one-page compression.
- The run printed `Loaded {N} active rules` before any other work, and the planned-output block lists the rules applied.
- After editing instruction files: `CLAUDE.md` still imports `AGENTS.md` and holds no duplicated rules (see the guard check at the top).
