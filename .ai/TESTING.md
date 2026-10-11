# Testing and Quality Verification Guidelines

This project mandates strict quality and verification gates prior to completing any task or committing changes.

---

## 1. Pre-Commit Verification Gate

Before an agent marks any task as `"completed"` in [`.ai/TASK.json`](.ai/TASK.json), execute the checks corresponding to the active **Project Modality**:

### For Website Modality (Astro v7)
```bash
# 1. Scoped Format and Linting Check (Run on target files to avoid out-of-boundary modifications)
bunx biome check --write <target_files>

# 2. Astro Type & Component Checking
bunx astro check

# 3. Unit & Integration Tests (if test suite is present)
bun test
```

### For Application Modality (SvelteKit)
```bash
# 1. Scoped Format and Linting Check (Run on target files to avoid out-of-boundary modifications)
bunx biome check --write <target_files>

# 2. Svelte & TypeScript Checking
bun x svelte-check --tsconfig ./tsconfig.json

# 3. Unit & Integration Tests
bun test
```

> **Enforcement Rule:** If any check fails, do NOT mark the task completed. Apply automated fixes (up to 2 attempts) or follow the Safe Rollback & Escalation Protocol in [`.ai/IA.md`](.ai/IA.md).

---

## 2. Unit & Integration Tests

* **Test Runner:** **[`bun test`](https://bun.sh/docs/cli/test)**
* **File Naming:** `[name].test.ts` placed adjacent to target code or inside `tests/unit/`.
* **Scope:**
  - Server endpoints, handlers, and actions.
  - Business logic, utilities, and schema validators.
  - Universal Svelte 5 reactive modules (`.svelte.ts`).
  - Drizzle database queries and data mutations (Application modality).

---

## 3. End-to-End (E2E) Tests

* **Test Runner:** **[Playwright](https://playwright.dev/docs/intro)**
* **File Naming:** `tests/e2e/[feature].spec.ts`
* **Scope:**
  - Critical user journeys: Authentication flows (Auth.js), form submissions, protected routes, responsive navigation collapse.
  - Run headless for CI/CD pipelines.
