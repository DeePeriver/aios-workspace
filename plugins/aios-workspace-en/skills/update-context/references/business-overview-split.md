# business-overview split strategy

## When to split
business-overview usually doesn't need splitting. Split only if:
- The whole file exceeds **200 lines**
- The "Origin" section grew to **10+ lines**
- You want to keep history or milestones in chronological form
- There are **10+ key facts**

## Target structure
```
context/business-overview.md   ← index
context/business-overview/
├── history.md                 ← long-form history / origin
└── milestones.md              ← list of important events (optional)
```

## What to keep in the index

The index should function as a **summary collection**, not a table of contents. Each subsection has a **2–3 sentence short summary** with a link to the subfile if needed.

- **"What this business does"** — one sentence
- **Stage**
- **Structure** — legal form, team size, location, founding year
- **Origin** — 2–3 sentence summary (the core founding story) + link to detail
- **Key facts** — top 3–5 in short form + link to the full list (if needed)
- **History / milestones** — 2–3 sentences for the overall arc (how it grew) + link to the subfile

## What to move to subfiles
- Long-form Origin (founding story, early decisions)
- History (chronological events)
- Milestones (funding, product launches, hires, relocations, etc.)
- Detailed background

## Naming conventions
- History: `history.md`
- Milestones: `milestones.md`

## Notes when splitting
- This file changes infrequently. Even after a split, quarterly updates are usually enough.
- Keep the `Last updated:` line at the top of the index.
