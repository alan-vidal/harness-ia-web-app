# Project Requirements and Scope

This document defines the business rules, functional scope, and architectural boundaries of what must be built.

---

## Modality Selection
* **Active Project Type:** `Application` (Full-Stack SvelteKit + Bun + PostgreSQL + Drizzle + Auth.js)
*(Refer to [`.ai/ARCHITECTURE.md`](.ai/ARCHITECTURE.md) for stack specifics)*

---

## Main User Stories

- [ ] **Authentication:** As a user, I want to be able to sign up and log in securely using Auth.js.
- [ ] **Navigation:** As a user, I want a responsive navigation bar that works on mobile and desktop viewports (`T-001`).
- [ ] **Admin Dashboard:** As an admin, I want to view a dashboard with operational metrics.
      *(Note: Fill this section with project-specific scope as new features are planned)*

---

## Global Acceptance Criteria

- **Performance:** All initial SSR routes must render and respond under 200ms in the Bun runtime.
- **Validation:** Every input form must implement dual-layer validation:
  - Client-side validation: Svelte 5 component state + Zod.
  - Server-side validation: SvelteKit server action / endpoint + Zod before Drizzle mutation.
- **Code Quality:** All commits must pass the Verification Gate in [`.ai/TESTING.md`](.ai/TESTING.md).

---

## Out of Scope

- External microservices (Nest, Express, etc.).
- Native mobile applications (iOS/Android) in this phase.
- Unapproved third-party analytics or tracking scripts.
