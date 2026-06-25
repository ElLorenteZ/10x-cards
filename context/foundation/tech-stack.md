---
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
---

## Why this stack

A solo developer shipping a web-app MVP in 3 after-hours weeks, with accounts and AI flashcard generation as core must-haves, needs a battle-tested, agent-friendly starter that delivers auth, a Postgres database, and edge deploy out of the box. 10x-astro-starter is the recommended default for (web, js) and clears all four agent-friendly gates (typed, convention-based, popular in JS training data, well-documented). Its bundled Supabase gives row-level security that maps directly onto the PRD's per-user data-isolation guardrail and the droppable set-sharing nice-to-have, while TypeScript plus Zod boundaries keep the AI generation/review flow legible to a coding agent. Auth and AI feature flags are set; payments, realtime, and background jobs are out of scope per the must-have FRs. Deployment targets Cloudflare Pages (the starter default) with CI on GitHub Actions; manual promotion was chosen so deploys gate behind an explicit step rather than firing on every merge. Bootstrapper confidence is first-class — expect mostly-smooth scaffolding with occasional manual steps.
