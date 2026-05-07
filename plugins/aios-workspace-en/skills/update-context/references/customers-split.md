# customers split strategy

## When to split
Split when any of:
- The whole file exceeds **300 lines**
- You track **5+ named customers** (individuals or client companies)
- Any single customer has **10+ lines** of writing (deal history, relationship, traits)
- "What you hear from customers" has **15+ entries**, or you want a dated history

## Target structure
```
context/customers.md           ← index (summary collection)
context/customers/
├── <client-name>.md           ← per-client detail
├── icp.md                     ← long-form ICP
└── feedback.md                ← feedback archive
```

Split by client name as the default, not by segment. Even within one segment, keep multiple clients in their own files.

## What to keep in the index

The index should function as a **summary collection**, not a table of contents. Each subsection has a **2–3 sentence short summary** with a link to the subfile at the end. The reader should be able to grasp the whole picture without opening the links.

- **Each client** — 2–3 sentence summary (who they are, since when, size/deal value, relationship traits or status) + link to subfile
- **ICP** — 2–3 sentence summary (the core ideal-customer traits) + link
- **Feedback themes** — 2–3 sentence summary (the most recurring feedback patterns) + link to the archive
- **Primary acquisition channels** (keep inline — short)
- **Segmentation** (optional) — if you have grouping categories, mention in 1–2 sentences (e.g., "SMB-heavy with growing enterprise interest")

## What to move to subfiles
- Per-client detailed profile (industry, size, contacts, contract terms, deal history, special notes, relationship memos)
- Long-form ICP, qualifying criteria
- An organized feedback archive (with dates and client names)

## Naming conventions
- Client: `<kebab-case-client-name>.md`
  - e.g., `acme-corp.md`, `tanaka-shoten.md`, `john-smith.md`
  - For individual names, kebab-case the name; mind privacy
  - To avoid name collisions, append a suffix or industry as needed (e.g., `acme-corp-jp.md`, `tanaka-construction.md`)
- ICP: `icp.md`
- Feedback: `feedback.md`

## Notes when splitting
- "Move" content from the index to subfiles — don't write it in both (no duplication)
- Use relative paths in the index (e.g., `[detail](customers/acme-corp.md)`)
- Each client file should always include the relationship start date, current status (active / dormant / churned), and last touch date
- Keep the `Last updated:` line at the top of the index
- Because this contains personal information, consider excluding `context/customers/` via `.gitignore` if you publish the repo
