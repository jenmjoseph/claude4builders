---
name: "Sprint Ticket Reconciler"
description: "Reconciles a Google Calendar time block into a Jira sprint ticket — finds or creates the ticket, adds story points additively, enriches the description with business impact, and presents everything for review before writing to Jira."
when_to_use: "Use during a Daily Sprint Sync session when you have a red (unprocessed) calendar block that needs to be logged against a Jira ticket. Invoke once per block during Steps 3+4 of the Daily Sprint Sync workflow."
version: "1.0"
author: "Jennifer Joseph"
workflow: "Daily Sprint Sync"
steps_covered: "Steps 3 and 4"
---

# Sprint Ticket Reconciler

You are running **Steps 3 and 4 of the Daily Sprint Sync** — Reconcile Sprint Tickets and Enrich Ticket Descriptions. These steps run in tandem for each calendar block.

Work through the following sequence completely for one block before moving to the next. Do not skip steps. Do not update Jira without explicit user approval.

---

## Story Point Conversion Rule

**1 hour of work = 0.25 story points**

| Hours | Points to Add |
|-------|--------------|
| 0.25 hr | 0.0625 |
| 0.5 hr | 0.125 |
| 1 hr | 0.25 |
| 2 hrs | 0.5 |
| 4 hrs | 1.0 |
| 8 hrs | 2.0 |

**Critical:** Always additive. Never recalculate existing story points. If a ticket already has 1.0 SP and you're logging 2 more hours, the new total is 1.5, not 0.5.

State the calculation explicitly before every update:
`"[X hrs] × 0.25 = [Y pts] — adding to existing [Z pts] for new total of [Z+Y pts]"`

---

## Ticket Description Template

Every ticket touched in this session uses this structure:

```
## Why This Matters
[Business impact — plain language, 1-3 sentences. What value does this work deliver? Why does it matter to the business, stakeholders, or the team's goals?]

---

[Technical details — approach, implementation notes, decisions made, tools used, PRs referenced]
```

**Rules:**
- Lead with business impact — a stakeholder should understand the value without reading the technical section
- Keep "Why This Matters" to 1-3 concrete sentences (not "this is important for data quality")
- OKR and epic context live in linked/parent tickets — do not repeat them here
- Note AI tool usage in technical details (e.g., "Used Claude Code to generate dbt test logic")
- If updating an existing description that already has a "Why This Matters" section, update or extend it — don't replace it
- If context is insufficient to write a meaningful "Why This Matters", leave `[TODO: add business context]` and flag it — never skip the section

---

## Step-by-Step Execution

When given a calendar block, execute the following in order:

### Step A — Find or Create the Jira Ticket

1. Using the block title and any context provided, identify the most likely matching Jira ticket from the sprint board.
2. State your match and reasoning: `"I believe this maps to [TICKET-ID]: [Ticket Title] because [reasoning]."`
3. Ask the user to confirm the match before proceeding.
4. If no match exists: propose a ticket title and brief description. Ask the user to confirm before creating it via Jira MCP.

### Step B — Calculate Story Points

1. Read the duration of the calendar block.
2. Multiply by 0.25 to get points to add.
3. State the full calculation: `"[X hrs] × 0.25 = [Y pts] — adding to existing [Z pts] for new total of [Z+Y pts]"`
4. Do not update story points yet — wait for full review in Step E.

### Step C — Confirm Status

Ask: "Is this ticket currently **In Progress** or **Done**?"

### Step D — Confirm Sprint Membership

- If the ticket is already in the current sprint: note it, no action needed.
- If the ticket is NOT in the current sprint: ask the user to confirm before adding it.
- If the ticket is in the sprint but no time was logged against it today: flag it as a candidate for sprint removal at end of sprint. Do not remove mid-sprint.

### Step E — Draft and Propose the Description Update

1. Draft the "Why This Matters" section based on:
   - The calendar block title and duration
   - The Jira ticket title and any existing description
   - Context the user has provided in this session
2. Draft the technical details section based on the block title and session context.
3. Present the full proposed description to the user:

```
## Proposed description update for [TICKET-ID]:

## Why This Matters
[Your draft]

---

[Your draft technical details]
```

4. Wait for explicit user approval before writing to Jira.

### Step F — Submit Updates (After Approval)

Once the user approves the full proposal:
1. Update story points in Jira via Jira MCP
2. Update ticket status in Jira via Jira MCP
3. Update sprint membership if confirmed
4. Write the approved description to Jira via Jira MCP
5. Confirm all updates completed successfully

### Step G — Mark Block Processed

Remind the user: "Please flip the **[Block Name]** calendar block from red to green to mark it as processed."

---

## Input Format

Provide one calendar block at a time in this format:

```
Block: [block title]
Date: [date]
Duration: [X hr / X min]
Notes: [any additional context — optional]
```

Or simply paste the block details and this skill will extract the relevant information.

---

## Error Handling

| Situation | Action |
|-----------|--------|
| Block is already green | Skip entirely. Say: "This block appears already processed (green). Skipping to prevent double-counting." |
| Ambiguous block title | Make the best inference based on sprint context. State reasoning. Ask for confirmation before proceeding. |
| No matching Jira ticket | Propose a new ticket. Do not create without user confirmation. |
| Jira MCP unavailable | Produce the full update proposal as a structured output the user can apply manually. |
| Insufficient context for "Why This Matters" | Insert `[TODO: add business context]` placeholder. Flag it explicitly. |
| Same ticket appears in multiple blocks | Sum all durations first, then convert. Process as a single update. |
