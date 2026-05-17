# Project Summary — 17 May 2026

## 1. Who is behind this

Girish is the solo founder of Avya Labs, an AI agency based in Germany. He is technically hands-on — he builds everything himself using Claude Code, Python, n8n, VAPI, ElevenLabs, Figma, and Supabase. He attends German language classes from 1pm to 5pm daily, which limits his working hours. He has a technical friend he is slowly warming up as a potential co-founder, but nothing is confirmed yet.

## 2. The business

Avya Labs is a pre-revenue AI agency with two active service lines:

**AI Voice Agents (primary focus).** Girish builds AI-powered phone agents for SMBs — businesses that need to handle inbound calls, qualify leads, or manage bookings without hiring staff. His initial target niche is home services. He has one warm lead: Lukas, a real estate broker in Berlin, last contacted around two months ago.

**AI UGC and Ad Pipelines (active, 3 clients).** Girish produces AI-generated video and image ads for small businesses. Current clients: Texa Mart (wholesale t-shirt supplier, India), Charles Bosco (UK marketing agency serving healthcare/weight loss providers), and Manama Bridal Couture (luxury bridal boutique, India). The production pipeline runs from Claude or ChatGPT for scripting, through Higgsfield or Figma Weave for visuals, to Captions AI or CapCut for editing.

The business has no formal revenue yet. Payment will be via bank transfer or Stripe once the first client lands. Avya Labs is not yet registered as a legal entity in Germany.

## 3. What this project is

An AI Operating System (AIOS) is a personal command center that sits between Girish and all the tools he uses — email, calendar, meeting recordings, client docs, and build tools — so that Claude Code can act as a real thought partner rather than just a chatbot.

The problem it solves: Girish knows what needs doing but has no structured planning layer. He gets spread thin across building, content, learning, and client work with no weekly or daily rhythm to hold it together. The AIOS gives Claude Code the context it needs to help him prioritize, decide, and ship without having to re-explain the business every session.

In practical terms, it is a folder of files in a git repository that Claude Code reads at the start of every session. It contains who Girish is, what the business does, what the priorities are, which tools are in use, and what decisions have been made. It also contains skills (mini-programs) that run structured workflows like the weekly planning interview and the gap audit.

## 4. How it is set up

**Key files:**

| File / Folder | What it does |
|---|---|
| `CLAUDE.md` | The root operating manual. Claude reads this first on every session. Contains business context, working style, priorities, and skill references. |
| `aios-intake.md` | The Day-1 questionnaire Girish filled in. Source of truth for the `/onboard` skill. |
| `context/about-me.md` | Girish's profile — background, constraints, biggest gaps. |
| `context/about-business.md` | Avya Labs details — verticals, clients, tech stack, warm leads. |
| `context/priorities.md` | Q2/Q3 goals in priority order. |
| `connections.md` | Registry of every tool the AIOS can reach — tracks connection status per domain. |
| `decisions/log.md` | Append-only log of meaningful decisions and why they were made. |
| `references/` | API guides, frameworks, voice register — researched once, saved forever. |
| `EXPANSIONS.md` | Guide for what to add to the AIOS as the business grows. |
| `.env` | Credential store for API keys (gitignored, not committed). |

**Tools and platforms:**

Connected to AIOS: None yet. All integrations are planned but not wired.

In active use (not yet AIOS-connected): Gmail, Google Calendar, WhatsApp, Fireflies, Google Docs, Notion, GitHub, Claude Code, n8n, VAPI, ElevenLabs, Figma Weave, Higgsfield, Captions AI, CapCut, Supabase, Airtable.

API reference guides already researched and saved: Gmail (full v1 API), Notion, Fireflies (GraphQL). Credential placeholders are in `.env` but all values are currently empty.

## 5. Decisions made

**Use `CLAUDE.md` as the AIOS brain.** Claude Code reads this file on every session, making it the natural place to embed business context and working rules. Any change to priorities or voice gets reflected here.

**Gitignore `.env` and `.claude/settings.json`.** Credentials and local editor settings are machine-specific and must not be committed. Both are excluded from version control.

**Prioritize Fireflies as the first live integration.** Meeting recordings are the highest-value context source available — they capture client conversations, which maps directly to the voice agent pipeline. This was the agreed Day-2 starting point.

**Use `references/{tool}-api.md` for API documentation.** Research each API once, save a reference guide, never re-fetch the docs. The `/audit` skill rewards this pattern.

**Use `connections.md` as the single registry for all integrations.** One file tracks every tool's connection status. The `/audit` skill checks this for coverage gaps.

**Store all project context in a git repo.** Gives version history, remote backup, and a clean handoff mechanism for future sessions or a co-founder.

## 6. Current state

**Built and working:**
The full AIOS scaffold is live and committed to `main`. CLAUDE.md is personalized with Girish's business context, voice, and priorities. The context files (`about-me.md`, `about-business.md`, `priorities.md`) are filled in. The connections registry is populated. The decisions log template is in place. API reference guides for Gmail, Notion, and Fireflies have been researched and written. The `/onboard`, `/audit`, and `/level-up` skills are installed.

**Exists but not yet live or connected:**
The three API reference files (`references/gmail-api.md`, `references/notion-api.md`, `references/fireflies-api.md`) are untracked and not yet committed. The `.env` file exists with the right keys but all values are empty — no real credentials have been added yet. No tool is AIOS-connected; `connections.md` shows "not yet connected" across all seven domains. Custom slash commands exist in `.claude/commands/` but are untracked.

**Still missing:**
Live integrations — nothing can be read or queried yet. A real first Fireflies connection with an actual API key. A backfill of the decisions log (several real decisions were made during setup but none are formally logged). A marketing engine and outreach system for client acquisition. Legal business registration in Germany.

## 7. Priorities and next steps

1. **Commit the untracked API reference files.** `references/gmail-api.md`, `references/notion-api.md`, and `references/fireflies-api.md` are research assets that belong in version control — they should not stay untracked.

2. **Add real credentials to `.env`.** Without credentials, no integration can be tested. Fireflies is the first to wire: get the API key from Fireflies Settings > Developer. Notion and Gmail follow the same pattern.

3. **Wire Fireflies as the first live integration.** This is the agreed first connection. Use the existing reference guide and API key. Write a simple script or MCP to pull recent meeting transcripts. Update `connections.md` row 6 when done.

4. **Run `/audit` before May 20.** The first Four-Cs gap report is due on Day 7 (approximately 20 May 2026). It will surface coverage gaps and what to prioritize next.

5. **Run `/level-up` this week.** The weekly 3Ms planning interview finds one automation to scope and ship. First run should happen this week to establish the rhythm before the June 15 deadline.

## 8. The deadline

**2 paying voice agent clients by June 15, 2026** — 29 days from today.

This is the H1 make-or-break target. If no clients land by then, the first half of 2026 is a write-off by Girish's own definition. Everything in the AIOS, every integration, every week of `/level-up` sessions should be evaluated against this single question: does it help close a voice agent client before June 15?

The warm lead (Lukas, real estate broker, Berlin) has not been contacted in approximately two months. Re-engaging him is the fastest path to a first client.

---

*Generated by Avya-OS on 17 May 2026. Run `/project-summary` to refresh.*
