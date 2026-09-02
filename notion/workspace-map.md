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
Private top-level page, mirrors Personal's role for work life. Structure
as of 2026-08-31 (still current as of this writing, but **slated for a
redesign** — see "Planned: Work page pivot" below):
- `## ✅ Tasks` → **Work Tasks** view (table, filter `Area = Work AND
  Completed ≠ true`, sorted by Due Date asc) — `view://3cd06a5a-b036-81c7-9262-000c34001c8e`
- `## 📁 Projects` → **Work Projects** view (table, filter `Area = Work`)
  — `view://3cd06a5a-b036-81ef-b96c-000c158c2e77`
- `## 🗒️ Scratchpad` → link to **AE Todo** (`3cd06a5a-b036-80b7-b242-fd86b27cd68b`),
  now a scratch/notes page, not a task list — the one open item it used to
  hold was migrated into the shared Tasks database.

Both views read from the shared **Tasks database** (see below), filtered by
the `Area` property — Work does not have its own separate task database.

**Planned: Work page pivot (stated 2026-09-02 evening, not yet executed).**
Now that the Dashboard's Mode Board is meant to be the single place the
user actually works from, the user no longer wants Work to function as a
second place to track/complete tasks — that's exactly the "things could
fall through the gaps" risk of two working surfaces. Direction given:
- The `Work Tasks` filtered view (`Area = Work`, read-only reference) is
  fine to keep — it's just for visibility, not a place to work from.
- The `Work Projects` view's usefulness was called into question too
  ("hasn't been that helpful") — worth discussing whether it stays, changes
  shape, or goes, rather than assuming it's kept as-is.
- Work should become a page for **project-specific notes/reference
  material** — things the user wants to remember — and **frequently used
  links/locations** (webpages, tools), not a task tracker in its own right.
This has not been implemented yet — it's a next-session task.

## Dashboard (formerly "Inbox") — `34d06a5a-b036-80e0-8ce9-ceeee841a3ab`
Title/icon: "Dashboard" / 🗂️ (renamed from "Inbox" / 📬 — see
`conventions.md` for why). This is the **planning** surface, not the capture
surface, and as of 2026-09-02 evening it's also the intended **single
working surface**: the user works by opening this page, looking at the
Mode they're in, and knocking out what's ready — not by visiting Work or
Personal separately (see Work section below and `session-log.md` for the
in-progress follow-through on that).

Layout (rebuilt 2026-09-02 evening, replacing the old two-column quick-view
+ three-full-embed layout entirely):
- **Mode Board** — an inline embed of the Tasks database's "Mode Board"
  view (board type, grouped by `Mode`, `Completed = false`). This is the
  page's primary content now.
- Below a divider, the three real databases (Tasks, Projects Database,
  Routines Database) are kept as small `inline="false"` linked references
  — not full tables — purely so they stay referenced (removing all
  reference to a real database via `replace_content` deletes it; see
  `conventions.md`). Click through to reach the full tables.
- The old quadrant boxes (To Process / This Week / Projects / Routines
  quick-views) were removed. They were confirmed to be view-only stub
  database blocks with no independent data (the Notion API's own
  delete-protection error listed them as separate untitled database
  objects, but per this file's own history they were always filtered
  views over the shared Tasks/Projects/Routines data) — removing them lost
  no data.

Canonical database pages (still the source of truth, just no longer shown
as full tables here):
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

**Active habits as of 2026-09-02: exactly 3** (the schema's own max) —
Two bottles front-loaded, Consistent wake time, and the new Morning
movement (added this date; Started reset to 2026-09-02 for all three so
the ~66-day automaticity estimate is measured from a clean point). Last
coffee is the lunch coffee and Morning daylight with coffee are paused
(not automatic yet per the user directly) — Morning daylight is first in
line to return once wake time is solid. The 5pm landing habit is paused
and superseded: its phone/bottles actions moved to being unconditional on
arrival, its shoes-off action moved to being the deliberate trigger for
the At Home Mode (see Habit System page's "Modes" section) — don't
reactivate it as a habit-to-automate, it's been intentionally restructured
into the Mode system instead.

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

**`Mode` property** (added 2026-09-02, replaces the old `Daily Priority`
field): select with options `Before Work`, `In Car`, `On Lunch`,
`Making Dinner`, `At Home`, `Errands` (`Errands` added 2026-09-02 evening —
see below) — recurring time/place contexts in the user's day, not urgency
and not time-of-day in the generic Morning/Afternoon/Evening sense the old
field used. See the Habit System page's "Modes" section for what belongs
in each and the physical cue tied to it. A task can reasonably fit more
than one Mode (laundry: Before Work or At Home) — that's expected, not a
data problem; the `/ganymedes` skill (see below) is how those get resolved
case by case rather than pinned permanently.

**`Errands` Mode** (added 2026-09-02 evening): distinct from `At Home` —
`At Home` is for quick tasks completable inside the apartment; `Errands` is
for tasks requiring the user to be out and about (shopping, appointments,
anything needing a physical trip). Added mid-session when `/ganymedes`
triage revealed several backlog tasks didn't fit any of the original 5
Modes for exactly this reason.

**`Top of Mind` property** (added 2026-09-02): a separate checkbox, not a
Mode option — flags urgency independent of context. Deliberately split out
from Mode so an item can be both urgent and tied to a context. The "Top of
Mind" view now actually filters on this checkbox (it previously only
filtered `Completed`, which made it identical to an unfiltered list).

**Prerequisite tracking** (added 2026-09-02 evening): came up when
`/ganymedes` split two-step tasks (e.g. "Replant Cactus and bring into
work" → "Replant Cactus" + "Bring cactus into work") and the sequencing
between the pair was otherwise lost. Four new properties on this data
source:
- `Blocked By` / `Enables` — a self-referencing `DUAL` relation (Tasks →
  Tasks), same mechanism as the Household Projects ↔ Tasks relation.
  `Blocked By` holds the prerequisite task(s); `Enables` is its
  auto-synced mirror on the prerequisite side.
- `Open Prerequisites` — rollup on `Blocked By` → `Completed`, aggregation
  `unchecked`. Counts how many linked prerequisites are still open (0 if
  empty or all done).
- `Ready` — formula, `prop("Open Prerequisites") == 0`. True when a task
  has no open prerequisite.
- `Created` — `created_time` property, added because `Ready`'s sibling
  `Aging Flag` formula needed a referenceable creation timestamp (the
  data source's implicit `createdTime` isn't exposed as a schema property
  usable in formulas).
- `Aging Flag` — formula, shows a bold red "⚠ 2+ weeks old" badge when a
  task is incomplete and `Created` is 14+ days ago, else blank. Pure
  visual signal (see `conventions.md` for why it can't drive a view filter
  yet).

Six filtered list views exist on this data source, one per Mode value:
Before Work, In Car, On Lunch, Making Dinner, At Home, Errands — same
shape as the "Top of Mind" view (list, `Completed = false` + `Mode =
<value>`), plus `Aging Flag` shown as a display column. A seventh view,
**Mode Board** (board type, `GROUP BY Mode`, `Completed = false`), also
exists as a tab on this database and is separately embedded on the
Dashboard page (see below) as the primary "what can I work on right now"
surface. **None of these 7 views filter on `Ready` yet** — see
`conventions.md`'s "View filter DSL" limitation entry; adding `Ready is
checked` to each is a pending manual step in the Notion app.

**`/ganymedes` skill** — a project-scoped Claude Code skill at
`.claude/skills/ganymedes/` in this repo. Invoked with `/ganymedes`, it
triages tasks with no `Mode` set and unprocessed Inbox items through
conversation. See the skill file itself for behavior; it's the mechanism
for keeping Mode assignments current without a one-time bulk pass.

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
