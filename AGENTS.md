# AGENTS.md — krisyotam/anki

A personal index of Anki decks, mounted at `krisyotam.com/anki`. This
repository is touched only by AI coding agents. The human (Kris) requests
new decks by topic + brief; you register them in `sets.json` and place
the `.apkg` file in `decks/`.

Read this whole file before editing anything.

---

## What this repo is

- A static site. No build step. No framework. No package.json.
- Each deck is a single `.apkg` file at `decks/<slug>.apkg`.
- `index.html` is the homepage — a searchable table of every deck.
- `sets.json` is the source of truth. The homepage fetches it at load
  and renders the rows from it.
- Mounted URL: `krisyotam.com/anki/`, with downloads at
  `krisyotam.com/anki/decks/<slug>.apkg`.

## What this repo is NOT

- Not a place to write prose or blog posts.
- Not React, not Next.js, not Vue. **Vanilla HTML / CSS / JS only.**
- Not allowed to load runtime dependencies beyond Google Fonts.

---

## Source of truth: `sets.json`

Schema:

```json
{
  "version": 1,
  "updated": "YYYY-MM-DD",
  "categories": ["language", "academic", "math", "cs", "history", "trivia", "misc"],
  "sets": [
    {
      "slug": "japanese-radicals",
      "name": "japanese radicals",
      "category": "language",
      "description": "the 214 kangxi radicals with mnemonic glosses",
      "cards": 214,
      "format": "apkg",
      "keywords": ["kanji", "japanese", "chinese", "kangxi"],
      "built": true,
      "added": "2026-05-14"
    }
  ]
}
```

| field         | rule                                                                |
|---------------|---------------------------------------------------------------------|
| `slug`        | kebab-case, matches `decks/<slug>.apkg`                             |
| `name`        | display name, lowercase                                             |
| `category`    | one of the `categories` array entries                               |
| `description` | one short phrase, no period, lowercase                              |
| `cards`       | integer card count                                                  |
| `format`      | `apkg` (default) or `colpkg`                                        |
| `keywords`    | alternate names / synonyms — drive search                           |
| `built`       | `true` if the file exists in `decks/`; `false` for a wishlist entry |
| `added`       | ISO date the entry was first added                                  |

The homepage search matches against `name + category + description +
keywords`. Put any alt-spelling the user might type into `keywords`.

## Lookup before adding (mandatory)

1. Read `sets.json` in full.
2. Check if a deck with the same slug, name, or close keyword match
   already exists.
3. If a match exists and is `built: true` → tell the user it already
   exists; do not duplicate.
4. If a match exists and is `built: false` → promote it (drop the file
   in `decks/`, flip `built` to `true`).
5. Only if no match exists → add a new entry.

## Workflow: adding a new deck

1. Read `sets.json` and check for an existing match.
2. Pick a slug. Lowercase, kebab-case, short.
3. Pick a category from the predefined list.
4. Place the file at `decks/<slug>.apkg`.
5. Update `sets.json`:
   - Append a new entry, or promote a placeholder by flipping
     `"built": false` → `"built": true`.
   - Refine `description` and `keywords` if needed.
   - Bump the top-level `"updated"` date.
6. Do not touch `index.html` unless changing the homepage design itself.
7. Do not run a build.

## Categories (predefined)

| category  | scope                                              |
|-----------|----------------------------------------------------|
| `language`| natural language: vocab, scripts, grammar, phonology |
| `academic`| subjects taught at degree level                    |
| `math`    | symbols, theorems, identities, formulas            |
| `cs`      | algorithms, syntax, theory                         |
| `history` | dates, figures, events, places                     |
| `trivia`  | facts that don't fit anywhere else                 |
| `misc`    | catch-all                                          |

---

## Design language — mirror `krisyotam/share` (and `krisyotam/tools`)

The visual language is the one in `~/dev/share/.claude/CLAUDE.md` and
`~/dev/tools/index.html`. Same HSL tokens. Same fonts (Lora / Inter /
JetBrains Mono). Same 640px shell. Same floored footer. Same toggle.

Do **not** drift from that language. If you reach for a value not
present in `~/dev/tools/index.html`, stop and ask.

### Quick reference

- `--bg --fg --fg-dim --muted --muted-fg --border --rule --accent`
- Lora for headings + italic taglines, Inter for body, JetBrains Mono
  for numbers / dates / labels / counters.
- Page wrapper: `min-height: 100dvh; display: flex; flex-direction: column`.
- Shell: `max-width: 640px; padding: 80px 32px 56px`.
- All transitions: `120ms ease`.
- Every row in the table is a single line; oversized text
  ellipsis-truncates.

---

## Self-containment rules

- One `.apkg` per deck. No multi-file decks unless `format: colpkg`.
- No analytics, no telemetry, no tracking.
- No data leaves the page. The deck downloads directly from the same
  origin.

## Anti-patterns

Refuse to:
- Generate Anki cards as content unless Kris explicitly asks.
- Add per-deck slug pages by default. Each row downloads directly.
  Only add a `details/<slug>.html` page if a specific deck warrants
  context (preview, changelog, source attribution).
- Render the index as a grid of cards. The 3-column table is the design.
- Add icons next to deck names.

## Commit messages

Short, imperative, lowercase. Examples:
- `add japanese-radicals deck`
- `promote tarot-major-arcana to built`
- `fix homepage table truncation`
- `update sets.json category list`

Never add a Claude/Codex co-author line.
