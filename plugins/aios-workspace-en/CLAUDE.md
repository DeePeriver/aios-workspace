# {{BUSINESS_NAME}} — Claude Code Workspace

This workspace is set up for **{{BUSINESS_NAME}}** using the `aios-workspace-en` plugin.

---

## How to use this workspace

Skills are triggered automatically based on the topic or request. If you're not sure what's possible, run `/aios-workspace-en:guide`.

---

## Common workflows

| What you want to do | Command to run |
|---|---|
| See available skills and current state | `/aios-workspace-en:guide` |
| Plan before executing a task | `/aios-workspace-en:plan <what you want to do>` |
| Execute a saved plan | `/aios-workspace-en:execute <plan-path>` |
| Think through an open question | `/aios-workspace-en:brainstorm <topic>` |
| Add or update business context | `/aios-workspace-en:update-context <content>` |
| Check workspace health | `/aios-workspace-en:audit-workspace` |
| Run a tennis-industry radar report | `/aios-workspace-en:tennis-radar` |

---

## Workspace structure

| Folder | Purpose |
|---|---|
| `context/` | What Claude knows about your business. Each skill reads relevant files as needed. |
| `plans/` | Dated plans created by `plan` and executed by `execute`. |
| `outputs/` | Finished deliverables produced during execution. The tennis-radar skill writes dated reports to `outputs/tennis-radar/`. |
| `reference/` | Templates and reusable materials. |

---

## Maintaining this file

Keep this file accurate. When you change how the workspace works — adding new skills, conventions, or directories — update this file so future sessions stay consistent.
