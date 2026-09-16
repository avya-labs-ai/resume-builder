---
name: red-team-review
description: |
  Two-stage adversarial review of the documents produced by /apply-for-job. Invoked
  automatically at the end of generation — Stage 1 after the primary-language (English)
  CV and cover letter pass the Step 5.5 self-review, Stage 2 after the non-primary
  (e.g. German) CV and cover letter are generated. Stage 1 scrutinizes the English CV
  and cover letter as a veteran headhunter reading against the JD and iterates exactly
  five times, editing in place, until they are in their best truthful form. Stage 2
  verifies each non-primary document is a faithful one-to-one translation of the final
  English document it was derived from. Also runs on explicit request: "red team the
  documents", "red team this application", "run the red team review".
---

# Red-Team Review — adversarial QA for /apply-for-job output

This skill is the adversarial layer that runs **on top of** the Step 5.5 self-review in
`/apply-for-job`. Step 5.5 is a quick pass/fail gate; this skill is the deep, iterative
scrutiny that follows it. It never fabricates content to score better — the profile
truthfulness boundary and every active rule in `input/feedback.md` are hard constraints
throughout.

There are two stages, invoked at two different points in the `/apply-for-job` flow.

- **Stage 1 (English / primary-language deep red-team)** runs after the primary-language
  CV and cover letter have been written and have passed the Step 5.5 self-review, and
  **before** any non-primary language is generated.
- **Stage 2 (non-primary translation-fidelity red-team)** runs after each non-primary
  CV and cover letter have been generated from the finalized primary-language documents.

Both stages **edit the `.tex` files in place**. Both stages print their report **to chat
only** — never write a report to disk, exactly like the gap analysis and the Step 5.5
self-review.

---

## Inputs (read before Stage 1)

Read or reuse from the current `/apply-for-job` session:

- The generated primary-language files in `output/Claude Code/{slug}/`:
  `Resume_{file_slug}_{primary}.tex` and `CoverLetter_{file_slug}_{primary}.tex`.
- The **Step 2.7 structured JD parse** (must-haves, nice-to-haves, core responsibilities,
  signals, verbatim keywords) — the canonical requirement list. Do not re-derive it; reuse it.
- The **Step 3 gap analysis** and the **Step 3.4 suitability score** (for which dimensions lost points).
- `input/profile.md` — the single source of truth for every claim. Nothing may exceed it.
- `input/feedback.md` active rules — apply all `[global]` rules plus rules tagged for the
  language being reviewed. These are hard constraints on framing, wording, anonymization,
  canonical project names, ordering, length, and structure.
- `lang_rules/{primary}.md` — section headings, escaping, and format rules for the primary language.

If the primary-language files do not exist yet, stop and say so — Stage 1 cannot run before generation.

---

## Stage 1 — English / primary-language deep red-team (exactly 5 iterations)

### Persona

Adopt the persona of a **veteran headhunter and hiring manager** for this exact role: someone
who has the JD parse open beside the documents, reads the JD's requirement blocks literally,
has read ten thousand CVs, defaults to doubt, and is actively looking for reasons to screen the
candidate out. You are not the candidate's advocate. You are the last skeptical reader before a
real recruiter sees it. Your job across five passes is to leave the CV and cover letter in the
strongest **truthful** form the profile can support against this specific JD.

### The five passes

Run **exactly five** full passes — do not stop early even if a pass looks clean, and do not stop
late. Each pass is a **complete read** of both documents as the veteran recruiter. To guarantee
breadth, each pass leads with a different primary lens, but in every pass you may act on any
weakness you notice, not only the lens of the moment.

- **Pass 1 — Requirement coverage.** Walk the JD parse. Is every **must-have** either evidenced
  in the CV or consciously and defensibly omitted? Is each of the JD's **core responsibility /
  task blocks** visibly answered somewhere in the CV or cover letter? Are **nice-to-haves** picked
  up where truthful? Flag any requirement the recruiter would tick "not shown".
- **Pass 2 — Evidence and credibility.** Every Experience and Projects bullet must lead with an
  outcome and carry the highest truthful evidence tier available in the profile (measured outcome >
  countable output > characterized magnitude > named specificity). Kill naked duty statements.
  Run the claim audit: no metric, ownership claim, project status, or language level exceeds the
  profile (B2 is never "fluent"; "approximately ten years" is never "10+"). Every number must
  trace to the profile.
- **Pass 3 — Positioning and keywords.** Would a stranger skimming for six seconds say
  "the consultant/engineer this JD wants", or "generic automation person"? Check the summary and
  top role carry the profile's consultant-who-ships framing. Confirm exact JD keywords appear
  verbatim where truthful. Confirm anonymization (feedback R025) and **canonical project names**
  (feedback R026) are exactly right, and ordering rules (e.g. Resume Builder last, R030) hold.
- **Pass 4 — The cover letter as its own document.** Enforce the one-story rule: the experience
  paragraph tells ONE JD-relevant story in depth, never a list of 3+ projects. Confirm the
  objection paragraph is present and honest whenever the Step 3.4 Seniority-fit dimension lost
  points or an obvious red flag exists (founder-to-employee, overqualification, pivot, short stint),
  drawn only from the profile's standing motivation (feedback R013) — never fabricated. Confirm the
  "why us" paragraph cites concrete company research (Step 2.5), not generic praise. Test the hook
  and the close for punch. Check for internal contradictions with the CV (dates, titles, tenure).
- **Pass 5 — Format, ATS, length, final skim.** Enforce the em-dash ban (plain hyphens only),
  `$\diamond$` separators in grouped lists, complete LaTeX escaping (including `\_` in code/file
  names and language umlaut/accent escapes), spelled-out URLs, ATS-safe section headings from
  `lang_rules/{code}.md`, one-column layout, plain-English bullets (feedback R032, no version
  numbers, no unexplained jargon), and the length target (approximately 1–1.5 A4 pages, hard max
  two). Finish with a six-second recruiter skim: what are the three things that land, and is the
  screen-out reason gone?

### Editing rules (apply on every pass)

- **Edit the `.tex` files in place.** When a pass finds a weakness, fix it in the file immediately,
  then continue. Do not defer edits to a later pass or to the user.
- **Truthfulness is absolute.** Adaptation means emphasize, reorder, reframe. Never invent
  experience, skills, metrics, tools, or outcomes to close a JD gap or lift the apparent match.
  A real gap stays a gap — surface it in the residual list instead of papering over it. If a
  stronger claim would require a fact not in `input/profile.md`, do not make it.
- **Obey every active `input/feedback.md` rule** and the profile's Identity & Framing Rules. The
  red-team may not override a locked rule; if a rule seems to fight the JD, note it in residuals
  rather than breaking it.
- **Do not touch source files** (`input/resume.tex`, `input/profile.md`, `input.example/`,
  `lang_rules/`) and do not modify `JobDescription.md` or `Clarifications.md` here.
- **Preserve the LaTeX class, packages, and structure** of the generated files; change content, not
  the document scaffold.

### Stage 1 output (to chat only) and hand-off

After the fifth pass, print a compact per-iteration change log and the honest residuals:

```
## Red-Team Stage 1 — {slug} (English)

Pass 1 (requirement coverage): <what was found and changed, or "no material change">
Pass 2 (evidence/credibility):  <...>
Pass 3 (positioning/keywords):  <...>
Pass 4 (cover letter):          <...>
Pass 5 (format/ATS/length):     <...>

Residual weaknesses (cannot be fixed without exceeding the profile):
- <honest screen-out risks that remain, e.g. missing hands-on X, language level, tenure> — or "none"

Recruiter verdict: <one or two sentences: would this pass a first screen, and why>
```

Then **pause and hand the polished English documents back to the user for sign-off** (this preserves
feedback R031). Do not generate any non-primary language until the user gives an explicit go. If the
user pushes back with corrections, apply them, run the Step 6.5 candidate-learning watch as usual,
and re-present — do not silently restart all five passes unless the user asks.

---

## Stage 2 — non-primary translation-fidelity red-team

Runs once each non-primary CV and cover letter have been generated (after the user's Stage 1
sign-off). The **source of truth is the finalized primary-language document**, not the profile:
the non-primary files must be a faithful one-to-one rendering of the English the user approved,
re-expressed in natural target-language business register.

### Persona

Adopt the persona of a **bilingual senior recruiter and professional translator**, fluent in both
languages, who will reject a translation that drifts from the source, omits a claim, adds a claim,
mistranslates a term of art, or reads as machine-translated rather than as native business prose.

### Fidelity checklist (per non-primary document)

Compare the non-primary `.tex` against its finalized primary-language counterpart:

1. **Content parity — nothing added, nothing dropped.** Every section, role, bullet, project,
   metric, date, and claim in the English document has a faithful equivalent in the translation,
   and the translation introduces no claim, number, or qualifier absent from the English. This is
   the primary check: a translation that is "better" than the source has drifted and must be pulled
   back to parity.
2. **Meaning preserved, not literal.** Idioms and framing are rendered naturally per
   `lang_rules/{code}.md` phrasing notes (e.g. German is not English with German words), while the
   substance stays identical. Terms of art the JD uses are kept as the JD uses them.
3. **Canonical project names** are translated per feedback R026 (natural target-language rendering
   of the English canonical name; English canonical name remains the source), consistently across
   CV and cover letter.
4. **Language-rule adherence** from `lang_rules/{code}.md`: ATS-safe section headings, date format
   and month abbreviations, cover-letter salutation / subject label / closing, and any language
   convention (for German: `KI` for AI, city+date placement, `Betreff:` line).
5. **Escaping and encoding.** Every special character escaped for the target language (for German:
   umlauts `\"a` etc. and `\ss{}`), plus the universal `\&`, `\%`, `\_` set. No unescaped character
   that would break compilation.
6. **Format parity.** Em-dash ban holds (plain hyphens only), `$\diamond$` separators preserved,
   one-column ATS layout preserved, and length obeys the `length_ratio` from `lang_rules/{code}.md`
   (tighten filler so the translated document stays comparably substantive, not bloated).
7. **Claim audit in the target language.** Language levels and every other claim match the English
   exactly (B2-C1 stays B2-C1, never upgraded in translation).

### Editing rules

- **Edit the non-primary `.tex` files in place** to fix any divergence from the source or any
  language-rule violation.
- **Do not re-derive from the profile** and do not re-run the full Stage 1 recruiter passes on the
  translation — the English document already passed that. Stage 2 is fidelity and language quality only.
- If Stage 2 uncovers a substantive problem that actually originates in the **English** source
  (a factual error, not a translation issue), fix it in the English file too and tell the user both
  files changed, so the pair stays in sync.

### Stage 2 output (to chat only)

```
## Red-Team Stage 2 — {slug} ({language})

Content parity     PASS/FAIL  (added: ... | dropped: ... | or "1:1")
Meaning/naturalness PASS/FAIL  (any literalisms fixed)
Canonical names    PASS/FAIL
Language rules      PASS/FAIL  (headings, dates, salutation/closing, KI, city+date)
Escaping/encoding   PASS/FAIL
Format/length       PASS/FAIL  (em-dash-free, diamonds, length ratio)
Claim audit         PASS/FAIL

Fixes applied: <short list, or "none needed">
Residual issues: none / <state plainly>
```

Repeat Stage 2 for every configured non-primary language.

---

## Constraints (both stages)

- Reports are **terminal-only**; never written to disk.
- **Never fabricate** to improve a score or a match. Real gaps are surfaced, not hidden.
- Respect **all** active `input/feedback.md` rules and the profile Identity & Framing Rules.
- Edit only the generated `output/Claude Code/{slug}/*.tex` files (Stage 2 may also correct the
  English source file if it finds a genuine factual error there). Never edit `input/` or `lang_rules/`.
- Preserve each document's LaTeX scaffold; change content and wording, not the class or packages.
