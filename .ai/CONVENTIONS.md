# Code Conventions and Engineering Standards

All agents working on this project must strictly comply with these conventions to maintain codebase integrity and consistency.

---

## 1. File & Directory Naming (Compatibility Guardrails)

To guarantee 100% interoperability with tooling (Shadcn UI, SvelteKit routing, Astro, Bun, Biome):

* **UI Components:** **`PascalCase.svelte`** or **`PascalCase.astro`** (e.g., `Navbar.svelte`, `Button.svelte`, `CardHeader.svelte`). Never use kebab-case for UI components so that Shadcn and automated component registries generate without collision.
* **SvelteKit Routes:** Follow strict SvelteKit conventions (`+page.svelte`, `+layout.svelte`, `+page.server.ts`, `+server.ts`, `+error.svelte`).
* **Universal Reactive State Modules (Svelte 5):** **`[name].svelte.ts`** (e.g., `cart-store.svelte.ts`, `user-session.svelte.ts`).
* **General TypeScript Files & Utilities:** **`kebab-case.ts`** (e.g., `db-schema.ts`, `auth-options.ts`, `format-currency.ts`).
* **Test Files:** **`[name].test.ts`** colocated or inside `tests/` (e.g., `format-currency.test.ts`, `Navbar.test.ts`).
* **Directories:** **`kebab-case`** (e.g., `src/lib/components/ui/`, `src/routes/api/auth/`).

---

## 2. State Management & Frontend Architecture (Svelte 5 Runes)

Legacy Svelte stores (`writable`, `derived`, `readable`) are **deprecated** in this codebase.

* **Reactive State:** Use `$state(...)` for reactive values and `$state.raw(...)` for large non-mutated objects.
* **Computed Values:** Use `$derived(...)` or `$derived.by(() => ...)` for complex calculations.
* **Side Effects:** Use `$effect(...)` exclusively for external DOM interactions, analytics, or subscriptions. Never update reactive state inside `$effect` unless strictly unavoidable to avoid infinite loops.
* **Component Props:** Use `$props()` with explicit TypeScript typing:
  ```svelte
  <script lang="ts">
    interface Props {
      title: string;
      isOpen?: boolean;
    }
    let { title, isOpen = false }: Props = $props();
  </script>
  ```
* **Shared / Global State:** Create a class or object in a `.svelte.ts` file using Runes, export singleton instances or factory functions.
* **Component Composition:** Avoid prop drilling deeper than 2 levels; use Svelte 5 snippets (`{#snippet name()}...{/snippet}`) and context (`setContext`/`getContext`) for deeply shared component hierarchies.

---

## 3. Security & Secrets Policy (Zero-Leakage Guardrails)

Maximum security is non-negotiable across client and server boundaries:

1. **Zero Secret Leakage:**
   - Never commit `.env`, `.env.local`, or `.env.production` to git.
   - All environment variables must be declared with dummy defaults in `.env.example`.
   - Never output API keys, database credentials, or secret tokens in console logs, commit messages, or [`.ai/TASK.json`](.ai/TASK.json).
2. **Runtime Environment Validation:**
   - Every environment variable must be validated at application startup using a Zod schema (e.g., `src/lib/env.ts`). If a required secret is missing or malformed, the process must throw immediately.
3. **Client vs Server Isolation:**
   - **SvelteKit:** Strictly import sensitive credentials from `$env/static/private` or `$env/dynamic/private`. Never use `$env/static/public` for secrets.
   - **Astro:** Only variables prefixed with `PUBLIC_` may be exposed to the client bundle. All database operations, ORM calls, and secret handshakes must occur in server endpoints or middleware.

---

## 4. Git Workflow & Change Boundaries

Agents must work cleanly without leaving untracked pollution or making unauthorized edits:

* **Branch Hierarchy:**
  - `main`: Reserved for production releases.
  - `develop`: The active integration branch. All task branches branch from and merge back into `develop`.
* **Branch Per Task:** Before modifying code for an approved task, switch to `develop` and create a task branch:
  ```bash
  git checkout develop
  git checkout -b task/<task_id>-<short-description>
  ```
  *(Example: `git checkout -b task/T-001-navigation-bar`)*
* **Task Staging & Commit Protocol:** When verification passes, stage the target files, lockfiles, and the harness state atomically before committing:
  ```bash
  git add <target_files> .ai/TASK.json .ai/STATE.md
  git add bun.lock bun.lockb 2>/dev/null || true
  git commit -m "<type>(<scope>): <description>"
  ```
* **Task Merge & Branch Housekeeping:** Merge back into `develop` and delete the local task branch to keep the workspace clean:
  ```bash
  git checkout develop
  git merge --ff-only task/<task_id>-<short-description>
  git branch -d task/<task_id>-<short-description>
  ```
* **Strict Scope Boundary:** Edits are restricted strictly to files listed in the task's `target_files` in [`.ai/TASK.json`](.ai/TASK.json).
  - *Direct Fix Exception:* Single-line fixes, typos, or documentation adjustments do not require `target_files` declaration and may be committed directly to `develop` after running scoped Biome/type checks.
* **Dependency Manifest Boundary (Exceptions):**
  - If a task requires installing or removing libraries (`bun add` / `bun remove`), `package.json` must be declared in `target_files` during the specification phase.
  - Automatic updates to lockfiles (`bun.lockb` / `bun.lock`) resulting directly from approved dependency installation steps are authorized configuration changes and must be staged alongside `package.json`.
* **Conventional Commits:** All git commits must adhere to the Conventional Commits specification:
  - `feat(<scope>): ...` - New features
  - `fix(<scope>): ...` - Bug fixes
  - `refactor(<scope>): ...` - Code structure improvements
  - `test(<scope>): ...` - Adding or updating tests
  - `docs(<scope>): ...` - Documentation changes
  - `chore(<scope>): ...` - Maintenance, configuration, harness state, or task failure logging
  - `ci(<scope>): ...` - Continuous integration / pipeline updates
* **Clean Working Tree:** Before completing a task, verify `git status` shows no stray or unintended file modifications.

---

## 5. Code Quality & Formatting

* **Biome:** Strict linting and formatting via Biome (`bunx biome check --write`). Do not configure competing formatters (e.g. Prettier/ESLint) unless integrated with Biome.
* **TypeScript:** Strict type checking enabled (`"strict": true` in `tsconfig.json`). The `any` type is forbidden. Use `unknown`, generic parameters, or explicit interface contracts.