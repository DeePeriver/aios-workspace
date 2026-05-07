# current-priorities split strategy

## When to split
Split when any of:
- The whole file exceeds **200 lines**
- "Recently completed" has **15+ items**
- "Parking lot" has **10+ items**
- You want to keep past priorities as quarterly retrospectives

## Target structure
```
context/current-priorities.md  ← active priorities (index)
context/current-priorities/
├── completed-YYYY-Q<n>.md     ← quarterly completion archive
├── parking-lot.md             ← parking-lot detail
└── archive-YYYY-Q<n>.md       ← past priorities snapshot
```

## What to keep in the index

The index should function as a **summary collection**, not a table of contents. Each subsection holds a **2–3 sentence short summary** or the current active items, with a link to a subfile if needed.

- **This month's priorities** (list, inline)
- **This quarter's priorities** (list, inline)
- **Active blockers** (list, inline)
- **Recent completions** — within the past 30 days, max 5 items
- **Parking lot** — 2–3 sentence overview (the kinds of ideas sitting there) + link to the full list
- **Completion archive** — 2–3 sentence summary of past quarters (what you accomplished) + link to the latest archive

## What to move to subfiles
- Items completed more than 30 days ago (to the per-quarter archive)
- The full parking lot
- Past priorities snapshots (at quarterly closeout)

## Naming conventions
- Completion archive: `completed-YYYY-Q<n>.md` (e.g., `completed-2026-Q1.md`)
- Parking lot: `parking-lot.md`
- Past archive: `archive-YYYY-Q<n>.md`

## Notes when splitting
- When moving completed items to an archive, always preserve the completion date.
- Plan for quarterly closeout — when an update spans a quarter boundary, move completed items to the appropriate quarter file.
- Keep the `Last updated:` line at the top of the index.
