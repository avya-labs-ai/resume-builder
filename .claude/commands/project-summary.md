---
description: Generate a plain-English project summary and save it to summaries/. Use to brief a third party who has zero prior context about the project.
---

## Step 1 — Discover context sources

Read `CLAUDE.md` first. Then scan the repo for any files that describe the project, its people, goals, priorities, decisions, integrations, or current state. Common locations to check: `context/`, `docs/`, `decisions/`, `references/`, `context_handoff/`, root-level markdown files. Read whatever exists — skip what doesn't.

Do not assume any specific file or folder structure. Adapt to what is actually present.

## Step 2 — Derive the project slug

From what you read, derive a short slug for the project name (lowercased, hyphenated). Use this in the output filename.

## Step 3 — Write the summary

Write the summary to `summaries/{slug}_summary_DD_MM_YYYY.md` (today's date, zero-padded). If a file for today already exists, overwrite it. Use the Write tool. Do not print the summary inline — only report the file path when done.

---

The file must contain the sections below, written in plain English. No jargon, no bullet soup. A non-technical person or new stakeholder should read this and immediately understand what's going on. Only include a section if there is enough information to fill it meaningfully — skip or merge sections if the project context doesn't support them.

---

# [Project Name] — Summary

*[Today's date, human-readable]*

## Who is behind this

Who the person or team is, what their background is, and what they are trying to build.

## The project

What this project is and what problem it solves. Describe it in plain terms — not the tech stack, but the purpose. Why does it exist?

## How it is set up

A short tour of the key files, folders, or components and what each one does (one line each). Then a summary of the tools, platforms, or integrations in use — split into active vs. planned if that distinction exists.

## Decisions made

Key decisions that shaped the project — what was decided and why, one or two lines each. Pull from any decisions log, handoff files, or structural choices evident in the repo. If nothing is explicitly documented, infer from what you observe (folder layout, gitignore choices, tool selection, etc.) and note that these are inferred.

## Current state

- What is built and working
- What exists but is not yet live or connected
- What is still missing

## Priorities and next steps

Top 3–5 things that need to happen next, in order, with a one-line reason for each.

## Goals and deadlines

Any explicit goals, targets, or deadlines — stated clearly with timeframes where available.

---

*Generated on [today's date]. Run `/project-summary` to refresh.*
