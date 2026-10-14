# Project State (Agent Memory)

> **Synchronization Guardrail:** This document must be kept strictly synchronized with [`.ai/TASK.json`](.ai/TASK.json). Whenever a task status transitions in `TASK.json`, update the corresponding status, current task, and milestones here atomically.
> **Single Task Rule:** Exactly one task can be active at a time. The agent must halt and await human confirmation before starting any subsequent task.

## Active Project Overview
* **Project Modality:** `application`  *(Set to `website` for Astro v7, or `application` for SvelteKit)*
* **Base Integration Branch:** `develop`
* **Active Task ID:** `null`
* **Status:** `idle` (Ready for task specification)
* **Task Branch:** `none`
* **Goal:** Awaiting next approved task specification.

---

## Completed Milestones
* [x] AI harness structure and agent governance initialization (`.ai/`, `AGENTS.md`).
* [ ] Project foundation scaffolding with SvelteKit & Bun.
* [ ] Initial layout and navigation bar.
* [ ] Database setup & ORM integration (if `application` modality).
* [ ] Authentication integration with Auth.js (if `application` modality).

---

## Current Task Details
* **Task ID:** `null`
* **Modality Context:** `application`
* **Target Files:** `none`
* **Verification Status:**
  - Phase 2 Design & A11y Audit: ⚪ Idle
  - Biome Lint & Format: ⚪ Idle
  - Type Check (`svelte-check`): ⚪ Idle
  - Unit/Integration Tests: ⚪ Idle

---

## Known Bugs / Technical Debt & Blockers
* *No active blockers or reported bugs.*

---

## Next Steps (Backlog)
* [ ] Formulate technical specification for the next feature or foundation task.
* [ ] Request human approval (Step 2) before registering task in `.ai/TASK.json` and branching.