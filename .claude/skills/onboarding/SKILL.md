---
name: onboarding
description: |
  Use to set up this Resume Builder repo for a new user. Triggers automatically at session start
  when the repo is unconfigured — no input/ directory, or input/profile.md lacks an identity
  front-matter block with a real name. Also triggers on explicit requests: "help me set this
  project up", "set up resume builder", "onboard me", "initialize the project",
  "I just cloned this repo", "configure for my profile", "set up my profile",
  "get me started", "how do I use this". Do NOT trigger if profile.md already has a populated
  identity.full_name that is not a {{placeholder}}.
---

# Resume Builder — Onboarding

You are setting up the Resume Builder for a new user. This is a one-time configuration process. When complete, the user can run `/apply-for-job` to generate tailored CVs and cover letters in their chosen languages.

## Step 1 — Detect state

Check whether onboarding is actually needed:

```bash
ls input/ 2>/dev/null && head -10 input/profile.md 2>/dev/null || echo "NOT_CONFIGURED"
```

- If `input/` does not exist → proceed with full onboarding.
- If `input/profile.md` exists and the front-matter contains `full_name` that is not `{{full_name}}` → the repo is already configured. Tell the user: "This repo is already set up for [name]. Run `/apply-for-job` to generate your CV, or delete `input/` and re-run this setup to start fresh." Then stop.
- If `input/profile.md` exists but still has `{{full_name}}` placeholders → proceed with onboarding (incomplete previous run).

## Step 2 — Bootstrap directories

Create the working directories from templates:

```bash
mkdir -p input "output/Claude Code" output/Codex proj_refs
cp input.example/profile.template.md input/profile.md
cp input.example/resume.template.tex input/resume.tex
```

Confirm to the user: "Created `input/`, `output/Claude Code/`, `output/Codex/`, and `proj_refs/` from templates. Now I'll interview you to fill in your details."

## Step 3 — Interview

Work through these sections in order. Use `AskUserQuestion` for structured choices; free-text prompts for open fields. Collect all answers before writing any files — you will write everything in Step 4.

### 3.0 Data collection route

Before collecting any profile data, ask the user how they want to provide their information:

> "How would you like to provide your professional information?
> **A) Interview** — I'll ask you questions and build your profile from your answers.
> **B) Import resume** — Provide an existing resume file (PDF, .tex, or .docx) and I'll extract the data from it, then fill any gaps interactively."

**If they choose Interview (A):** proceed through Steps 3.1–3.8 as normal.

**If they choose Import (B):**
- Ask: "Please paste the content of your resume, or describe how to access the file."
- Read the provided content and extract: identity, contact, career history (all roles with dates and bullets), education, skills, spoken languages, constraints/goals.
- For any field that is missing or unclear from the imported document, ask the user directly to fill the gap.
- If imported data conflicts with a user's verbal correction during gap-filling (e.g. resume says B1 German but user says B2), flag it explicitly: "Your resume says [X] but you mentioned [Y] — which is current?" Use their answer.
- After extraction and gap-filling, proceed directly to Step 3.9 (proj_refs) — skip Steps 3.1–3.8.

### 3.1 Output languages

Ask: "Which languages do you want your CV and cover letter generated in?"

Offer as multi-select: English, German, French, Spanish, Italian, Portuguese, Dutch.
Also offer "Other — I'll type the language name(s)".

Then ask which one is their primary language (the source; others will be translated from it).

Store as: `chosen_languages` (list of {code, name}) and `primary_language_code`.

### 3.2 Identity

Collect the following (all free-text):
- Full name (as it should appear on the CV)
- Email address
- Phone number (include country code, e.g. +49 176 …)
- LinkedIn URL (full URL: https://www.linkedin.com/in/…)
- Portfolio or personal site URL (optional — leave blank to omit from CV)
- Street address and city/postal (for cover letter headers; optional)

Derive `file_slug` from full name: remove spaces and special characters, CamelCase. e.g. "Jane Doe" → "JaneDoe", "María García" → "MariaGarcia".

### 3.3 Professional framing

Ask: "How do you want to be labelled on your CV — as a Founder, Independent Consultant, or Employee?"

Then: "What is your current company or role? (e.g. 'Founder, Acme AI' or 'Senior Engineer at TechCorp')"

Then: "Is there a situation where you'd want a different framing? For example, applying to large-enterprise jobs where 'Founder' might raise a flight-risk concern. If so, what alternative label would you use?"

### 3.4 Career history

Tell the user: "Let's go through your work experience, most recent first. I'll ask about each role and you can stop whenever you're done."

For each role, ask:
- Company name
- Job title
- Start date and end date (or "Present")
- Location
- 3–5 bullet points describing achievements (not just responsibilities — what did you actually deliver?)

After each role: "Add another role, or are you done?" Stop when the user says done.

**Project description discipline:** When a user describes a project or a key achievement within a role, always encourage elaboration. Ask for: what was built, the stack used, key technical decisions made, methodology applied, and measurable outcomes. If their answer is a single sentence or clearly thin, push back once:
> "That's a great start — can you tell me more? What specific technologies did you use, what was the hardest problem you solved, and what was the measurable outcome?"
If the second answer is still thin, push back one more time:
> "Any more detail you can share? Even rough numbers or a key decision helps make the CV more specific."
After two pushbacks, accept what they gave and move on. Never push a third time.

### 3.5 Education

For each degree/qualification, ask:
- Institution name and country
- Degree / qualification name
- Start and end year

After each: "Add another qualification, or are you done?"

### 3.6 Skills

Ask for skills grouped by category. Suggest common categories but let the user define their own:
- Programming languages and frameworks
- Tools and platforms
- Domain expertise
- Certifications and standards

For each category: name + comma-separated list of skills.

### 3.7 Spoken languages

Ask: "What languages do you speak, and at what level? Use CEFR levels (A1, A2, B1, B2, C1, C2) or 'Native'."

Collect as a list: e.g. English — C1, French — B2, Spanish — Native.

### 3.8 Constraints and goals

Ask:
- Location: where are you based, and what's your flexibility? (city, region, remote-only, etc.)
- Availability: any hours restrictions or start-date constraints?
- Career goal: in 1–2 sentences, what kind of role are you targeting?
- Salary range (optional): only collect if the user wants it included in cover letters.

### 3.9 Project references (wait before writing files)

Tell the user:
> "Before I write your profile, I want to make sure I have your project context too — this is what makes the CV specific and technical rather than generic.
>
> The `proj_refs/` folder is for detailed project write-ups — stack, methodology, key decisions, outcomes. The CV agent pulls from these when tailoring your application.
>
> You can:
> - **Describe a project now** and I'll write a structured summary to `proj_refs/` for you
> - **Drop existing files** into `proj_refs/` and tell me when you're done
> - **Skip for now** — you can add them later with `/update-profile`
>
> Take your time. Tell me when you're ready to continue."

Wait for the user to explicitly say they are done or want to skip before proceeding.

If the user describes a project: collect project name, what it does, stack, key decisions, outcomes, and methodology. Apply the same pushback discipline as in Step 3.4 — encourage elaboration, push back up to twice if the description is thin. Write a structured `proj_refs/{slug}.md` file using the format from `proj_refs.example/sample_project_summary.md`.

If the user drops files: confirm receipt and read them — they will be synthesized directly into `profile.md` in Step 4 (no need to run `/update-profile` afterward).

If the user skips: note that `/update-profile` can be run later.

## Step 4 — Write files

### 4.1 Write `input/profile.md`

Write the complete profile file **after** all interview/import data and any proj_refs have been collected (Steps 3.0–3.9 complete). This is the final step — profile.md is written last so it is complete from the first write.

Structure:

```
---
identity:
  full_name: "[collected full name]"
  file_slug: "[derived slug]"
languages:
  - code: [code]
    name: [name]
  [... for each chosen language]
primary_language: [primary_language_code]
---

# Profile — [full name]
> Single source of truth for the resume builder. Read by `/apply-for-job` alongside `input/resume.tex` (LaTeX template) and `lang_rules/` (per-language rules).

## Identity & Framing Rules for the Agent
[Fill from 3.3 framing answers]

## Name
[full name]

## Address (for letters / CV header)
[address if provided]

## Contact
[email, phone, linkedin, portfolio]

## Current Role
[most recent role from 3.4]

## Headline Summary
[derive a 2–3 sentence positioning statement from the career history and framing]

## Key Technical Skills
[structured from 3.6, grouped by category]

## Professional Experience
[all roles from 3.4, formatted as per template]

## Notable Projects
[one entry per project described in Step 3.4 or added via Step 3.9, following this format:]

### 1. [Project Name]
[One-sentence description]

- **Stack:** [technologies]
- **Engineering highlights:** / **Engineering discipline:**
  - [key decision or methodology point]
  - [key decision or methodology point]
- **Status:** [current status]

[repeat for each project]

## Notable Achievements
[key achievements from career history — not duplicating project detail]

## Domain Expertise
[domains from career history]

## Career Goals
[from 3.8]

## Education
[from 3.5]

## Languages (spoken)
[from 3.7]

## Constraints & Preferences
[from 3.8]

## Hobbies
[if provided]

<!-- proj_refs_ingested:
  [filename of each proj_refs file written or provided during onboarding]
-->
```

If no proj_refs were added during onboarding, write the stamp block with an empty list:
```
<!-- proj_refs_ingested:
-->
```

### 4.2 Write `input/resume.tex`

Replace all `{{placeholders}}` in `input/resume.tex` with actual values:
- `{{full_name}}` → collected full name
- `{{phone}}` → phone number
- `{{email}}` → email
- `{{linkedin_url}}` → LinkedIn URL
- `{{linkedin_display}}` → LinkedIn URL display text (e.g. `linkedin.com/in/janedoe`)
- `{{headline_summary}}` → 2–3 sentence professional summary from profile
- Fill skill categories, experience, education, and languages from collected data
- Keep exactly the same LaTeX `\documentclass`, packages, and section structure — only replace content

### 4.3 Generate missing lang_rules files

For each chosen language that does NOT already have a `lang_rules/{code}.md`:
- Copy `lang_rules/_template.md` as a starting point
- Fill in all fields using your language knowledge:
  - ATS-safe section headings in that language
  - Date format and month abbreviations
  - Cover letter salutation, closing, and subject-line conventions
  - Length ratio vs. English (approximate)
  - LaTeX special character escapes for that language's script
  - Any phrasing notes or ATS gotchas specific to that language/region
- Write to `lang_rules/{code}.md`

Announce each one: "Generated `lang_rules/{code}.md` for {language name}."

English (`lang_rules/en.md`) and German (`lang_rules/de.md`) already exist in the repo — skip those.

## Step 5 — Confirm and summarise

Print a summary of everything created:

```
Onboarding complete!

Files written:
  input/profile.md       — your career profile and CV config
  input/resume.tex       — your LaTeX CV template
  [lang_rules/{code}.md  — language rules for {language} (if newly generated)]
  [proj_refs/{slug}.md   — project summary for {project} (if added)]

Your configuration:
  Name:      [full name]
  Languages: [comma-separated list with codes]
  Primary:   [primary language]

Next steps:
  Run /apply-for-job — paste or provide a job description and I'll generate
  your tailored CV and cover letter in all configured languages.

  When you add new projects later:
  1. Drop a summary into proj_refs/ (or run /project-summary in that project's session)
  2. Run /update-profile to merge it into your profile
  3. Re-run /apply-for-job

To add a new language later:
  Add it to the front-matter `languages` list in input/profile.md,
  then restart Claude Code. The command will auto-generate lang_rules/{code}.md
  on the next run if the file doesn't exist yet.

To add project references later:
  Drop Markdown files into proj_refs/, then run /update-profile to merge them
  into input/profile.md.
```

## Constraints

- Never overwrite files in `input.example/` — those are the shareable templates.
- Never commit `input/`, `output/`, or `proj_refs/` — they are gitignored for good reason.
- If the user provides data that seems incomplete (e.g. no work history at all), proceed anyway and note in the profile which sections need to be filled manually.
- Be truthful: don't invent skills or experience based on vague answers. If the user is vague, ask one clarifying follow-up, then capture what they gave you.
