---
description: Create a context handoff snapshot so a fresh agent can resume work without prior session context
---

Create a context handoff file at `context_handoff/context_handoff_DD_MM_YYYY_HH_MM.md` (use the current date/time, zero-padded).

The file must capture everything a fresh agent needs to continue the work with zero prior context:

- **Decisions** — what was decided and why (key decisions + rationale)
- **Built** — files created/modified, current state
- **In progress** — task at hand, where it stands
- **Pending** — next steps, in priority order
- **Gotchas** — known constraints, open questions, anything non-obvious
- **Verification** — how to confirm the current state (commands to run, files to check)

Keep it tight. A fresh agent should be productive within 2 minutes of reading. The handoff file is the source of truth for resuming work.

After writing, report the file path.
