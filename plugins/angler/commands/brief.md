---
description: Write a cited brief from the user's Angler-indexed documents — find the atoms, extract content, cite every claim to file + slide/page, send feedback.
argument-hint: <topic the brief should cover>
disable-model-invocation: true
---

Write a brief on: $ARGUMENTS

Follow the `angler-brief` workflow:

1. **Break the topic into 2-4 facets** and run one `angler find "<facet>" --limit 10 --only session_id,cursor,confidence.agent_action_hint,exhausted,results.chunk_id,results.atom_ref,results.citation,results.preview` per facet — capture `session_id` from the FIRST find and pass `--session-id "<session>"` to every later facet find (each bare find mints a new session, which would break step 5's feedback matching). Paginate with `angler more "<cursor>" --only <same projection>`. Per candidate, capture `{chunk_id, atom_ref, citation}`.
2. **Triage** — `angler gather --chunk-ids <csv> --include-data` (max 25) to skim raw text/metadata and pick which atoms earn a place in the brief.
3. **Extract the winners** — `angler extract --chunk-ids <csv> --format markdown` for prose, `angler extract <id> --format data` for table cells. Extract results have `atom_ref` + `content` but no citation — join back to step 1 by `atom_ref`; failed refs land in a separate `errors` array.
4. **Compose the brief** as a markdown file: inline `(file, Slide/Page N — title)` citations after every claim, numbers verbatim from extracted content (not eyeballed previews), and a closing "Sources" section listing each citation once. Save under `$HOME` or `/tmp` and tell the user where it is.
5. **Feedback** — `angler feedback --session-id "<session>" --used "<cited-ids>" --unused "<rejected-ids>"` once the brief is finished.

Angler.app must be running and signed in.
