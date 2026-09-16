# Changelog

All notable structural changes to the Resume Builder agent itself — `CLAUDE.md`,
`AGENTS.md`, slash commands under `.claude/commands/` and `.codex/commands/`,
skills under `.claude/skills/`, language rule templates, `resources/`, and
`.gitignore`. User data files (`input/`, `proj_refs/`, `output/`) and
documents under `docs/` are intentionally excluded — this log tracks the
agent's own evolution, not the content it produces.

Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [2026-08-17]

### Changed

- Changed the default CV identity title in `.claude/commands/apply-for-job.md` and `.claude/commands/gap-analysis.md` from "Independent AI Consultant" to "AI Consultant", per new feedback rule R036 which supersedes R007.

## [2026-07-22]

### Changed

- Replaced the project-wide single-page CV mandate with a substance-first target of approximately 1-1.5 A4 pages across the canonical Claude workflow, Codex wrapper, `CLAUDE.md`, `AGENTS.md`, README, and onboarding profile template.
- Updated CV generation guidance so a deliberately structured second physical page is acceptable, selected projects receive enough space to show problem, judgement, architecture, and outcome, and strong evidence or readable spacing is never sacrificed merely to force one page.

## [2026-07-16]

### Added

- Added an append-only `Clarifications.md` artifact to the canonical Claude and Codex application workflows. Each application folder now records application-related user questions, the clarification given, and the reasoning or interview implication while preserving existing entries across reruns.
- `.gitignore`: added `portfolio/` to the user-data ignore block. The directory holds the user's generated portfolio deck (HTML + PDF with personal contact data), which stays on the user's machine like `input/` and `output/`.

### Changed

- Updated `CLAUDE.md` and `AGENTS.md` so the application output contract, operating rules, and verification checklist include the per-application clarification log.

## [2026-07-09]

### Added
- `.claude/commands/apply-for-job.md`: **Bullet-craft rules** (new hard-requirement block under "Rules for ALL generated files"). Every Experience/Projects bullet must be outcome-first (accomplished X, evidenced by Y, by doing Z) and carry the highest truthful evidence tier available in the profile: measured outcome > countable output > characterized magnitude > named specificity. Bans naked duty statements ("Responsible for", "Worked on", etc.), comma-chained mega-bullets (>3 items), and repeated leading verbs within a role. Mandates harvesting metrics from the profile's Headline Summary / Notable Achievements / Notable Projects before drafting, and that the summary + top role always read as the profile's consultant-who-ships framing. Numbers not present in the profile are never invented or estimated.
- `.claude/commands/apply-for-job.md`: **Step 5.5 — Post-generation self-review** (mandatory, chat-only). After the `.tex` files are written, the agent re-adopts the skeptical-recruiter persona and runs six pass/fail checks on the primary-language output: JD must-have coverage, verbatim keyword coverage (with an included/omitted report), evidence audit against the bullet-craft hierarchy, claim audit (nothing may exceed the profile, e.g. B2 is never "fluent"), consultant-who-ships positioning, and format. Failed checks trigger a revise-and-recheck loop (max 3 iterations); residual failures are reported plainly, never papered over. A numeric "ATS score" is explicitly forbidden — there is no ground truth for one and self-graded numbers inflate; checks are binary instead. Non-primary languages get a reduced pass (claim audit + format) verifying translations preserve the revisions.

- `.claude/commands/apply-for-job.md`: **Step 2.7 — Structured JD parse** (chat-only, never saved). Before the gap analysis, the JD is broken into must-haves (only explicitly required items, quoting the JD's phrasing), nice-to-haves, core responsibilities, and signals (language/tone, seniority level, verbatim keywords). Ambiguous items demote to nice-to-have. The parse is the canonical requirement list downstream: Step 3 gap analysis compares against it, the Step 3.4 must-have dimension (40%) scores against exactly this list, and the keyword list feeds the Step 5.5 keyword-coverage check. User corrections to the classification are re-printed and used downstream.
- `.claude/commands/apply-for-job.md`: **Cover letter one-story rule + objection paragraph** (Cover letter structure block). The experience paragraph now tells ONE JD-relevant story in depth (problem, decisions, build, outcome) instead of enumerating projects; 3+ project lists are banned. When the Step 3.4 Seniority-fit dimension loses points or an obvious red flag exists (overqualification, career pivot, short recent stint), the letter must name and defuse the objection in 1-2 sentences — asking the user for the real motivation when unclear, never fabricating one. Step 5.5 self-review gained a matching 7th check (**Cover letter**) and a report line.

### Changed
- `.codex/commands/apply-for-job.md`: mirrored all additions — new procedure steps 9.5 (JD parse) and 12.5 (self-review), plus Hard Rules entries for bullet craft, the one-story rule, and the objection paragraph, all deferring to the canonical spec in `.claude/commands/apply-for-job.md`.
- `.claude/commands/gap-analysis.md`: added the same Step 2.7 structured JD parse (referencing the canonical spec) so the standalone gap analysis and the apply-for-job gap analysis classify requirements identically; Step 3 now compares against the parse instead of re-deriving requirements.
- `CLAUDE.md` / `AGENTS.md`: added matching working-rule summaries (**Bullet craft: outcome-first, evidence-backed**, **Post-generation self-review is mandatory**, **Structured JD parse before gap analysis**, **Cover letter: one story + objection handling**) for Claude/Codex parity.

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
