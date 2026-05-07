---
name: claude-guide
description: Q&A skill that researches and answers questions about Claude Code and Cowork themselves — the products. Use whenever a business owner asks "what can Claude Code do?", "how is Cowork different from regular Claude Code?", "what's a hook?", "does MCP matter to me?", "I want to change keybindings", or anything else about the tooling itself. Also use whenever Claude itself is about to implement a new skill, hook, or MCP integration and needs to verify "does Claude Code / Cowork actually support this behavior?" before acting. Distinct from /aios-workspace-en:guide (which explains how to use this plugin); this one is about the product itself. Trigger on keywords like Claude Code, Cowork, hooks, slash commands, MCP, skills, plugins, sub-agents, CLAUDE.md, settings.json, keybindings, permission modes, Cowork path structure, or write-protection.
---

# Claude Guide

A Q&A skill that researches and answers questions about Claude Code and Cowork — both the owner's plain questions and Claude's own behavior verification.

## Role and boundaries

What this skill covers and doesn't:

| Topic | Covered | Not covered |
|---|---|---|
| Claude Code (CLI) | ✓ | |
| Cowork (Anthropic's VM execution environment) | ✓ | |
| Claude Agent SDK | | ✓ (out of scope) |
| Claude API / Anthropic SDK | | ✓ (out of scope) |
| How to use this plugin (`aios-workspace-en`) | | ✓ → point to `/aios-workspace-en:guide` |

For out-of-scope questions, don't force an answer. Say "this skill doesn't cover that area" and offer one line of pointer (official docs, `/aios-workspace-en:guide`, etc.).

## Flow

### 1. Determine whether a question was provided

Look at the input the skill was invoked with.

- **A question was passed alongside** (e.g., `/aios-workspace-en:claude-guide what is a hook?`) → go to step 3
- **Invoked with no question** → step 2 (intake mode)

### 2. Intake mode

Display one short prompt and wait for the question. Do nothing else (don't guess an answer).

```
Ask anything about Claude Code or Cowork.

Examples:
- "What is a hook?"
- "How is Cowork different from Claude Code?"
- "Do MCP servers matter to a regular business owner?"
- "How do I add a new skill to this plugin?"
```

When a question arrives, go to step 3.

### 3. Determine the calling context (owner mode / Claude internal mode)

Read the immediately preceding conversation context to decide which way the skill was invoked:

- **Owner mode**: The previous message was a natural-language question from the owner. The owner is the actor of this turn.
- **Claude internal mode**: Claude itself called it during work (implementing a new skill, verifying hook behavior, debugging, validating an MCP integration, etc.).

When ambiguous, **default to owner mode**. Risk-wise, an over-simplified answer is safer than dumping a wall of technical spec on an owner who didn't ask for it.

### 4. Pick the knowledge source

Use the question type to decide.

| Question type | Knowledge source |
|---|---|
| What is Claude Code / Cowork / how do they differ — basic concepts | The **static knowledge** below in this file |
| Cowork path structure / write protection — practical knowledge | The **static knowledge** below in this file |
| "Does the latest version support X?" / "How does the v0.X.Y hook behave?" — version-specific spec | **WebFetch** the official docs |
| Claude wants to verify "does it actually work this way?" | **Always WebFetch the latest official spec** (don't trust static knowledge) |

That last line is critical. The point of Claude internal mode is to confirm reality. Answering from local knowledge alone defeats the purpose. **In Claude internal mode, fire WebFetch by default.**

For starting URLs, see "Official documentation URLs" near the bottom.

### 5. Answer

Format depends on mode (next section). After the answer, expect follow-up questions — don't auto-close the turn (end with "Anything else you want to dig into?" and wait).

## Output format

### Owner mode

Three short sections.

```
**Bottom line**: <1–2 sentence answer>

**A bit more**: <3–5 sentence follow-up, with one analogy if helpful>

**Want to go deeper?**: <two example follow-up questions>
```

Break down jargon on the spot. E.g., "hook (a trigger that auto-runs at a specific moment)", "MCP server (a shared bridge so Claude can call external tools)".

### Claude internal mode

Three sections, accuracy-first.

```
**Spec**: <fact-based, concise>

**Example**: <command / code / path example>

**Source**: <WebFetch URL, or "SKILL.md embedded knowledge (last updated YYYY-MM-DD)">
```

When Claude called this to verify its own behavior, the source **must be WebFetch-derived**. Answering from `SKILL.md embedded knowledge` is not "verification". In internal mode, fire WebFetch by default.

### Common rules

- For multiple questions in one turn, repeat the three-section structure per question (don't blend).
- When unsure, don't guess. Say "let me confirm" and WebFetch.
- For questions beyond static knowledge (e.g., the latest argument spec for a specific hook), WebFetch regardless of mode.

## Static knowledge (basic concepts only — minimal)

Anything not on this list, confirm via WebFetch. Kept deliberately minimal to avoid going stale.

### What is Claude Code

A CLI tool from Anthropic. In the terminal, you converse with the AI "Claude" while it actually reads, writes, edits, and runs files on your computer. Unlike a typical chat AI, it can grasp an entire codebase and act on your behalf — a "hands-on assistant." For an owner, it's a partner you can hand file/folder work to (documents, plans, data wrangling).

### What is Cowork

Another Claude execution environment from Anthropic. Unlike Claude Code, each session runs on a **cloud VM (virtual machine)**. Use it when you want Claude to work from the browser without using your local machine. You can run multiple sessions in parallel. Most Claude Code-compatible skills and plugins work as-is, but file path handling differs (see below).

### Claude Code vs. Cowork comparison

| Aspect | Claude Code | Cowork |
|---|---|---|
| Where it runs | User's local machine | Anthropic-side cloud VM |
| Working directory | Current directory (user-chosen) | `/sessions/<name>/mnt/...` |
| Read/Write/Edit tools | Same path as above | Host-side path (`/Users/<user>/Documents/Claude/Projects/...`) |
| Bash tool | Same path as above | VM-side path (`/sessions/<name>/mnt/...`) |
| File persistence | Local filesystem | Session-bound remote storage |
| Parallel sessions | Usually 1 | Multiple |
| Intended use | Work locally on your own machine | Browser-only / multiple in parallel |

The single most important Cowork point: **Bash and Read/Write/Edit use different paths**. The same file is `/sessions/<name>/mnt/foo.txt` from Bash but `/Users/<user>/Documents/Claude/Projects/foo.txt` from Read/Write/Edit. Mixing them up means files-not-found and failed writes.

Also, in Cowork **pre-existing files at the workspace root are write-protected**. Before deleting or overwriting, you must call `mcp__cowork__allow_cowork_file_delete` to grant permission.

### How slash commands work

Typing a string starting with `/` in the input runs a registered skill (a defined procedure). The format is `/<plugin>:<skill>` (e.g., `/aios-workspace-en:setup`). You can also just talk to it in English — slash commands are shortcuts to "guarantee a specific procedure runs."

### Major extension concepts (one-line summaries)

For details on each, WebFetch the official docs.

| Concept | One-line description |
|---|---|
| **Skill** | A procedure written in `SKILL.md`. Triggers via slash command or via description-keyword match. |
| **Plugin** | A distribution unit that bundles multiple skills, commands, hooks, and MCP configs. Installable from a marketplace. |
| **Hooks** | A mechanism that auto-runs shell commands at specific moments (PreToolUse, PostToolUse, SessionStart, Stop, etc.). Registered in `settings.json`. |
| **MCP server** | A shared interface for Claude to call external tools/services (GitHub, Slack, browser, etc.). |
| **Sub-agent** | A child agent invoked from the main Claude in a separate context to run a specific task. Called via the `Agent` tool. |
| **CLAUDE.md** | Placed at project root, auto-loaded every session — a persistent instruction sheet for Claude. |
| **settings.json** | Claude Code's settings file. Two layers: `~/.claude/settings.json` (global) and `<repo>/.claude/settings.json` (project). Holds permissions, env, hooks, etc. |
| **Permission mode** | How much tool execution Claude is auto-allowed to do. `default` / `acceptEdits` / `plan` / `bypassPermissions`, etc. |
| **Keybindings** | Keyboard shortcuts. Configurable in `~/.claude/keybindings.json`. |
| **IDE integration** | Extensions for VS Code / JetBrains that surface Claude Code. |

## Official documentation URLs (WebFetch starting points)

When confirming the latest spec, start here. Links break and pages get renamed — fall back to WebSearch if you can't reach the target.

| Topic | URL |
|---|---|
| Claude Code overview | https://docs.claude.com/en/docs/claude-code/overview |
| Slash commands | https://docs.claude.com/en/docs/claude-code/slash-commands |
| Hooks | https://docs.claude.com/en/docs/claude-code/hooks |
| MCP | https://docs.claude.com/en/docs/claude-code/mcp |
| Skills | https://docs.claude.com/en/docs/claude-code/skills |
| Plugins | https://docs.claude.com/en/docs/claude-code/plugins |
| Sub-agents | https://docs.claude.com/en/docs/claude-code/sub-agents |
| Settings (settings.json) | https://docs.claude.com/en/docs/claude-code/settings |
| IDE integrations | https://docs.claude.com/en/docs/claude-code/ide-integrations |
| Cowork | If no dedicated URL is found, WebSearch for "Anthropic Cowork", "Claude Cowork sessions mnt" |

For any area not on this list, or where this list may be stale, WebSearch first to find the current official page, then WebFetch.

## Tone

- Owner mode: minimize jargon and break it down on the spot. 2–4 sentences per paragraph.
- Claude internal mode: fact-based, cite sources. No fluff.
- In either mode, don't assert what you're unsure of. Don't be afraid to say "let me confirm" and WebFetch.

## Don't

- **Force an answer for out-of-scope topics** (Agent SDK, Claude API, this plugin's usage). One line of pointer, then stop.
- **Answer in Claude internal mode from static knowledge alone**. That defeats verification. WebFetch by default.
- **Guess URLs**. If you need a page not in the list, route through WebSearch.
- **Wall an owner with technical spec in owner mode**. Short, conclusion-first, details on request.
- **Answer something on a guess when invoked with no question**. Stay in intake mode and wait.
- **Close the turn after one answer**. Leave room for follow-ups ("Anything else you want to dig into?").
