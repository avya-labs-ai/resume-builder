# Language Rules — English (en)

## Language metadata
code: en
name: English
length_ratio: 1.00          # baseline; other languages are measured relative to this

## ATS-safe section headings

| Section | Primary heading | Acceptable alternate |
|---------|----------------|----------------------|
| Summary / Profile | Summary | Professional Summary |
| Skills | Skills | Technical Skills |
| Experience | Experience | Professional Experience |
| Projects | Projects | — |
| Education | Education | — |
| Languages | Languages | — |
| Certifications | Certifications | Licenses & Certifications |

## Date format

- CV date ranges: `Mon YYYY - Mon YYYY` (e.g. `Sep 2022 - Mar 2025`)
- Cover letter date: `17 May 2026` or `May 17, 2026`
- Use ASCII hyphens ( - ) with spaces in date ranges — no en-dashes or em-dashes.

## Month abbreviations

Jan Feb Mar Apr May Jun Jul Aug Sep Oct Nov Dec

## Cover letter conventions

- Salutation (no name): `Dear Hiring Team,`
- Salutation (specific name): `Dear Ms. Smith,` / `Dear Mr. Jones,`
- Subject line label: `Re:`
- Closing: `Kind regards,`
- Signature block: Name / Title, Company / Phone | Email

## LaTeX special character escapes

Standard ASCII — no extra escapes beyond the universal set:
- `&` → `\&`
- `%` → `\%`
- `$` → `\$`
- `#` → `\#`
- `_` → `\_`
- `{` → `\{`
- `}` → `\}`
- `~` → `\textasciitilde{}`
- `^` → `\textasciicircum{}`

## Phrasing notes

- First-person active voice is standard: "Led the migration", "Designed the system"
- Avoid passive where possible — ATS and human readers both prefer active
- Quantify achievements where possible: %, $, time saved, team size
- UK/US spelling: pick one and be consistent; adapt to the company's locale if known

## Known ATS gotchas

- Avoid "smart quotes" (`"` `"`) — use straight quotes (`"`)
- Avoid ligatures (fi, fl, ff) — Computer Modern in LaTeX handles these safely
- No emoji, icons, or Font Awesome glyphs — ATS skips them and may corrupt surrounding text
