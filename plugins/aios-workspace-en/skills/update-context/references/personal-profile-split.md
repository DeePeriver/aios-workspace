# personal-profile split strategy

## When to split
personal-profile usually doesn't need splitting. Split only if:
- The whole file exceeds **200 lines**
- Strengths/weaknesses lists each grew to **10+ items**
- The work-style section ballooned past **15 lines**
- You want to keep self-coaching notes or a reflection log

## Target structure
```
context/personal-profile.md    ← index
context/personal-profile/
├── working-style.md           ← long-form work style
├── strengths-weaknesses.md    ← detailed strengths/weaknesses
└── reflections.md             ← archive of self-reflection (optional)
```

## What to keep in the index

The index should function as a **summary collection**, not a table of contents. Each subsection has a **2–3 sentence short summary** with a link to the subfile if needed.

- **Owner basics** — name, role, background
- **Work style** — 2–3 sentence summary (the core working pattern) + link to detail
- **Strengths/weaknesses** — top 3 of each in short form + link to the full lists
- **Time available**
- **Energizes / drains** — keep inline if short, link to reflections if there's more

## What to move to subfiles
- Detailed work-style writing (working hours, focus pattern, situations you struggle with)
- Full strengths/weaknesses lists
- Self-reflections, coaching notes, past self-assessments

## Naming conventions
- Work style: `working-style.md`
- Strengths/weaknesses: `strengths-weaknesses.md`
- Reflections: `reflections.md`

## Notes when splitting
- This file contains personal content. Be careful when sharing the repo.
- Keep the `Last updated:` line at the top of the index.
