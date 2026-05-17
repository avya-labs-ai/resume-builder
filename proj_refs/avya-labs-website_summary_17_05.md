# Project Summary — 17 May 2026

## 1. Who is behind this

Girish Mohan is the founder of Avya Labs. He runs the business as a solo operator targeting the European market, specifically German-speaking clients. He is building and iterating on the website himself with AI-assisted development, using Claude Code as the primary coding agent under strict rules he has set in `CLAUDE.md`. The project reflects a hands-on founder who cares deeply about legal compliance, code quality, and brand craft — not someone who ships sloppily and patches later.

## 2. The business

Avya Labs is an AI automation agency operating in the EU/German market. It has two service verticals:

1. **AI Voice Agents** — automated outbound qualification and inbound handling using VAPI-powered AI callers (branded "Sarah").
2. **AI Content Production** — AI-generated UGC and content at scale, powered by tools like Kling AI, Higgsfield, Veo, and ElevenLabs.

The business is in an early revenue stage — the website is the primary sales funnel, routing every visitor toward a 30-minute discovery call booked via Cal.com. There are no disclosed existing clients in the codebase context. The agency operates under strict GDPR and TTDSG compliance because its clients are in Germany and the EU.

## 3. What this project is

This is the production marketing website for Avya Labs — the single surface that introduces the business, demonstrates credibility, and converts visitors into booked calls. It is not a product; it is a sales tool.

The problem it solves: without a well-built, legally compliant website, Girish cannot present Avya Labs professionally to European B2B buyers who are highly sensitive to GDPR posture and visual credibility. A cheap-looking or legally sloppy site kills deals before a conversation starts.

The website does one job: get a qualified visitor from "I just landed here" to "I have a call booked" — with a legally defensible record of how their data was handled along the way.

## 4. How it is set up

**Key files:**

- `index.html` — Main landing page. Hero, services, marquee of AI tools, inline Cal.com booking widget.
- `voice-agent.html` — Voice agents product page. Has two Cal.com embeds (primary + final CTA).
- `datenschutz.html` — GDPR privacy policy. Legally required; discloses Cal.com and VAPI as data processors.
- `impressum.html` — German legal imprint, required by TMG §5.
- `vercel.json` — Deployment config and all security headers (CSP, HSTS, X-Frame-Options, etc.).
- `api/get-booking.js` — Vercel serverless function: proxies booking lookup to Cal.com REST API server-side.
- `api/retry-call.js` — Vercel serverless function: proxies the "yes, call me after all" retry to n8n.
- `assets/js/cal-booking-post.js` — Renders the post-booking overlay (yes-splash, no-path, fallback).
- `assets/js/cal-embed.js` / `cal-embed-landing.js` — Registers Cal.com booking event listeners per page.
- `CLAUDE.md` — Project constitution: all rules, guardrails, GDPR constraints, git workflow.
- `serve.mjs` — Local dev server. Must be started with `node --env-file=.env serve.mjs`.
- `screenshot.mjs` — Puppeteer screenshot helper for visual QA.
- `context-handoff.md` — Full architectural handoff document for agent continuity across sessions.

**Tools and platforms:**

| Connected and live | Planned / not yet done |
|---|---|
| Vercel (hosting, auto-deploy from `main`) | Cal.com DPA (not yet signed) |
| Cal.com (booking, via REST API + embed) | HSTS preload submission |
| VAPI (AI voice calls — "Sarah") | Proxy hardening (rate limiting, shared secret) |
| n8n + Google Sheets (CRM, workflow automation) | n8n idempotency for retry flow |
| Tailwind CSS v3, Three.js (self-hosted) | sendBeacon for retry POST |
| Cormorant Garamond + Inter (self-hosted fonts) | |

## 5. Decisions made

**No build framework.** Pure HTML, CSS, and vanilla JS. Tailwind is the only build step. Keeps the deploy surface minimal and eliminates framework churn for a site this size.

**Self-host everything.** No external CDN calls for fonts, icons, or libraries. Driven entirely by GDPR — loading assets from US-hosted CDNs requires SCCs disclosed in the privacy policy and introduces data transfer risk.

**Cal.com REST API for post-booking reads (April 2026 cutover).** Previously, the site read booking data from an n8n-maintained CRM mirror, which raced against the Cal.com webhook that populated it. This caused the wrong name and wrong overlay to appear for every visitor. The fix: browser reads directly from Cal.com's API via a same-origin Vercel proxy, eliminating the race entirely.

**Two-lane post-booking architecture.** Server lane (CRM + VAPI trigger) and browser lane (UX overlay) share only the booking `uid`. Neither depends on the other completing first.

**Same-origin proxies for all external calls.** The browser never sees an n8n URL, a Cal.com API key, or any third-party endpoint directly. All sensitive calls go through `/api/*` Vercel functions. This satisfies CSP without carve-outs and keeps credentials server-side only.

**Inline scripts via SHA-256 hash, not `unsafe-inline`.** One inline script is hash-whitelisted in the CSP. No blanket `unsafe-inline` for `script-src`.

**Test helpers gated to localhost.** `window.__testCalBooking*` globals only attach when the hostname is `localhost` or `127.0.0.1`. The production bundle exposes no internals.

## 6. Current state

**Built and working:**
The full website is live at avya-labs.com (Vercel). Both pages (`index.html`, `voice-agent.html`) are functional. The post-booking overlay flow works correctly: visitors who book a call see a personalised confirmation with their name and a VAPI-call offer; the graceful fallback fires on any API failure. The security headers, CSP, GDPR disclosures, and legal pages are all in place. The n8n workflows for CRM persistence and VAPI triggering are active.

**Exists but not yet live or connected:**
The `assets/json/` directory exists but is empty — intended for FAQ / structured data schemas. The HSTS preload submission has not been completed (the headers are set; the submission to hstspreload.org is a pending manual step). The proxy endpoints have no rate limiting, origin checking, or shared-secret hardening.

**Still missing:**
The Cal.com DPA (Data Processing Agreement under GDPR Art. 28) has not been signed — this is a legal gap, not a technical one, and liability begins when data is processed, not at any launch milestone. The Cal.com API key used in production was exposed in a chat session and has not been rotated. The n8n retry workflow has no idempotency guard, meaning double-clicks or network retries could trigger duplicate VAPI calls.

## 7. Priorities and next steps

1. **Sign the Cal.com DPA immediately.** This is a legal obligation under GDPR Art. 28. Available self-service in Cal.com → Settings → Privacy & Security. Every day this is unsigned is a compliance exposure.

2. **Rotate the Cal.com API key.** The production key was pasted in a chat session. Generate a fresh key in Cal.com → Developer → API Keys, update local `.env` and the Vercel environment variable, then revoke the old one. Takes 10 minutes.

3. **Add the shared-secret header to `/api/retry-call` and n8n.** Highest-impact proxy hardening: Vercel adds `X-Avya-Secret: <random>`; n8n rejects requests without it. Makes the n8n webhook URL unusable even if leaked. Estimated 1 hour.

4. **Add origin/referer check and per-IP rate limiting to `/api/retry-call`.** Prevents casual abuse and protects the VAPI credit budget once the site gets real traffic. About 1–2 hours.

5. **Submit to HSTS preload.** The headers are already correct. Submit at hstspreload.org. One manual step; no code change needed.

## 8. The deadline

There is no single hard calendar deadline stated in the codebase. The implicit deadline is **before scaling traffic** — specifically, the Cal.com DPA must be signed before any meaningful volume of bookings is processed, because GDPR liability attaches to data processing, not to a "launch." If Girish starts running paid ads or driving volume to the site without the DPA in place, he is already in breach regardless of whether the site "officially" launched. The API key rotation is similarly time-sensitive — the window during which the old key can be abused is open right now.

---

*Generated by Avya-OS on 17 May 2026. Run `/project-summary` to refresh.*
