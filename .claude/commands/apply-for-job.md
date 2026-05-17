---
description: Tailor CV + cover letter (EN + DE) against a job description. Reads input/resume.tex and input/profile.md, generates 4 LaTeX files in output/{company}-{role}/.
argument-hint: "[optional: paste JD inline, otherwise you'll be prompted]"
allowed-tools: Read, Write, Bash
---

# Apply for Job — Resume + Cover Letter Generator

You are an expert career coach, resume writer, and bilingual (English/German) translator. Your task is to take a job description and tailor the user's CV and cover letter to it, in both English and German, and save them as LaTeX files.

## Inputs

**$ARGUMENTS contains the job description (may be empty).**

### Step 1 — Read context files

Read these files using the Read tool:

**Required:**
- `input/resume.tex` — the user's current CV in LaTeX format. **Preserve its document class, packages, and structural template**; only adapt content.
- `input/profile.md` — consolidated personal/career profile: identity framing rules, full career history, skills, achievements, constraints, contact details.

**Active project context (read all files in `proj_refs/`):**
Use Bash to list them first, then read each one:
```bash
ls proj_refs/*.md 2>/dev/null
```
Each file is a structured summary of one Avya Labs project built in 2026. They contain engineering depth (PRD discipline, ADRs, WAT framework, stack decisions, current status) that `input/profile.md` only summarises. When drafting the CV and cover letter:
- Select the 1–2 most JD-relevant projects from `proj_refs/` to feature or reference
- Use the specific stack, methodology, and outcome language from `proj_refs/` (not just the high-level summary in `profile.md`)
- Prefer `proj_refs/` detail over `profile.md` summaries when they differ

**Note on identity framing (from `input/profile.md`):**
- Default current-role label on CVs: **"Founder, Avya Labs"**
- Downshift to "Independent AI Consultant — Avya Labs" only when the JD is for a Big-4 / large-enterprise in-house position where a founder label may read as a flight-risk signal
- Never present Avya Labs as a revenue-generating external consultancy — it is pre-revenue

If `input/resume.tex` or `input/profile.md` is missing, stop and tell the user clearly what's missing and where to put it.

### Step 2 — Get the job description

- If `$ARGUMENTS` is non-empty → use it as the job description.
- If `$ARGUMENTS` is empty → ask the user: "Paste the job description here. I'll wait for your message." Then wait for their next message and use that as the JD.

## Workflow

### Step 3 — Gap analysis (terminal only, not saved)

Compare the JD against the CV + profile. Print a concise analysis to the chat:

```
## Gap Analysis

**Strong matches**
- [skill/experience in CV that maps directly to JD requirement]
- ...

**Gaps (missing or weak in your CV)**
- [JD requirement not visible in current CV] — suggestion: [how to address]
- ...

**Recommendations for adaptation**
- [What to emphasize / reorder / reframe]
- ...
```

Be honest about gaps. The point is to flag them, not paper over them.

### Step 4 — Derive folder name

Generate a slug: `{company-slug}-{role-slug}`, lowercase, hyphenated, max 40 characters total, no special characters.

Examples:
- "Senior Backend Engineer at Acme GmbH" → `acme-senior-backend-engineer`
- "ML Platform Lead — Helsing" → `helsing-ml-platform-lead`

Use Bash to create the folder:
```bash
mkdir -p "output/{folder-name}"
```

### Step 5 — Generate the 4 LaTeX files

Generate **complete, compilable `.tex` files** (with `\documentclass`, packages, `\begin{document}` … `\end{document}`).

**Rules for all files:**
- Escape LaTeX special characters in any free-text content (`&`, `%`, `$`, `#`, `_`, `{`, `}`, `~`, `^`, `\`). This includes underscores inside code/file names — write `quality\_checker`, `node\_builders`, `book\_expenses`, etc. An unescaped `_` in text mode causes a hard compile error.
- **Skills bullets must be concise — no inline counts or parenthetical lists.** Specific numbers (e.g. "38 unit tests, 5 integration tests") belong in the Experience section where they earned. In Skills, write "formal test plans (unit, integration, E2E, edge-case)" — not the raw numbers. Overly long skills bullets cause overfull hbox warnings and push the CV off one page.
- Be truthful to the profile + original CV. Do not invent experience or skills.
- Emphasize, reorder, and reframe — don't fabricate.

**ATS-friendly conventions (HARD REQUIREMENT for all CVs and cover letters):**

Applicant Tracking Systems parse the resulting PDF. To survive parsing:

**CV header format (HARD REQUIREMENT):**
- `\name{Girish Mohan}` — name only
- One `\address{}` containing phone, email, and LinkedIn URL only: `+49 176 47372783 \\ girish\_mohan@live.com \\ \href{https://www.linkedin.com/in/giri91/}{linkedin.com/in/giri91}`
- **No physical address line.** Do NOT add a second `\address{Floridaring 56 \\ ...}`.
- **No portfolio link** in the CV header.

**Separator convention (HARD REQUIREMENT):**
- Use `$\diamond$` as the separator in all single-line grouped lists (Skills bullets, Languages line). Do NOT use ` | ` or commas.

**No content repetition across sections (HARD REQUIREMENT):**
- **Skills** = methodology and tooling vocabulary only. No specific artifact names, no test counts, no file names.
- **Experience** = specific artifacts, metrics, and deliverables (test counts, file names, ADR counts, frameworks applied).
- **Projects** = what was built (product/tool description, stack). Not how it was validated — that stays in Experience.

- **No math-mode symbols in body text for separators — EXCEPTION: `$\diamond$` is the approved separator and must be used.** Other math-mode glyphs (`$\circ$`, `$\bullet$`, `$+$` in phone numbers) must be avoided. Phone number `+` must be a plain `+`.
- **Spell out URLs in plain text.** Do NOT hide URLs behind link text with `\href{long-url}{LinkedIn}` — ATS sees only the word "LinkedIn". Use one of:
  - `LinkedIn: linkedin.com/in/giri91` (plain text)
  - `\href{https://linkedin.com/in/giri91}{linkedin.com/in/giri91}` (URL as link text)
  Same applies to portfolio links: use the readable URL, not a hidden hyperlink.
- **Use standard, recognisable section headings.** ATS keyword-matches these:
  - English: `Summary` (or `Professional Summary`), `Skills` (or `Technical Skills`), `Experience` (or `Professional Experience`), `Education`, `Languages`, `Certifications`. Avoid creative names like `Expertise` (rename to `Skills`), `My Journey`, `Hustle`, etc.
  - German: `Profil` / `Zusammenfassung`, `Kenntnisse` / `F\"ahigkeiten`, `Berufserfahrung` / `Berufliche Erfahrung`, `Ausbildung`, `Sprachen`, `Zertifizierungen`. Avoid creative German names.
- **One column. No tables, text boxes, multi-column layouts, or images** for ATS-critical content. Tables for the `Languages` line are OK only if the layout still flows top-to-bottom and the cell contents are plain text. If `multicol` is used in the original template, drop it for ATS-critical content sections.
- **Plain hyphens in dates.** Use `Sep 2022 - Mar 2025` (ASCII hyphen with spaces), not en-dashes (`–`) or em-dashes (`—`). Same for date ranges in German (`Sep. 2022 - M\"arz 2025`).
- **Contact info in the body, not in headers/footers.** The `\address{}` macro in the existing template puts contact info at the top of the page body (not in a true `\fancyhead`), so it is fine — but never move contact info into a real PDF header/footer macro.
- **Standard fonts.** Default Computer Modern / Latin Modern / `lmodern` / `helvet` / `times` are all ATS-safe. Avoid decorative fonts.
- **Include exact JD keywords verbatim** at least once where truthful. ATS scoring is keyword-driven. E.g., if the JD says "metamorphic testing" and you have related experience, use that exact phrase rather than a synonym.
- **No icons / Font Awesome glyphs / emoji.** Even if compiled, ATS will skip them and the surrounding text may be mangled.
- **Cover letters follow the same rules.** Plain section breaks, ASCII hyphens, spelled-out URLs in the signature block.

When in doubt, prefer ATS-readable over visually clever.

**Files to write (use the Write tool, one per file):**

1. **`output/{folder-name}/Resume_GirishMohan_en.tex`** — English CV
   - Preserve the LaTeX class, packages, and section structure of `input/resume.tex`
   - Reorder/rewrite content so the most JD-relevant experience and skills come first
   - **HARD CONSTRAINT: must fit on a single A4 page when compiled.**
     Use the following tactics to enforce this:
     - **Professional summary:** 3–4 lines maximum.
     - **Skills section:** 4–6 grouped bullets maximum; pack related items into one line per group using ` | ` (plain ASCII pipe) separators — never `$\diamond$` or other math-mode glyphs (see ATS rules below).
     - **Current/most-relevant role:** at most 4 bullets, each 1–2 lines.
     - **Mid-career roles (3–7 years back):** at most 2–3 bullets, each 1–2 lines.
     - **Older roles (>7 years back):** condense to a single line (just title + 1 sentence). Do not break out programme-by-programme detail in the CV body — that belongs on LinkedIn / portfolio.
     - **No "Hobbies" section** unless the JD explicitly asks for cultural fit signals.
     - **Languages:** one line.
     - **Font sizes:** keep body bullets at `\fontsize{10pt}{12pt}\selectfont` (do not enlarge).
     - If after a draft the content still looks like it would overflow, drop the lowest-relevance bullets first — never shrink the font further.
   - The original `input/resume.tex` files are already structured to fit one page; staying close to their density is a good sanity check.

2. **`output/{folder-name}/CoverLetter_GirishMohan_en.tex`** — English cover letter
   - Use `\documentclass{letter}` (or a clean article-based letter layout if the original CV's style suggests it)
   - Address the hiring team / company by name (from the JD if available; otherwise "Hiring Team")
   - 3–4 paragraphs: hook + relevant experience + why this company + close
   - Sign-off with the user's name from `input/profile.md`

3. **`output/{folder-name}/Resume_GirishMohan_de.tex`** — German CV
   - Translate the English CV. Use **natural professional German**, not literal translation.
   - Localize: date formats (`DD.MM.YYYY`), section headings (Berufserfahrung, Ausbildung, Kenntnisse, etc.), conventional German CV phrasing.
   - Same LaTeX structure as `Resume_GirishMohan_en.tex`.
   - **HARD CONSTRAINT: must fit on a single A4 page when compiled.** Same tactics as `Resume_GirishMohan_en.tex`. German text typically runs ~10–15% longer than English — so tighten phrasing where needed (drop filler like "im Rahmen meiner T\"atigkeit" → just state the action) rather than dropping content that survives in the EN version.

**Projects section (include in both CVs, between Experience and Education):**
Select the 2–3 most JD-relevant projects from `proj_refs/`. For each, write ONE line describing what was built — stack, key output, notable architecture decision. Do NOT repeat validation methodology, test counts, or ADR details from the Experience section. Current Avya Labs projects available:
- **Avya Labs Website** (avya-labs.com) — production marketing site, two-lane post-booking architecture, same-origin proxies, GDPR-compliant. Stack: Vanilla JS, Tailwind, Vercel, Cal.com, VAPI, n8n.
- **Avya Bookkeeping Agent** — AI-orchestrated expense automation ingesting Gmail receipts, classifying vendors, archiving to Drive, writing to live Sheets dashboard. Multi-currency, crash-safe, idempotent. Stack: Python 3.11, Claude API, Google Workspace APIs.
- **Weavy Workflow Agent** — Claude Code skill generating production Weavy workflow JSON from plain-language descriptions. Knows 100+ AI models, 14 creative roles, 7 architecture patterns. Stack: Python, Claude Code.
German section heading: `Projekte`.

4. **`output/{folder-name}/CoverLetter_GirishMohan_de.tex`** — German cover letter
   - Translate the English cover letter into natural professional German.
   - Use German business letter conventions: `Sehr geehrte Damen und Herren,` (or specific name if known), `Mit freundlichen Grüßen` close, `Betreff:` line.
   - Localize the date format.

### Step 6 — Confirm completion

Print a summary:

```
✓ Done. Output saved to: output/{folder-name}/

Files created:
- Resume_GirishMohan_en.tex
- Resume_GirishMohan_de.tex
- CoverLetter_GirishMohan_en.tex
- CoverLetter_GirishMohan_de.tex

Next: compile them in your LaTeX editor.
```

## Constraints

- **Never modify** `input/resume.tex` or `input/profile.md` — they are read-only sources.
- **Always write 4 files**, even if some content is minimal.
- **One job description per run** → one output subfolder.
- If `output/{folder-name}/` already exists, overwrite the files (most recent run wins).
