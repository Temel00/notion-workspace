# Conventions

## Area is a relation to an Areas database, not a select property
**Superseded 2026-09-02** — this used to be a select property
(`Work`/`Personal`/`Household`) on Tasks, described below in the original
wording for history. At some point before 2026-09-02, outside any logged
Claude Code session, it was converted to a relation pointing at a new
**Areas** database (`collection://757e232c-13bb-409d-9430-3255aca40768`,
embedded on Dashboard) — one row per area of life (Work, Household,
Backpacking, Software Projects, Finances, etc. — 13 active rows as of
2026-09-02, see `workspace-map.md`). Both Tasks and Projects now carry an
`Area` relation to this database instead of a select value. When filtering
a view by Area now, use the DSL's relation syntax — `FILTER "Area" =
"<area-page-url>"` — not an option name; resolve the area's page URL first
(search/fetch or `workspace-map.md`).

**Original rationale (still holds, just via relation now):** one shared
Tasks database rather than per-area databases — one place to search, one
place to review, filtered views do the separation instead of the data
model. When adding a new task, always set `Area`. When adding a new
task-like database, prefer adding an `Area` relation to it over spinning up
a parallel database, unless there's a real reason the item categorically
isn't a task (e.g. Recipe Book entries).

**Gotcha to watch for:** converting a property's type (select → relation,
or similar) does not migrate existing view filters that reference it — the
filter condition silently vanishes from the view's filter group instead of
erroring. The old top-level Work page's "Work Tasks"/"Work Projects" views
were built against the old select property and are now silently
unfiltered-by-area (still filtering Completed only) — see the ⚠️ note on
the "Work (old, top-level)" entry in `workspace-map.md`. After any property
type change, re-check every view that filtered on it, don't assume it
migrated cleanly.

## Area pages (under the Areas database) — standard layout
Each Area's page (a row in the Areas database) should hold two headed,
Area-filtered inline views, in this order:
```
## ✅ Tasks
<Tasks view, table, FILTER "Area" = "<this-area's-page-url>", named
"<Area> Tasks">
## 📁 Projects
<Projects view, table, FILTER "Area" = "<this-area's-page-url>", named
"<Area> Projects">
```
Built this way for all 13 active Area rows on 2026-09-02 (see
`workspace-map.md` for the list and the retired row that was skipped).
When creating a new Area row later, replicate this exact pattern rather
than reinventing a layout — it's what "an Area page" means in this
workspace now. Use `notion-create-view` with `parent_page_id` (not
`database_id`) to append linked views to the Area page itself; add each
section's heading via `update-page insert_content` *before* creating that
section's view, and do the two Tasks-then-Projects sections as separate
insert→create-view round trips — both `insert_content` and `create-view`
append to the end of the page, so interleaving heading-then-view per
section is what keeps the final order correct (batching both headings
before both views produces the wrong order).

## "Inbox" is a reserved name — capture only
Only one thing in this workspace should ever be named "Inbox": the capture
database inside Habit System (`a20ebfc0-699b-4608-bb23-4db2cbff6f9d`). It
exists so a stray thought can be captured in one tap with zero triage
decisions (GTD capture principle — see `productivity-principles.md`).

The page that used to be named "Inbox" (`34d06a5a-b036-80e0-8ce9-ceeee841a3ab`)
was actually a **planning dashboard** — curated views for weekly/daily
review — and got renamed to "Dashboard" to remove the collision. If you're
ever tempted to name something "Inbox," check whether you actually mean
"Dashboard," "Backlog," or "Unsorted" instead.

## Ideas has no separate page — capture them in Inbox too
There used to be a standalone "Ideas" page. As of 2026-09-02 it's gone (it
stopped appearing in search/fetch entirely — never diagnosed why, possibly
deleted via the Notion app), but a description callout on Personal still
referenced it, and two of its old items had been manually copied into Tasks
as untagged, undated rows with nowhere else to go. This is the exact "which
place do I use" friction the Inbox is supposed to prevent, so the fix was to
stop having a second landing spot rather than recreate the page: ideas now
go into the same Inbox as everything else and get sorted into a real task
(or dropped) at weekly review, same as any other captured thought. Don't
recreate a separate Ideas page — if the user wants one back, that's a
deliberate reversal to confirm with them, not a default to restore.

## Shared vs. private placement
Anything the user's brother also needs to edit belongs in the **Brother's
Joint Teamspace**, parented under Teamspace Home — not under Personal, even
if it started life as a personal page. (Household Projects was moved there
for exactly this reason.) A relation back into the personal Tasks database
is the intended way to pull a personal action item out of a shared project
without duplicating the shared item itself.

## Two-way relations for cross-database links
When linking a shared/household item to a personal task, use a `DUAL`
(two-way sync) relation via `notion-update-data-source`, e.g.:

```sql
ADD COLUMN "Related Personal Tasks" RELATION('<tasks-data-source-id>', DUAL 'Related Household Project')
```

This keeps both sides navigable without manual upkeep.

## Known tool limitations
- **No delete/trash tool.** As of 2026-08, the available Notion MCP tools
  (`notion-update-page`, `notion-update-data-source`, `notion-move-pages`,
  `notion-create-pages`, `notion-create-view`, etc.) cannot delete or
  archive a page or database row. Re-check with `ToolSearch` at the start
  of a session before assuming this is still true — if a delete tool has
  since appeared, prefer it over the workaround below.
  - **Workaround:** rename the row to a clearly flagged, unambiguous title
    (e.g. `"⚠️ Duplicate — safe to delete (merged into X)"` or
    `"⚠️ Empty row — safe to delete"`), leave a one-line note explaining
    why, and tell the user to manually delete it via right-click → Delete
    in the Notion app. Don't leave a blank/duplicate row silently
    unlabeled — flag it even if you can't remove it.

## Mode vs. Area vs. Top of Mind — don't conflate these
Three different axes exist on Tasks and are easy to blur together:
- `Area` — which part of life (Work/Personal/Household).
- `Mode` — which recurring time/place context the task fits (Before Work,
  In Car, On Lunch, Making Dinner, At Home). Added 2026-09-02, replacing
  the old `Daily Priority` field (Top of Mind/Morning/Afternoon/Evening),
  which was time-of-day-only and mostly unused.
- `Top of Mind` — a plain checkbox for urgency, independent of the other
  two. It used to be a `Daily Priority` option; it was deliberately split
  into its own property so an item can be both urgent and tied to a Mode
  at once, which a single shared field couldn't express.

Don't add a new option to `Mode` for "urgent" or fold urgency logic into
it — that's what `Top of Mind` is for.

## `notion-update-data-source`: rename + alter in one statement batch can misfire
Combining `RENAME COLUMN "X" TO "Y"` with `ALTER COLUMN "Y" SET SELECT(...)`
in the same `statements` call did not alter the renamed column in place —
it silently created a second column (`"Y 1"`) with the new options instead,
leaving the renamed column with its old option set. The same thing happened
combining `DROP COLUMN "Y"` with `RENAME COLUMN "Y 1" TO "Y"` in one call —
it produced `"Y 2"` instead. The fix: issue each DDL statement that touches
the same column name in its own separate tool call, letting each fully
commit before the next runs. Always re-fetch the schema after a rename or
alter to confirm the column landed with the name and options you expect
before moving on — don't assume the call did what it said.

## `notion-create-view` needs both `database_id` and `data_source_id`
Passing only `data_source_id` (the collection URL) fails with "Exactly one
of `database_id` or `parent_page_id` must be provided" — pass the parent
database's page ID as `database_id` alongside `data_source_id`, not instead
of it.

## Content edits: search-and-replace safety
When restructuring a page's content with `update_content` (search/replace)
rather than `replace_content`, reusing an existing block's *own* URL inside
a `<page>`/`<database>` tag does not move it — it's already parented there.
Only pointing a `<page>`/`<database>` tag at a *different* existing page or
database's URL triggers an actual move. Keep this in mind before assuming a
content edit is purely cosmetic.
