# Cursor Project Guide

Use this file as the source of truth for how this project is built, organized, and changed. Fill in every `[placeholder]` before relying on the guide. When a section does not apply, write `N/A` instead of deleting the heading.

---

## Project Overview

**Project name:** `[Project Name]`

**One-line summary:** `[What this product does in one sentence]`

**Problem it solves:** `[Who the user is, what pain this removes, and what success looks like]`

**Primary users:** `[e.g. admins, end users, internal team]`

**Product type:** `[web app | API | CLI | library | mobile | desktop | other]`

**Goals:**

- `[Goal 1 — measurable if possible]`
- `[Goal 2]`
- `[Goal 3]`

**Out of scope:**

- `[What this project will not do]`

**Current status:** `[prototype | active development | production]`

**Key constraints:** `[deadlines, performance, compliance, offline, browser support, etc.]`

**Links:**

- Repository: `[url]`
- Live / staging: `[url]`
- Design / specs: `[url]`
- Issue tracker: `[url]`

---

## Tech Stack

This is a **Node.js** application written in **TypeScript** and built with **Next.js**. Record the exact versions from `package.json` / `package-lock.json` (or `pnpm-lock.yaml`) once the project is initialized. Do not list libraries that are not actually installed.

### Core

| Layer | Choice | Version | Notes |
| --- | --- | --- | --- |
| Runtime | Node.js | `[20 LTS or newer — match engines in package.json]` | JavaScript runtime for Next.js (dev, build, and Node server) |
| Language | TypeScript | `[5.x — match package.json]` | Strict mode (`strict: true` in `tsconfig.json`). Prefer types over `any`. |
| Framework | Next.js | `[15.x or current — match package.json]` | App Router by default (`src/app`). Server Components first; add `"use client"` only when needed. |
| UI library | React | `[19.x — the version Next.js depends on]` | Use the React version bundled / required by Next.js. Do not add a second UI framework. |

### Next.js conventions in this repo

- **Router:** App Router (`app/`), not Pages Router (`pages/`), unless a file already exists in `pages/`.
- **Rendering:** Server Components by default. Client Components for interactivity, browser APIs, or React state.
- **Data fetching:** Server-side `fetch` / async Server Components for first load. `[React Query / SWR / none]` only if client cache is required.
- **API / backend:** Next.js Route Handlers (`app/api/.../route.ts`) and Server Actions. No separate Express/Fastify app unless explicitly added later.
- **Styling:** `[Tailwind CSS | CSS Modules | global CSS — pick one and stick to it]`
- **Component library:** `[none | shadcn/ui | …]`
- **Fonts / assets:** `next/font` and the `public/` folder. Use `next/image` for images.

### Tooling

| Layer | Choice | Version | Notes |
| --- | --- | --- | --- |
| Package manager | `[npm | pnpm | yarn]` | `[lockfile in repo decides this]` | Use one manager only. Prefer the lockfile that already exists. |
| Bundler / compiler | Next.js (Turbopack in `next dev`, Next compiler for production) | (with Next.js) | Do not add Vite or a second bundler for the app. |
| Linting | ESLint (`eslint-config-next`) | `[match Next.js]` | `next lint` / `eslint`. |
| Formatting | `[Prettier | none]` | | If Prettier is added, do not fight ESLint over the same rules. |
| Testing | `[Vitest + React Testing Library | Playwright | none]` | | Unit tests for logic/components; Playwright for critical user flows. |
| Validation | `[Zod | none]` | | Validate at route and form boundaries. |
| Auth | `[NextAuth.js / Auth.js | Clerk | none]` | | |
| Database | `[PostgreSQL | SQLite | none]` | | `[ORM: Prisma | Drizzle | none]` |
| Hosting | `[Vercel | Node host | Docker]` | | Next.js production: `next build` then `next start` (or Vercel). |
| CI | `[GitHub Actions | none]` | | Lint, typecheck (`tsc --noEmit`), and tests on PRs. |

### Required tools on a new machine

```text
Node.js 20+ (LTS)
npm, pnpm, or yarn — whichever lockfile this repo uses
Git
```

Optional: Docker only if the project later adds a database or a containerized deploy.

### TypeScript / Next.js config (expected)

- `tsconfig.json`: `"strict": true`, path alias `@/*` → `./src/*` (or project root if `src/` is not used).
- `next.config.ts` (or `next.config.mjs`): keep Next-specific options here, not in ad-hoc webpack config unless required.
- `next-env.d.ts` is generated — do not edit it by hand.

### Environment variables (names only — never commit secrets)

Next.js exposes only variables prefixed with `NEXT_PUBLIC_` to the browser. Everything else stays server-only.

| Name | Required | Purpose |
| --- | --- | --- |
| `NODE_ENV` | set by Next.js | `development` / `production` / `test` |
| `[DATABASE_URL]` | `[yes if a DB is added]` | Server-only connection string |
| `[NEXT_PUBLIC_APP_URL]` | `[no]` | Public site origin for links and auth callbacks |
| `[AUTH_SECRET]` | `[yes if auth is added]` | Server-only; never prefix with `NEXT_PUBLIC_` |

Copy `.env.example` to `.env.local` for local Next.js. Do not commit `.env`, `.env.local`, or real keys.

---

## Project Structure

Keep this tree in sync with the real folders. Remove unused paths; add new ones when they land.

```text
[project-root]/
├── cursor.md                 # This guide (Cursor / AI context)
├── README.md                 # Human-facing setup and usage
├── [package.json]            # Dependencies and scripts
├── [src/]                    # Application source
│   ├── [app/]                # Routes / pages / entrypoints
│   ├── [components/]         # Reusable UI
│   ├── [lib/]                # Shared helpers, clients, config
│   ├── [hooks/]              # Client-side hooks (if any)
│   ├── [services/]           # External APIs / domain services
│   ├── [types/]              # Shared types / schemas
│   └── [utils/]              # Pure utilities
├── [public/]                 # Static assets
├── [tests/] or [__tests__/]  # Unit / integration tests
├── [scripts/]                # One-off or maintenance scripts
└── [docs/]                   # Extra documentation
```

**Where things belong:**

| Kind of change | Put it here |
| --- | --- |
| New page / route | `[src/app/…]` |
| Reusable UI | `[src/components/…]` |
| Business logic | `[src/services/…]` or `[src/lib/…]` |
| Types / schemas | `[src/types/…]` |
| Tests | `[tests/…]`, colocated `*.test.*` next to the file if that is the convention |
| Config | `[root config files — name them]` |

**Do not put:**

- Secrets, `.env`, credentials, or production dumps in the repo
- Generated build output (`dist/`, `.next/`, `__pycache__/`) except when the project explicitly tracks it
- One-off experiments in production folders — use a scratch branch or `scripts/`

---

## Coding Conventions

Follow these rules in every change unless a file already uses a documented exception.

### Language and naming

- `[Language / dialect: TypeScript strict, Python 3.12, etc.]`
- Files: `[kebab-case | snake_case | PascalCase for components]`
- Functions and variables: `[camelCase | snake_case]`
- Types, classes, components: `[PascalCase]`
- Constants: `[SCREAMING_SNAKE_CASE]`
- Prefer descriptive names over abbreviations.

### Style

- Indentation: `[2 spaces | 4 spaces | tabs]`
- Max line length: `[80 | 100 | 120]`
- Quotes, semicolons, trailing commas: `[match the formatter]`
- Run the project formatter before finishing (`[pnpm format | ruff format | …]`).
- Do not mix formatting-only diffs with feature work.

### Architecture

- Keep UI, domain logic, and I/O separate.
- Prefer small, single-purpose modules over large catch-all files.
- Do not duplicate logic — extract a helper when the same rule appears twice.
- Public APIs (routes, exported functions) should validate input at the boundary.
- Fail loudly on unexpected states; do not swallow errors.

### Type safety and data

- `[No implicit any / use type hints on public functions]`
- Share schemas between runtime validation and types where possible (`[Zod | Pydantic]`).
- Do not use `any`, `Object`, or untyped `dict` to dodge the type system.

### Comments and docs

- Comment *why*, not *what*, and only when the code is not obvious.
- Update this `cursor.md` when structure, stack, or conventions change.
- Public functions get a short docstring if their contract is not obvious from the signature.

### Git and pull requests

- Branch names: `[feat/… | fix/… | chore/…]`
- Commits: imperative, focused, one concern per commit.
- Do not commit `node_modules/`, build artifacts, or secrets.
- Do not add drive-by refactors to a bugfix unless asked.

### Testing

- Add or update tests for new behavior and for bug fixes.
- `[Unit tests for pure logic; integration tests for API/DB; e2e for critical user flows]`
- Do not merge if `[pnpm test | pytest]` is failing.

### Cursor / AI working in this repo

- Read this file before making changes.
- Match existing patterns in nearby files over inventing a new style.
- Touch only files required for the requested task.
- After UI changes, verify the affected flow in the browser (or the closest substitute).
- If a request conflicts with this guide, follow the guide and say so.

---

## Getting Started

```bash
# 1. Clone and enter the project
git clone [repo-url]
cd [project-name]

# 2. Install
[pnpm install | uv sync | pip install -r requirements.txt]

# 3. Configure
cp .env.example .env
# fill in local values

# 4. Run
[pnpm dev | uv run uvicorn app.main:app --reload]
```

**Common commands:**

| Command | Purpose |
| --- | --- |
| `[pnpm dev]` | Local development server |
| `[pnpm build]` | Production build |
| `[pnpm test]` | Test suite |
| `[pnpm lint]` | Lint |
| `[pnpm format]` | Format |

---

## Architecture Notes

`[Short description of request flow: UI → API → service → database]`

- Auth: `[how sessions/tokens work]`
- Data: `[main entities and how they relate]`
- External services: `[list APIs and what they are used for]`
- Important invariants: `[rules the code must never break]`

---

## Definition of Done

A change is done only when all of the following are true:

- [ ] Behavior matches the request, including empty, error, and edge cases
- [ ] Types/lint/tests pass
- [ ] Related screens or endpoints that share the same state still work
- [ ] This guide is updated if stack, structure, or conventions changed
- [ ] No secrets or unrelated files were added

---

## Open Questions / TODOs

- `[Decision still pending]`
- `[Known debt to clean up]`
