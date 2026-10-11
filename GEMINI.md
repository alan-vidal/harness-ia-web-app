# AI Agent Instructions (Antigravity CLI Entrypoint)

Welcome to the project. This repository operates under an AI Agent Orchestrator (Harness) located in the `.ai/` directory.

## Core Directives

1. **Orchestrator Entrypoint:**
   - Always read and follow [`.ai/IA.md`](file:///Users/alanvidal/Desktop/IA/.ai/IA.md) as the master orchestrator guide.
   - Check [`.ai/STATE.md`](file:///Users/alanvidal/Desktop/IA/.ai/STATE.md) and [`.ai/TASK.json`](file:///Users/alanvidal/Desktop/IA/.ai/TASK.json) to understand the current project state, active task, and recent history.

2. **Spec-Driven Development (SDD):**
   - Do NOT write or modify application code without an approved task specification in [`.ai/TASK.json`](file:///Users/alanvidal/Desktop/IA/.ai/TASK.json).
   - For any new feature or complex request:
     1. Formulate a technical proposal.
     2. Obtain human confirmation.
     3. Register the task in [`.ai/TASK.json`](file:///Users/alanvidal/Desktop/IA/.ai/TASK.json) with status `"pending"`.
     4. Sync [`.ai/STATE.md`](file:///Users/alanvidal/Desktop/IA/.ai/STATE.md).

3. **Subsystem Routing (Read Before Action):**
   - **Architecture & Backend:** [`.ai/ARCHITECTURE.md`](file:///Users/alanvidal/Desktop/IA/.ai/ARCHITECTURE.md) and [`.ai/REQUIREMENTS.md`](file:///Users/alanvidal/Desktop/IA/.ai/REQUIREMENTS.md)
   - **UI, Components & Design:** [`.ai/DESIGN.md`](file:///Users/alanvidal/Desktop/IA/.ai/DESIGN.md) and [`.ai/CONVENTIONS.md`](file:///Users/alanvidal/Desktop/IA/.ai/CONVENTIONS.md)
   - **Testing & Quality Assurance:** [`.ai/TESTING.md`](file:///Users/alanvidal/Desktop/IA/.ai/TESTING.md)
   - **Security & Secrets:** [`.ai/CONVENTIONS.md#security--secrets-policy`](file:///Users/alanvidal/Desktop/IA/.ai/CONVENTIONS.md)

4. **Change Boundaries & Quality Gates:**
   - Strictly respect `target_files` defined in the active task. Do not make unapproved changes across other files.
   - Run verification checks (formatting/linting, type checks, unit tests) before marking any task as `"completed"`.
   - Adhere to the error handling and rollback protocols if builds, checks, or migrations fail.
