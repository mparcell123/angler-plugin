<p align="center"><img src="assets/angler.png" width="96" alt="Angler"></p>

<h1 align="center">Angler — Claude Code plugin</h1>

<p align="center">Search, extract, and compose from your own PowerPoint, Excel, Word, PDF, and Markdown — down to the individual chart, table, range, text block, or figure — from inside Claude Code.</p>

## Install

```
/plugin marketplace add mparcell123/angler-plugin
/plugin install angler@angler-plugins
```

Requires the [Angler app](https://github.com/mparcell123/angler-releases) installed, running, and signed in. Run `/angler:doctor` after installing to verify the connection.

## What's included

- **5 skills** — `using-angler` (find → extract → feedback loop), `angler-compose` (HTML → PPTX deck composition + visual QA), `angler-intel` (corpus & entity intelligence), `angler-render` (one-shot chart/figure images), `angler-brief` (cited briefs from your documents)
- **3 commands** — `/angler:doctor`, `/angler:compose`, `/angler:brief`
- The `angler` MCP server (local stdio bridge)

Full plugin details: [`plugins/angler/README.md`](plugins/angler/README.md)
