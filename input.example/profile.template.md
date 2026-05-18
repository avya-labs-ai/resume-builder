---
identity:
  full_name: "{{full_name}}"
  file_slug: "{{file_slug}}"         # e.g. "JaneDoe" — used in output filenames; auto-derived during onboarding
languages:
  - code: en
    name: English
  # Add more languages here. The onboarding skill will generate lang_rules/{code}.md for each.
  # - code: de
  #   name: German
  # - code: fr
  #   name: French
  # - code: es
  #   name: Spanish
primary_language: en                  # source language; all others are translated from this
---

# Profile — {{full_name}}
> Consolidated source of truth for the resume builder. Used by `/apply-for-job` alongside
> `proj_refs/` (active project detail) and `input/resume.tex` (LaTeX CV template).

---

## Identity & Framing Rules for the Agent

<!--
Choose one framing and delete the others. Add any nuances relevant to your situation.
-->

**{{full_name}} identifies as [Founder / Independent Consultant / Employee] first.**

- **Default CV framing:** "{{primary_title}}, {{company_or_role}}"
- **Downshift to "{{alternative_title}}" only when:** [describe JD types where the primary framing may read negatively]
- **Never** [state any fabrication guardrail relevant to your situation]

---

## Name
{{full_name}}

## Address (for letters / CV header)
{{street_address}}, {{city_postal}}, {{country}}

## Contact
- **Email:** {{email}}
- **Phone:** {{phone}}
- **LinkedIn:** {{linkedin_url}}
- **Portfolio:** {{portfolio_url}}  ← delete this line if not applicable

---

## Current Role
**{{primary_title}}, {{company_or_role}}** — {{location}} | {{start_month_year}} – Present

[2–4 sentences describing what you do in this role: scope, verticals, customers, technology focus.]

Day-to-day work:
- [Bullet 1 — key responsibility]
- [Bullet 2 — key responsibility]
- [Bullet 3 — key responsibility]

---

## Headline Summary
[1–2 sentence positioning statement. Lead with your strongest differentiator: years of experience, domain, and what makes your profile unusual. This becomes the CV Professional Summary seed.]

---

## Key Technical Skills

### [Skill Category 1 — e.g. "Agentic AI & LLM Systems"]
- [Skill or technology]
- [Skill or technology]
- [Skill or technology]

### [Skill Category 2 — e.g. "Programming & Development"]
- [Language / framework / tool]
- [Language / framework / tool]

### [Skill Category 3]
- [Skill]
- [Skill]

<!--
Add as many categories as needed. The agent will condense these into ATS-friendly groups.
Typical categories: Languages & Frameworks, Cloud & Infrastructure, Domain Expertise,
Toolchain, Engineering Governance, etc.
-->

---

## Professional Experience

### {{company_1}} | {{title_1}} | {{start_date_1}} – {{end_date_1}} | {{location_1}}
- [Achievement bullet — lead with a verb, include a metric where possible]
- [Achievement bullet]
- [Achievement bullet]

### {{company_2}} | {{title_2}} | {{start_date_2}} – {{end_date_2}} | {{location_2}}
- [Achievement bullet]
- [Achievement bullet]

<!--
Continue adding roles. For roles older than 7 years, one sentence is usually enough.
The agent condenses older roles automatically to keep the CV to one A4 page.
-->

---

## Notable Achievements

**{{company_or_role}} ({{year}})**
- [Major achievement — quantified if possible]
- [Major achievement]

**{{earlier_role_or_company}}**
- [Major achievement]

---

## Domain Expertise
- **[Domain 1]** — [brief description]
- **[Domain 2]** — [brief description]
- **[Domain 3]** — [brief description]

---

## Career Goals
- [Primary goal — what you want to be doing in 1–2 years]
- [Secondary goal or constraint — e.g. open to roles that allow X in parallel]
- [Near-term milestone]

---

## Education
- **{{degree_1}}** — {{institution_1}}, {{country_1}} | {{start_year_1}} – {{end_year_1}}
- **{{degree_2}}** — {{institution_2}}, {{country_2}} | {{start_year_2}} – {{end_year_2}}

---

## Languages (spoken)
<!--
List your spoken languages with CEFR levels (A1–C2) or Native.
These appear in the CV Languages section — separate from the output document languages above.
-->
- {{language_1}} — {{level_1}}  ← e.g. English — C1, or English — Native
- {{language_2}} — {{level_2}}
- {{language_3}} — {{level_3}}

---

## Constraints & Preferences
- **Location:** [City/region. Open to X, Y, remote.]
- **Availability:** [Any restrictions — part-time, specific hours, notice period.]
- **Salary range:** [Optional — only include if you want the agent to reference it in cover letters.]
- **Visa / work authorisation:** [e.g. "EU citizen, no sponsorship needed"]
