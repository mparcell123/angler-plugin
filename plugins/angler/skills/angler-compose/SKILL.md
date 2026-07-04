---
name: angler-compose
description: Use when composing a new PowerPoint deck from the user's existing document atoms via Angler — the HTML → PPTX compose workflow and its required visual-QA loop.
---

# Composing decks with Angler

`angler compose --html slides.html --output deck.pptx` injects the original OOXML at HTML placeholders. Requires Angler.app running (it uses the app's WebView). Output must be under `$HOME` or `/tmp`.

## HTML contract

- Each slide is a `<div class="angler-slide">` at 96 dpi (1280×720 px for 16:9). Emit one `.angler-slide` per slide, in order, for a multi-slide deck.
- A placeholder is a div with `data-angler-atom-ref="<chunk_id or canonical atom_ref>"` and **required** `position:absolute` + explicit `left/top/width/height`. Only the `angler-slide` class and `data-angler-atom-ref` are parsed — no other attributes affect composition.
- Not every atom transplants as OOXML: PPTX atoms inject OOXML; PDF/DOCX atoms embed as rendered PNGs; **xlsx (`xl_*`) atoms bypass the placeholder path** and compose differently. The `--dry-run` output tells you what each ref resolves to.
- Preview first: `angler compose --html slides.html --dry-run` shows slide_groups + cluster expansions without paying for dom-to-pptx.

## Visual QA loop (required)

Compose is half-blind — clusters can drag in unwanted shapes and text can overflow placeholders. Iterate (typically 1–3 rounds):

1. Compose: `angler compose --html slides.html --output /tmp/out.pptx`
2. Render a preview and **read it**:
   - Single slide: `mkdir -p /tmp/angler_qa && soffice --headless --convert-to png --outdir /tmp/angler_qa /tmp/out.pptx` → open `/tmp/angler_qa/out.png`. (LibreOffice's PNG export emits only the FIRST slide.)
   - Multi-slide: `mkdir -p /tmp/angler_qa && soffice --headless --convert-to pdf --outdir /tmp/angler_qa /tmp/out.pptx` → open the PDF and inspect every page.
   - Requires `soffice`/LibreOffice on PATH. Angler's app-managed LibreOffice may NOT be on PATH — if `soffice` isn't found, the `.pptx` is still produced; open it in PowerPoint/LibreOffice to QA manually.
3. Edit the HTML to fix overflow / placement.
4. Repeat until it looks right.

When a cluster repeatedly drags in an unwanted shape, drop that `atom_ref` and inline the text via `angler extract --format markdown` instead.

After delivering the deck, send `angler feedback` for the atoms you used/didn't.
