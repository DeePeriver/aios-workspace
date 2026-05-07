---
name: brainstorm
description: Open-ended exploration of a business question or decision. Auto-loads the relevant context files based on the topic and surfaces options, tradeoffs, and angles the user may not have considered — without committing to a recommendation. Use when the user wants to think out loud — "how should I approach X?", "what are my options for Y?", "I'm stuck on Z", "let's brainstorm".
---

# Brainstorm

Help the owner think a question through. Surface options, don't pick one yet.

## Steps

### 1. Identify the topic
Restate the question in your own words and confirm with the user before going further. Catches misunderstandings early.

### 2. Load relevant context
Read only what applies:

| Topic | Read |
|---|---|
| Pricing | `customers.md`, `competitors.md`, `financials.md` |
| Marketing / messaging | `customers.md`, `competitors.md`, `strategy.md` |
| Hiring / team | `personal-profile.md`, `financials.md`, `current-priorities.md` |
| Product direction | `customers.md`, `strategy.md` |
| Time / energy / focus | `personal-profile.md`, `current-priorities.md` |
| Strategy pivot | `strategy.md`, `financials.md`, `current-priorities.md` |
| Vendors / tools | `financials.md`, `current-priorities.md` |

Don't read all seven. If the topic doesn't map cleanly, ask the user which dimensions are relevant.

### 3. Generate 3–5 distinct angles
Each angle should be:
- **Substantively different** — not three flavors of the same idea
- **Grounded in the loaded context** — reference specific facts from the context files
- **Paired with the main tradeoff** — what does this option cost or risk?

### 4. Question the assumptions
Surface 1–2 questions that probe assumptions or reveal blind spots. Examples:
- "Have you considered whether X is actually the constraint here?"
- "What would have to be true for option B to be the obvious choice?"
- "Is there a reason this hasn't already been tried?"

### 5. Don't recommend yet
Brainstorming offers options. If the user asks for a recommendation, ask whether they want to commit to a direction with `/aios-workspace-en:plan`.

## Format

Keep it conversational. Don't over-structure with headings and bullets unless the user asks for a written summary. The goal is a thinking partner, not a report. If the user does want a written artifact at the end, save it to `outputs/brainstorms/YYYY-MM-DD-<slug>.md`.

## Don't

- Don't load the full `context/` directory. Be selective.
- Don't generate options that violate something explicit in `strategy.md` (e.g., the "What you are NOT doing" items). If you couldn't generate one because of that, mention it.
- Don't push toward a recommendation. The decision belongs to the user.
