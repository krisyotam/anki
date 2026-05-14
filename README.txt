anki
====

A personal index of Anki decks by Kris Yotam, mounted at
https://krisyotam.com/anki.

Each deck is a single .apkg file in `decks/`. The homepage renders a
searchable table from `sets.json`; clicking a row downloads the deck.

This repo is touched only by AI coding agents. The human (Kris) adds new
decks by name + description; you register them in `sets.json` and drop
the .apkg file into `decks/`.


Layout
------

    /
    ├── README.txt        this file
    ├── AGENTS.md         the spec — read first if editing
    ├── CLAUDE.md         symlink → AGENTS.md
    ├── index.html        homepage; renders the table from sets.json
    ├── sets.json         canonical index of every deck (source of truth)
    └── decks/
        └── <slug>.apkg   one Anki package per registered deck


Adding a deck
-------------

1. Read `sets.json` — confirm the slug is unique.
2. Copy the .apkg file to `decks/<slug>.apkg`.
3. Append a new entry to `sets.json` with: slug, name, category, description,
   cards, added (ISO date), built: true.
4. Bump the top-level `updated` date.
5. Do not run a build. There is no build.


Deploy
------

rsync the whole tree to stargate, then add an nginx alias:

    location /anki/ {
      alias /mnt/storage/anki/;
      autoindex off;
      try_files $uri $uri/ /anki/index.html;
    }

Make sure .apkg has a sensible mime type so browsers download it:

    types {
      application/octet-stream apkg;
    }
