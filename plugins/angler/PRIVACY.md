# Privacy Policy — Angler

**Effective date:** 2026-07-04

This plugin is a thin bridge (`angler mcp-stdio`) between Claude Code and the
Angler desktop app running locally on your machine. This policy covers what
Angler.app does with your data. It does not cover Claude Code or Anthropic's
own handling of your conversation with the model — see Anthropic's privacy
policy for that.

## 1. Local-only: parsing, indexing, retrieval

Document parsing, embedding generation, the knowledge graph, and search/
retrieval all run locally in the app's local data directory
(`~/Library/Application Support/Angler` or equivalent). **The content of your
documents is never sent to Angler's own servers.** Angler does not operate a
backend that proxies or stores your document content — there is no such
service active in this release.

## 2. Account and auth (Supabase)

Signing up, signing in, session refresh, Google OAuth, and the email
account-status check (used to route you to sign-up vs. sign-in) send your
**email address and authentication data** to Angler's Supabase project. This
is account/session metadata only — it does not include document content.

## 3. Enrichment (direct to Anthropic, using your own API key)

If you've configured an Anthropic API key (via the `ANTHROPIC_API_KEY`
environment variable or the macOS Keychain), Angler posts enrichment requests
**directly to `api.anthropic.com`** using that key. These requests are
between you and Anthropic — Angler does not see, log, or route them through
any Angler-operated server.

Each enrichment request's payload can include:
- the raw text of the atom being enriched (a slide's text box, a table's
  cells, a spreadsheet range, a PDF/DOCX text block, etc.)
- neighboring context from the same slide, sheet, page, or section (used to
  ground entity/fact extraction)
- the source filename
- for image-bearing atoms (e.g. charts, figures) with vision enrichment
  enabled, the base64-encoded image bytes

This data is governed by Anthropic's own terms and privacy policy, not
Angler's — the app is a courier, not a party to what Anthropic does with the
request. If no API key is configured, no enrichment calls are made.

## 4. App updates and dependency downloads

**Update check (automatic, at most once per launch):** a few seconds after the
app starts, Angler checks Angler's own release host (GitHub, via the Tauri
updater) for a newer version — unless an index is being built at that moment,
in which case the automatic check is skipped for that session so it doesn't
interrupt indexing. It is not a recurring background poll and does not run
again until the next launch (you can also trigger a check manually from
Settings).

**Update download (only on your action):** if a newer version is found, the
app shows a banner; the update itself is downloaded and installed only when
you click "Update & Relaunch." Angler never downloads or installs an update
without that explicit click.

**First-run dependency downloads:** at first-run setup, Angler downloads
software it needs to parse and render your documents:
- LibreOffice, downloaded from **The Document Foundation** — not Angler
  infrastructure.
- The embedding model, downloaded from Angler's own release host (the same
  GitHub host used for app updates).

None of these update or dependency downloads carry document content.

## 5. No content telemetry

Angler does not collect telemetry on your document content, search queries,
or extracted results. Aggregate, non-content signals used to improve local
retrieval quality (e.g. relevance feedback) are stored locally in your own
app data directory, not transmitted off-device.

## Contact

Questions about this policy: max@dairyknight.com
