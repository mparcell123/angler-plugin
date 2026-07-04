---
name: angler-render
description: Use when the user wants an image of a specific chart, table, or figure from their documents via Angler — one-shot search-and-render, plus the inspect-and-refine loop when the rendered crop includes too much or too little.
---

# Rendering a chart/table/figure with Angler

**Preconditions:** Angler.app must be running and signed in. Run `/angler:doctor` to diagnose.

## 1. One-shot render

`angler render --query "Q3 revenue chart" -o /tmp/chart.png` — find + extract-image in a single call. `--scope cluster` (default) bundles the visual group (title, labels, annotations); `--scope atom` renders just the seed.

Useful flags: `--pick N` (take the Nth find result, 1-indexed), `--type chart` / `--file <name>` (narrow the search), `--background transparent`, `--dry-run` (preview which atom would be picked, no render), `--json` (structured output instead of a summary line).

Then READ the PNG to confirm it's the right visual.

## 2. Judge the render

The summary/`--json` reports `render_source` — `isolated` (true isolated render, best quality) or `crop` (fallback crop from the cached slide image; expect possible neighboring-shape bleed). If the atom has no visual at all, `angler render` FAILS with a "missing image content block" error — that means text-only content: use `angler extract <id> --format markdown` instead.

## 3. Refine a bad crop

`angler inspect-cluster <chunk_id>` shows the proposed cluster members and `unclustered_neighbors[]` (each with `distance_to_proposed_pct` / `would_merge_if`). Then re-render:

- `--add-atom-refs <ref>` — pull in a missed label or caption.
- `--exclude-atom-refs <ref>` — drop a stray shape.
- `--atom-refs <ref1>,<ref2>,...` — replace the cluster membership outright.

Iterate render → read → refine; typically 1-2 rounds.

Note: the inspect/refine loop applies to PPTX visual clusters only. PDF/DOCX figures render `--scope atom` (cluster refinement doesn't apply); xlsx `inspect-cluster` lists sheet-mates rather than a visual cluster.

## 4. Non-image needs

These refinement flags are image-specific. For table data or text content, use `angler extract --format markdown|data` instead.

## 5. Feedback

`angler render` calls `kg_find` internally but does not surface a `session_id` in its output (`--json` gives `output_path`/`size_bytes`/`render_source`/`scope`/`picked`, no session field). To submit feedback on which render was used, run the equivalent `angler find "<query>" --only session_id` alongside it and send `angler feedback --session-id "<session>" --used "<chunk_id>"` for the chunk you rendered.

Run `angler render --help` for the full flag surface.
