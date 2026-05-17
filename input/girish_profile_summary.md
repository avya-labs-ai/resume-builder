# Profile Summary — Girish Mohan

> **Purpose:** Context dossier for an AI agent that will tailor CVs against specific JDs. Source of personal/career data is the user-provided `profile.md`; project portfolio detail is woven in from four project summaries dated 17 May 2026. Agent should select, emphasise, or omit sections based on JD relevance.

> **Identity vs. positioning — important for the agent:**
> Girish identifies as **Founder first, Consultant second.** Default the "Current Role" on CVs and cover letters to "Founder, Avya Labs." Only downshift to "Independent AI Consultant — Avya Labs" when applying to enterprise/consulting employer roles where a founder label may read as a flight-risk signal (e.g. Big-4 Senior Consultant tracks, large-enterprise in-house positions). Never present pre-revenue / internal Avya Labs projects as externally paid consulting engagements unless the JD explicitly rewards founder/builder framing.

---

## Name
Girish Mohan

## Address (for letters / CV header)
Floridaring 56, 70806 Kornwestheim, Germany

## Current Role & Company
**Founder, Avya Labs** (Kornwestheim, Germany), Jan 2026 – Present
*(Alternate CV framing for enterprise-employer JDs: "Independent AI Consultant — Avya Labs")*

## Headline Summary
Automotive Engineer (M.S.) with **6+ years in AD/ADAS testing and V&V strategy** and **3+ years in software development / process automation**, now founder of Avya Labs — operating at the intersection of automotive V&V rigour and agentic AI. Proven track record of establishing Verification & Validation frameworks for SAE L4 autonomous truck software in an organisation transitioning from R&D to production. Currently designing, building, and operating LLM-based voice/chat agent systems in production through Avya Labs.

---

## What I'm Currently Working On (Avya Labs)

Avya Labs is the AI automation agency I founded in January 2026, targeting the EU / German-speaking SME and enterprise market. Two service verticals:

1. **AI Voice & Chat Agents (primary focus)** — LLM-based phone agents (VAPI-powered, branded "Sarah") for SMB and enterprise: inbound handling, outbound qualification, appointment booking, CRM lookup, email follow-up. Target niches: home services and German-speaking SMEs. One internal production agent live; one warm external lead (Lukas, real-estate broker, Berlin).
2. **AI Content Production / UGC** — AI-generated video and image ads at scale using Kling AI, Higgsfield, Veo, ElevenLabs, Captions AI, CapCut, Figma Weave. Three active client pipelines: **Texa Mart** (wholesale t-shirts, India), **Charles Bosco** (UK marketing agency serving healthcare/weight-loss providers), **Manama Bridal Couture** (luxury bridal boutique, India).

Day-to-day work as founder:
- Designing and deploying voice & chat agents end-to-end (lead qualification, booking, CRM, email workflows)
- Building agent operating models — escalation logic, human handover, incident logging, post-call review, continuous improvement loops
- Integration architectures connecting conversational front-ends with CRM, scheduling, email, and reporting systems
- Translating fuzzy business workflows into deterministic backend automations with measurable service outcomes
- All commercial/strategic decisions (positioning, pricing, niche selection, legal compliance, GTM)
- Heavy daily use of Claude Code, n8n, Python, VAPI, ElevenLabs, Supabase, Figma

> **Commercial status note for the agent:** Avya Labs is pre-revenue; not yet registered as a legal entity in Germany. Production engineering work is real and operational. Use founder framing for AI-product / startup / builder-track JDs. Use consultant framing only where founder status would hurt the application.

---

## Notable Avya Labs Engineering Projects (built 2026)

Four flagship projects demonstrating disciplined engineering practice. Each shows different skills — agent should pick the most relevant for any given JD.

### 1. Avya Labs Marketing Website With Integrated Lead Qualification AI Voice Agent — `avya-labs.com`
Production marketing site engineered as the single sales funnel converting visitors into Cal.com-booked discovery calls, with end-to-end legal compliance for the German/EU market.

- **Stack:** Pure HTML/CSS/vanilla JS (no framework), Tailwind v3, Three.js, Vercel hosting + serverless functions, Cal.com REST API, VAPI, n8n + Google Sheets CRM
- **Engineering highlights:**
  - **Two-lane post-booking architecture** — server lane (CRM persistence + VAPI trigger) and browser lane (UX overlay) share only the booking `uid`; neither blocks the other
  - **Same-origin Vercel proxies** for every external call (Cal.com, n8n) — browser never sees API keys or third-party URLs; satisfies strict CSP without `unsafe-*` carve-outs
  - **CSP with SHA-256 hash-whitelisted inline scripts** (no blanket `unsafe-inline`)
  - Resolved a production race condition by switching from n8n CRM mirror reads to direct Cal.com REST API reads via same-origin proxy (April 2026 cutover)
  - Fully self-hosted assets (fonts, Three.js, Tailwind) to eliminate US-CDN data-transfer risk under GDPR
- **Compliance:** GDPR & TTDSG — privacy policy discloses Cal.com and VAPI as Art. 28 processors; German legal Impressum per TMG §5; HSTS, X-Frame-Options, all standard security headers configured
- **AI-assisted dev:** Built using Claude Code under strict project rules captured in `CLAUDE.md`

### 2. Avya Labs Bookkeeping Agent (Avya Subscriptions Manager)
AI-orchestrated bookkeeping automation that replaces ~2–3 hrs/month of manual SaaS expense tracking. Ingests label-scoped Gmail receipts, parses HTML + PDF across 13 vendor templates, classifies vendor/category, archives to Google Drive, and writes deduplicated rows to a multi-tab Google Sheet with live monthly + YTD dashboard. Multi-currency aware (USD/EUR/INR/GBP) with no silent FX conversion. Manual trigger via `/book-expenses`; idempotent and crash-safe.

- **Stack:** Python 3.11+, Google APIs (Gmail/Sheets/Drive), OAuth 2.0, pdfplumber, Claude Code as in-session orchestrator, Sonnet 4.6 + Haiku 4.5 with prompt caching
- **Engineering discipline (the headline skill on this project):**
  - **PRD-first delivery** — 10-section, ~390-line PRD with 10 user stories + acceptance criteria, 16 functional & 10 non-functional requirements, 6 measurable success metrics (e.g. ≥95% row accuracy, zero duplicates over 3 months), 8-item risk register, 20-entry edge-case matrix
  - **12 Architecture Decision Records (ADRs)** documenting every load-bearing choice
  - **Formal Test Plan before implementation** — 38 unit tests, 5 integration tests, 6 E2E tests, 20 edge-case acceptance checks traceable to PRD's E01–E20 matrix
  - **Three-phase gated delivery** — Phase A (PRD) → Phase B (ADRs + Test Plan + Tools) → Phase C (production hardening), each with hard review gates
  - **WAT framework** (Workflows ↔ Agents ↔ Tools) — probabilistic AI for reasoning, deterministic Python for execution; avoids the 0.9⁵ = 59% compounding-error problem of LLM-chained steps
  - Crash-safe idempotency via per-message `mark_processed()` state persistence
  - Self-improving SOP — workflow auto-documents new edge cases back into PRD's matrix
- **Status:** Phase A & B merged to `main`; first successful E2E run completed

### 3. Avya AIOS (AI Operating System)
Personal command-centre layer that sits between Girish and his tool stack (Gmail, Calendar, Fireflies, Notion, GitHub, n8n, VAPI, etc.) so Claude Code can act as a structured thought partner rather than a chatbot. Git-tracked scaffold of context files, decision logs, references, and installable skills.

- **Architecture:** `CLAUDE.md` as session root; `context/` for profile/business/priorities; `connections.md` as single integration registry; `decisions/log.md` append-only ADR-style log; `references/{tool}-api.md` for research-once API guides; installable skills (`/onboard`, `/audit`, `/level-up`)
- **First live integration target:** Fireflies (meeting transcripts → voice-agent training data)
- **Status:** Scaffold live and committed; live tool integrations pending; API reference guides for Gmail, Notion, Fireflies authored

### 4. Weavy Workflow Agent
Claude Code skill that converts plain-language descriptions ("Meta video ad with script writer, cinematographer, art director") into complete, production-ready Weavy workflow JSON, pastable directly into the Weavy canvas (weave.figma.com).

- **Scale:** Knows 100+ AI models (image, video, 3D, lipsync, editing), 14 creative LLM roles with templated system prompts, all Weavy node types and connection rules
- **Architecture:** WAT framework — Claude handles reasoning/role mapping; 13 Python node-builder functions handle deterministic JSON generation; quality checker enforces 10+ validation rules (UUID v4 integrity, edge format, `isModel` flags, system-prompt presence) and 14 non-negotiable architect rules
- **7 codified architecture patterns** (Simple LLM Chain, LLM → Variants → Generate, Router Hub, etc.)
- **5 slash commands:** `/workflow`, `/add`, `/update`, `/info`, `/patterns`
- **Status:** Live and in active production use; 4 example workflows shipped (t-shirt campaign, weight-loss UGC ad, voice AI Meta ad, generic template)

---

## Key Technical Skills

### Agentic AI & LLM Systems
- Voice/chat agent architecture, conversation design, NLU intent mapping, dialogue flows
- Prompt architecture, guardrails, token management, model tiering (Sonnet for reasoning, Haiku for classification, prompt caching)
- LLM-based agents on **VAPI** and similar platforms
- Escalation logic, human handover patterns, post-call logging
- **WAT framework** — separating probabilistic AI reasoning from deterministic execution
- LLM orchestration design (Claude Code as in-session orchestrator)

### Validation & Verification (Automotive / Safety-Critical)
- V&V strategy across SiL / HiL / ViL / vehicle-level testing
- Requirements-based testing; requirements review for completeness, testability, consistency
- Test architecture, scenario-based testing, regression strategy
- Defect management, traceability, test coverage reporting, release-readiness assessment
- Test team strategy and management

### Tools & Frameworks (Automotive V&V)
- **Test execution / measurement:** CANoe, CANape, VTestStudio
- **Simulation:** IPG CarMaker, Spirent SimGen, Oktal SCANeR Studio
- **Requirements / Test Management:** JAMA, IBM DOORS, IBM RQM, DNG, TestIDE, Jira
- **Data visualisation:** Tableau, imc FAMOS, UNIVW
- **Version control:** Git, SVN

### Programming & AI-Assisted Development
- **Python 3.11+** — primary scripting language; Google APIs (Gmail/Sheets/Drive), OAuth 2.0, pdfplumber, requests, JSON tooling
- MATLAB, Simulink, Stateflow
- Power Query, vb.NET, YAML
- HTML / CSS / vanilla JavaScript, Tailwind v3, Three.js
- **AI-assisted dev tooling:** Claude Code (Sonnet 4.5/4.6, Haiku 4.5, prompt caching), n8n
- REST APIs, webhooks, Twilio, Google Workspace APIs, Cal.com REST API, VAPI APIs

### AI Content Generation Stack
- Kling AI, Higgsfield, Veo, ElevenLabs
- Figma Weave (node-based generative AI)
- Captions AI, CapCut
- Nano Banana Pro, Flux

### Infrastructure & Web Engineering
- Vercel (hosting, serverless functions, env management)
- Supabase, Airtable
- CSP, HSTS, X-Frame-Options, security-header hardening
- GDPR/TTDSG compliance engineering (Impressum, Datenschutzerklärung, Art. 28 processor disclosure, SCC awareness)
- Docker (n8n self-hosting)

### Engineering Governance & Standards
- **ISO 26262** (functional safety)
- **SOTIF (ISO 21448)**
- **ISO 21434** (cybersecurity engineering)
- **ASPICE**
- **ISTQB**
- **ISO 29119** (software testing)

### Software Engineering Discipline
- PRD authoring with user stories and acceptance criteria
- ADR-based decision capture
- Risk register and edge-case matrix construction
- Success metrics tied to measurable targets
- Phase-gated delivery with explicit review checkpoints
- Idempotency engineering, crash-safe state machines
- Self-improving systems design (vendor maps auto-grow, edge cases auto-documented)
- Docs-before-code, test-plan-before-implementation
- Git + GitHub from project inception; context-handoff documents at session boundaries

### Founder / Consulting Delivery
- Founding and running a solo AI agency end-to-end (positioning, pricing, niche selection, legal/GDPR compliance, GTM, delivery, ops)
- Requirements analysis, stakeholder workshops with senior leadership (SVP / VP / Director level)
- Operating-model design, rollout planning, incident & change handling, documentation, test strategy

---

## Domain Expertise

- **Agentic / Voice AI** — design, deployment, operationalisation (current focus at Avya Labs)
- **Autonomous Vehicle V&V** — SAE L4 autonomous trucks (Torc Europe), SAE L4 People Mover (HOLON), ADAS / driver-assistance ECUs (Bosch via T&S Engineering). Cross-domain test strategy across requirements, software, safety, supplier integration
- **Automotive Systems Engineering** — Test platforms, supplier deliverable coordination, acceptance criteria definition, process gap analysis, R&D → production transition
- **Process Automation** — Enterprise software automation (Infosys, 2013–2016)
- **AI Content Production / UGC** — direct-response ad pipelines for SMB clients across India, UK, EU
- **GDPR / German legal compliance** for digital products (TTDSG, TMG, Art. 28 DPAs)

---

## Career Goals
- Continue building Avya Labs as primary commercial vehicle
- Combine V&V / systems-engineering rigour from automotive with hands-on agentic AI work to bridge a rare gap: **validating AI-driven, non-deterministic systems in safety-relevant contexts**
- Open to roles where conversational / multi-agent AI meets automotive or other regulated, mission-critical domains — provided they allow Avya Labs to continue in parallel
- Near-term commercial target: **2 paying voice-agent clients by 15 June 2026**

---

## Languages
- **English** — C1
- **German** — B2 (most recent self-assessment; older CV listed B1; attends daily German language classes 1pm–5pm)
- **French** — B2
- **Malayalam** — Native
- **Hindi** — C1
- **Tamil** — C1
- **Kannada** — A2

---

## Notable Achievements (consolidated)

**Past employment (automotive)**
- **Torc Europe (L4 autonomous trucks):** Led V&V strategy development across multiple test platforms (SiL / HiL / vehicle). Collaborated with requirements engineers, software engineers, test engineers, and senior leadership (SVPs, VPs, Directors) to ensure a consistent V&V workflow. Enabled engineering teams to design industry-standard, scalable, scenario-based and requirements-based testing methodologies. Contributed to efficient triage and software release strategies.
- **HOLON (L4 People Mover):** Defined cross-domain verification strategies for an autonomous mobility platform — aligning supplier deliveries, test levels, and acceptance criteria across complex stakeholder groups. Identified and addressed process gaps that improved cross-team testing consistency between software and hardware integration layers.
- **Bosch / T&S Engineering:** Designed and executed requirements-based test cases for ADAS / driver-assistance ECUs (CANoe-based system and BSW integration tests; VMC for L3 HAD; multifunctional DASy ECU for JLR; VMPS system tests; Radar ECU integration tests).

**Avya Labs (founder)**
- Founded Avya Labs in January 2026 as a two-vertical AI automation agency (voice agents + UGC content) targeting EU/German-speaking SME and enterprise market
- Built and operates a production internal voice agent handling end-to-end lead qualification → CRM lookup → appointment booking → email follow-up
- Shipped four flagship engineering projects in <6 months (see Projects section): production marketing website, bookkeeping automation agent (PRD-first / 12 ADRs / formal test plan), personal AI Operating System scaffold, Weavy workflow-generation skill
- Established three active UGC client pipelines (Texa Mart, Charles Bosco, Manama Bridal Couture)
- Designed integration architectures translating fuzzy business requirements into deterministic, measurable backend automations

---

## Notable Past Projects (automotive, drawn from prior employment)
- Test Strategy for L4 autonomous trucks (Torc)
- CANoe Tests for Radar ECU — System & BSW integration Tests (VW programme)
- Vehicle Motion Positioning System (VMPS) — System Tests
- CANoe Tests for multifunctional DASy ECU (SL/ASL, CC/ACC, LKA, TJA) — System Tests (JLR programme)
- Vehicle Motion Controller (VMC) for L3 Highly Automated Driving — Requirements-Based Tests (Daimler programme)

---

## Education
- **M.S. Automotive Engineering for Sustainable Mobility** — Joint Degree, Université d'Orléans & ISAT Nevers (France), September 2018
- **B.Tech. Automobile Engineering** — Mahatma Gandhi University, Kerala, India, April 2012

---

## Constraints / Preferences
- **Location:** Kornwestheim (near Stuttgart), DE. Open to Stuttgart / Munich corridor and remote-EU
- **Working hours constraint:** Attends daily German classes 1pm–5pm, which limits availability windows
- **Strong preference:** AI / agentic systems work, ideally in automotive or other regulated domains where V&V background is an asset
- **Identity preference:** Founder-first framing on CVs by default; downshift to "Independent AI Consultant" only for JDs where founder status hurts the application
- **Honesty boundary:** Do not present Avya Labs as a revenue-generating consultancy or claim external paying consulting income — Avya Labs is currently pre-revenue and not yet a registered legal entity

---

## Contact (for cover letter signatures)
- **Email:** girish_mohan@live.com
- **Phone:** +49 176 47372783
- **Address:** Floridaring 56, 70806 Kornwestheim, Germany
- **LinkedIn:** https://www.linkedin.com/in/giri91/
- **Portfolio:** https://docs.google.com/presentation/d/1R9iueJ01IHoN4cdMJXbtG1tvEj9XI9uHEtNyuvmhtag/edit?usp=sharing

---

## Hobbies
Dancing, Travelling, Learning Languages, DIY
