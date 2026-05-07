# financials split strategy

## When to split
Split when any of:
- The whole file exceeds **250 lines**
- You want to keep monthly or quarterly financial snapshots as history
- Unit economics (CAC, LTV, payback period, etc.) needs **detailed calculation or trend tracking**
- You have **10+ key metrics**, each with explanation
- You want to store investor-facing reports or accountant-ready data

## Target structure
```
context/financials.md          ← current snapshot (index)
context/financials/
├── history-YYYY.md            ← annual archive
├── snapshots/YYYY-MM.md       ← monthly snapshots (optional, if you update often)
├── unit-economics.md          ← unit economics detail
└── metrics-glossary.md        ← metric definitions and interpretation
```

## What to keep in the index

The index should function as a **summary collection**, not a table of contents. Each subsection has a **2–3 sentence short summary** (or the latest numerical snapshot) with a link to the subfile when relevant.

- **Revenue model** — 2–3 sentences (how you make money, core pricing structure)
- **Current state** — latest-month snapshot of numbers (revenue, margin, burn/runway, cash position)
- **Cost structure** — high-level breakdown
- **Key metrics** — current values list + **1–2 sentences of interpretation** for each (link to the glossary)
- **Constraints**
- **History** — 2–3 sentences on recent trends (YoY, quarterly trend) + link to the history file
- **Unit economics** — 2–3 core sentences (current CAC/LTV and trends) + link to the subfile

## What to move to subfiles
- Past monthly / annual snapshots (time-series numbers)
- Detailed unit-economics calculations and trends (CAC, LTV, payback, ARR, etc.)
- Per-metric definitions, calculation method, interpretation guide, benchmarks

## Naming conventions
- Annual archive: `history-YYYY.md` (e.g., `history-2025.md`)
- Monthly snapshot: `snapshots/YYYY-MM.md` (e.g., `snapshots/2026-04.md`)
- Unit economics: `unit-economics.md`
- Metrics glossary: `metrics-glossary.md`

## Notes when splitting
- Numerical reliability matters. When subfiling, label the aggregation period and currency explicitly.
- The "Current state" in the index is the **latest month's** numbers only. Past months go to the history file.
- Keep the `Last updated:` line at the top of the index.
