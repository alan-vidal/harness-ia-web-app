# AI Agent Instructions (Antigravity CLI Entrypoint)

Welcome to the project. This repository operates under an AI Agent Orchestrator (Harness) located in the `.ai/` directory.

## Core Directives

1. **Orchestrator Entrypoint:**
   - Always read and follow [`.ai/IA.md`](.ai/IA.md) as the master orchestrator guide.
   - Check [`.ai/STATE.md`](.ai/STATE.md) and [`.ai/TASK.json`](.ai/TASK.json) to understand the current project state, active task, and recent history.

2. **Spec-Driven Development (SDD):**
   - For all new features, refactors, or non-trivial tasks, do NOT write or modify application code without an approved task specification in [`.ai/TASK.json`](.ai/TASK.json):
     1. Formulate a technical proposal.
     2. Obtain human confirmation.
     3. Register the task in [`.ai/TASK.json`](.ai/TASK.json) with status `"pending"`.
     4. Sync [`.ai/STATE.md`](.ai/STATE.md).
   - **Exception (Direct Fix):** Single-line fixes, typographical corrections, or unambiguous self-contained questions do NOT require a formal specification in `.ai/TASK.json` and may be executed directly.

3. **Subsystem Routing (Read Before Action):**
   - **Business, Brand & Copywriting:** [`.ai/MANIFESTO.md`](.ai/MANIFESTO.md) and [`.ai/REQUIREMENTS.md`](.ai/REQUIREMENTS.md)
   - **Architecture & Backend:** [`.ai/ARCHITECTURE.md`](.ai/ARCHITECTURE.md) and [`.ai/REQUIREMENTS.md`](.ai/REQUIREMENTS.md)
   - **UI, Components & Design:** [`.ai/DESIGN.md`](.ai/DESIGN.md) and [`.ai/CONVENTIONS.md`](.ai/CONVENTIONS.md)
   - **Testing & Quality Assurance:** [`.ai/TESTING.md`](.ai/TESTING.md)
   - **Security & Secrets:** [`.ai/CONVENTIONS.md#security--secrets-policy`](.ai/CONVENTIONS.md)

4. **Change Boundaries & Quality Gates:**
   - Strictly respect `target_files` defined in the active task. Do not make unapproved changes across other files.
   - Run verification checks (formatting/linting, type checks, unit tests) before marking any task as `"completed"`.
   - Adhere to the error handling and rollback protocols if builds, checks, or migrations fail.

5. **Execution Cadence & Git Workflow:**
   - Work on strictly **ONE task at a time**.
   - All task branches branch from and merge back into **`develop`** (`main` is reserved for releases).
   - When a task is completed and merged into `develop`, **HALT** and wait for human instruction before proceeding. Never start the next task automatically.
