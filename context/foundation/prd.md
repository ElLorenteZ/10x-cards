---
project: "10x Cards"
version: 1
status: draft
created: 2026-06-25
context_type: greenfield
product_type: web-app
target_scale:
  users: medium
  qps: low
  data_volume: small
timeline_budget:
  mvp_weeks: 3
  hard_deadline: null
  after_hours_only: true
---

# PRD — 10x Cards

## Vision & Problem Statement

Manually creating high-quality flashcards is time-consuming. That friction discourages people from using spaced repetition, which undercuts effective learning — the tool that would help them most goes unused because preparing for it costs too much effort.

The insight is twofold. First, AI flashcard generation is not an obvious win: sometimes a card's value is in a precise detail, sometimes in general-context framing, and a naive generator sacrifices one for the other — the product's job is to care about both. Second, on timing: AI generation quality has now risen far enough relative to cost that producing genuinely good cards from source text is finally viable.

## User & Persona

**Primary persona — the self-directed learner.** Two equally-served profiles share the same core pain:

- **The student** — learning defined material (e.g., textbook chapters, lecture notes, articles) and wanting to study it via spaced repetition.
- **The self-directed professional** — learning a new field on their own.

Both want to turn source material into good flashcards without paying the manual-creation cost. The MVP serves them equally — there is no primary/secondary split.

## Success Criteria

### Primary
- The full MVP flow works end-to-end: a logged-in user pastes source text, receives AI-generated candidate cards, reviews them (accept / edit / reject), saves the accepted ones, and studies the saved cards via the spaced-repetition scheduler.
- ≥ 75% of AI-generated flashcards are accepted by the user.
- ≥ 75% of the flashcards a user ends up with were created via AI (rather than manually). This share is expected to increase over time as generation quality improves.

### Secondary
- A user can view flashcard sets that other people have shared with them (set sharing works). This is a nice-to-have; the product proves out without it.

### Guardrails
- Per-user data isolation: no user can see another user's flashcards or data except sets explicitly shared with them.
- The user always reviews AI-generated cards before they are saved — no card is persisted without explicit user acceptance.
- Study scores are visible only to the user who submitted the answers — private even when the underlying set has been shared with others.

## User Stories

### US-01: Generate flashcards from pasted text

- **Given** a logged-in user with a flashcard set
- **When** they paste source text and request AI generation
- **Then** they receive a list of candidate flashcards to review, and each card they accept (as-is or after editing) is saved into the set while rejected cards are discarded

#### Acceptance Criteria
- No candidate card is saved until the user explicitly accepts it.
- The user can edit a candidate card's content before accepting it.
- Rejected candidates do not appear in the saved set.

## Functional Requirements

### Accounts
- FR-001: A user can create an account. Priority: must-have
  > Socrates: Counter — auth is overhead that delays first value. Resolution:
  > kept; persistent per-user storage and the 75%-AI metric require accounts.
- FR-002: A user can log in and log out. Priority: must-have
  > Socrates: Counter — same auth-overhead argument. Resolution: kept; required
  > to reach private per-user data.
- FR-003: A user can delete their account. Priority: must-have
  > Socrates: Counter — deletion is compliance hygiene, could slip to v2.
  > Resolution: kept as must-have; user confirmed.

### Flashcard sets
- FR-004: A user can create a flashcard set. Priority: must-have
  > Socrates: Counter — sets add structure before payoff; a single implicit pool
  > would be simpler. Resolution: kept; user explicitly wants multiple sets.
- FR-005: A user can view their flat collection of flashcard sets. Priority: must-have
  > Socrates: Counter — none material. Resolution: kept.
- FR-006: A user can rename a flashcard set. Priority: must-have
  > Socrates: Counter — rename could be nice-to-have. Resolution: kept as
  > must-have; user judged it small and worth including.
- FR-007: A user can delete a flashcard set. Priority: must-have
  > Socrates: Counter — none material. Resolution: kept.

### AI generation & review
- FR-008: A user can paste source text and request AI-generated flashcards into a set. Priority: must-have
  > Socrates: Counter — unbounded paste size could blow up cost/latency/quality.
  > Resolution: kept; input-size expectation tracked as an NFR.
- FR-009: A user can review each AI-generated candidate card and accept, edit, or reject it before saving. Priority: must-have
  > Socrates: Counter — per-card review adds friction that could skew the
  > 75%-accepted metric. Resolution: kept; it is the guardrail against silent
  > low-quality saves.

### Manual creation & card management
- FR-010: A user can manually create a flashcard in a set. Priority: must-have
  > Socrates: Counter — manual creation competes with the AI value prop and the
  > 75%-via-AI metric. Resolution: kept; it is a core function in the seed notes.
- FR-011: A user can view the flashcards in a set. Priority: must-have
  > Socrates: Counter — standard CRUD. Resolution: kept.
- FR-012: A user can edit a flashcard. Priority: must-have
  > Socrates: Counter — standard CRUD. Resolution: kept.
- FR-013: A user can delete a flashcard. Priority: must-have
  > Socrates: Counter — standard CRUD. Resolution: kept.

### Study
- FR-014: A user can study a set via the spaced-repetition scheduler. Priority: must-have
  > Socrates: Counter — biggest timeline risk (algorithm integration).
  > Resolution: kept; SRS confirmed in MVP, integrating a ready algorithm
  > (not building one).
- FR-015: A user can see their score/result from each completed study session (private to them). Priority: must-have
  > Socrates: Counter — scoring needs a defined "what counts as correct" rule.
  > Resolution: kept; exact score definition routed to Open Questions.

### Sharing
- FR-016: A user can share a flashcard set with another user. Priority: nice-to-have
  > Socrates: Counter — cross-user access complicates the isolation guardrail and
  > invites scope creep. Resolution: kept as droppable nice-to-have.
- FR-017: A user can view flashcard sets that others have shared with them. Priority: nice-to-have
  > Socrates: Counter — same as FR-016. Resolution: kept as droppable nice-to-have.

## Non-Functional Requirements

- The interface never freezes or blanks while AI generation is in progress; the user keeps a responsive UI throughout.
- A single generation request accepts source text up to 15,000 characters (roughly five printed A4 pages); larger input is rejected or trimmed rather than processed.
- Pasted source text leaves no trace in stored data after the generation request that consumed it completes — it is not persisted.
- The product is web-first and usable on the latest two major versions of mainstream desktop browsers. Mobile is supported only as a viewing experience and is a nice-to-have, not an MVP requirement.
- Everyday interactions — viewing cards, editing a card, and revealing/answering during a study session — respond fast enough to feel instant (well under one second).
- A basic accessibility baseline (e.g., keyboard navigability, sensible contrast) is a nice-to-have for the MVP — desirable but not required to ship.

## Business Logic

Given a block of source text, the app decides which pieces of it are worth learning and turns each into a question/answer flashcard phrased to balance specific detail with general-context understanding.

Each flashcard has two sides: a question (front) and an answer (back). When creating or managing cards, the user sees both sides at once. During a spaced-repetition study session, the app shows only the question first; the user reveals the answer, then self-marks whether they knew it or not. That self-assessment is the input the spaced-repetition schedule uses to decide when the card should appear again, and it is also what a session's score is derived from.

The spaced-repetition scheduling itself is a ready, integrated algorithm rather than a rule the product invents — the product's own domain decision is the generation step: what to turn into a card, and how to phrase it.

## Access Control

Multi-user, account-based, with a single flat role — every user is the same kind of user and owns their own private flashcards. There are no admin, teacher, or other elevated roles.

- Registered users authenticate to reach the app; flashcard data is private to the owning user.
- **In MVP scope:** account creation (sign-up) and account deletion.
- The exact authentication mechanism (password, OAuth, passwordless) is a downstream stack-selection decision and is intentionally not fixed here.
- Password reset and email verification are not specified as MVP requirements (see Open Questions).

## Non-Goals

- **No custom or advanced spaced-repetition algorithm** (SuperMemo/Anki-style) — the MVP integrates a ready algorithm rather than inventing one.
- **No multi-format import** — generation input is pasted text only; no PDF, document, or other file imports.
- **No integrations with other education platforms** — the product stands alone.
- **No native mobile apps** — web-first; mobile is at most a viewing experience through the web app, and even that is a nice-to-have.
- **No categories/folders for organizing sets** — a user's flashcard sets live in a single flat collection.

> Note: cross-user sharing of flashcard sets is intentionally NOT a non-goal — it is a droppable nice-to-have (FR-016, FR-017), kept so it is not assumed out of scope.

## Open Questions

1. **What exactly counts as a study session "score" / result?** FR-015 ships the score feature but the precise definition (e.g., share of cards self-marked "knew it", or another measure) is unresolved. — Owner: user. Block: no (FR-015 needs the definition before its UI can be finalized).
2. **Are password reset and email verification in MVP scope?** Access Control left these "open for later inclusion." — Owner: user. Block: no.
