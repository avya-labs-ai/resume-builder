# Project: Customer Intelligence Automation Agent
**Status:** Active (shipped MVP, iterating)
**Stack:** Python 3.11, Claude API (claude-sonnet-4-6), Google Workspace APIs (Gmail, Sheets, Drive), OAuth 2.0, pdfplumber
**What it does:** AI-orchestrated pipeline that ingests inbound emails, extracts structured data, classifies records, archives source documents, and writes live summaries to a Sheets dashboard. Designed for multi-currency, high-volume inputs with crash-safe idempotency.

---

## Architecture decisions

- **WAT framework (Workflows → Agents → Tools):** separates probabilistic LLM reasoning from deterministic tool execution. The agent decides *what* to do; Python functions *do* it — no LLM calls inside tool implementations.
- **12 ADRs documented before first line of code:** covered model selection, OAuth scope minimisation, idempotency strategy, error-recovery design, rate-limit handling, schema versioning.
- **Crash-safe state machine:** each record passes through PENDING → CLASSIFIED → ARCHIVED → WRITTEN states, persisted to a local SQLite journal. A mid-run crash on record N leaves records 0..N-1 untouched and resumes cleanly.
- **Claude API prompt caching:** system prompt and classification schema are cached; only the per-record payload is billed at full token cost. ~65% reduction in API spend vs. uncached equivalent.
- **ADR-based LLM/SLM selection:** used a lightweight decision matrix (latency, cost, accuracy, context window) to evaluate 4 models before selecting claude-sonnet-4-6 for classification and claude-haiku-4-5 for pre-filtering.

---

## Key outcomes / metrics

- Processes 200–400 records per batch in under 90 seconds on a laptop
- Zero data-loss incidents across 15 production runs; idempotency confirmed via re-run stress tests
- Formal test plan: 38 unit tests, 5 integration tests, edge-case matrix (malformed inputs, API timeouts, duplicate records, currency-edge cases)
- Multi-currency support: normalises GBP, USD, EUR, INR to a configurable base currency at extraction time

---

## Methodology

- PRD authored before implementation: user stories, acceptance criteria, non-functional requirements (performance, reliability, auditability)
- Docs-before-code: all ADRs, edge-case matrices, and test plans written and reviewed before first implementation commit
- Phase-gated delivery: 4 phases (design → core pipeline → robustness → monitoring), each with an explicit review checkpoint before the next phase begins
- Git from project inception; context-handoff documents at every session boundary

---

## What to highlight for JDs about

| JD signal | What to pull from this project |
|-----------|-------------------------------|
| AI / LLM engineering | WAT framework, prompt caching, ADR-based model selection, claude-sonnet-4-6 |
| Data pipelines / automation | Multi-source ingestion, idempotency, crash-safe state machine, batch throughput |
| Engineering rigour / testing | Formal test plan, 38 unit + 5 integration tests, edge-case matrix |
| Process / methodology | PRD-first, docs-before-code, phase-gated delivery, 12 ADRs |
| Google Workspace / integration | Gmail, Sheets, Drive APIs, OAuth 2.0, multi-currency normalisation |
