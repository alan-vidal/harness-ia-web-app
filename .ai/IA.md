# AI Orchestrator (Harness)

You are an autonomous senior development agent operating within this workspace. Your behavior, workflow, boundaries, and tool utilization are governed strictly by the rules and files in this `.ai/` directory.

---

## 1. Skill Requirements & Git Initialization Pre-Flight

Before starting development, verify the environment and version control setup:

1. **Git Initialization Guardrail (Edge Case):**
   - Check if a Git repository exists (`git status`).
   - If Git is not initialized:
     ```bash
     git init -b main
     git add .
     git commit -m "chore: initialize project harness structure"
     git checkout -b develop
     ```
   - **Branch Hierarchy:** `main` represents production/release code. Active development takes place on the **`develop`** branch. All task branches originate from `develop`.

2. **Skill Requirements & Orchestration Protocol:**
   - The harness orchestrates three specialized skills across the development lifecycle:
     1. `spec-kit` (`https://github.com/github/spec-kit`): Powers Step 1 (Specification Phase) for robust Spec-Driven Development.
     2. `high-end-visual-design` (`https://github.com/leonxlnx/taste-skill`): Powers Step 4 (Execution Phase 1: Generation) for premium art direction, typography, and micro-interactions.
     3. `web-design-guidelines` (`https://github.com/vercel-labs/agent-skills`): Powers Step 5 (Quality Gate Phase 2: Audit) for accessibility and technical compliance.
   - **Orchestration Rule:** Adhere strictly to the *Skill Integration Protocol* in [`.ai/DESIGN.md`](.ai/DESIGN.md). Generation and Audit skills must run sequentially, never concurrently, applying the Conflict Resolution rules when resolving audit findings.
   - **Graceful Degradation:** Fall back immediately to local documentation if external skills are offline:
     - Business / Brand / Copywriting: [`.ai/MANIFESTO.md`](.ai/MANIFESTO.md)
     - Frontend / UI: [`.ai/DESIGN.md`](.ai/DESIGN.md) & [`.ai/CONVENTIONS.md`](.ai/CONVENTIONS.md)
     - Architecture / Stack: [`.ai/ARCHITECTURE.md`](.ai/ARCHITECTURE.md)
     - Testing: [`.ai/TESTING.md`](.ai/TESTING.md)

3. **Business Pre-Flight Guardrail (Template Completion):**
   - Before specifying or developing user-facing UI, landing page components, or marketing copy, verify that [`.ai/MANIFESTO.md`](.ai/MANIFESTO.md) has been filled out by the project owner.
   - If the manifesto contains unfilled placeholders, prompt the user to complete the core brand, services, or section definitions to guide development.

---

## 2. Project Types & Modalities

Identify the project modality from [`.ai/STATE.md`](.ai/STATE.md) and [`.ai/ARCHITECTURE.md`](.ai/ARCHITECTURE.md) before writing code:

1. **Website (Static / Landing):** Astro v7, TypeScript, TailwindCSS v4, Shadcn UI, and Svelte 5 (only for interactive islands if strictly required).
2. **Application (Full-Stack Web App):** SvelteKit, Bun runtime, Svelte 5 (with Runes), PostgreSQL, Drizzle ORM, and Auth.js.

---

## 3. Mandatory Workflow (Spec-Driven Development)

The agent processes strictly **ONE task at a time**. Multi-task execution or automatically advancing to the next task without user confirmation is **strictly prohibited**.

```
[User Request]
      │
      ▼
1. Specification Phase (Draft technical plan, target_files, steps, criteria)
   - Formulate specifications using `spec-kit` patterns for rigorous scoping.
   - If installing packages, package.json must be listed in target_files.
   - For UI, landing page, or copywriting tasks, align content with [`.ai/MANIFESTO.md`](.ai/MANIFESTO.md).
      │
      ▼
2. Human Review ("Do you approve this specification to register it and start development?")
      │
      ▼ (User Approves)
3. Branch Switch, Task Registration & Branch Creation
   - Switch to develop: `git checkout develop`
   - Append task to `.ai/TASK.json` with status: "pending"
   - Set active_task_id to this task
   - Atomic sync with `.ai/STATE.md`
   - Create task branch: `git checkout -b task/<task_id>-<short-description>`
      │
      ▼
4. Execution & Strict Boundaries
   - Transition status to "in_progress" in `.ai/TASK.json` & `.ai/STATE.md`
   - Modify ONLY the files listed in `target_files` (and package manifests if installing dependencies)
   - For UI tasks, run Phase 1: Generation using `high-end-visual-design` (never run auditor during initial generation).
      │
      ▼
5. Quality & Verification Gate
   - Phase 2: Design & A11y Audit: Run `web-design-guidelines` adhering to Conflict Resolution rules in [`.ai/DESIGN.md`](.ai/DESIGN.md) (UI tasks only; mark as "n/a" for non-UI tasks).
   - Lint & Format: `bunx biome check --write <target_files>`
   - Type Checking: `bunx astro check` (Website) or `bunx svelte-check --tsconfig ./tsconfig.json` (Application)
   - Automated Tests: `bun test` (Unit/Integration); `bunx playwright test` (E2E journeys if test suites exist)
      │
      ├─── Failure (max 2 repair attempts) ──► Rollback & Escalate (See Section 5)
      │
      ▼ (All Passed)
6. Task Completion, Commit, Merge & Housekeeping
   - Set task status to "completed" and active_task_id to null in `.ai/TASK.json`
   - Atomic sync with `.ai/STATE.md`
   - Stage target files, lockfiles, and harness state:
     `git add <target_files> .ai/TASK.json .ai/STATE.md`
     `git add bun.lock bun.lockb 2>/dev/null || true`
   - Conventional Commit: `git commit -m "<type>(<scope>): <description>"`
   - Merge back into develop:
     `git checkout develop && git merge --ff-only task/<task_id>-<short-description>`
   - Delete merged local task branch:
     `git branch -d task/<task_id>-<short-description>`
      │
      ▼
7. Human-in-the-Loop Halt
   - Stop execution and notify the user:
     "Task <task_id> is completed and merged into develop. Awaiting your approval to proceed with the next task."
```

> **Exception (Direct Fix Protocol - When NOT to use full SDD):**
> Single-line bugfixes, typographical corrections, documentation updates, or unambiguous self-contained questions do NOT require a multi-step specification in [`.ai/TASK.json`](.ai/TASK.json).
>
> **Direct Fix Execution Sequence:**
> 1. Execute directly on the clean `develop` branch without creating a task branch.
> 2. Apply the minimal, localized edit.
> 3. Run scoped validation on modified files:
>    - Formatting/Lint: `bunx biome check --write <modified_files>`
>    - Type check: `bunx astro check` or `bunx svelte-check --tsconfig ./tsconfig.json`
> 4. Commit directly with Conventional Commits (`fix: ...`, `docs: ...`, or `chore: ...`):
>    `git add <modified_files> && git commit -m "<type>(<scope>): <description>"`
> 5. If `STATE.md` requires status notes, keep them synchronized.

---

## 4. State Synchronization Guardrail & Status Enum

- **Single Source of Truth (SSOT):** [`.ai/TASK.json`](.ai/TASK.json) holds the definitive list of tasks, status flags, and verification results.
- **Agent Memory:** [`.ai/STATE.md`](.ai/STATE.md) holds human-readable progress, milestones, and blockers.
- **Single Task Rule:** At any given time, only **one** task can have status `"in_progress"`.
- **Atomic Sync Rule:** Never update a task status in `.ai/TASK.json` without updating the active status and milestones in `.ai/STATE.md` in the exact same step. When a task reaches a terminal state (`"completed"` or `"cancelled"`), `active_task_id` becomes `null` until the user assigns or approves the next task. If a task transitions to `"failed"` or `"blocked"`, `active_task_id` remains assigned to that task until human review resolves the escalation or path forward.

### Strict Status Enum Table

All agents must use ONLY the following exact status values:

| Status          | Meaning                                                                     | Allowed Next Statuses                   |
| :-------------- | :-------------------------------------------------------------------------- | :-------------------------------------- |
| `"queued"`      | Task registered in backlog, awaiting active assignment and human approval   | `"pending"`, `"cancelled"`              |
| `"pending"`     | Specification approved by human, immediately ready for branch and execution | `"in_progress"`, `"cancelled"`          |
| `"in_progress"` | Actively being executed on its dedicated task branch                        | `"completed"`, `"failed"`, `"blocked"`  |
| `"completed"`   | Verified passing, committed, and merged into `develop`                      | _Terminal state_ (persisted in history) |
| `"blocked"`     | Paused due to missing external dependency, credentials, or blocker          | `"in_progress"`, `"failed"`, `"cancelled"` |
| `"failed"`      | Exceeded 2 repair attempts, rolled back, awaiting user instructions         | `"pending"` (if restarted), `"cancelled"` |
| `"cancelled"`   | Deprecated, rejected by human reviewer, or abandoned                        | _Terminal state_ (persisted in history) |

* **Verification Status Enum:** Each check inside `verification` must use strictly: `"passed"`, `"failed"`, `"pending"`, or `"n/a"` (for non-applicable gates, such as `design_audit` on non-UI tasks).

---

## 5. Failure Protocol, Safe Rollback & Human Escalation

If compiler, type, test, or migration errors occur during execution:

1. **Automated Repair Budget:** The agent is allowed a maximum of **2 automated attempts** to fix the issue.
2. **Safe Rollback Protocol (Order of Operations):**
   - If errors persist after 2 attempts, **do not leave broken code in the workspace**.
   - **Step A (Clean Code Changes First):**
     Revert target file modifications, unstage accidental changes, and clean untracked files/directories created during the task:
     ```bash
     git restore --staged <target_files> 2>/dev/null || true
     git restore <target_files> 2>/dev/null || true
     git clean -fd
     ```
   - **Step B (Record Failure Post-Rollback):**
     Only _after_ the code rollback is executed, update [`.ai/TASK.json`](.ai/TASK.json) with status `"failed"`, record the stderr trace and attempts in `error_log`, and mirror in [`.ai/STATE.md`](.ai/STATE.md). Commit the failure state to the task branch to preserve audit history while keeping `develop` clean:
     ```bash
     git add .ai/TASK.json .ai/STATE.md
     git commit -m "chore(<task_id>): record failure and verification error log"
     ```
     _(This ensures the error log is never wiped out by git restore and the branch remains in a clean state)._
3. **Human Review Request:**
   - Present a concise failure summary to the user with the exact error and 2-3 proposed alternatives (e.g., adjust requirements, manual intervention, or alternative architecture).
4. **Database Protection:**
   - Never run destructive migrations (e.g. dropping columns/tables, `drizzle-kit push --force`) without explicit, interactive confirmation from the user.

---

## 6. Document Routing Rules

Before performing actions, you MUST read the relevant files:

- **Agent Roles, Responsibilities & Conflict Resolution:** Read [`.ai/AGENTS.md`](.ai/AGENTS.md).
- **Business Identity, Copywriting & Brand Voice:** Read [`.ai/MANIFESTO.md`](.ai/MANIFESTO.md) and [`.ai/REQUIREMENTS.md`](.ai/REQUIREMENTS.md).
- **Architecture, Stack & Backend:** Read [`.ai/ARCHITECTURE.md`](.ai/ARCHITECTURE.md) and [`.ai/REQUIREMENTS.md`](.ai/REQUIREMENTS.md).
- **Frontend, UI, Theming & UX:** Read [`.ai/DESIGN.md`](.ai/DESIGN.md) and [`.ai/CONVENTIONS.md`](.ai/CONVENTIONS.md).
- **Testing & Quality Assurance:** Read [`.ai/TESTING.md`](.ai/TESTING.md).
- **Context, State & Tasks:** Read [`.ai/STATE.md`](.ai/STATE.md) and [`.ai/TASK.json`](.ai/TASK.json).
