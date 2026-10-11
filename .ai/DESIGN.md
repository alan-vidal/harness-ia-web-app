# Design and UI Guidelines

This document governs the visual layer, component design, and user experience of the application.

---

## Core Tools & Ecosystem

* **CSS Framework:** [TailwindCSS v4](https://tailwindcss.com/docs) (utilizing modern `@theme` variables and utility classes).
* **UI Component Library:** [Shadcn UI](https://ui.shadcn.com/) (targeting Svelte 5 / Astro).
* **Icons:** Lucide Icons (`lucide-svelte` / `lucide-astro`).

---

## Modality Component & Island Rules

### 1. Website Modality (Astro Islands)
In Astro, all components render to **static HTML with zero JavaScript** by default.
* **Static Elements:** Always build heroes, cards, layouts, and typography directly as `.astro` components.
* **Interactive Elements:** When client-side state is required (e.g. mobile drawer menu, dropdown, interactive filter), build it as a Svelte 5 component (`.svelte`).
* **Mandatory Client Directives:** You **MUST** pass an explicit client directive in Astro pages/layouts. Never import an interactive Svelte component without one:
  ```astro
  ---
  import Navbar from '../components/Navbar.svelte';
  import SearchDialog from '../components/SearchDialog.svelte';
  ---
  <!-- Critical above-the-fold interactivity: loads immediately -->
  <Navbar client:load />

  <!-- Lower priority interactivity: loads when browser is idle -->
  <SearchDialog client:idle />
  ```
  * `client:load`: For critical navigation, drawers, or mobile menus above the fold.
  * `client:idle`: For search modals, theme switchers, or lower-priority interactive widgets.
  * `client:visible`: For carousels or interactive components located further down the page.

### 2. Application Modality (SvelteKit)
* All components are Svelte 5 components using Runes (`$state`, `$derived`, `$props`).
* Render client and server components natively within SvelteKit routing without Astro client directives.

---

## UI/UX Rules & Accessibility Guardrails

1. **Mobile-First Responsiveness:**
   - Always design from mobile screens (`< 768px`) upwards using Tailwind breakpoints (`md:`, `lg:`, `xl:`).
   - Touch targets for interactive elements (buttons, links, toggles) must meet a minimum size of 44x44px on mobile devices.

2. **Theming & Color Schemes:**
   - Support light and dark modes natively using Tailwind theme tokens (e.g., `bg-background text-foreground`).
   - Use CSS variables for design token alignment between Shadcn and custom components.

3. **Accessibility (a11y):**
   - Strictly employ semantic HTML elements (`<header>`, `<nav>`, `<main>`, `<article>`, `<button>`).
   - Maintain accessible ARIA attributes on modals, accordions, and dropdown menus. Do not alter or strip default accessibility features provided by Shadcn UI components.
   - Maintain a minimum contrast ratio of 4.5:1 for standard text and 3:1 for large text.

4. **Component Architecture:**
   - All custom UI components must be saved as `PascalCase.svelte` or `PascalCase.astro`.
   - Keep components presentation-focused; delegate shared state to universal Svelte 5 `.svelte.ts` modules.
