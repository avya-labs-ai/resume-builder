# output/

This is where generated CV and cover letter files land. It is gitignored — your job applications stay on your machine only.

## Structure

Each run of `/apply-for-job` creates one subfolder:

```
output/
└── {company}-{role}/
    ├── Resume_{YourName}_{lang}.tex
    ├── CoverLetter_{YourName}_{lang}.tex
    └── ... (one pair per configured language)
```

**Example** — for a user named Jane Doe applying to Acme GmbH as a Data Engineer, with English + German configured:

```
output/
└── acme-data-engineer/
    ├── Resume_JaneDoe_en.tex
    ├── CoverLetter_JaneDoe_en.tex
    ├── Resume_JaneDoe_de.tex
    └── CoverLetter_JaneDoe_de.tex
```

## Compiling the LaTeX files

Compile each `.tex` file in your LaTeX editor (TeXShop, Overleaf, VS Code + LaTeX Workshop). The `resume.cls` class file must be on your LaTeX path — copy it from the project root if needed.

Re-running `/apply-for-job` with the same job description overwrites the previous output for that folder.
