---
name: guide
description: Complete onboarding guide for business owners using the aios-workspace-en plugin for the first time. Walks through what Claude Code is, what this plugin does, how to use slash commands, the purpose and value of each skill, the current workspace state, and what to do first — in order. Use when the user asks "what can this do?", "I don't know how to use this", "help", "guide me", or as the entry point when first launching the plugin. Assumes zero prior knowledge of Claude Code.
---

# Guide

An educational skill that helps business owners understand Claude Code and the aios-workspace-en plugin so they can take their first step.

## Assumptions

The user may be any of the following:

- Doesn't know what Claude Code is
- Has never heard the words "skill", "slash command", or "plugin"
- Doesn't understand why they should "plan" a task or organize "context"
- Unfamiliar with developer terms like "command" or "terminal"

Explain everything **from scratch**. When you must use a technical term, break it down on the spot. Don't talk down to the owner, but be patient with first-time concepts.

## Output structure

Output the **5 sections below** in order. Even if the user already knows some of it, calling `guide` = wanting to confirm — display the full thing every time.

---

### Part 1: What is this plugin (always show first)

Explain the following three points, each in 2–4 plain sentences.

1. **What is Claude Code**

   - A tool from Anthropic that lets you work with the AI "Claude" in conversation.
   - Unlike a normal ChatGPT-style chat, it can actually look at, write, and inspect files on your own computer.
   - In other words, it's both a "thinking partner" and an "assistant that gets its hands dirty."

2. **What is the aios-workspace-en plugin**

   - An extension that turns Claude Code into a "business partner for the owner."
   - Once you teach Claude about your business (customers, strategy, financials, priorities, etc.), it carries that understanding into every conversation — no need to re-explain each time.
   - Beyond one-off questions, it supports your full daily cycle: planning, executing, and reflecting.

3. **How to use it (slash commands explained)**

   - When you type something starting with `/` (a slash) in the input box, you can ask Claude to run a "skill" — a specific procedure.
   - Example: type `/aios-workspace-en:setup` to run the initial setup skill.
   - You don't have to use slash commands — just talking in plain English works too. Slash commands are a convenient shortcut to "make sure a specific procedure runs."

---

### Part 2: Available skills and why to use each

Introduce **all seven** skills in this format, in this order:

```
**`/aios-workspace-en:<name>`**
- What it does: [1–2 sentences]
- Why use it: [the value / problem it solves]
- When to use it: [concrete scenarios]
```

#### 1. `/aios-workspace-en:setup` — Initial setup

- **What it does**: Interviews you about your business (industry, customers, strategy, financials, current priorities, etc.) and teaches Claude about it.
- **Why use it**: Without this, every business conversation with Claude starts with you saying "we're a company that does X, our target customer is Y, and we're focused on Z right now…" Once you teach it, future sessions load that automatically and you can jump straight into substance.
- **When to use it**: The very first time you use this plugin. Run once (takes about 30 minutes).

#### 2. `/aios-workspace-en:plan` — Build a plan

- **What it does**: Before you act, it produces a written plan with goal, steps, success criteria, and risks.
- **Why plan at all**: Some tasks are small enough to start immediately; others are big enough that diving in causes rework and missed pieces. Writing the plan first organizes your own thinking, makes progress and review easier, and makes delegation cleaner.
- **When to use it**: Before substantial tasks like "launch a new service next month," "hire a sales rep," "review pricing," or "decide where to spend marketing budget."

#### 3. `/aios-workspace-en:execute` — Execute a plan

- **What it does**: Opens a plan you wrote with `plan` and walks through each step with you. Claude helps with research and drafting at each step.
- **Why use it**: A plan that just sits there has no value. Having Claude do the legwork on each step speeds you up.
- **When to use it**: After writing a plan, when you're ready to actually do the work.

#### 4. `/aios-workspace-en:brainstorm` — Think out loud together

- **What it does**: For a question you're still chewing on, it lays out several options with their tradeoffs. It does not rush to a single answer — it broadens the view first.
- **Why use it**: Owners are often alone with hard questions. Claude, knowing your business, can be an objective sounding board. You see more angles than you would alone.
- **When to use it**: When the answer isn't obvious — "how should we price this," "should we start this new line," "what kind of person should we hire next."

#### 5. `/aios-workspace-en:update-context` — Update the facts

- **What it does**: Updates the business information you taught Claude in setup, as things change. Add a new customer, revise the strategy, refresh the financials.
- **Why use it**: Your business moves. Six-month-old facts will skew Claude's advice. Keeping context fresh keeps Claude useful.
- **When to use it**: Monthly review, quarterly checkpoint, or right after any significant change.

#### 6. `/aios-workspace-en:audit-workspace` — Health check

- **What it does**: Reviews how organized your business context is, flags stale or missing pieces, and suggests fixes.
- **Why use it**: It catches gaps and staleness you wouldn't notice yourself. A periodic check keeps the quality of your conversations with Claude high.
- **When to use it**: When you come back after a long gap. Or as a regular check every few months.

#### 7. `/aios-workspace-en:guide` — This guide

- **What it does**: Re-displays the guide you're reading right now.
- **When to use it**: When you forget what's available or get lost. Come back any time.

---

### Part 3: Where you are right now

Inspect the working directory and decide which of four states applies:

| State | How to detect | Meaning |
| --- | --- | --- |
| **Empty** | No `CLAUDE.md`, no `context/` | Nothing has been set up — completely fresh |
| **In progress** | Some files exist, but the seven required files aren't all present, or `{{...}}` placeholders remain | Setup was interrupted partway |
| **Ready** | `CLAUDE.md` exists and all seven files in `context/` are filled in | Ready to work |
| **Stale** | Ready, but `Last updated:` dates are old (`current-priorities` / `financials` over 30 days; others over 90 days) | Information needs refreshing |

Report the result with a one or two line note:

- **Empty** → "Nothing is set up yet. Your business information isn't loaded into Claude."
- **In progress** → "Setup is partway done." + what's still missing
- **Ready** → One-line summary (business name + top current priority)
- **Stale** → One-line summary + which files haven't been updated, since when

Read only as much as you need to make this decision. Don't read every file in full.

---

### Part 4: First step (by state)

Suggest exactly **one** most natural next action based on the state:

| State | Suggested next step |
| --- | --- |
| **Empty** | Run `/aios-workspace-en:setup`. It's the first step that teaches Claude about your business (about 30 minutes). |
| **In progress** | Re-run `/aios-workspace-en:setup` — it will pick up where you left off. |
| **Ready** | Just talk to it. If you have a clear thing to do, `/aios-workspace-en:plan` is a natural entry; if you're undecided, `/aios-workspace-en:brainstorm`. |
| **Stale** | Run `/aios-workspace-en:audit-workspace` first, or update the stale files with `/aios-workspace-en:update-context`. |

---

### Part 5: Ask

Close with this:

> What would you like to do? You can pick from the skills above, or just say something like "I want to think through X" or "I want to plan Y" in plain English.

Don't go further than that. This skill is just a guide.

---

## Tone

- Minimize jargon. When you must use it, break it down on the spot (e.g., "slash command = a string starting with `/`").
- 2–4 sentences per paragraph. Don't build walls of text.
- "Use it when ___" is more memorable than "It can do ___."
- Treat the owner as a professional, but stay accessible to non-technical readers.

## Don't

- Don't skip Parts 1 and 2. Even for repeat users, calling `guide` = they want the full thing.
- Don't drop the "why use it" from each skill description. That's the heart of this skill.
- Don't return only the state check (Part 3) and skip the menu.
- Don't auto-run a skill on a guess. Always end by asking the user.
