---
description: Build a PowerPoint deck from the user's Angler-indexed documents — drives the find → assemble HTML → compose → visual-QA loop.
argument-hint: <what the deck should contain>
disable-model-invocation: true
---

Build a deck for: $ARGUMENTS

Follow the `angler-compose` workflow:

1. **Find the atoms** using the `using-angler` loop: `angler find` (with `--only`) for each piece the deck needs, paginating as needed. Bundle each visual's labels/titles with it.
2. **Assemble the HTML** — one `<div class="angler-slide">` per slide (1280×720 px, 96 dpi); place each atom as an absolutely-positioned div with `data-angler-atom-ref="<chunk_id>"` + explicit `left/top/width/height`. xlsx (`xl_*`) atoms: author them as real `<table>`/`<img>` elements tagged with `data-angler-atom-ref` (extract chart images first with `--format image`); an empty placeholder div for an `xl/` ref yields a blank hole with no error, and dry-run gives no signal for xlsx refs.
3. **Dry-run** — `angler compose --html slides.html --dry-run` to preview slide_groups, planned shape counts (`total_shapes_planned`, `shapes_unresolved`), `cluster_expansions`, and `warnings` (pptx/pdf/docx refs only — not xlsx).
4. **Compose + visual QA (required)** — `angler compose --html slides.html --output /tmp/out.pptx`, then `mkdir -p /tmp/angler_qa && soffice --headless --convert-to png --outdir /tmp/angler_qa /tmp/out.pptx` (single slide; use `--convert-to pdf` for multi-slide), read the preview, fix overflow/placement in the HTML, repeat 1–3× until correct. If `soffice` isn't on PATH the `.pptx` is still produced — open it in PowerPoint/LibreOffice to QA manually.
5. **If compose fails** — `compose_listener_not_ready`/`compose_unavailable`: launch Angler.app and retry. `compose_render_timeout` (`-32603`): retry once, simplify the HTML if it persists. `-32602` on unknown/stale chunk_ids: no partial pptx is written — re-run `angler find` for fresh ids and retry.
6. **Deliver** the final `.pptx` (under `$HOME` or `/tmp`).
7. **Feedback** — `angler feedback --session-id "<session>" --used "<ids>" --unused "<ids>"` for the atoms used/unused in the final deck.

Angler.app must be running (compose uses its WebView); the preview render needs `soffice` on PATH.
