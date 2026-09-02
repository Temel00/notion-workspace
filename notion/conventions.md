# Conventions

## Area property, not separate databases
Personal, Work, and Household tasks live in **one shared Tasks database**,
distinguished by an `Area` select property (`Work` blue / `Personal` green /
`Household` orange). This was a deliberate choice over per-area databases:
one place to search, one place to review, filtered views (Work Tasks,
Work Projects, Dashboard's "This Week") do the separation instead of the
data model. When adding a new task, always set `Area`. When adding a new
task-like database, prefer adding `Area` to it over spinning up a parallel
database, unless there's a real reason the item categorically isn't a task
(e.g. Recipe Book entries).

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
