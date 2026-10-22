---
name: to-tickets
description: Break a plan, spec, PRD, or the current conversation into a set of tracer-bullet markdown tickets, saved into a tickets folder in the project root.
disable-model-invocation: true
---

# To Tickets

Break a plan, spec, PRD, or conversation into a set of **tickets**: tracer-bullet vertical slices, each declaring the tickets that **block** it. All tickets are generated as individual markdown files in a `tickets/` directory created at the project root.

## Input

- **With argument**: If invoked with an argument (a spec path, PRD path, plan, or raw requirements), read and use it as the source.
- **Without argument**: If no argument is provided, synthesize requirements and scope directly from the current conversation context.

## Process

### 1. Gather context

Work from the provided argument (spec, PRD, plan) or extract requirements from the current conversation context.

### 2. Explore the codebase (optional)

If you have not already explored the codebase, do so to understand the current architecture and state of the code.

Look for opportunities to prefactor the code to make the implementation easier: *"Make the change easy, then make the easy change."*

### 3. Draft vertical slices

Break the work into **tracer bullet** tickets.

<vertical-slice-rules>

- Each slice cuts a narrow but COMPLETE path through every layer (schema, API, UI, tests): vertical, NOT a horizontal slice of one layer
- A completed slice is demoable or verifiable on its own
- Each slice is sized to fit in a single fresh context window
- Any prefactoring should be done first

</vertical-slice-rules>

Give each ticket its **blocking edges**: the other tickets that must complete before it can start. A ticket with no blockers can start immediately.

**Wide refactors are the exception to vertical slicing.** A **wide refactor** is one mechanical change (rename a column, retype a shared symbol) whose **blast radius** fans across the whole codebase, so a single edit breaks thousands of call sites at once and no vertical slice can land green. Don't force it into a tracer bullet; sequence it as **expand–contract**:
1. **Expand**: add the new form beside the old so nothing breaks.
2. **Migrate**: migrate call sites in batches sized by blast radius (per package, per directory), each batch its own ticket blocked by the expand step.
3. **Contract**: delete the old form once no caller remains, in a ticket blocked by every migrate batch.

### 4. Quiz the user

Present the proposed breakdown as a numbered list. For each ticket, show:

- **Title**: short descriptive name
- **Blocked by**: which other tickets (if any) must complete first
- **What it delivers**: the end-to-end behaviour this ticket makes work

Ask the user:

- Does the granularity feel right? (too coarse / too fine)
- Are the blocking edges correct: does each ticket only depend on tickets that genuinely gate it?
- Should any tickets be merged or split further?

Iterate until the user approves the breakdown.

### 5. Save the tickets locally

Create the `tickets/` directory in the project root if it does not already exist.

Save one file per approved ticket under `tickets/<NN>-<slug>.md`, numbered from `01` in dependency order (blockers first).

Use the ticket template below: one ticket per file, never a single combined file.

<ticket-template>

# <NN>: <Ticket title>

**What to build:** the end-to-end behaviour this ticket makes work, from the user's perspective, not a layer-by-layer implementation list.

**Blocked by:** the numbers/titles of the tickets that gate this one, or "None (can start immediately)".

## Acceptance Criteria

- [ ] Acceptance criterion 1
- [ ] Acceptance criterion 2

</ticket-template>

Avoid specific file paths or code snippets: they go stale fast. Exception: if a prototype produced a snippet that encodes a decision more precisely than prose can (state machine, reducer, schema, type shape), inline it and note briefly that it came from a prototype. Trim to the decision-rich parts, not a working demo, just the important bits.