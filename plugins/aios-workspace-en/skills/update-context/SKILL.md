---
name: update-context
description: Edit the files inside the workspace's context/ directory. Use whenever changes to business context need to land — adding new facts, updating existing content, refreshing entire files. Triggered by "remember X", "we decided Y", "I want to revise the strategy", "update the financials", and similar requests to add to or update content under context/.
---

# Update Context

Edit files inside the workspace's `context/`.

## Steps

### 1. Identify the target file

Decide which of the seven dimensions the user's request belongs to:

| What the fact is about | Goes to |
|---|---|
| The business itself, structure, team | `context/business-overview.md` |
| The owner personally | `context/personal-profile.md` |
| Vision, goals, strategic bets, what you're not doing | `context/strategy.md` |
| Customers, segments, feedback | `context/customers.md` |
| Competitors or alternatives | `context/competitors.md` |
| Money, revenue, costs, runway | `context/financials.md` |
| What you're working on right now | `context/current-priorities.md` |

If the dimension is unclear, ask one clarifying question. If it doesn't fit any dimension, suggest creating a new file in `context/` and confirm before creating.

### 2. Always read before editing

Read the existing content before rewriting. Required for adds, updates, and deletes alike. If the file already uses a subfolder structure (`context/<dimension>/`), grasp that structure too.

### 3. Edit

Edit appropriately for the request:

- Adding a new fact → append to the right section
- Rewriting a specific section → update the existing content
- Refreshing the whole file → rewrite while preserving the template structure (headings). Keep accurate content that's still correct.
- Deleting → remove the relevant part
- If a subfolder structure already exists → maintain it and update only the relevant piece

If a new fact contradicts existing content, ask the user "replace, not add?" Skip duplicate facts and tell the user.

### 4. Auto-split by file size

If the rewritten file gets too large (above ~200–250 lines), split it into a subfolder structure automatically.

- For the target dimension, read the split criteria and target structure from `${CLAUDE_PLUGIN_ROOT}/skills/update-context/references/<dimension>-split.md` (where `<dimension>` is the name identified in step 1)
- Keep the original file as a **summary index** — not just a table of contents, but each subsection with a **2–3 sentence short summary** followed by a link to the subfile. The reader should grasp the whole picture without opening the links.
- Move the details into the corresponding subfiles per the target structure
- If a split happens, mention it in the step-6 report

### 5. Update Last updated

Set the `Last updated:` line at the top of the file to today's date.

### 6. Report

Tell the user what changed where (file path + section, and whether a split happened, if relevant). Report substantive changes, not line diffs.

## Don't

- Don't rewrite without reading first
- Don't add the same fact twice
- Don't create new files in `context/` without confirming
- Don't silently delete content (mention deletions in the report)
