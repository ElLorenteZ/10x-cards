---
bootstrapped_at: 2026-06-25T01:32:03Z
starter_id: 10x-astro-starter
starter_name: 10x Astro Starter (Astro + Supabase + Cloudflare)
project_name: 10x-cards
language_family: js
package_manager: npm
cwd_strategy: git-clone
bootstrapper_confidence: first-class
phase_3_status: ok
audit_command: npm audit --json
---

## Hand-off

Verbatim copy of `context/foundation/tech-stack.md`.

**Frontmatter:**

```yaml
starter_id: 10x-astro-starter
package_manager: npm
project_name: 10x-cards
hints:
  language_family: js
  team_size: solo
  deployment_target: cloudflare-pages
  ci_provider: github-actions
  ci_default_flow: manual-promotion
  bootstrapper_confidence: first-class
  path_taken: standard
  quality_override: false
  self_check_answers: null
  has_auth: true
  has_payments: false
  has_realtime: false
  has_ai: true
  has_background_jobs: false
```

**Why this stack:**

A solo developer shipping a web-app MVP in 3 after-hours weeks, with accounts and AI flashcard generation as core must-haves, needs a battle-tested, agent-friendly starter that delivers auth, a Postgres database, and edge deploy out of the box. 10x-astro-starter is the recommended default for (web, js) and clears all four agent-friendly gates (typed, convention-based, popular in JS training data, well-documented). Its bundled Supabase gives row-level security that maps directly onto the PRD's per-user data-isolation guardrail and the droppable set-sharing nice-to-have, while TypeScript plus Zod boundaries keep the AI generation/review flow legible to a coding agent. Auth and AI feature flags are set; payments, realtime, and background jobs are out of scope per the must-have FRs. Deployment targets Cloudflare Pages (the starter default) with CI on GitHub Actions; manual promotion was chosen so deploys gate behind an explicit step rather than firing on every merge. Bootstrapper confidence is first-class — expect mostly-smooth scaffolding with occasional manual steps.

## Pre-scaffold verification

| Signal       | Value                                                          | Severity | Notes                                                         |
| ------------ | ------------------------------------------------------------- | -------- | ------------------------------------------------------------- |
| npm package  | not run                                                       | n/a      | cmd_template starts with `git clone` — no npm CLI to resolve  |
| GitHub repo  | przeprogramowani/10x-astro-starter last pushed 2026-05-17     | fresh    | from card.docs_url; ~1 month before bootstrap (within 3 mo)   |

No stale signals. Proceeded without a heads-up warning.

## Scaffold log

**Resolved invocation**: `git clone https://github.com/przeprogramowani/10x-astro-starter .bootstrap-scaffold && cd .bootstrap-scaffold && npm install`
**Strategy**: git-clone
**Exit code**: 0
**Files moved**: 20
**Conflicts (.scaffold siblings)**: CLAUDE.md.scaffold, README.md.scaffold
**.gitignore handling**: moved silently (absent in cwd before scaffold)
**.bootstrap-scaffold cleanup**: deleted

Notes:
- Cloned `.git/` was removed before the move-up so the upstream starter history did not leak into the project repo.
- The project's pre-existing `.git/` (at commit `07f0419`) was left untouched by the existing-wins rule.
- `context/` carried no scaffold counterpart; the existing `context/` tree (prd.md, shape-notes.md, tech-stack.md, README.md) is preserved verbatim.
- Existing `CLAUDE.md`, `README.md`, and `idea-notes.md` preserved; the starter's `CLAUDE.md`/`README.md` landed as `.scaffold` siblings for diffing.

## Post-scaffold audit

**Tool**: `npm audit --json`
**Summary**: 0 CRITICAL, 6 HIGH, 10 MODERATE, 2 LOW (18 total)
**Direct vs transitive**: 0/1/2/0 direct of total 0/6/10/2 (npm-audit `isDirect`). Direct findings: `astro` (HIGH), `@astrojs/check` (MODERATE), `supabase` (MODERATE), `wrangler` (MODERATE).

#### CRITICAL findings

None.

#### HIGH findings

- **astro** — direct dependency. Update Astro to the latest patched release per `npm audit` advisory chain.
- **devalue** — transitive.
- **miniflare** — transitive.
- **undici** — transitive.
- **vite** — transitive.
- **ws** — transitive.

#### MODERATE findings

- **@astrojs/check** — direct.
- **supabase** — direct.
- **wrangler** — direct.
- **@astrojs/language-server** — transitive.
- **@cloudflare/vite-plugin** — transitive.
- **js-yaml** — transitive.
- **tar** — transitive.
- **volar-service-yaml** — transitive.
- **yaml** — transitive.
- **yaml-language-server** — transitive.

#### LOW / INFO findings

- **@babel/core** — transitive.
- **esbuild** — transitive.

Notes: `npm audit` exits non-zero when vulnerabilities exist; this is informational only and did not halt the bootstrap. No auto-fix was applied — review and run `npm audit fix` (or `--force` for breaking changes) per your risk tolerance.

## Hints recorded but not acted on

| Hint                    | Value             |
| ----------------------- | ----------------- |
| bootstrapper_confidence | first-class       |
| quality_override        | false             |
| path_taken              | standard          |
| self_check_answers      | null              |
| team_size               | solo              |
| deployment_target       | cloudflare-pages  |
| ci_provider             | github-actions    |
| ci_default_flow         | manual-promotion  |
| has_auth                | true              |
| has_payments            | false             |
| has_realtime            | false             |
| has_ai                  | true              |
| has_background_jobs     | false             |

These were carried forward into the run for audit-trail completeness but drive no automated action in v1 (no CI scaffolding, no feature-flag-driven scaffold changes, no agent-context generation).

## Next steps

Next: a future skill will set up agent context (CLAUDE.md, AGENTS.md). For now, your project is scaffolded and verified — happy hacking.

Useful manual steps in the meantime:
- `git init` is not needed — your existing repo history is intact; just commit the scaffolded files when ready.
- Review the `.scaffold` siblings (`CLAUDE.md.scaffold`, `README.md.scaffold`) and decide which version of each file to keep (`diff README.md README.md.scaffold`).
- Configure Supabase RLS early (per the starter's gotcha note) so auth gaps do not creep in.
- Address audit findings per your project's risk tolerance — the full breakdown is above. The single direct HIGH (`astro`) is the most actionable.
