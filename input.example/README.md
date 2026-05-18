# input.example/

These are the template files that the onboarding skill copies into `input/` (which is gitignored) and fills in with your personal details.

**Do not edit these templates directly** unless you want to change the structure for everyone who clones this repo. Your personal data lives in `input/` on your own machine only.

## Files

| File | Purpose |
|------|---------|
| `profile.template.md` | Your profile skeleton — identity, career history, skills, framing rules. The onboarding skill populates this interactively. |
| `resume.template.tex` | LaTeX CV template — class, packages, and section structure preserved. The `{{placeholders}}` are replaced during onboarding. |

## Re-running onboarding

If you want to reset your profile (e.g. you shared the repo with a new colleague and they need their own setup):

```bash
rm -rf input/
# Then open Claude Code and say: "help me set this project up"
```

The onboarding skill will re-run from the templates in this folder.
