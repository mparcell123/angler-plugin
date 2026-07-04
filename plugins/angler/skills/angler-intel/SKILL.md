---
name: angler-intel
description: Use when exploring, mapping, or auditing the user's document corpus via Angler — corpus orientation (what's indexed, main topics), entity intelligence (relationships, coverage, mentions, cross-document comparison), and finding the latest version of a document.
---

# Angler corpus intelligence

Angler builds a knowledge graph over the user's documents. These commands answer corpus-level questions ("what's in here?", "how well is X covered?") rather than pulling a specific atom.

**Preconditions:** Angler.app must be running and signed in. Run `/angler:doctor` to diagnose.

## 1. Orient in a corpus

- `angler status` — index readiness, document/atom counts, enrichment mode.
- `angler intel topics --top 10` — corpus-wide co-mention topic clusters (`clusters: [{label, entity_count, top_entities}]`).
- `angler dev ontology` — semantic-type inventory + counts.
- `angler documents --limit 50` — which files are indexed (each has `doc_id`, `file_path`).

## 2. Map an entity ("everything about Acme Corp")

1. `angler entities --name "Acme"` — find the canonical entity name/spelling.
2. `angler intel related --entity "Acme Corp" --depth 2` — co-mention neighbors: `related: [{name, entity_type, co_occurrence_strength}]`.
3. `angler intel mentions --entity "Acme Corp"` — every document → slide → chunk mentioning it (`documents[].slides[].chunk_ids`).
4. `angler intel coverage --entities "Acme Corp,Beta Inc"` — per-entity atom counts: `entities: [{name, status: "found"|"not_found", atom_count}]`. This is a coarse found/not-found check, not a well-covered/sparse tier — use `atom_count` yourself if you need a threshold.

## 3. Compare documents

`angler intel compare --docs "<doc_id1>,<doc_id2>"` (get ids from `angler documents`; needs ≥2) returns `{overlap_score, shared_entities: [names], unique_to_docs: [{doc_id, entities: [names]}]}`.

## 4. Find the latest version of a document

When multiple copies/revisions of a deck are indexed, Angler links them with VERSION_OF edges. Get the document's id from `angler documents` (or a find result), then run `angler dev history --doc-id <doc_id>`. The response lists the known version chain **most-recently-indexed first** — the first entry (`version_order: 0`) is the most recently indexed copy, usually (not always — files indexed out of order can flip it) the newest revision; prefer its `file_path`. Caveat: version detection is filename + content similarity heuristics, and a chain queried from an older copy may be incomplete — when it matters, sanity-check with file names/`indexed_at`.

## 5. Close the loop

When intel results feed real work (a slide, a brief, a comparison doc), still run the `using-angler` find → extract → feedback loop for the atoms you actually use — intel commands don't submit feedback on their own.

Run `angler intel --help` / `angler dev --help` for the full flag surface.
