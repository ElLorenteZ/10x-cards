---
name: db-migration
description: Scaffold a new Supabase migration file in supabase/migrations/ following this project's timestamp naming and RLS policy conventions. User-triggered with /db-migration <short_description>.
disable-model-invocation: true
---

# New Supabase migration

Create a migration in `supabase/migrations/` for the change described in `$ARGUMENTS`.

## Filename

`YYYYMMDDHHmmss_<short_description>.sql` — generate the UTC timestamp with:

```
date -u +%Y%m%d%H%M%S
```

Slugify `$ARGUMENTS` into `<short_description>` (lowercase, underscores). If `$ARGUMENTS` is empty, ask the user what the migration does before writing anything.

## Content rules (project convention)

- **Always `enable row level security`** on any new table.
- Write **granular policies**: one policy per operation (`select`, `insert`, `update`, `delete`) per role (`anon`, `authenticated`) — do not collapse into a single `for all` policy.
- Add a brief comment header explaining intent.

## Template

```sql
-- <intent of this migration>

create table if not exists public.<table> (
  id uuid primary key default gen_random_uuid(),
  user_id uuid not null references auth.users(id) on delete cascade,
  created_at timestamptz not null default now()
);

alter table public.<table> enable row level security;

-- authenticated users can read their own rows
create policy "<table>_select_authenticated"
  on public.<table> for select to authenticated
  using ( (select auth.uid()) = user_id );

-- authenticated users can insert their own rows
create policy "<table>_insert_authenticated"
  on public.<table> for insert to authenticated
  with check ( (select auth.uid()) = user_id );

-- add update / delete policies per operation as needed
```

After writing, remind the user to apply it (`npx supabase db push`, or `npx supabase migration up` against a local stack started with `npx supabase start`).
