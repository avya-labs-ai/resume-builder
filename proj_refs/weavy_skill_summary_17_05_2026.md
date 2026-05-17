# Weavy Workflow Agent — Summary

*17 May 2026*

## Who is behind this

Girish is building an AI-powered content creation toolkit at Avya Labs, focused on automating creative workflows. This project sits at the intersection of Claude Code (Anthropic's AI coding assistant) and Weavy (a node-based generative AI platform from Figma).

## The project

The **Weavy Workflow Agent** is a Claude Code skill that converts plain-language descriptions into complete, production-ready Weavy workflow JSON — ready to paste directly into the Weavy canvas. Instead of manually wiring dozens of nodes, setting model parameters, and writing system prompts, users describe what they want to create ("Meta video ad with script writer, cinematographer, and art director") and the skill generates the entire graph.

The skill acts as a creative technologist, translating intent into deterministic JSON that Weavy can execute. It knows 100+ AI models (image generation, video generation, 3D, lipsync, editing), 14 creative LLM roles with templated system prompts, and all Weavy node types and their connection rules.

## How it is set up

**Core files:**
- `skills/weavy-workflow-agent/SKILL.md` — the skill definition with 5-step workflow creation protocol and 14 non-negotiable architect rules
- `workflows/weavy_workflow_creation.md` — SOP defining decision trees, role selection, 7 architecture patterns, and edge case handling
- `tools/weavy/node_builders.py` — 13 Python functions that generate valid Weavy nodes (File Upload, LLM, Kling 3, Nano Banana Pro, Router, Array, etc.)
- `tools/weavy/quality_checker.py` — 10+ validation checks (UUID v4, edge integrity, `isModel` flags, system prompts)
- `skills/weavy-workflow-agent/references/` — reference library with creative role system prompts, all node types, and builder signatures

**Built on:** WAT framework (Workflows → Agents → Tools), separating probabilistic AI orchestration from deterministic Python execution. Claude handles reasoning and role mapping; Python builders handle JSON generation and validation.

**Integrations:** Claude Code (Sonnet 4.5), Weavy (weave.figma.com), Python 3 with uuid/json, environment variables for API keys (.env).

## Decisions made

- **WAT architecture over monolithic approach** — Early recognition that compounded AI errors (90% per step = 59% success over 5 steps) required isolating JSON generation into tested Python scripts. Agent focuses on orchestration and creative judgment, not serialization.
- **Skill-based trigger** — Made the agent available as a Claude Code skill `/workflow` command rather than a one-off script, enabling natural reuse and refinement across sessions.
- **14 architect rules, enforced at quality gate** — Rather than generate "best-effort" JSON, the quality checker enforces non-negotiable rules (Router after every File Upload, no root-level `kind` on simple nodes, `§§` separators, etc.), eliminating entire categories of bugs before delivery.
- **7 architecture patterns codified in the SOP** — After discovering patterns (Simple LLM Chain, LLM → Variants → Generate, Router Hub, etc.), captured them in the workflow so they're reusable across future requests.
- **Tool subfolders by topic** — New generator scripts go into `tools/weavy/<topic>/` (e.g., `weight_loss_ugc_ad/`), keeping them organized as more workflows are added.

## Current state

**Built and working:**
- Core skill fully functional with 5 slash commands (`/workflow`, `/add`, `/update`, `/info`, `/patterns`)
- 13 verified node builders covering structural and AI nodes
- Quality checker with 10+ validation checks
- 4 example workflows shipped and tested (tshirt campaign, weight loss UGC ad, voice AI Meta ad, generic template)
- System prompt templates for 14 creative roles (Script Writer, DOP, Stylist, Art Director, Copywriter, etc.)
- Decision tree and 7 architecture patterns documented in the SOP

**Exists but not yet connected:**
- Tool subfolder organization — weight loss UGC ad has subfolder structure; tshirt and toothpaste generators still at root level
- Additional post-processing node builders (Levels, Compositor, Mask Extractor, Extract Video Frame, Compare) — referenced in docs but not yet coded
- `/add` command automation for registering new models

**Still missing:**
- Extended quality checker for LLM-to-LLM edge validation (checking that system prompt outputs match input handle expectations)
- Batch workflow generation (running multiple requests at once)
- Performance profiling for large workflows (>30 nodes)

## Priorities and next steps

1. **Organize tool subfolders** — Move tshirt_campaign.py and generate_toothpaste_ad.py into topic-specific subfolders (tshirt_campaign/, toothpaste_ad/) to match the pattern established by weight_loss_ugc_ad/. This keeps the tools directory navigable as more generators are added.

2. **Implement post-processing node builders** — Add Python builder functions for Levels, Compositor, Mask Extractor, Extract Video Frame, and Compare nodes. These are referenced in the skill's domain templates but not yet available.

3. **Extend quality checker to validate LLM handle connections** — Currently checks for presence of system prompts and edge format, but doesn't validate that the output type of one LLM matches the expected input type of the next (e.g., a text-to-image LLM outputting to an image editing model).

4. **Automate `/add` command** — When a user pastes a new model node via `/add`, parse the JSON, extract the node structure, and propose an update to `tools/weavy/node_builders.py` automatically rather than requiring manual documentation.

5. **Test large workflows (>30 nodes)** — Run workflows with 40+ nodes through Weavy to identify canvas layout and computational limits, then refine the skill's guidance for breaking large workflows into groups or sub-workflows.

## Goals and deadlines

- **Active production use** — The skill is currently live and generating workflows for Weavy projects; refinement is ongoing as new patterns and use cases emerge.
- **Self-improving loop** — Per the WAT framework, each new model, pattern, or error type discovered updates the SOP, node builders, or quality checker, making the system more robust over time.
- **No stated deadline** — This is evolving infrastructure; priority is quality and completeness over speed.

---

*Generated on 17 May 2026. Run `/project-summary` to refresh.*
