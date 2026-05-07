# strategy split strategy

## When to split
Strategy is best kept as a single coherent document. Split only when:
- The whole file exceeds **300 lines**
- You want to preserve a past strategy as a snapshot (e.g., on a quarterly update)
- Individual strategic bets each warrant **10+ lines** of deep treatment
- You want to store decision rationale or option comparisons separately

## Target structure
```
context/strategy.md            ← current strategy (index)
context/strategy/
├── archive-YYYY-Q<n>.md       ← past strategy snapshot
├── bet-<name>.md              ← deep dive on an individual bet
└── deep-dives/<topic>.md      ← strategic deep-dives by topic
```

## What to keep in the index

The index should function as a **summary collection**, not a table of contents. Each subsection has a **2–3 sentence short summary** with a link to the subfile if needed.

- **3-year vision**
- **12-month goals**
- **Each strategic bet** — 2–3 sentence summary (what you're betting on, why, exit condition) + link to deep-dive
- **What you are NOT doing**
- **Why this strategy** — 2–3 sentence rationale
- **Past archives** — 2–3 sentence overview of recent strategy changes (what shifted) + link to the latest archive

## What to move to subfiles
- The full text of a past strategy (archived during an update)
- Detailed rationale, assumptions, progress, and exit conditions for each strategic bet
- Topic-level strategic deep-dives (market analysis, entry strategy, exit discussions)

## Naming conventions
- Archives: `archive-YYYY-Q<n>.md` (e.g., `archive-2026-Q1.md`)
- Bets: `bet-<kebab-case-name>.md` (e.g., `bet-enterprise-pivot.md`)
- Deep-dives: `deep-dives/<kebab-case-topic>.md` (e.g., `deep-dives/japan-market-entry.md`)

## Notes when splitting
- When archiving a strategy, copy the entire current file as a snapshot to `archive-YYYY-Q<n>.md` before rewriting with new content.
- Each bet deep-dive must include when and why the bet started, the exit condition, and progress.
- Keep the `Last updated:` line at the top of the index.
