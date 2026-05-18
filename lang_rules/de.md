# Language Rules — German (de)

## Language metadata
code: de
name: German
length_ratio: 1.12          # German text runs ~10–15% longer than English — tighten phrasing proactively

## ATS-safe section headings

| Section | Primary heading | Acceptable alternate |
|---------|----------------|----------------------|
| Summary / Profile | Profil | Zusammenfassung |
| Skills | Kenntnisse | Fähigkeiten |
| Experience | Berufserfahrung | Berufliche Erfahrung |
| Projects | Projekte | — |
| Education | Ausbildung | Bildung |
| Languages | Sprachen | — |
| Certifications | Zertifizierungen | Zertifikate |

## Date format

- CV date ranges: `Sep. 2022 - Mrz. 2025` (abbreviated month with trailing period, ASCII hyphen with spaces)
- Cover letter date: `17. Mai 2026`
- Use ASCII hyphens ( - ) with spaces in date ranges — no en-dashes or em-dashes.

## Month abbreviations

Jan. Feb. Mrz. Apr. Mai Jun. Jul. Aug. Sep. Okt. Nov. Dez.

## Cover letter conventions

- Salutation (no name): `Sehr geehrte Damen und Herren,`
- Salutation (specific woman): `Sehr geehrte Frau Smith,`
- Salutation (specific man): `Sehr geehrter Herr Jones,`
- Subject line label: `Betreff:` (bold, on its own line above the salutation)
- Closing: `Mit freundlichen Grüßen`
- Signature block: Name / Title, Company / Phone | Email
- German cover letters traditionally include: city + date on the right (`Kornwestheim, 17. Mai 2026`), followed by the `Betreff:` line, then salutation

## LaTeX special character escapes

| Character | LaTeX escape |
|-----------|-------------|
| ä | `\"a` |
| ö | `\"o` |
| ü | `\"u` |
| Ä | `\"A` |
| Ö | `\"O` |
| Ü | `\"U` |
| ß | `\ss{}` |
| é | `\'e` |
| è | `\`e` |
| ê | `\^e` |

Plus all standard ASCII escapes (`\&`, `\%`, `\$`, `\#`, `\_`).

## Phrasing notes

- Omit the subject "I" — German professional writing often omits the first-person subject: "Verantwortete die Migration" not "Ich verantwortete die Migration"
- Use nominalisations for impact: "Entwicklung und Implementierung von..." (natural in German; would be wordy in English)
- Avoid literal translation — rephrase for natural German business register
- Drop filler like "im Rahmen meiner Tätigkeit" → just state the action directly
- Compound nouns are correct German: "Teststrategieentwicklung", "Anforderungsanalyse" — don't hyphenate unless very long
- Quantify achievements the same way as English: %, €, Zeitersparnis, Teamgröße

## Phrasing for common CV phrases

| English | German (preferred) |
|---------|-------------------|
| Led the development of | Verantwortete die Entwicklung von |
| Designed and implemented | Konzipierte und implementierte |
| Collaborated with stakeholders | Arbeitete eng mit Stakeholdern zusammen |
| Improved efficiency by X% | Steigerte die Effizienz um X% |
| Managed a team of N | Leitete ein Team von N Mitarbeitenden |
| Delivered on time and within budget | Fristgerecht und im Budgetrahmen abgeschlossen |

## Known ATS gotchas

- German ATS systems handle umlauts (ä, ö, ü) well in PDF-to-text extraction — use them; don't substitute ae/oe/ue
- ß is safe; use it where correct rather than ss
- Avoid fancy LaTeX fonts that rasterise umlauts as glyphs — Computer Modern / lmodern both handle umlauts correctly
- Some older German ATS systems scan for German section keyword variants — always include `Berufserfahrung` (not just `Erfahrung`) and `Kenntnisse` (not just `Skills`)
