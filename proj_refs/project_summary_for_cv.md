# Project Summary — Avya Labs Bookkeeping Agent (a.k.a. Avya Subscriptions Manager)

> **Purpose of this document:** A structured project summary intended for a CV/profile-building agent. It captures both *what* was built and *how* it was built — emphasising the disciplined engineering approach (PRD-first, ADRs, formal Test Plan, gated phases, context handoffs) used throughout.

---

## What the project is

An AI-orchestrated **bookkeeping automation agent** built to replace ~2–3 hours/month of manual SaaS expense tracking for an AI agency. It ingests receipt emails from Gmail (label-scoped), parses them (HTML + PDF), classifies vendor and category, archives the raw receipt to Google Drive, and writes deduplicated rows into a multi-tab Google Sheet that renders a live monthly + YTD dashboard with charts. Multi-currency aware (USD, EUR, INR, GBP) with no silent FX conversion. Manual trigger via `/book-expenses` slash command; idempotent and crash-safe.

**Status:** Phase A (PRD) and Phase B (Tools + Test Plan + Workflow) complete and merged to `main`. First end-to-end run succeeded (1 receipt → Sheet + Summary tab generated).

---

## Structured Engineering Approach Taken

This project was deliberately built using a **docs-first, gated, phase-based delivery discipline** rather than ad-hoc coding.

### 1. Architecture Design Before Code
- Rejected an initial 3-subagent design (Peon → Clerk → Accountant) after critical analysis — multiplied cost, latency and failure modes for zero coordination benefit.
- Adopted the **WAT framework** (Workflows ↔ Agents ↔ Tools): probabilistic AI handles reasoning, deterministic Python handles execution. This separation of concerns drives reliability (avoiding the 0.9⁵ = 59% compounding error problem of LLM-chained steps).
- Chose **Claude Code as the in-session orchestrator** instead of the Anthropic SDK, with a documented v2 path to scheduled routines.

### 2. Three-Phase Delivery with Hard Review Gates

| Phase | Deliverable | Gate |
|-------|-------------|------|
| **A — PRD** | `docs/PRD.md` (heavy format) | Hard gate: user approval before B |
| **B — ADRs + Test Plan + Tools** | 12 ADRs, `docs/TEST_PLAN.md`, all `tools/*.py` | Hard gate: review before C |
| **C — Production hardening** | Full E2E validation, idempotency, edge cases | In progress |

### 3. Heavy-Format PRD (10 sections, ~390 lines)
- Problem statement, target user, in-scope vs. out-of-scope goals
- **10 user stories with explicit acceptance criteria** (US-01 to US-10, each with ACs like AC-02.1 to AC-02.5)
- **16 functional requirements** (FR-01 to FR-16)
- **10 non-functional requirements** (idempotency, recovery, cost, privacy, security, latency, correctness, auditability, self-improvement, portability)
- **6 measurable success metrics** with concrete targets (≥95% row accuracy, ≤2 missed receipts/month, zero duplicates across 3 months)
- **8-risk register** with likelihood/impact/mitigation
- **20-entry edge-case matrix** (E01–E20) — kept evergreen by the workflow itself

### 4. Architecture Decision Records (ADRs)
Twelve ADRs codify *why* every load-bearing decision was made:
- Single-orchestrator vs. multi-agent
- Label-scoped Gmail access (privacy)
- Monthly-tab Sheet structure (vs. single transactions tab)
- Self-improving vendor map with LLM fallback
- No-FX policy
- Drive archival vs. Gmail linkage
- Auto-book with inline confidence flag (vs. staging tab)
- Native Sheets dashboard (vs. separate layer)
- Model selection (Sonnet 4.6 + Haiku 4.5 + prompt caching)
- No Playwright in v1
- Manual trigger in v1
- Claude Code as orchestrator (no SDK)

### 5. Formal Test Plan Before Implementation
- **38 unit tests** across 8 tool modules with explicit Pass Conditions
- **5 integration tests**
- **6 end-to-end tests**
- **20 edge-case acceptance checks** traceable to the PRD's E01–E20 matrix
- Defined test environment requirements, mocking boundaries, and verification protocol

### 6. Workflow-as-SOP (Operational Discipline)
`workflows/book_expenses.md` is a 6-step Markdown SOP the AI agent reads at runtime. **Step 6 is a self-improvement loop**: the agent auto-documents any new edge case discovered during a run back into both the workflow and the PRD's Edge Case Matrix — institutional learning baked in.

### 7. Version Control + Context Continuity Discipline
- Git-tracked from day one (`avya-labs-ai/avya-bookkeeping-agent` on GitHub)
- **Context handoff documents** at every phase boundary (`context_handoff/context_handoff_DD_MM_YYYY_HH_MM.md`) so a fresh agent or developer can resume in <2 minutes
- `plans/` directory holds project plans version-controlled with the code
- `CLAUDE.md` codifies persona, sparring-partner norms, file-structure rules, and the WAT operating model for future AI sessions

### 8. Security and Operational Hygiene
- Secrets isolated to `.env`, `credentials.json`, `token.json` — all gitignored from inception
- OAuth scopes restricted to least privilege (Gmail readonly, Sheets RW, Drive RW)
- Per-message state persistence (`mark_processed()` after each row) for **crash-safe idempotency** — re-runs never duplicate rows
- Exponential backoff on all Google API calls
- Graceful degradation (Drive upload failure ≠ blocked booking)

---

## Skills Demonstrated

**Product / Engineering Leadership**
- PRD authoring with user stories and acceptance criteria
- ADR-based decision capture (12 ADRs)
- Risk register and edge-case matrix construction
- Success metrics definition tied to measurable targets
- Phase-gated delivery with explicit review checkpoints

**Software Architecture**
- WAT framework adoption (deterministic ↔ probabilistic separation)
- Critical evaluation and rejection of over-engineered designs
- Idempotency engineering, crash-safe state machines
- Self-improving systems (vendor map auto-grows, edge cases auto-documented)

**AI Engineering**
- LLM orchestration design (Claude Code as in-session orchestrator)
- Model-tiered cost optimisation (Sonnet for reasoning, Haiku for classification, prompt caching)
- Deferred-vs-now scoping (SDK, scheduling, Playwright all consciously deferred with documented v2 path)

**Implementation**
- Python 3.11+, Google APIs (Gmail, Sheets, Drive), OAuth 2.0 flow
- HTML + PDF (pdfplumber) parsing across 13 vendor templates
- Multi-currency handling without silent conversion
- Native Sheets formulas (SUMIFS, QUERY) and charts for dashboarding

**Process Discipline**
- Docs-before-code workflow
- Test plan before implementation
- Git + GitHub from project inception
- Context handoff documents at every session boundary
- Sparring-partner / no-yes-man review culture documented in `CLAUDE.md`

**Outcome:** A production-ready v1 with first successful E2E run; one open issue (Drive folder sharing across accounts) tracked with two fix paths.
