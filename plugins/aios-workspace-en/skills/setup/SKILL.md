---
name: setup
description: First-time bootstrap of a business owner's Claude Code / Cowork workspace. Lays down the templates bundled with the plugin (CLAUDE.md and the context/ directory) into the working directory, then interviews the user across seven context dimensions (business overview, personal profile, strategy, customers, competitors, financials, current priorities) to fill in the context/ files. Use to start a business workspace from scratch in an empty or near-empty directory, or when phrases like "set up workspace", "onboarding", "get started", "initialize" come up.
---

# Setup

Fully bootstrap an owner's workspace from scratch. Works in both Claude Code and Cowork.

## End state

When this skill finishes, the user has:
- A `CLAUDE.md` at the workspace root, customized for the business
- `context/`, `plans/`, `outputs/`, `reference/` directories
- Seven filled-in context files inside `context/`
- Confirmation that the workspace is ready to use

## Steps

### 1. Identify the runtime environment

This skill runs in both Claude Code and Cowork. Pin down the environment and paths first. Later steps reference the variable names defined here.

**Detection**:
- `${CLAUDE_PLUGIN_ROOT}` resolves to a value, AND `/sessions/` doesn't exist → **Claude Code**
- `${CLAUDE_PLUGIN_ROOT}` is empty, AND `/sessions/<name>/mnt/` exists → **Cowork**

If ambiguous, run `ls /sessions/ 2>/dev/null` and `echo "${CLAUDE_PLUGIN_ROOT}"` to confirm.

**Variables**:

| Variable | Claude Code | Cowork |
|---|---|---|
| `<plugin-root>` (where the templates live) | `${CLAUDE_PLUGIN_ROOT}` | Discover with `find /sessions/*/mnt/.remote-plugins -type d -name "aios-workspace*"` |
| `<workspace>` (working dir for bash) | Current directory (pwd) | `/sessions/<name>/mnt/AI-Workspace/` (or the connected folder) |
| `<workspace-host>` (for Read/Write/Edit) | Same as `<workspace>` | `/Users/<user>/Documents/Claude/Projects/AI-Workspace/` (host-side path) |

**Important**: In Cowork, the bash tool uses VM paths (`/sessions/...`) while Read/Write/Edit use host-side paths (`/Users/...`). Don't mix them. In Claude Code there is no such distinction.

### 2. Check the starting state

`ls` the contents of `<workspace>`.
- If `CLAUDE.md` or `context/` already contain real content (actual business information, not template placeholders like `{{BUSINESS_NAME}}`), confirm with the user whether to **overwrite**, **merge**, or **abort**. Default to abort.
- If the directory is empty, or only template stubs (CLAUDE.md still says `{{BUSINESS_NAME}}`, empty context/), proceed.

### 3. Reset existing protected directories (Cowork only)

**In Claude Code, skip this step** and go to step 4.

In Cowork, pre-existing directories and files at the `<workspace>` root are write-protected. Before copying the templates, you must remove the existing `context/` directory (empty stub) and `CLAUDE.md` (placeholder) with delete permission first.

Handle only the things that exist:
1. If `context/` exists:
   - Call `mcp__cowork__allow_cowork_file_delete` for `<workspace-host>/context`
   - `rmdir <workspace>/context && mkdir <workspace>/context`
2. If `CLAUDE.md` exists as a stub:
   - Call `mcp__cowork__allow_cowork_file_delete` for `<workspace-host>/CLAUDE.md`
   - `rm <workspace>/CLAUDE.md`

If a write fails with Permission denied, don't tell the user "this is impossible" — try this reset sequence first.

### 4. Copy templates

Lay down the bundled templates into the working directory:

```bash
cp <plugin-root>/CLAUDE.md <workspace>/CLAUDE.md
cp <plugin-root>/context/*.md <workspace>/context/
```

This places `CLAUDE.md` at the workspace root and the seven dimension templates inside `context/`.

### 5. Create empty directories

```bash
mkdir -p <workspace>/plans <workspace>/outputs <workspace>/reference
```

### 6. Run the context interview

Walk through the seven dimensions in this order. For each one:
- Briefly explain in one sentence what it covers
- Ask 3–6 open-ended questions
- Listen for conversational answers. Don't make the user write markdown.
- Use the answers to fill in the placeholders (`{{...}}`) in `<workspace-host>/context/<name>.md` via the Edit tool
- If the user can't answer, don't guess. Leave a clear `TODO:` marker.
- Set `Last updated:` to today's date

| # | Dimension | Target file |
|---|---|---|
| 1 | Business overview | `context/business-overview.md` |
| 2 | Personal profile | `context/personal-profile.md` |
| 3 | Strategy | `context/strategy.md` |
| 4 | Customers | `context/customers.md` |
| 5 | Competitors | `context/competitors.md` |
| 6 | Financials | `context/financials.md` |
| 7 | Current priorities | `context/current-priorities.md` |

### 7. Replace the business name in CLAUDE.md

Once you know the business name from step 6, replace `{{BUSINESS_NAME}}` in `<workspace-host>/CLAUDE.md` with the real name (Edit tool, `replace_all`).

### 8. Verify and report

Once all seven files exist:
- List the created files with absolute paths
- Confirm the workspace is ready
- Tell the user the workspace is ready, and that depending on what they want to do next, skills like `/aios-workspace-en:plan` or `/aios-workspace-en:brainstorm` will trigger naturally.

## Resumability

If the user pauses or quits mid-interview, save what was collected so far. On a re-run, detect the existing context files, skip completed dimensions, and offer to "pick up where we left off." Don't re-ask about already-filled files.

## Tone

The user is a busy owner, not a developer. Avoid jargon. Frame as "tell me about your business," not "let's fill in the context files." Stay conversational — 1–2 questions per turn, no long bullet lists.

## Don't

- Don't try to handle all seven dimensions in one giant prompt. Go one at a time.
- Don't leave template placeholders (`{{...}}`) in the final files. Either fill them in or convert them to a `TODO:` line.
- Don't proceed to step 8 if any of the seven context files are missing.
- In Cowork, don't work with relative paths via `cd`. The bash tool resets cwd between calls — pass absolute paths everywhere.
- In Cowork, don't tell the user "this is impossible" when a write returns Permission denied. Try the step 3 sequence: `allow_cowork_file_delete` → `rmdir`/`rm` → `mkdir`.
- In Cowork, don't pass host-side paths (`/Users/...`) to the bash tool, and don't pass VM paths (`/sessions/...`) to Read/Write/Edit. Bash = VM, file tools = host.
