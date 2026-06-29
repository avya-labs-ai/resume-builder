# Changelog

All notable structural changes to the Resume Builder agent itself — `CLAUDE.md`,
`AGENTS.md`, slash commands under `.claude/commands/` and `.codex/commands/`,
skills under `.claude/skills/`, language rule templates, `resources/`, and
`.gitignore`. User data files (`input/`, `proj_refs/`, `output/`) and
documents under `docs/` are intentionally excluded — this log tracks the
agent's own evolution, not the content it produces.

Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [2026-06-19]

### Changed
- `.claude/commands/apply-for-job.md`: **Step 4.5** now writes the gap analysis and suitability score(s) into `JobDescription.md` alongside the verbatim job description. The file captures the initial score, any intermediate revised scores with their reasons, and the final score. If the score is revised mid-session, `JobDescription.md` is overwritten to reflect the full scoring history before the CV/cover letter files are written.

## [2026-06-15]

### Added
- **Feedback learning mechanism.** The agent now watches every user message during `/apply-for-job` and `/update-profile` for feedback that could improve future runs — not just voice or style, but framing, identity positioning, section naming, structure, ordering, formatting, tone, language-specific phrasing, project selection, and anything else the user corrects that could plausibly recur. The classification test is: *could this same correction usefully recur on a future application?* For each candidate, the agent presents a `[y/n/edit]` confirmation block. On `y`, a new `R###` rule is appended to `input/feedback.md` (gitignored; created lazily on first lock). Rules are filtered by language tag (`[global]`, `[en]`, `[de]`, etc.) and applied during generation. Conflicts between rules are detected at read-time and at lock-time; the user resolves via supersede / scope-narrower / discard.
- **`CHANGELOG.md`.** New file at repo root tracking structural agent changes. This is the first entry.

### Changed
- `CLAUDE.md`: appended two working rules under the Working Rules block — **Capture user feedback as learnings** and **Log structural agent changes to `CHANGELOG.md`**.
- `AGENTS.md`: mirrored the same two rules under Operating Rules for Codex parity.
- `.claude/commands/apply-for-job.md`:
  - Step 1 now also reads `input/feedback.md` (optional input) and parses active rules into working memory.
  - New **Step 1.5 — Surface feedback-rule conflicts** runs before generation begins; blocks until the user resolves any contradictions among active rules.
  - **Step 3.5 (Pre-generation sign-off gate)** extended: while waiting on a sub-85% gate, plan-level pushback triggers the candidate-learning watch defined in Step 6.5.
  - **Step 5 (Generate files)** now applies `[global]` plus `[{code}]` feedback rules as hard constraints on each language's output.
  - New **Step 6.5 — Watch follow-up turns** is the canonical spec for the candidate-learning prompt, file template, conflict-check-at-lock, scope inference, identity-fact handling, and the one-off vs. recurring classifier.
- `.claude/commands/update-profile.md`:
  - Step 1 now also reads `input/feedback.md` and runs the same conflict surfacing as Step 1.5 of `apply-for-job.md`.
  - Step 3 now honors `[global]`-tagged feedback rules during recruiter-lens extraction; feedback rules override recruiter-lens defaults when they conflict.
  - New **Step 5.5 — Watch follow-up turns** reuses the canonical Step 6.5 logic from `apply-for-job.md`, scoped to ingestion corrections.
- `.codex/commands/apply-for-job.md`: mirrored the Claude Code changes — Step 2 reads feedback and surfaces conflicts; Step 10.5 captures candidate learnings during sign-off; Step 12 applies feedback rules during generation; new Step 14 is the post-generation watch.
- `.codex/commands/update-profile.md`: mirrored — Step 2 reads feedback and surfaces conflicts; Step 5 honors `[global]` rules; new Step 8 is the post-ingestion watch.

### Notes
- `input/feedback.md` is gitignored alongside the rest of `input/` and is created lazily the first time the user locks a candidate learning.
- The format for the feedback file (template, `R###` block schema, supersede markers) lives in `.claude/commands/apply-for-job.md` Step 6.5 — that file is canonical.
- Codex sessions and Claude Code sessions read the same `input/feedback.md`, so rules locked in one tool are honored in the other.
