# Notion Workspace — Memory Bank

This repo is not a software project. It's persistent memory for Claude sessions
that help manage **temelaudio@gmail.com's** personal Notion workspace.

Notion has no durable "notes to future self" mechanism that Claude can rely on
between sessions, so this repo fills that gap: it records how the workspace is
structured, why it's structured that way, and what's already been decided —
so a new session doesn't have to re-survey the whole workspace or re-litigate
settled questions before it can be useful.

## How to use this repo (for Claude)

At the start of any session that touches the Notion workspace:

1. Read `notion/workspace-map.md` first — it's the map of what exists, where,
   and its Notion page/database ID so you can `notion-fetch` it directly
   instead of searching.
2. Read `notion/conventions.md` — naming and schema conventions already in
   place. Follow them; don't invent parallel conventions.
3. Skim `notion/session-log.md` for recent entries — recent decisions and
   open loose ends live there.
4. Read `notion/productivity-principles.md` when a change touches how the
   user captures, plans, or reviews work — it's the standing rationale for
   *why* things are shaped the way they are, so new structure stays
   consistent with it instead of drifting.

When you make a structural change in Notion (new database, new relation,
moved page, renamed property, resolved ambiguity), **update these files in
the same session**, on the working branch, and push. Treat this repo as
falling out of date the moment a Notion change isn't reflected here — that's
the failure mode this repo exists to prevent.

## Working agreement

- This repo describes Notion; it does not replace it. Notion is the source
  of truth for content. This repo is the source of truth for *structure and
  rationale* — the stuff that's easy to reverse-engineer wrong from Notion
  alone (why a property exists, why a database lives where it lives).
- Keep entries factual and current. When something changes, edit the
  relevant file rather than appending contradictory notes — `session-log.md`
  is the one place append-only history belongs.
- If a Notion MCP tool limitation blocks something (e.g. no delete tool
  exists as of this writing), note it in `conventions.md` under
  "Known tool limitations" so future sessions don't waste a turn
  rediscovering it.

## Files

- `notion/workspace-map.md` — inventory of pages/databases: what they are,
  where they live, their IDs/URLs, and how they relate.
- `notion/conventions.md` — schema conventions, naming rules, and tool
  limitations to work around.
- `notion/productivity-principles.md` — the science-based techniques this
  workspace is deliberately built around, and why.
- `notion/session-log.md` — dated log of what changed each session and any
  open loose ends handed to the next session.
