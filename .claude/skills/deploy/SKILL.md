---
name: deploy
description: Deploy this Astro SSR app to Cloudflare Workers (production). User-triggered with /deploy. Runs astro sync, build, then wrangler deploy after verifying secrets and a clean lint/build.
disable-model-invocation: true
---

# Deploy to Cloudflare Workers

Production deploy is **human-initiated** — CI only runs lint + build, never deploys. Run this only when the user asks.

## Preflight

1. Confirm the working tree is clean (or the user accepts deploying local changes): `git status`.
2. Verify the user is on the intended commit/branch.
3. Confirm Cloudflare auth: `npx wrangler whoami`. If unauthenticated, tell the user to run `! npx wrangler login` in the session (interactive).
4. Confirm production secrets exist on the worker: `npx wrangler secret list`. Required: `SUPABASE_URL`, `SUPABASE_KEY`. These are **not** synced from GitHub Actions — they must be set with `npx wrangler secret put <NAME>`.
5. Sanity-check `wrangler.jsonc` (`name`, `compatibility_flags`) matches the intended worker before deploying.

## Deploy

```
npx astro sync && npm run build && npx wrangler deploy
```

- `astro sync` regenerates `.astro/` types — skipping it can cause type errors in CI-clean builds.
- If the build fails on missing env, the issue is local `.dev.vars` / `.env`, not the deploy.

## After deploy

- Report the deployed URL printed by `wrangler deploy`.
- Offer to tail logs: `npx wrangler tail`.
