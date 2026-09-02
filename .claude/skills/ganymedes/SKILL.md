---
name: ganymedes
description: Triage session for the user's personal Notion Tasks database and Inbox — sorts new or unassigned tasks into a "Mode" (Before Work, In Car, On Lunch, Making Dinner, At Home) through quick back-and-forth conversation, and processes unprocessed Inbox items into real tasks. Use this whenever the user types /ganymedes, or asks to "sort my tasks," "process my inbox," "figure out modes for my tasks," "do a triage session," or otherwise wants help organizing their personal Notion Tasks/Inbox by time-of-day or context. This is specific to the user's personal life-organization system in this repo, not a general project-management or code-task tool — don't trigger it for software project tasks or GitHub issues.
---

# Ganymedes: Mode triage for the personal Notion workspace

A short, low-friction session that sorts loose tasks into the right `Mode` and
clears the Inbox — nothing more. The whole point of this system (see
`notion/productivity-principles.md`) is minimizing decisions and friction, so
this should feel like a quick pass, not an audit. If a session runs long,
that's a sign to stop and pick it up next time, not to push through.

## Before doing anything

Read these three files in this repo — they're the standing memory for this
workspace and this session must not contradict them or re-derive facts they
already settled:

- `notion/workspace-map.md` — IDs for every page/database mentioned below,
  plus current structure.
- `notion/conventions.md` — schema rules and hard constraints. In
  particular: **there is no delete tool.** If something needs removing (a
  duplicate, a blank row), rename it to flag it for manual deletion per the
  documented workaround — never claim to have deleted something you
  haven't.
- `notion/productivity-principles.md` — why this system is shaped the way
  it is. The short version: one capture point (Inbox), no decisions at
  capture time, small reviews that actually happen beat thorough ones that
  don't.

Use the Notion MCP server tools (`notion-query-data-sources`,
`notion-update-page`, `notion-fetch`, etc.) for everything below — this
skill has no other data source. If those tools aren't available, say so and
stop rather than guessing at the user's task list.

## What you're triaging

Two sources, both fetched at the start of the session:

1. **Tasks with no Mode set.** Query the Tasks data source
   (`collection://34d06a5a-b036-805d-a876-000b3b97dc18`) for
   `Completed = false AND Mode IS NULL`. These are the backlog.
2. **Unprocessed Inbox items.** Query the Inbox data source
   (`collection://107c9fb7-193c-4197-b685-5d02e4b42264`, inside Habit
   System) for `Processed = false`. These aren't tasks yet — they're raw
   captures that need a decision.

The five Mode options are: **Before Work**, **In Car**, **On Lunch**,
**Making Dinner**, **At Home**. There's also a separate `Top of Mind`
checkbox (urgency, independent of Mode) and the existing `Area` property
(Work/Personal/Household) — don't conflate these with Mode.

## How to run the session

Work through items in small batches (3-5 at a time), not one giant list dump
followed by silence. For each item:

1. **Propose a Mode based on the content**, and say why in one line — the
   user should be able to just say "yep" most of the time rather than
   re-explain their own task back to you.
2. **When a task genuinely fits more than one Mode** (laundry could be
   Before Work or At Home, a phone call could be In Car or On Lunch), don't
   silently pick one and don't try to resolve it "permanently" — ask the
   user which one fits *today*. The whole reason this is a recurring skill
   and not a one-time migration is that the right answer can change next
   time depending on what the day actually looks like. If the user says
   "skip this one," leave its Mode unset and move on — an unresolved item
   isn't a failure state, it's just still in the backlog for next time.
3. **Write the change immediately** via `notion-update-page` as soon as the
   user confirms a Mode — don't batch every decision to the end of the
   conversation and then apply them all at once. If the session gets
   interrupted, whatever's been decided so far should already be saved.

For Inbox items, the goal is turning a raw capture into either a real task
or nothing:

1. Show the item's `Item` and `Next action` text.
2. Ask (or infer and confirm) whether it's still relevant. If not, that's a
   fine outcome — mark `Processed = true` and move on. Not everything
   captured needs to become a task.
3. If it's still relevant, help the user turn it into a Tasks row: title,
   `Area`, `Mode` if a clear one applies (don't force it if none fits),
   and a `Due Date` if there's a real one — per
   `productivity-principles.md`, prefer attaching a concrete date when one
   genuinely exists, but don't invent one just to fill the field.
4. Create the Task, then mark the Inbox row `Processed = true`. Do this as
   two explicit steps — a processed Inbox item with no resulting task (a
   deliberate "drop it") is a valid outcome, not a bug, so don't skip
   marking it processed just because nothing got created.

## Ending the session

Give a short summary: how many tasks got a Mode, how many Inbox items got
processed (and into what), and what's still sitting unresolved. Keep this to
a few lines — the value of this system is the review *happening* quickly,
not the writeup being thorough.

If anything came up worth remembering for next time — a task that keeps
bouncing between two Modes depending on the day, a new convention the user
stated in passing, a recurring type of Inbox item — add a short entry to
`notion/session-log.md` the same way other sessions touching this workspace
do (see this repo's `README.md` working agreement), then commit it. Don't
create a log entry for a routine session where nothing new was decided —
that file is for what changed, not a transcript of every run.
