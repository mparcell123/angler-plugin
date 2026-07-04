# Angler — Claude Code plugin

Search, extract, and compose from your own PowerPoint, Excel, Word, PDF, and Markdown — down to the individual chart, table, range, text block, or figure — from inside Claude Code.

## Install

```
/plugin marketplace add mparcell123/angler-plugin
/plugin install angler@angler-plugins
```

## Prerequisites

- **Angler.app installed, running, and signed in.** The plugin drives Angler's local server over the `angler mcp-stdio` bridge; it returns a clean "not running / not signed in" error otherwise.
- The `angler` CLI on PATH at `~/.local/bin/angler` (Angler installs it at first launch). Run `/angler:doctor` if anything is off.
- Deck composition additionally needs LibreOffice (`soffice`) on PATH for the visual-QA render.

## Included

- **Skill `using-angler`** — the find → extract → feedback loop for pulling specific atoms out of your documents.
- **Skill `angler-compose`** — the HTML → PPTX composition workflow + visual-QA loop.
- **`/angler:doctor`** — diagnose the connection.
- **`/angler:compose <goal>`** — build a deck from your indexed documents.
- An MCP server (the `angler mcp-stdio` bridge).

Run `angler --help` (or `angler <command> --help`) for the full CLI surface.
