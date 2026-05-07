---
name: execute
description: Execute a previously created plan from plans/. Walks through each step in order, produces the specified deliverables, saves them to outputs/, and updates the plan's status as steps complete. Use after creating a plan with /aios-workspace-en:plan, when the user says "let's do it", "execute the plan", "run the plan", or specifies a particular plan file.
---

# Execute

Walk through a plan and produce its deliverables.

## Steps

### 1. Locate the plan
- If the user named a path or name, use it.
- Otherwise list recent files under `plans/` and ask which to run.
- If the plan's status is already `Complete`, confirm whether to re-run.

### 2. Read the plan end to end
Read the whole plan before starting. Internalize the goal, approach, and success criteria — they govern judgment calls during execution.

### 3. Execute each step
For each step in order:

1. **Announce** which step you're working on, in one line.
2. **Do the work** — research, drafting, calculation, comparison, outreach prep, whatever the step requires.
3. **Save the deliverable** to `outputs/<plan-slug>/<step-name>.<ext>`.
   - Use `.md` for written deliverables (most cases).
   - Create the `outputs/<plan-slug>/` directory if it doesn't exist.
4. **Mark the step done** in the plan file: change the leading `1.` to `1. ✓`.
5. **Pause for confirmation** before the next step if the step had user-visible impact (a draft to review, a decision to make, numbers to verify).

### 4. Update plan status
Once all steps are complete:
- Change `Status: Draft` to `Status: Complete`
- Add a `Completed:` line with today's date below `Created:`

### 5. Report
Summary:
- What was produced
- Paths to all output files
- Whether success criteria are met
- Any open items remaining

## When to pause and check with the user

- A step requires information you don't have. Don't guess.
- A step's success criteria can't be met with the resources at hand. Flag it and propose a plan amendment.
- The output commits the business to something sensitive (financial commitment, external comms, hiring decision, customer-facing message). Show the draft and require explicit approval before final save.

## Don't

- Don't silently skip steps. If a step no longer applies, mark it `~~strikethrough~~` with a one-line reason.
- Don't write all deliverables without showing any. Pause after meaningful outputs.
- Don't change the plan's `Goal`, `Approach`, or `Success criteria` mid-execution. If those need to change, stop and re-plan.
