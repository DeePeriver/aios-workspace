# competitors split strategy

## When to split
Split when any of:
- The whole file exceeds **300 lines**
- "Direct competitors" has **4+ companies**, each with 10+ lines of writing
- The "Watchlist" has **10+ entries**
- You want to keep deeper competitive analysis or a history of competitor moves

## Target structure
```
context/competitors.md         ← index
context/competitors/
├── <competitor-name>.md       ← per-competitor deep dive
├── watchlist.md               ← the watchlist
└── differentiation.md         ← detailed take on your differentiation
```

## What to keep in the index

The index should function as a **summary collection**, not a table of contents. Each competitor and section has a **2–3 sentence short summary** with a link to detail when relevant.

- **Each direct competitor** — 2–3 sentence summary (what they do, positioning, threat level to you) + link to detail
- **Indirect alternatives** — 2–3 sentence overview (what customers do instead)
- **Your differentiation** — 1–2 core sentences + link to `differentiation.md` if there's a deeper analysis
- **Watchlist** — 2–3 sentence overview of the trends you're tracking + link to the full list

## What to move to subfiles
- Per-competitor detailed profile (product, pricing, strengths/weaknesses, recent moves, comparison to you)
- The full watchlist (which churns frequently)
- Detailed rationale and analysis for differentiation (deep dive into why you win or lose)

## Naming conventions
- Competitor: `<kebab-case-name>.md` (e.g., `acme-corp.md`, `competitor-x.md`)
- Watchlist: `watchlist.md`
- Differentiation: `differentiation.md`

## Notes when splitting
- For competitor file names, use kebab-case proper nouns (transliterate or abbreviate non-Latin names)
- To avoid name collisions, add a suffix as needed (e.g., `acme-jp.md`)
- Keep the `Last updated:` line at the top of the index
