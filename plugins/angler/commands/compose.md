---
description: Build a PowerPoint deck from the user's Angler-indexed documents — drives the find → assemble HTML → compose → visual-QA loop.
argument-hint: <what the deck should contain>
disable-model-invocation: true
---

Build a deck for: $ARGUMENTS

Follow the `angler-compose` workflow:

1. **Find the atoms** using the `using-angler` loop: `angler find` (with `--only`) for each piece the deck needs, paginating as needed. Bundle each visual's labels/titles with it.
2. **Assemble the HTML** — one `<div class="angler-slide">` per slide (1280×720 px, 96 dpi); place each atom as an absolutely-positioned div with `data-angler-atom-ref="<chunk_id>"` + explicit `left/top/width/height`. Note xlsx (`xl_*`) atoms compose via a different path — check the dry-run.
3. **Dry-run** — `angler compose --html slides.html --dry-run` to preview slide_groups + cluster expansions.
4. **Compose + visual QA (required)** — `angler compose --html slides.html --output /tmp/out.pptx`, then `mkdir -p /tmp/angler_qa && soffice --headless --convert-to png --outdir /tmp/angler_qa /tmp/out.pptx` (single slide; use `--convert-to pdf` for multi-slide), read the preview, fix overflow/placement in the HTML, repeat 1–3× until correct. If `soffice` isn't on PATH the `.pptx` is still produced — open it in PowerPoint/LibreOffice to QA manually.
5. **Deliver** the final `.pptx` (under `$HOME` or `/tmp`).
6. **Feedback** — `angler feedback` for the atoms used/unused in the final deck.

Angler.app must be running (compose uses its WebView); the preview render needs `soffice` on PATH.
