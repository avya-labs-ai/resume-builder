# Resume Builder

A Claude Code automation that tailors your CV and cover letter to a specific job description, in **English and German**, and saves them as LaTeX files ready to compile.

No API key required — it runs inside your Claude Code session.

## Setup (once)

1. Drop your current CV into `input/resume.tex` (LaTeX source).
2. Copy `input/profile_template.md` → `input/profile.md` and fill it in.
   - This is your persistent profile: current role, skills, goals, achievements.
   - Keep it updated as your career evolves — the automation re-reads it every run.

## Usage

Open Claude Code in this directory and run:

```
/apply-for-job
```

Claude will ask you to paste the job description. After you paste it, Claude will:

1. Print a **gap analysis** — strong matches, missing skills, recommendations.
2. Create a folder under `output/` named like `acme-senior-backend-engineer`.
3. Save 4 LaTeX files into that folder:
   - `cv_en.tex` — adapted CV in English
   - `cv_de.tex` — adapted CV in German
   - `cover_letter_en.tex` — cover letter in English
   - `cover_letter_de.tex` — cover letter in German

You can also pass the JD inline if it's short:

```
/apply-for-job Senior Backend Engineer at Acme GmbH — Go, Kubernetes, ...
```

## Output

Each application gets its own folder:

```
output/
├── acme-senior-backend-engineer/
│   ├── cv_en.tex
│   ├── cv_de.tex
│   ├── cover_letter_en.tex
│   └── cover_letter_de.tex
├── helsing-ml-platform-lead/
│   └── ...
└── ...
```

Open each `.tex` file in your LaTeX editor (Overleaf, TeXShop, VS Code + LaTeX Workshop, etc.) and compile.

## How it works

The `/apply-for-job` slash command lives in [.claude/commands/apply-for-job.md](.claude/commands/apply-for-job.md). It instructs Claude Code to:

- Read your `resume.tex` and `profile.md` for context
- Use the job description you provide as the target
- Generate adapted, truthful, well-structured LaTeX output

No external services, no API keys, no Python dependencies — just Claude Code and LaTeX.

## Notes

- **Truthfulness:** the tool adapts emphasis, ordering, and framing. It will not invent skills or experience.
- **Preserves your template:** the adapted CV keeps your original `resume.tex` document class and structure.
- **German conventions:** German outputs use proper business-letter style and localized formatting — not literal translations.
- **Re-running:** if you re-run with the same JD, the existing output folder is overwritten.

See [CLAUDE.md](CLAUDE.md) for the project working rules and constraints.
