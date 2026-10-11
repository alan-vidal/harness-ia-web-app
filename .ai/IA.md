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

2. **Skill Requirements (Resilient Initialization):**
   - Attempt to verify or add external skills if the CLI tool is available:
     ```bash
     bunx skills add https://github.com/anthropics/skills --skill frontend-design || true
     bunx skills add https://github.com/vercel-labs/agent-skills --skill web-design-guidelines || true
     bunx skills add https://github.com/github/spec-kit || true
     ```
   - **Graceful Degradation:** If the network is restricted, repositories change, or the `skills` CLI fails/hangs, **do not crash or halt**. Fall back immediately to local documentation:
     - Frontend / UI: [`.ai/DESIGN.md`](file:///Users/alanvidal/Desktop/IA/.ai/DESIGN.md) & [`.ai/CONVENTIONS.md`](file:///Users/alanvidal/Desktop/IA/.ai/CONVENTIONS.md)
     - Architecture / Stack: [`.ai/ARCHITECTURE.md`](file:///Users/alanvidal/Desktop/IA/.ai/ARCHITECTURE.md)
     - Testing: [`.ai/TESTING.md`](file:///Users/alanvidal/Desktop/IA/.ai/TESTING.md)

---

## 2. Project Types & Modalities

Identify the project modality from [`.ai/STATE.md`](file:///Users/alanvidal/Desktop/IA/.ai/STATE.md) and [`.ai/ARCHITECTURE.md`](file:///Users/alanvidal/Desktop/IA/.ai/ARCHITECTURE.md) before writing code:

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
   - If installing packages, package.json must be listed in target_files.
      │
      ▼
2. Human Review ("Do you approve this specification to register it and start development?")
      │
      ▼ (User Approves)
3. Task Registration & Branch Creation
   - Append task to `.ai/TASK.json` with status: "pending"
   - Set active_task_id to this task
   - Atomic sync with `.ai/STATE.md`
   - Switch to develop: `git checkout develop`
   - Create task branch: `git checkout -b task/<task_id>-<short-description>`
      │
      ▼
4. Execution & Strict Boundaries
   - Transition status to "in_progress" in `.ai/TASK.json` & `.ai/STATE.md`
   - Modify ONLY the files listed in `target_files` (and package manifests if installing dependencies)
      │
      ▼
5. Quality & Verification Gate
   - Lint & Format: `bunx biome check --write <target_files>`
   - Type Checking: `bunx astro check` (Website) or `bun x svelte-check` (Application)
   - Automated Tests: `bun test`
      │
      ├─── Failure (max 2 repair attempts) ──► Rollback & Escalate (See Section 5)
      │
      ▼ (All Passed)
6. Task Completion, Commit, Merge & Housekeeping
   - Stage target files AND harness state:
     `git add <target_files> .ai/TASK.json .ai/STATE.md`
   - Conventional Commit: `git commit -m "<type>(<scope>): <description>"`
   - Merge back into develop:
     `git checkout develop && git merge --ff-only task/<task_id>-<short-description>`
   - Delete merged local task branch:
     `git branch -d task/<task_id>-<short-description>`
   - Set task status to "completed" and active_task_id to null in `.ai/TASK.json`
   - Atomic sync with `.ai/STATE.md`
      │
      ▼
7. Human-in-the-Loop Halt
   - Stop execution and notify the user:
     "Task <task_id> is completed and merged into develop. Awaiting your approval to proceed with the next task."
```

> **Exception (When NOT to use full SDD):** Single-line bugfixes, typographical corrections, or unambiguous self-contained questions. Execute directly without creating a multi-step specification.

---

## 4. State Synchronization Guardrail & Status Enum

* **Single Source of Truth (SSOT):** [`.ai/TASK.json`](file:///Users/alanvidal/Desktop/IA/.ai/TASK.json) holds the definitive list of tasks, status flags, and verification results.
* **Agent Memory:** [`.ai/STATE.md`](file:///Users/alanvidal/Desktop/IA/.ai/STATE.md) holds human-readable progress, milestones, and blockers.
* **Single Task Rule:** At any given time, only **one** task can have status `"in_progress"`.
* **Atomic Sync Rule:** Never update a task status in `.ai/TASK.json` without updating the active status and milestones in `.ai/STATE.md` in the exact same step. When a task completes, `active_task_id` becomes `null` until the user assigns or approves the next task.

### Strict Status Enum Table
All agents must use ONLY the following exact status values:

| Status | Meaning | Allowed Next Statuses |
| :--- | :--- | :--- |
| `"pending"` | Specification approved, waiting to start branch and execution | `"in_progress"` |
| `"in_progress"` | Actively being executed on its dedicated task branch | `"completed"`, `"failed"`, `"blocked"` |
| `"completed"` | Verified passing, committed, and merged into `develop` | *Terminal state* (persisted in history) |
| `"blocked"` | Paused due to missing external dependency, credentials, or blocker | `"in_progress"`, `"failed"` |
| `"failed"` | Exceeded 2 repair attempts, rolled back, awaiting user instructions | `"pending"` (if restarted) |

---

## 5. Failure Protocol, Safe Rollback & Human Escalation

If compiler, type, test, or migration errors occur during execution:

1. **Automated Repair Budget:** The agent is allowed a maximum of **2 automated attempts** to fix the issue.
2. **Safe Rollback Protocol (Order of Operations):**
   - If errors persist after 2 attempts, **do not leave broken code in the workspace**.
   - **Step A (Clean Code Changes First):**
     Revert target files and remove newly created untracked files:
     ```bash
     git restore <target_files>
     git clean -fd <target_directories>
     ```
   - **Step B (Record Failure Post-Rollback):**
     Only *after* the code rollback is executed, update [`.ai/TASK.json`](file:///Users/alanvidal/Desktop/IA/.ai/TASK.json) with status `"failed"`, record the stderr trace and attempts in `error_log`, and mirror in [`.ai/STATE.md`](file:///Users/alanvidal/Desktop/IA/.ai/STATE.md).
     *(This ensures the error log is never wiped out by the git restore command).*
3. **Human Review Request:**
   - Present a concise failure summary to the user with the exact error and 2-3 proposed alternatives (e.g., adjust requirements, manual intervention, or alternative architecture).
4. **Database Protection:**
   - Never run destructive migrations (e.g. dropping columns/tables, `drizzle-kit push --force`) without explicit, interactive confirmation from the user.

---

## 6. Document Routing Rules

Before performing actions, you MUST read the relevant files:

* **Architecture, Stack & Backend:** Read [`.ai/ARCHITECTURE.md`](file:///Users/alanvidal/Desktop/IA/.ai/ARCHITECTURE.md) and [`.ai/REQUIREMENTS.md`](file:///Users/alanvidal/Desktop/IA/.ai/REQUIREMENTS.md).
* **Frontend, UI, Theming & UX:** Read [`.ai/DESIGN.md`](file:///Users/alanvidal/Desktop/IA/.ai/DESIGN.md) and [`.ai/CONVENTIONS.md`](file:///Users/alanvidal/Desktop/IA/.ai/CONVENTIONS.md).
* **Testing & Quality Assurance:** Read [`.ai/TESTING.md`](file:///Users/alanvidal/Desktop/IA/.ai/TESTING.md).
* **Context, State & Tasks:** Read [`.ai/STATE.md`](file:///Users/alanvidal/Desktop/IA/.ai/STATE.md) and [`.ai/TASK.json`](file:///Users/alanvidal/Desktop/IA/.ai/TASK.json).
