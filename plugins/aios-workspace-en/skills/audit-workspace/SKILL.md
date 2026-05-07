---
name: audit-workspace
description: Assess the current state of the business workspace and produce a checklist for improvement. Checks for missing context files, stale information, unfilled template placeholders, and gaps or contradictions across context dimensions. Use when the user asks "is the workspace set up correctly?", "what's missing?", "audit it", or after a long gap between sessions.
---

# Audit Workspace

Evaluate the workspace against a checklist and report gaps with concrete next actions.

## Checks to run

### Structure
- `CLAUDE.md` exists at the workspace root
- `context/`, `plans/`, `outputs/`, `reference/` directories exist

### Context files
For each of the seven dimensions, confirm the file exists and contains real content (not just templates):
- `context/business-overview.md`
- `context/personal-profile.md`
- `context/strategy.md`
- `context/customers.md`
- `context/competitors.md`
- `context/financials.md`
- `context/current-priorities.md`

### Freshness
- Read the `Last updated:` line of each context file.
- Flag as **stale** if older than 90 days.
- Flag `strategy.md` as stale if older than 60 days.
- Flag `current-priorities.md` as stale if older than 30 days.
- Flag `financials.md` as stale if older than 30 days.

### Completeness
For each context file, scan for:
- Unfilled `{{...}}` placeholders
- `TODO:` markers
- Empty sections (a heading with no content under it)

### Consistency
Check for contradictions across files:
- A goal in `strategy.md` doesn't appear anywhere in `current-priorities.md`
- A customer segment mentioned in `strategy.md` is missing from `customers.md`
- A competitor named in `strategy.md` or `customers.md` is missing from `competitors.md`
- A financial metric referenced in `strategy.md` or `current-priorities.md` is missing from `financials.md`

## Output format

Produce a markdown report with three sections:

```markdown
## Workspace audit — <date>

### What's working
- <green check, one line each>

### Gaps
- **<file path>** — <what's wrong> → <one-line fix>
- ...

### Recommended next actions
1. <highest-impact fix> — run `/aios-workspace-en:<skill> <args>`
2. <next>
3. ...
```

Order **Recommended next actions** by impact:
1. Missing files first (other skills can't read context without them)
2. Stale strategy / priorities next (they drive current work)
3. Stale financials third
4. Consistency gaps fourth
5. Style or completeness gaps last

## End

After producing the report, ask the user if they want to fix the highest-priority gap right now.

## Don't

- Don't auto-fix. This skill reports; the user decides.
- Don't flag missing optional sections that aren't required. Only flag missing required dimensions.
