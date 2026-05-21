# output/

This is where generated CV and cover letter files land. It is gitignored — your job applications stay on your machine only.

## Structure

Each run of `/apply-for-job` creates one subfolder inside the tool-specific bucket:

```
output/
├── Claude Code/
│   └── {company}-{role}/
│       ├── resume.cls
│       ├── Resume_{YourName}_{lang}.tex
│       ├── CoverLetter_{YourName}_{lang}.tex
│       └── ... (one pair per configured language)
└── Codex/
    └── {company}-{role}/
        ├── resume.cls
        ├── Resume_{YourName}_{lang}.tex
        ├── CoverLetter_{YourName}_{lang}.tex
        └── ... (one pair per configured language)
```

**Example** — for a user named Jane Doe applying to Acme GmbH as a Data Engineer, with English + German configured:

```
output/
└── Codex/
    └── acme-data-engineer/
        ├── resume.cls
        ├── Resume_JaneDoe_en.tex
        ├── CoverLetter_JaneDoe_en.tex
        ├── Resume_JaneDoe_de.tex
        └── CoverLetter_JaneDoe_de.tex
```

## Compiling the LaTeX files

Compile each `.tex` file in your LaTeX editor (TeXShop, Overleaf, VS Code + LaTeX Workshop). Each generation copies `resources/resume.cls` into the output folder as `resume.cls` so the CV can compile from that folder.

Re-running `/apply-for-job` with the same job description overwrites the previous output for that folder.
