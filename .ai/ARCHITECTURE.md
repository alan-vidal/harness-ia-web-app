# Architecture and Tech Stack

This project uses a **Modular Monolith** architecture. Business logic, server APIs, and the client application reside within the same repository.

---

## Modalities and Approved Stack

The project operates under one of two mutually exclusive modalities, declared in [`.ai/STATE.md`](.ai/STATE.md) and [`.ai/TASK.json`](.ai/TASK.json). **[Bun](https://bun.sh/docs)** is the mandatory runtime and **[TypeScript](https://www.typescriptlang.org/docs/)** is the base language for both.

### 1. Website Modality (Landing Pages, Portfolios & Static Sites)
* **Main Framework:** [Astro v7](https://docs.astro.build/)
* **Styling:** [TailwindCSS v4](https://tailwindcss.com/docs)
* **UI Components:** [Shadcn UI](https://ui.shadcn.com/) (Astro/Svelte versions)
* **Interactivity:** [Svelte 5](https://svelte.dev/docs) (strictly used as interactive islands via Astro client directives like `client:load`, `client:visible`).
* **Database / Backend:** None by default (or serverless endpoints / static API fetching).

```
Website (Astro) Directory Layout:
├── public/                 # Static assets (images, favicon, robots.txt)
├── src/
│   ├── components/         # Astro & Svelte UI components (PascalCase)
│   │   ├── ui/             # Shadcn components
│   │   └── Navbar.svelte   # Interactive island
│   ├── layouts/            # BaseLayout.astro, SiteLayout.astro
│   ├── pages/              # File-based routing (index.astro, blog/[slug].astro)
│   │   └── api/            # Server/static endpoints (.ts)
│   └── styles/             # Global styles (Tailwind v4 app.css)
├── tests/                  # Automated test suites
│   └── unit/               # bun test suites (*.test.ts)
├── astro.config.mjs
└── package.json
```

---

### 2. Application Modality (Full-Stack Web Apps)
* **Main Framework:** [SvelteKit](https://kit.svelte.dev/docs) (with Svelte 5 Runes)
* **Database:** PostgreSQL
* **ORM:** [Drizzle ORM](https://orm.drizzle.team/docs/overview)
* **Authentication:** [Auth.js](https://authjs.dev/)
* **Environment Validation:** Zod (`src/lib/env.ts`)

```
Application (SvelteKit) Directory Layout:
├── static/                 # Static assets (images, favicon)
├── src/
│   ├── lib/
│   │   ├── components/     # Svelte 5 UI components (PascalCase.svelte)
│   │   │   └── ui/         # Shadcn components
│   │   ├── server/         # Server-only isolated code
│   │   │   ├── db/         # Drizzle schema & connection client
│   │   │   └── auth.ts     # Auth.js configuration
│   │   ├── state/          # Global Svelte 5 reactive stores (.svelte.ts)
│   │   └── env.ts          # Zod runtime environment schema
│   └── routes/             # SvelteKit routing (+page.svelte, +server.ts)
│       ├── +layout.svelte
│       ├── +page.svelte
│       └── api/            # Internal REST/RPC endpoints
├── tests/                  # Automated test suites
│   ├── unit/               # bun test suites (*.test.ts)
│   └── e2e/                # Playwright E2E spec suites (*.spec.ts)
├── svelte.config.js
└── package.json
```

---

## Transversal Tools
* **Documentation:** [Astro Starlight](https://starlight.astro.build/)
* **Linter and Formatter:** [Biome](https://biomejs.dev/)
* **Testing:** Bun Test (Unit/Integration) and Playwright (E2E)

---

## Data Flow & Boundary Security
* **Website:** Content is pre-rendered at build time or fetched via static endpoints. No direct database drivers.
* **Application:** PostgreSQL queries are executed **exclusively** on the server via Drizzle ORM in `src/lib/server/`.
* Direct database queries or private credentials inside client-side bundles are prohibited.
* External microservices (Nest, Express, etc.) are out of scope.