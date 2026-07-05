# Changelog

All notable changes to the Angler Claude Code plugin. Versions follow
semver; dates are release dates for the public `mparcell123/angler-plugin`
mirror.

## [1.2.1]

- Added `PRIVACY.md` disclosing local-only indexing, Supabase auth traffic,
  and direct-to-Anthropic BYOK enrichment.
- Added this changelog.
- No skill, command, or MCP-wiring changes — plugin directory contents only.

Richer tool metadata (annotations, `readOnlyHint`/`destructiveHint`, and
server `instructions`) is reported by Angler's own MCP server, which this
plugin's `.mcp.json` merely launches via `angler mcp-stdio` — it ships with
the Angler **app**, not this plugin. Minimum compatible Angler app version
for that metadata: **0.4.0** or later.

## [1.2.0]

- Added skill `angler-brief` (cited briefs/reports from indexed documents)
  and `/angler:brief`.
- Single-session multi-facet finds.

## [1.1.0]

- Added skills `angler-intel` (corpus/entity intelligence) and
  `angler-render` (search-and-render a specific chart/table/figure).
- Compose accuracy improvements.

## [1.0.0]

- Initial release: skill `using-angler` (find → extract → feedback loop),
  skill `angler-compose` (HTML → PPTX composition + visual-QA loop),
  `/angler:doctor`, `/angler:compose`, and the `angler mcp-stdio` MCP
  server bridge.
