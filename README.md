# Resume Builder

A Claude Code and Codex automation that tailors your CV and cover letter to a specific job description and saves them as LaTeX files ready to compile. Supports any number of languages — English, German, French, Spanish, and more.

No API key required. No Python. It runs entirely inside your Claude Code or Codex session.

Codex reads `AGENTS.md` for the project contract. Claude Code keeps using `CLAUDE.md` and `.claude/`.

---

## Quick start (new users)

```bash
git clone <repo-url>
cd Resume_builder
# Open in Claude Code or Codex, then say:
```

> **"Help me set this project up"**

The onboarding workflow will guide you through the rest: it asks about your background, career history, and which languages you want your documents in — then writes your personal profile and CV template automatically.

---

## Claude Code or Codex

Claude Code users can keep using the existing slash commands:

```text
/apply-for-job
/project-summary
```

Codex users can ask for the same workflows in plain language:

```text
Apply for this job: [paste job description]
Help me set this project up
Create a project summary for my CV
```

Note: Codex does not currently show repo-local `.codex/commands/*.md` files as slash commands in the composer. Those files are workflow references for Codex to read via `AGENTS.md`, so use plain language prompts instead of expecting `/apply-for-job` to appear in the command menu.

---

## What onboarding does

The onboarding skill (auto-triggered on fresh clones, or type "help me set this project up"):

1. Creates `input/`, `output/`, and `proj_refs/` from the templates in `input.example/`
2. Interviews you conversationally — identity, career history, skills, education, goals
3. Asks which languages you want (English, German, French, Spanish, and more)
4. Writes `input/profile.md` (your profile) and `input/resume.tex` (your LaTeX CV template)
5. Generates `lang_rules/{code}.md` for any language without an existing rules file
6. Tells you to run `/apply-for-job` when done

These personal files are **gitignored** — your data stays on your machine only.

---

## Daily use

Once onboarded, open Claude Code in this directory and run:

```
/apply-for-job
```

In Codex, ask in plain language instead:

```text
Apply for this job: [paste job description]
```

The agent will ask you to paste the job description. It then:

1. Prints a **gap analysis** (chat only — not saved)
2. Creates a folder: `output/{company}-{role}/`
3. Generates one CV + one cover letter per configured language:
   - `Resume_{YourName}_{lang}.tex`
   - `CoverLetter_{YourName}_{lang}.tex`

You can also pass the JD inline:

```
/apply-for-job Senior Backend Engineer at Acme GmbH — Go, Kubernetes, 5 yrs experience required
```

In Codex, the equivalent is:

```text
Apply for this job: Senior Backend Engineer at Acme GmbH - Go, Kubernetes, 5 yrs experience required
```

Open the `.tex` files in your LaTeX editor and compile to PDF. If you've never used LaTeX before, see [Compiling your resume (no LaTeX experience needed)](#compiling-your-resume-no-latex-experience-needed) below.

---

## Example output structure

For a user named Jane Doe, with English + German configured, applying to Acme GmbH:

```
output/
└── acme-senior-backend-engineer/
    ├── Resume_JaneDoe_en.tex
    ├── CoverLetter_JaneDoe_en.tex
    ├── Resume_JaneDoe_de.tex
    └── CoverLetter_JaneDoe_de.tex
```

---

## Repo layout

```
.
├── .claude/
│   ├── commands/
│   │   └── apply-for-job.md        # The /apply-for-job slash command
│   └── skills/
│       └── onboarding/
│           └── SKILL.md            # Auto-triggered onboarding skill
│
├── input.example/                  # Templates (ship with repo; gitignored real folder built from these)
│   ├── profile.template.md         # Profile template with {{placeholders}}
│   ├── resume.template.tex         # LaTeX CV template with {{placeholders}}
│   └── README.md
│
├── proj_refs.example/              # Example project summary + explainer
│   ├── _README.md
│   └── sample_project_summary.md
│
├── output.example/                 # Explainer for the output folder structure
│   └── _README.md
│
├── lang_rules/                     # Per-language rules (section headings, date formats, cover letter conventions)
│   ├── _template.md                # Schema for adding new languages
│   ├── en.md                       # English rules
│   ├── de.md                       # German rules
│   └── {code}.md                   # Auto-generated for any additional language during onboarding or /apply-for-job
│
├── input/                          # YOUR personal files (gitignored — created by onboarding)
│   ├── profile.md                  # Your profile + language config (front-matter)
│   └── resume.tex                  # Your LaTeX CV template
│
├── proj_refs/                      # Your project summaries (gitignored — you add these)
│   └── *.md
│
├── output/                         # Generated applications (gitignored)
│   └── {company}-{role}/
│       ├── Resume_{YourName}_{lang}.tex
│       └── CoverLetter_{YourName}_{lang}.tex
│
├── .gitignore                      # Gitignores input/, output/, proj_refs/
├── CLAUDE.md                       # Working rules for Claude Code sessions
└── README.md                       # This file
```

---

## Configuring languages

Your chosen languages live in the YAML front-matter at the top of `input/profile.md`:

```yaml
---
identity:
  full_name: "Jane Doe"
  file_slug: "JaneDoe"
languages:
  - code: en
    name: English
  - code: de
    name: German
primary_language: en
---
```

**To add a language later:** add an entry to the `languages` list and re-run `/apply-for-job`. If `lang_rules/{code}.md` doesn't exist yet, it will be auto-generated.

**To remove a language:** delete its entry from the list. That language won't be generated on the next run.

---

## Adding project references

`proj_refs/` is for detailed write-ups of projects you've built or worked on. The CV agent reads these and pulls specific stack details, methodology, and metrics when tailoring your CV — producing much better output for technical roles than `profile.md` alone.

See `proj_refs.example/_README.md` for the expected format.

**Generating a project summary:** 
1. Right click and copy the project-summary.md file from the root of this project (under .claude/commands or .codex/commands).  
2. Create a commands (case-sensitive) folder under .claude (or .codex) in your root directory, if it doesn't already exist.
3. Paste the project-summary.md into the commands folder.
4. Start a new claude session in your VS-Code or Terminal inside VS-Code and run

```
For claude code: /project-summary
```

or:

```
For Codex:

"Create a project summary"

OR more specifically

"Create a project summary for this repo using the workflow in .codex/commands/project-summary.md"
```

This generates a structured markdown file documenting your project's architecture, tech stack, metrics, and achievements. Save the output file to `proj_refs/` in this Resume Builder directory. On the next `/apply-for-job` run, the agent will read these summaries and weave relevant technical details into your CV and cover letter.

Alternatively, if you use [Avya AIOS](https://github.com/avya-labs/aios) or another Claude Code setup with the `/project-summary` skill, the output is ready to drop directly into `proj_refs/`.



## Compiling your resume (no LaTeX experience needed)

The agent produces `.tex` files — these are the source files for a PDF. You can compile them locally directly from VS-code or with a free LaTeX editor such as **TeXstudio**, or upload them to **Overleaf** and compile in your browser. The local steps below use TeXstudio, which is free and works on Windows and Mac.

### Step 1 — Install a LaTeX distribution

A LaTeX distribution is the engine that does the actual PDF rendering. You only need to do this once.

- **Windows:** Download and install [MiKTeX](https://miktex.org/download). During install, choose "Install missing packages on the fly: Yes".
- **Mac:** Download and install [MacTeX](https://www.tug.org/mactex/). It's a large download (~4 GB) but installs everything you need in one go.

### Step 2 — Install TeXstudio

Download and install [TeXstudio](https://www.texstudio.org/) for your operating system. It's free.

### Step 3 — Prepare your output folder

After running `/apply-for-job`, your output folder will look like this:

```
output/
└── acme-senior-backend-engineer/
    ├── Resume_JaneDoe_en.tex
    └── CoverLetter_JaneDoe_en.tex
```

Copy the file `resources/resume.cls` from this repo into that same folder, so it looks like:

```
output/
└── acme-senior-backend-engineer/
    ├── resume.cls                  ← copy this here
    ├── Resume_JaneDoe_en.tex
    └── CoverLetter_JaneDoe_en.tex
```

The `.cls` file is the style sheet that controls the visual layout. Without it in the same folder, the `.tex` file will not compile.

### Step 4 — Open and compile in TeXstudio

1. Open TeXstudio.
2. Go to **File → Open** and open the `.tex` file you want to compile (e.g. `Resume_JaneDoe_en.tex`).
3. Press **F5** (or click the green **Build & View** button in the toolbar).
4. TeXstudio compiles the file and opens a PDF preview on the right.
5. The PDF is saved in the same folder as the `.tex` file.

Repeat steps 2-5 for the cover letter.

> **If you see errors on first compile:** MiKTeX may need to download a few additional packages. A dialog will appear asking for permission — click "Install". After it finishes, press F5 again. If you still run into problems, claude code would be able to help you fix them.

### Alternative — Use Overleaf

You can also use [Overleaf](https://www.overleaf.com/) instead of installing LaTeX locally:

1. Create a new blank Overleaf project.
2. Upload the generated `.tex` file you want to compile.
3. Upload `resume.cls` from `resources/` into the same Overleaf project.
4. Set the generated `.tex` file as the main document if Overleaf does not select it automatically.
5. Click **Recompile** to generate the PDF.

Repeat with the cover letter `.tex` file if needed.

---

## Re-running onboarding

To reset and start fresh (e.g. a new colleague cloning the repo on their machine):

```bash
rm -rf input/
# Open Claude Code and say: "help me set this project up"
```

---

## Notes

- **Truthful only:** the tool adapts emphasis, ordering, and framing. It never invents skills or experience.
- **One page:** CVs are enforced to fit one A4 page. Older roles are condensed automatically.
- **ATS-safe:** no math glyphs for separators, no hidden link text, standard section headings — every output is built to survive Applicant Tracking System parsing.
- **Preserves your template:** the adapted CV keeps your LaTeX document class and structure.
- **Re-running:** same JD → same output folder → files are overwritten.

See [CLAUDE.md](CLAUDE.md) for full working rules and constraints.
