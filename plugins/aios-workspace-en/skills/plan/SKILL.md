---
name: plan
description: Produce a step-by-step plan before executing a business task. Writes a dated markdown plan to plans/ capturing context, rationale, steps, and success criteria. Use before substantial tasks — product launches, hiring, marketing campaigns, pricing changes, vendor selection, strategic decisions — anything bigger than an immediate action. Triggered by requests like "plan X", "let's plan", "how should I approach Y", and similar phrases that signal meaningful work ahead.
---

# Plan

Produce a written plan before action.

## Steps

### 1. Understand the request

If the goal is unclear, ask clarifying questions. Aim for about three, but if the request is too vague, keep asking until everything is clear. Never plan on guesses. If it's already clear, skip the questions. Common clarifiers:

- Is there a deadline (and when)?
- What does success look like to you?
- Budget / resource constraints?

### 2. Pull relevant context

Read the context files that apply:

- Marketing / sales / brand → `customers.md`, `competitors.md`, `strategy.md`
- Hiring / operations / vendors → `personal-profile.md`, `financials.md`, `current-priorities.md`
- Pricing → `customers.md`, `competitors.md`, `financials.md`
- Product direction → `customers.md`, `strategy.md`
- Strategic decisions → `strategy.md`, `financials.md`, `current-priorities.md`
- Anything financial → always include `financials.md`

Don't read everything. Read only what applies.

### 3. Draft the plan

Use this format and save to `plans/YYYY-MM-DD-<short-slug>.md`. The slug is a 2–4 word kebab-case description.

```markdown
# <plan title>

**Created:** <today's date>
**Status:** Draft

## Context
<2–4 sentences: the situation, why now, which context files informed this>

## Goal
<1–2 sentences: what success looks like, with a measurable outcome where possible>

## Approach
<2–4 sentences: high-level strategy — why this approach over alternatives>

## Steps
1. <action> — <expected deliverable / output>
2. <action> — <expected deliverable / output>
3. ...

## Success criteria
- <measurable outcome>
- <measurable outcome>

## Risks / open questions
- <risk> → <mitigation>
- <open question to resolve>
```

### 4. Show before saving

Show the plan to the user. Ask if it's right. Iterate based on feedback before saving.

### 5. Save and report

Once approved, save the file. Tell the user the path and suggest `/aios-workspace-en:execute <path>` as the next step.

## Guardrails

- This plan is for an owner who decides and acts, not a project manager. Keep steps concrete and few.
- More than seven steps probably means it's two plans — split it.
- Don't include research-only steps with no decision or deliverable. If research is needed, name the deliverable (e.g., "decide on a vendor by comparing X, Y, Z").

## Don't

- Don't save a plan before showing it to the user.
- Don't write fluffy management-speak. Plain words win.
