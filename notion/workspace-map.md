# Workspace Map

Inventory of the pages and databases that make up the Personal/Work system,
as of 2026-09-02. IDs are Notion page/data-source IDs — fetch directly with
`notion-fetch` using the `https://app.notion.com/p/<id-no-dashes>` URL form,
or query a data source with `notion-query-data-sources` using the
`collection://<data-source-id>` form.

**Heads up:** as of 2026-09-02 this file was found to be significantly
stale relative to the live workspace — `Area` had been changed from a
select property to a relation pointing at a new **Areas** database,
entirely outside any Claude Code session (no session-log entry covers the
change). Re-verify anything below with a live `notion-fetch` before relying
on it for a non-trivial change; don't assume this file is caught up just
because it was recently edited.

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

### Work (old, top-level) — `3cd06a5a-b036-8099-a149-fb66ceadf024`
Private top-level page, predates the Areas database below. Duplicates the
new **Work** Area page's Tasks/Projects views — kept as-is per the user
(2026-09-02), not retired. Structure:
- `## ✅ Tasks` → **Work Tasks** view — `view://3cd06a5a-b036-81c7-9262-000c34001c8e`
  — filter `Completed ≠ true AND Area = Work` (relation), sorted by Due
  Date asc.
- `## 📁 Projects` → **Work Projects** view — `view://3cd06a5a-b036-81ef-b96c-000c158c2e77`
  — filter `Area = Work` (relation).
- `## 🗒️ Scratchpad` → link to **AE Todo** (`3cd06a5a-b036-80b7-b242-fd86b27cd68b`),
  now a scratch/notes page, not a task list — the one open item it used to
  hold was migrated into the shared Tasks database.

**Fixed 2026-09-02:** both views had been built against the old `Area`
*select* property and silently lost their Area filter when it was
converted to a relation (see the gotcha noted in `conventions.md`) — Work
Tasks was showing all incomplete tasks from every area, Work Projects had
no filter at all. Both now filter on the `Area` relation pointing at the
Work Area page (`https://app.notion.com/p/3cf06a5ab036818b93a2c9ad7ec540fb`).
Same gotcha is worth checking for on any other view built before the
select→relation conversion that this pass didn't happen to touch.

## Dashboard (formerly "Inbox") — `34d06a5a-b036-80e0-8ce9-ceeee841a3ab`
Title/icon: "Dashboard" / 🗂️ (renamed from "Inbox" / 📬 — see
`conventions.md` for why). This is the **planning** surface, not the capture
surface. Live layout as of 2026-09-02 (rebuilt since this file was last
accurate — the two-column quick-view section described previously no
longer exists):
- `## 🎯 Mode Board — ready to work, by context` → inline Tasks database
  embed (Mode-filtered views live on the Tasks data source itself, see
  below)
- Three full database embeds: Tasks — `34d06a5a-b036-804b-b00e-c5cfb202d856`
  (data source `collection://34d06a5a-b036-805d-a876-000b3b97dc18`),
  Projects Database — `34d06a5a-b036-8098-accd-c5bd4e5ab7bf` (data source
  `collection://34d06a5a-b036-8014-93bb-000ba1a0486a`), Routines Database —
  `34d06a5a-b036-803c-a9da-fe7b0aef724b` (data source
  `collection://34d06a5a-b036-8092-aa2c-000b6efe63ed`)
- `## 🧭 Areas — the taxonomy Tasks and Projects both point to` → inline
  **Areas** database embed — `5848b419-2527-4481-8aa5-ee5a9cec5bdb`
  (data source `collection://757e232c-13bb-409d-9430-3255aca40768`). See
  "Areas database" below — this is the important structural addition this
  file had missed entirely.

## Areas database — `5848b419-2527-4481-8aa5-ee5a9cec5bdb`
(data source `collection://757e232c-13bb-409d-9430-3255aca40768`)
Lives inline on the Dashboard page (see above). Schema: `Name` (title),
`Description` (text), `Projects` (relation → Projects Database), `Tasks
(direct)` (relation → Tasks database). This replaced the old `Area` select
property on Tasks/Projects — both now carry an `Area` **relation** pointing
at rows here instead (see "Not yet integrated" note in conventions.md's
old text — that whole model is gone).

**Rows (13 active, 1 retired) as of 2026-09-02:** Work, Household,
Backpacking, Biking, Software Projects, Outings, Health & Fitness, Music,
Fishing, Disc Golf, Running, Finances, Sewing, and `⚠️ Personal (retired —
broken into specific areas) — safe to delete` (flagged, not deleted — no
delete tool available, see conventions.md).

**Every active row got a page built out 2026-09-02** (this was empty
before): each Area page now has —
```
## ✅ Tasks
<inline Tasks view, table, FILTER "Area" = "<this-area's-page-url>">
## 📁 Projects
<inline Projects view, table, FILTER "Area" = "<this-area's-page-url>">
```
View names follow `"<Area> Tasks"` / `"<Area> Projects"` (e.g. "Household
Tasks", "Household Projects"). No additional sort or Completed filter was
added — deliberately literal to what was asked; add one later if the
unfiltered-completed-tasks noise turns out to matter in practice.
Area pages sit under Dashboard → Areas structurally (each is a database
row/page under the Areas data source, which is embedded on Dashboard), so
"live in the dashboard" is satisfied by nesting, not a sidebar link.

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

Single database for all tasks, split by the `Area` **relation** (→ Areas
database, see above) rather than by separate databases (see
`conventions.md`). As of 2026-08 this was a select property with three
values (Work/Personal/Household) — it was converted to a relation at some
point before 2026-09-02 outside any logged session; treat the select-based
description as historical only. Also carries the `Related Household
Project` relation described above.

**`Mode` property** (added 2026-09-02, replaces the old `Daily Priority`
field): select with options `Before Work`, `In Car`, `On Lunch`,
`Making Dinner`, `At Home`, and `Errands` (confirmed intentional by the
user 2026-09-02, after this file had flagged it as unexplained — it's a
real sixth mode, just not yet written up on the Habit System page's Modes
table below since its "When"/cue hasn't been worked out yet) — recurring
time/place contexts in the user's
day, not urgency and not time-of-day in the generic Morning/Afternoon/
Evening sense the old field used. See the Habit System page's "Modes"
section for what belongs in each and the physical cue tied to it. A task
can reasonably fit more than one Mode (laundry: Before Work or At Home) —
that's expected, not a data problem; the `/ganymedes` skill (see below) is
how those get resolved case by case rather than pinned permanently.

**`Top of Mind` property** (added 2026-09-02): a separate checkbox, not a
Mode option — flags urgency independent of context. Deliberately split out
from Mode so an item can be both urgent and tied to a context. The "Top of
Mind" view now actually filters on this checkbox (it previously only
filtered `Completed`, which made it identical to an unfiltered list).

Five new filtered views exist on this data source, one per Mode value:
Before Work, In Car, On Lunch, Making Dinner, At Home — same shape as the
"Top of Mind" view (list, `Completed = false` + `Mode = <value>`).

**`/ganymedes` skill** — a project-scoped Claude Code skill at
`.claude/skills/ganymedes/` in this repo. Invoked with `/ganymedes`, it
triages tasks with no `Mode` set and unprocessed Inbox items through
conversation. See the skill file itself for behavior; it's the mechanism
for keeping Mode assignments current without a one-time bulk pass.

## Projects Database — `34d06a5a-b036-8098-accd-c5bd4e5ab7bf`
(data source `collection://34d06a5a-b036-8014-93bb-000ba1a0486a`)
Has the same `Area` relation as Tasks (→ Areas database, see above).
Tagging coverage as of 2026-09-02 not re-audited this session — re-check
row-by-row before assuming it's complete; the 2026-08 note that most rows
were untagged predates the select→relation conversion and may no longer be
accurate.

## Not yet integrated
- Projects Database `Area` tagging coverage: not re-audited as part of the
  2026-09-02 Area-pages build — worth a pass if Area-filtered Project views
  look sparse.
- The Household Projects ↔ Tasks two-way relation exists but is unused —
  none of the 12 open Household Projects rows are linked to a personal
  task yet (status as of 2026-08, not re-checked 2026-09-02).
