---
name: angler-brief
description: Use when writing a summary, brief, memo, or report grounded in the user's own documents via Angler — find the supporting atoms, extract their content, cite every claim to its source file and slide/page, and close the feedback loop.
---

# Writing a cited brief with Angler

**Preconditions:** Angler.app must be running and signed in. Run `/angler:doctor` to diagnose.

## 1. Find the material

One `angler find` per topic facet — but **one session for the whole brief**. Each bare `find` invocation mints its own session_id, and feedback (step 5) matches on `(session_id, chunk_id)`, so multi-facet finds MUST share a session: capture `session_id` from the first find, then pass `--session-id "<session>"` to every subsequent facet find.

```
angler find "<facet-1>" --limit 10 --only session_id,cursor,confidence.agent_action_hint,exhausted,results.chunk_id,results.atom_ref,results.citation,results.preview
angler find "<facet-2>" --session-id "<session>" --limit 10 --only ...
```

Per candidate, capture the `{chunk_id, atom_ref, citation}` triple. `citation` is a ready-made source label, e.g. `"Q3_Review.pptx, Slide 4 — Revenue by Region"` (PDFs with cross-page atoms may show a page span, e.g. `"Pages 3–5"`). Paginate with `angler more "<cursor>" --only <same projection>` while a cursor comes back — do NOT re-run `find` with reworded queries; `confidence.agent_action_hint: "paginate"` is the signal.

## 2. Triage

`angler gather --chunk-ids <csv> --include-data` (max 25 per call) to skim raw text and metadata for each candidate and decide which atoms earn a place in the brief.

## 3. Pull content

- `angler extract --chunk-ids <csv> --format markdown` for prose/text — one batch call.
- `angler extract <id> --format data` for table cells (`{headers, rows, truncated, total_rows}`, 50-row cap).

Extract results carry `atom_ref` + `content` but **no `chunk_id` and no `citation`** — join each result back to your step-1 map by `atom_ref`. Failed refs land in a separate `errors` array; result order and length can diverge from the input list, so match by `atom_ref`, not position.

## 4. Write the brief

Every claim carries its source citation inline, e.g.:

> …grew 14% QoQ (Q3_Review.pptx, Slide 4 — Revenue by Region).

Keep numbers verbatim from extracted content — don't round or paraphrase figures. Put any table through `--format data` rather than eyeballing the `preview` string.

## 5. Close the loop

```
angler feedback --session-id "<session>" --used "<cited-ids>" --unused "<rejected-ids>"
```

Feedback rows are matched by `(session_id, chunk_id)` and kept for ~90 days — only pagination cursors expire in ~10 minutes, so send feedback once the brief is finished, not mid-flight. `rows_updated: 0` means the session/chunk pair didn't match, not a failure.

Same preconditions as `using-angler` apply throughout. Run `angler --help` or `angler <command> --help` for the full flag surface.
