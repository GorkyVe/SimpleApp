-- Table
create table public.notes (
  id uuid primary key default gen_random_uuid(),
  user_id uuid not null references auth.users(id) on delete cascade,
  content text default '',
  updated_at timestamptz not null default now()
);

create unique index notes_user_unique on public.notes (user_id);

-- Row Level Security
alter table public.notes enable row level security;

create policy "select own" on public.notes
  for select using (auth.uid() = user_id);

create policy "insert own" on public.notes
  for insert with check (auth.uid() = user_id);

create policy "update own" on public.notes
  for update using (auth.uid() = user_id);