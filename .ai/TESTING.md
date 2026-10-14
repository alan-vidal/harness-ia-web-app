# Testing and Quality Verification Guidelines

This project mandates strict quality and verification gates prior to completing any task or committing changes.

---

## 1. Pre-Commit Verification Gate

Before an agent marks any task as `"completed"` in [`.ai/TASK.json`](.ai/TASK.json), execute the checks corresponding to the active **Project Modality**:

### Phase 2: Design & Accessibility Audit (UI Tasks Only)
For any task creating or modifying UI components, layouts, or pages:
1. Audit the generated output using `web-design-guidelines` (`https://github.com/vercel-labs/agent-skills`).
2. Verify accessibility (a11y), semantic HTML tags, spacing, and `:focus-visible` keyboard states.
3. Apply any audit fixes strictly following the **Conflict Resolution Rules** in [`.ai/DESIGN.md`](.ai/DESIGN.md) to preserve the Phase 1 premium aesthetic (opacity-adjusted contrast, no harsh solid 1px borders, smooth focus rings).
4. For tasks that do **not** touch UI components (e.g., backend logic, migrations, utilities), mark `design_audit` as `"n/a"` in [`.ai/TASK.json`](.ai/TASK.json).

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
bunx svelte-check --tsconfig ./tsconfig.json

# 3. Unit & Integration Tests
bun test
```

> **Enforcement Rule:** If any check fails, do NOT mark the task completed. Apply automated fixes (up to 2 attempts) or follow the Safe Rollback & Escalation Protocol in [`.ai/IA.md`](.ai/IA.md).
>
> **Verification Status Schema:** Every item in `verification` must be recorded as:
> - `"passed"`: Check executed successfully with 0 errors.
> - `"failed"`: Check produced errors.
> - `"pending"`: Awaiting execution.
> - `"n/a"`: Not applicable to this task's scope (e.g. `design_audit` for backend-only tasks). Non-UI tasks with `design_audit: "n/a"` are valid passing gates.

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

* **Test Runner:** **[Playwright](https://playwright.dev/docs/intro)** (`bunx playwright test`)
* **File Naming:** `tests/e2e/[feature].spec.ts`
* **Scope:**
  - Critical user journeys: Authentication flows (Auth.js), form submissions, protected routes, responsive navigation collapse.
  - Run headless for CI/CD pipelines and user journey tasks where E2E test suites exist.
