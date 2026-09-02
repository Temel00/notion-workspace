# Workspace Map

Inventory of the pages and databases that make up the Personal/Work system,
as of 2026-08-31. IDs are Notion page/data-source IDs — fetch directly with
`notion-fetch` using the `https://app.notion.com/p/<id-no-dashes>` URL form,
or query a data source with `notion-query-data-sources` using the
`collection://<data-source-id>` form.

## Top level

### Personal — `3cd06a5a-b036-804b-aa9e-d73794d805af`
Private top-level page. Entry point for personal life admin. Links out to:
- **Dashboard** (below) — planning views into Tasks/Projects/Routines
- **Habit System** (below) — daily loop, weekly review, capture Inbox (ideas
  included — see "Ideas has no separate page" in `conventions.md`)
- **Known subscriptions** — `37c06a5a-b036-80cd-ba97-f50c71e1c26d` — static
  reference list (Garmin, Claude Pro, Squarespace domains, Bulwark), not a
  database, no upkeep expected
- **Backpacking** — `35306a5a-b036-80f0-8c4e-f78f55fb0d81` — static link/note
  dump for trip research, not a database, no upkeep expected

Shared items formerly parented here (Household Projects, Recipe Book) were
moved to the Brother's Joint Teamspace — see below. Personal's own callout
says so; don't re-add them here.

### Work — `3cd06a5a-b036-8099-a149-fb66ceadf024`
Private top-level page, mirrors Personal's role for work life. Structure:
- `## ✅ Tasks` → **Work Tasks** view (table, filter `Area = Work AND
  Completed ≠ true`, sorted by Due Date asc) — `view://3cd06a5a-b036-81c7-9262-000c34001c8e`
- `## 📁 Projects` → **Work Projects** view (table, filter `Area = Work`)
  — `view://3cd06a5a-b036-81ef-b96c-000c158c2e77`
- `## 🗒️ Scratchpad` → link to **AE Todo** (`3cd06a5a-b036-80b7-b242-fd86b27cd68b`),
  now a scratch/notes page, not a task list — the one open item it used to
  hold was migrated into the shared Tasks database.

Both views read from the shared **Tasks database** (see below), filtered by
the `Area` property — Work does not have its own separate task database.

## Dashboard (formerly "Inbox") — `34d06a5a-b036-80e0-8ce9-ceeee841a3ab`
Title/icon: "Dashboard" / 🗂️ (renamed from "Inbox" / 📬 — see
`conventions.md` for why). This is the **planning** surface, not the capture
surface. Layout: two-column quick-view section (To Process / This Week |
Projects / Routines) followed by the three full database embeds:
- Tasks — `34d06a5a-b036-804b-b00e-c5cfb202d856` (data source
  `collection://34d06a5a-b036-805d-a876-000b3b97dc18`)
- Projects Database — `34d06a5a-b036-8098-accd-c5bd4e5ab7bf` (data source
  `collection://34d06a5a-b036-8014-93bb-000ba1a0486a`)
- Routines Database — `34d06a5a-b036-803c-a9da-fe7b0aef724b` (data source
  `collection://34d06a5a-b036-8092-aa2c-000b6efe63ed`)

## Habit System — `3bf06a5a-b036-815c-b144-ca3ab35d8b9c`
Well-built, deliberately untouched by the org cleanup. Contains:
- **Habits** database — habit tracking
- **Daily Log** database
- **Inbox** database (the *real* capture inbox) — `a20ebfc0-699b-4608-bb23-4db2cbff6f9d`.
  This is the one place to drop unsorted thoughts — one tap, no triage
  required at capture time. Distinct from the Dashboard page above.

Grounded in behavior-change research (Gollwitzer & Sheeran, Lally et al.,
Harkin et al., Masicampo & Baumeister, Fogg) — see `productivity-principles.md`.

## Brother's Joint Teamspace — team id `37506a5a-b036-81d0-90fe-0042296cdb94`
Shared space, editable by the user and their brother.

### Teamspace Home — `8ae06a5a-b036-8281-a782-8117794b5736`
Landing page for the shared space. Contains inline **Household Projects**
embed and a mention-link to **Recipe Book**.

### Household Projects — `37606a5a-b036-80a8-b31b-c2823010a7de`
(data source `collection://22a06a5a-b036-83d8-88d1-077733d7f627`)
Moved here from an orphaned, parentless state. Has a two-way relation to the
personal Tasks database:
- Household Projects → **Related Personal Tasks** (relation property)
- Tasks → **Related Household Project** (mirrored relation property)

Use this relation when a household project spins off a task the user
personally owns — link it back to its source project instead of duplicating
info in the task title/description.

### Recipe Book — `6db06a5a-b036-82e5-99d2-8138e02c68ff`
Shared resource, unchanged by this project — referenced here only because
Household Projects was deliberately placed alongside it.

## Shared Tasks database — `34d06a5a-b036-804b-b00e-c5cfb202d856`
(data source `collection://34d06a5a-b036-805d-a876-000b3b97dc18`)

Single database for all tasks — Personal, Work, and Household — split by the
`Area` property rather than by separate databases (see `conventions.md`).
All 42 rows (as of the 2026-08 cleanup) are tagged with `Area`. Also carries
the `Related Household Project` relation described above.

## Projects Database — `34d06a5a-b036-8098-accd-c5bd4e5ab7bf`
(data source `collection://34d06a5a-b036-8014-93bb-000ba1a0486a`)
Has the same `Area` select property as Tasks (added for future work-project
use) but existing rows are all Personal-flavored and were left untagged:
001 Emelbros website, 002 Garden Innovations, 003 Mother's Day 2026,
004 Financial Tools, Sew a backpack.

## Not yet integrated
- Projects Database rows are still mostly untagged with `Area` (only
  "Order Alignment App" carries one, as `Work`) — flagged in the 2026-08
  pass, still true as of 2026-09-02.
- The Household Projects ↔ Tasks two-way relation exists but is unused —
  none of the 12 open Household Projects rows are linked to a personal
  task yet.
