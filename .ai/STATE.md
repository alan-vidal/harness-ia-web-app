# Project State (Agent Memory)

> **Synchronization Guardrail:** This document must be kept strictly synchronized with [`.ai/TASK.json`](.ai/TASK.json). Whenever a task status transitions in `TASK.json`, update the corresponding status, current task, and milestones here atomically.
> **Single Task Rule:** Exactly one task can be active at a time. The agent must halt and await human confirmation before starting any subsequent task.

## Active Project Overview
* **Project Modality:** `application`  *(Set to `website` for Astro v7, or `application` for SvelteKit)*
* **Base Integration Branch:** `develop`
* **Active Task ID:** `T-001`
* **Status:** `pending` (Awaiting execution start)
* **Task Branch:** `task/T-001-navigation-bar`
* **Goal:** Implement responsive Navbar component using Shadcn UI, Tailwind v4, and Svelte 5 Runes.

---

## Completed Milestones
* [x] Environment initialization with Bun and `.ai/` harness structure.
* [ ] Initial layout and navigation bar (`T-001`).
* [ ] Database setup & ORM integration (if `application` modality).
* [ ] Authentication integration with Auth.js (if `application` modality).

---

## Current Task Details
* **Task ID:** `T-001`
* **Modality Context:** `application`
* **Target Files:** `src/lib/components/ui/Navbar.svelte`, `package.json`
* **Verification Status:**
  - Phase 2 Design & A11y Audit: ⏳ Pending
  - Biome Lint & Format: ⏳ Pending
  - Type Check (`svelte-check` / `astro check`): ⏳ Pending
  - Unit/Integration Tests: ⏳ Pending

---

## Known Bugs / Technical Debt & Blockers
* *No active blockers or reported bugs.*

---

## Next Steps (Backlog)
* [ ] Await user trigger to start execution of `T-001`.
* [ ] Complete `T-001`, merge into `develop`, and await human instructions for next task.