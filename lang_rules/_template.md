# Language Rules Template
# Copy this file to lang_rules/{code}.md and fill in for your language.
# The onboarding skill generates this automatically for any chosen language.

## Language metadata
code: xx                    # ISO 639-1 code (e.g. fr, es, it, pt, nl)
name: Language Name         # Full name (e.g. French, Spanish)
length_ratio: 1.00          # Approximate word-count ratio vs. English (e.g. German ≈ 1.12, French ≈ 1.05)

## ATS-safe section headings

Use these exact headings (or the alternates listed) — ATS keyword-matches them:

| Section | Primary heading | Acceptable alternate |
|---------|----------------|----------------------|
| Summary / Profile | [translate "Summary"] | [translate "Professional Summary"] |
| Skills | [translate "Skills"] | [translate "Technical Skills"] |
| Experience | [translate "Experience"] | [translate "Professional Experience"] |
| Projects | [translate "Projects"] | — |
| Education | [translate "Education"] | — |
| Languages | [translate "Languages"] | — |
| Certifications | [translate "Certifications"] | — |

## Date format

- CV date ranges: [example: "Jan 2022 - Mar 2025" in language-appropriate month abbreviations]
- Cover letter date: [full date format, e.g. "15. Januar 2026" or "15 janvier 2026"]
- Use ASCII hyphens ( - ) with spaces in date ranges — no en-dashes or em-dashes.

## Month abbreviations

Jan Feb Mar Apr May Jun Jul Aug Sep Oct Nov Dec → [translate each]

## Cover letter conventions

- Salutation: [e.g. "Dear Hiring Team," / "Sehr geehrte Damen und Herren," / "Madame, Monsieur,"]
- Specific-name salutation: [e.g. "Dear Ms. Smith," / "Sehr geehrte Frau Smith,"]
- Subject line label: [e.g. "Re:" / "Betreff:" / "Objet :"]
- Closing: [e.g. "Kind regards," / "Mit freundlichen Grüßen" / "Cordialement,"]
- Signature block format: [Name on line 1, title/company on line 2, contact on line 3]

## LaTeX special character escapes

List any characters that need escaping beyond the standard ASCII set:
- [character] → [LaTeX escape]   e.g. ä → \"a   or   é → \'e

## Phrasing notes

- [Any language-specific CV conventions, e.g. "German CVs traditionally omit 'I' as subject"]
- [Formality register — e.g. "Spanish cover letters are more formal than English equivalents"]
- [Any localization notes — e.g. "French CVs often include date of birth and nationality; omit unless specifically requested"]

## Known ATS gotchas for this language

- [Any known parsing issues — e.g. ligatures, special punctuation, right-to-left scripts]
