# Repository Guidelines

10x-cards is an Astro 6 **SSR** app (React 19 islands, Tailwind 4, Supabase auth, shadcn/ui) that runs on the Cloudflare Workers runtime. This file is the source of truth for project rules.
## Hard rules

- **Every API route must export `const prerender = false`.** Without it the route is statically prerendered and breaks under SSR.
- `createClient(headers, cookies)` from `@/lib/supabase` **returns `null` when `SUPABASE_URL`/`SUPABASE_KEY` are unset** — callers must handle null, never assume a client.
- `npm run dev` runs on the Cloudflare `workerd` runtime, **not plain Node** — don't reach for Node-only APIs.
- Use Astro components for static/layout; add React only when interactive. No Next.js directives (`"use client"`).
- Never write to `context/archive/` — archived changes are immutable.

## Project structure

- `src/pages/` — routes; `src/pages/api/` — API handlers (uppercase `GET`/`POST` exports); `src/pages/auth/` — auth pages.
- `src/components/` — `.astro` for static, `auth/` React forms, `ui/` shadcn ("new-york"). Extract React hooks to `src/components/hooks/`.
- `src/lib/` — services/business logic (`supabase.ts`, `utils.ts`); shared entities/DTOs go in `src/types.ts`.
- `src/middleware.ts` — resolves `context.locals.user`, redirects `PROTECTED_ROUTES` to `/auth/signin`. Config in @astro.config.mjs, @wrangler.jsonc.
- `supabase/migrations/` — named `YYYYMMDDHHmmss_short_description.sql`. **Always enable RLS on new tables** with granular per-operation, per-role policies.

## Build, test & development commands

- `npm run dev` — local dev server (workerd).
- `npm run build` — production build; `npm run preview` — preview the build.
- `npm run lint` / `npm run lint:fix` — ESLint with **type-checked** rules (slower than plain lint).
- `npm run format` — Prettier with astro + tailwind plugins.
- `npx shadcn@latest add <name>` — add a shadcn component.

## Coding style & conventions

- Node 22.14.0 (`.nvmrc`). Path alias `@/*` → `./src/*`.
- Merge Tailwind classes with `cn()` from `@/lib/utils` — never concatenate class strings manually.
- Validate API input with zod.
- Husky + lint-staged run `eslint --fix` (ts/tsx/astro) and `prettier --write` (json/css/md) on commit.

## Environment & deploy

- Copy `.env.example`. For Cloudflare local dev prefer **`.dev.vars`** (gitignored) over `.env` for `SUPABASE_URL`/`SUPABASE_KEY`. Local Supabase: `npx supabase start` (needs Docker).
- Commit messages observed as `feature - <description>`. CI (@.github/workflows/ci.yml) runs `npx astro sync`, `npm run lint`, then `npm run build` — no deploy step.
- Production deploy is **human-initiated** via the `/deploy` skill, never from CI.
