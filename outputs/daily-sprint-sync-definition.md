# Workflow Definition: Daily Sprint Sync

**Workflow Name:** Daily Sprint Sync
**Type:** Collaborative AI (Augmented)
**Status:** Under Development
**Owner:** Jennifer Joseph
**Trigger:** End of day (daily habit); urgency increases in the days before bi-weekly sprint retro

---

## Scenario Metadata

| Field | Value |
|-------|-------|
| **Name** | Daily Sprint Sync |
| **Description** | A daily end-of-day session that reconciles Google Calendar, Google Tasks, and Jira to keep sprint state accurate throughout the sprint |
| **Process Outcome** | Jira sprint tickets updated with accurate story points, status, and enriched descriptions — with Calendar and Tasks reflecting actual time spent |
| **Trigger** | End of day (daily habit); urgency increases in the days before bi-weekly sprint retro |
| **Type** | Collaborative AI (Augmented) |
| **Business Objective** | Keep sprint state accurate in real time so work is visible, story points reflect actual effort, and the sprint board tells a true story of value delivered |
| **Current Owner** | Jennifer Joseph |

---

## Refined Steps

### Step 1 — Open & Orient

**Action:** Open Google Calendar (filtered to today or the current week), Jira sprint board, and Google Tasks. Scan for unprocessed (red) calendar blocks. Close out any completed Google Task reminders.

**Sub-steps:**
1. Open Google Calendar — filter to day or week view
2. Identify all red (unprocessed) time blocks
3. Open Jira sprint board
4. Open Google Tasks — close out any completed reminders

**Decision Points:**
- Daily run: filter to today's blocks only
- End-of-sprint run: filter to full sprint window

**Data In:** Google Calendar, Jira sprint board, Google Tasks
**Data Out:** Mental map of what needs to be processed
**Context Needs:** None beyond access to the three systems
**Failure Modes:** Calendar not up to date (blocks not added during the day) — workflow produces incomplete output; must backfill calendar first

---

### Step 2 — Review Calendar Blocks

**Action:** For each red (unprocessed) block, classify it as: focus/task work (needs a ticket), meeting, personal, or ad hoc (PR review, research, etc.). Tag the blocks that need Jira tickets.

**Sub-steps:**
1. Scan each red block
2. Apply classification rules (see Context Shopping List)
3. Mark blocks that need ticket reconciliation

**Decision Points:**

| Block Type | Action |
|------------|--------|
| Focus/task work | Needs Jira ticket — proceed to Step 3 |
| Meeting | No ticket needed — skip |
| Personal block | No ticket needed — skip |
| Ad hoc (PR review, research) | Usually no ticket — judgment call |

**Data In:** Red calendar blocks from Step 1
**Data Out:** Classified list of blocks requiring ticket action
**Context Needs:** Block classification rules (see Context Shopping List)
**Failure Modes:** Ambiguous block titles (e.g., "v2 testing") — ask Claude to infer from context or flag for manual review

---

### Step 3 — Reconcile Sprint Tickets *(runs in tandem with Step 4)*

**Action:** For each focus block identified in Step 2, find or create the matching Jira ticket, update story points, status, and sprint membership, then flip the calendar block from red to green.

**Sub-steps:**
1. Find the matching Jira ticket for the block
   - If ticket exists → proceed to update
   - If no ticket exists → ask Claude to create one via Jira MCP
2. Add story points using conversion rule: **1 hour = 0.25 story points** (additive — never recalculate)
3. Update ticket status (In Progress / Done)
4. Move ticket into or out of sprint as appropriate
5. Mark calendar block green (processed)

**Decision Points:**

| Scenario | Action |
|----------|--------|
| Block has matching ticket | Update story points + status |
| Block has no ticket | Ask Claude (Jira MCP) to create one |
| Ticket in sprint with no calendar time | Flag for removal (act at end of sprint) |
| End of sprint | Audit board — remove unworked tickets |

**Data In:** Classified focus blocks from Step 2
**Data Out:** Updated Jira tickets (story points, status, sprint); green calendar blocks
**Context Needs:** Story point conversion rule (1hr = 0.25 SP); Jira MCP access
**Failure Modes:**
- Calendar block already green (already processed) — skip to prevent double-counting
- Jira MCP unavailable — manual ticket update required
- Ambiguous block-to-ticket mapping — ask Claude to suggest best match

---

### Step 4 — Enrich Ticket Descriptions *(runs in tandem with Step 3)*

**Action:** For each ticket touched in Step 3, apply the standard description template: lead with business impact, then technical details.

**Sub-steps:**
1. Check if ticket description already has a "Why This Matters" section
   - If yes → update or append as needed
   - If no → apply full template
2. Write "Why This Matters" — business impact in plain language (1-3 sentences)
3. Add horizontal divider
4. Add or update technical details — approach, implementation notes, AI usage if relevant

**Description Template:**
```
## Why This Matters
[Business impact — plain language, 1-3 sentences]

---

[Technical details — approach, implementation notes, AI usage, etc.]
```

**Decision Points:**
- OKR and epic context are captured via linked/parent tickets — do NOT duplicate in description
- AI usage should be noted in technical details when Claude or other AI tools were used

**Data In:** Tickets from Step 3; context from calendar block (what was worked on and why)
**Data Out:** Jira ticket descriptions updated with business impact + technical details
**Context Needs:** Ticket description template; understanding of business context for each work block
**Failure Modes:** Insufficient context to write a meaningful "Why This Matters" — leave a placeholder and revisit; do not skip the section

---

### Step 5 — Review Sprint State

**Action:** Scan the full sprint board for tickets that have no calendar time logged against them. Flag for removal from sprint, especially during end-of-sprint runs.

**Sub-steps:**
1. Scan sprint board for tickets with no story point additions from this session
2. Cross-reference against calendar — confirm no time was spent
3. Flag tickets with no time as candidates for sprint removal
4. At end of sprint: remove unworked tickets or move to next sprint

**Decision Points:**
- Daily run: flag only — do not remove mid-sprint
- End-of-sprint run: remove or defer unworked tickets

**Data In:** Updated Jira sprint board from Steps 3-4
**Data Out:** Clean sprint board; flagged or removed tickets
**Context Needs:** Sprint end date (to determine if this is an end-of-sprint run)
**Failure Modes:** A ticket was worked but the calendar block wasn't added — false positive flag; verify before removing

---

## Step Sequence and Dependencies

### Sequential Flow
```
Step 1 (Open & Orient)
  → Step 2 (Review Calendar Blocks)
    → Step 3 (Reconcile Sprint Tickets) ←→ Step 4 (Enrich Ticket Descriptions)  [in tandem, per ticket]
      → Step 5 (Review Sprint State)
```

### Parallel Steps
- Steps 3 and 4 run in tandem — for each ticket, reconcile first, then enrich before moving to the next

### Critical Path
1 → 2 → 3/4 → 5

### Dependency Map
| Step | Depends On |
|------|-----------|
| Step 2 | Step 1 (calendar must be open and blocks visible) |
| Step 3 | Step 2 (classification must be complete) |
| Step 4 | Step 3 (ticket must exist before enriching) |
| Step 5 | Steps 3+4 (board must be updated before reviewing state) |

---

## Context Shopping List

| Artifact | Description | Used By Steps | Status | Key Contents |
|----------|-------------|---------------|--------|--------------|
| Google Calendar (color-coded) | Time blocks for the day/week; red = unprocessed, green = processed | 1, 2, 3 | Exists — red/green convention needs to be adopted | Focus blocks, meeting blocks, all-day task events, time durations |
| Jira Sprint Board + MCP | Active sprint board with all tickets; Claude has Jira MCP access | 3, 4, 5 | Exists | Ticket titles, descriptions, story points, status, sprint membership |
| Google Tasks | Lightweight task reminders | 1 | Exists | Short-lived reminders; do not convert to sprint tickets |
| Story Point Conversion Rule | 1 hour of work = 0.25 story points; always additive, never recalculate | 3 | Needs to be written down | Rule: +0.25 SP per hour logged; existing points are never reduced |
| Block Classification Rules | How to determine if a calendar block needs a Jira ticket | 2 | Needs to be written down | Focus/task = ticket; meeting = no ticket; personal = no ticket; ad hoc (PR review, research) = judgment call |
| Ticket Description Template | Standard structure for all Jira ticket descriptions | 4 | Needs Creation | "## Why This Matters" + business impact + `---` divider + technical details |
