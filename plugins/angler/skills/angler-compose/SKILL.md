---
name: angler-compose
description: Use when composing a new PowerPoint deck from the user's existing document atoms via Angler — the HTML → PPTX compose workflow and its required visual-QA loop.
---

# Composing decks with Angler

`angler compose --html slides.html --output deck.pptx` injects the original OOXML at HTML placeholders. Requires Angler.app running (it uses the app's WebView). Output must be under `$HOME` or `/tmp`.

## HTML contract

- Each slide is a `<div class="angler-slide">` at 96 dpi (1280×720 px for 16:9). Emit one `.angler-slide` per slide, in order, for a multi-slide deck.
- A placeholder is a div with `data-angler-atom-ref="<chunk_id or canonical atom_ref>"` and **required** `position:absolute` + explicit `left/top/width/height`. Only the `angler-slide` class and `data-angler-atom-ref` are parsed — no other attributes affect composition.
- Not every atom transplants the same way: PPTX atoms inject OOXML; PDF/DOCX atoms embed as rendered PNGs; **xlsx (`xl_*`) atoms do NOT transplant via placeholders at all.** Author them as real HTML elements — an actual `<table>` (for ranges/named tables) or `<img>` (for chart images, extracted first via `angler extract <id> --format image`) — tagged with `data-angler-atom-ref`. For the `<img>`, point `src` at the extracted PNG: `<img src="file:///tmp/atom.png" data-angler-atom-ref="...">` (a `file://` path, or a data URI). An empty placeholder `<div>` with an `xl/` ref produces a blank hole in the output with **no error or warning**, and dry-run gives no signal for xlsx refs either.
- `md/` atoms stay as the HTML you authored (with a warning) — they are not transplanted.
- Preview first: `angler compose --html slides.html --dry-run` shows slide_groups, planned shape counts (`total_shapes_planned`, `shapes_unresolved`), `cluster_expansions`, and `warnings` — for pptx/pdf/docx refs only; it does not report on xlsx refs.

## When compose fails

- `compose_listener_not_ready` / `compose_unavailable` — the Angler app (or its main window) isn't up. Launch Angler.app and retry.
- `compose_render_timeout` (`-32603`) — the render didn't finish. Retry once; simplify the HTML if it persists.
- `-32602` on unknown/stale chunk_ids — compose fails fast and writes NO partial pptx. Re-run `angler find` for fresh ids, fix the refs, retry.
- In a successful compose, an unresolved placeholder keeps whatever HTML you authored (a styled div renders as-is; an empty placeholder div becomes a blank hole) — check `shapes_unresolved`/`warnings[]` in the dry-run first.

## Visual QA loop (required)

Compose is half-blind — clusters can drag in unwanted shapes and text can overflow placeholders. Iterate (typically 1–3 rounds):

1. Compose: `angler compose --html slides.html --output /tmp/out.pptx`
2. Render a preview and **read it**:
   - Single slide: `mkdir -p /tmp/angler_qa && soffice --headless --convert-to png --outdir /tmp/angler_qa /tmp/out.pptx` → open `/tmp/angler_qa/out.png`. (LibreOffice's PNG export emits only the FIRST slide.)
   - Multi-slide: `mkdir -p /tmp/angler_qa && soffice --headless --convert-to pdf --outdir /tmp/angler_qa /tmp/out.pptx` → open the PDF and inspect every page.
   - Requires `soffice`/LibreOffice on PATH. Angler's app-managed LibreOffice may NOT be on PATH — if `soffice` isn't found, the `.pptx` is still produced; open it in PowerPoint/LibreOffice to QA manually.
   - For decks >10 slides, read the QA PDF in chunks (page ranges) rather than all at once.
3. Edit the HTML to fix overflow / placement.
4. Repeat until it looks right.

When a cluster repeatedly drags in an unwanted shape, drop that `atom_ref` and inline the text via `angler extract --format markdown` instead.

After delivering the deck, send `angler feedback --session-id "<session>" --used "<ids>" --unused "<ids>"` for the atoms you used/didn't (`session_id` comes from your `angler find` calls).
