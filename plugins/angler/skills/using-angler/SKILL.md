---
name: using-angler
description: Use when finding, retrieving, or reusing content from the user's own PowerPoint, Excel, Word, PDF, or Markdown files via Angler — the find → extract → feedback loop for pulling out a specific chart, table, named range, text block, or figure.
---

# Using Angler

Angler indexes the user's documents down to individual atoms (a chart, a table, a named range, a text block, a figure) and returns the specific one you need — for reuse in new work, not just search.

**Preconditions:** Angler.app must be running and signed in, and the `angler` CLI must be on PATH (`~/.local/bin/angler`). If a command fails with "Angler is not running", tell the user to launch Angler.app. If it fails with "Authentication failed" / "token may be stale", the app is running but the session is invalid — tell the user to sign in to Angler (or restart Angler.app if they're already signed in). Run `/angler:doctor` to diagnose.

**Prefer the CLI over the MCP tools when you have shell access** (you do, in Claude Code) — the CLI supports response projection (`--only`) that trims ~400-line responses to the ~30–50 lines you need.

## The loop

1. **Find** — `angler find "<query>" [--type <type>] [--limit 5] --only session_id,cursor,agent_action_hint,exhausted,results.chunk_id,results.preview,results.semantic_type`. Always pass `--only`. Capture `session_id` and `cursor` up front. (`--type` filters by atom type; exact values vary by format — check `angler find --help`.)
2. **Paginate** — while a `cursor` comes back, call `angler more "<cursor>" --only …`. Do NOT re-run `find` with reworded queries — an `agent_action_hint: "paginate"` in the response is the signal to page, not re-search. `exhausted: true` (or no cursor) means done. Cursors expire in ~10 min.
3. **Extract** — `angler extract <chunk_id> --format image|markdown|data|pptx [--output <file>]`, or `--chunk-ids id1,id2` for a batch. `--format image` defaults to the seed atom + its visual cluster; add `--scope atom` for just the seed.
4. **Feedback** — `angler feedback --session-id "<session>" --used "<used_ids>" --unused "<unused_ids>" [--reason off_topic]`. Send it for every meaningful task; the explicit signal outranks every other feedback layer.

## Bundle related atoms

"The chart" means the whole visual group — the shape plus its title, labels, captions, and annotations. Bundle those with the primary atom rather than extracting the bare shape.

## Building a deck?

Use the `angler-compose` skill (or `/angler:compose`) for the HTML → PPTX composition workflow.

Run `angler --help` or `angler <command> --help` for the full flag surface.
