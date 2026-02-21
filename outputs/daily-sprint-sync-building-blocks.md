# AI Building Block Map: Daily Sprint Sync

**Workflow Name:** Daily Sprint Sync
**Type:** Collaborative AI (Augmented)
**Status:** Under Development
**Owner:** Jennifer Joseph
**Trigger:** End of day (daily habit); urgency increases in the days before bi-weekly sprint retro

---

## Scenario Summary

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

## Step-by-Step Decomposition Table

| # | Step | Action | Autonomy Level | Decision Points | Failure Mode | Data In | Data Out | Context Needed | AI Building Block(s) |
|---|------|--------|---------------|----------------|-------------|---------|----------|----------------|---------------------|
| 1 | Open & Orient | Open Calendar, Jira board, and Google Tasks. Identify red (unprocessed) blocks. Close completed Task reminders. | **Human** | Daily run: today's blocks only; End-of-sprint: full sprint window | Calendar not up to date — must backfill before proceeding | Google Calendar, Jira sprint board, Google Tasks | Mental map of what needs processing | None | — |
| 2 | Review Calendar Blocks | Classify each red block as: focus/task, meeting, personal, or ad hoc. Tag blocks needing Jira reconciliation. | **AI-Semi-Autonomous** | Ad hoc blocks (PR review, research) require judgment call | Ambiguous block titles — Claude infers from context or flags for review | Red calendar blocks | Classified list of blocks requiring ticket action | Block Classification Rules (Context MD) | Prompt + Context MD |
| 3 | Reconcile Sprint Tickets | For each focus block: find or create Jira ticket, add story points (+0.25/hr, additive), update status and sprint membership, flip block green. | **AI-Semi-Autonomous** | Block has no ticket → create via Jira MCP; ticket in sprint with no time → flag for removal; end of sprint → audit and remove | Jira MCP unavailable; ambiguous block-to-ticket mapping; double-counting if green blocks are re-processed | Classified focus blocks; story point rule | Updated Jira tickets; green calendar blocks | Story Point Conversion Rule; Jira MCP | Skill + Jira MCP |
| 4 | Enrich Ticket Descriptions | For each ticket touched in Step 3: apply description template — "Why This Matters" (business impact) + divider + technical details. | **AI-Semi-Autonomous** | Existing description: update/append; No description: apply full template; OKR/epic context comes from linked tickets — do not duplicate | Insufficient context to write "Why This Matters" — leave placeholder, do not skip | Tickets from Step 3; calendar block context | Jira ticket descriptions with business impact + technical details | Ticket Description Template; business context per block | Skill + Jira MCP |
| 5 | Review Sprint State | Scan sprint board for tickets with no calendar time. Flag for removal (daily) or remove/defer (end of sprint). | **AI-Semi-Autonomous** | Daily: flag only; End of sprint: remove or defer unworked tickets | Ticket was worked but calendar block missing — false positive; verify before removing | Updated Jira sprint board | Clean sprint board; flagged or removed tickets | Sprint end date | Prompt + Jira MCP |

---

## Autonomy Spectrum Summary

### Human Steps
- **Step 1 — Open & Orient:** Manual app-opening and visual scan. No AI value in automating initiation. Human sets the context window (day vs. sprint).

### AI-Deterministic Steps
- None. The story point math in Step 3 is rule-based, but the surrounding ticket matching and creation require judgment — making the step semi-autonomous overall.

### AI-Semi-Autonomous Steps (with Human Review Gates)
- **Step 2 — Review Calendar Blocks:** AI applies classification rules; human reviews judgment calls on ad hoc blocks before proceeding.
- **Step 3 — Reconcile Sprint Tickets:** AI handles story point math and ticket updates via Jira MCP; human approves new ticket creation and ambiguous matches.
- **Step 4 — Enrich Ticket Descriptions:** AI drafts "Why This Matters" using template and calendar context; human reviews before saving to Jira.
- **Step 5 — Review Sprint State:** AI surfaces flagged tickets with no time logged; human makes final removal decision.

### AI-Autonomous Steps
- None. Every step with AI involvement has a human review gate — appropriate for a Collaborative AI workflow where Jennifer drives each step.

---

## Step Sequence and Dependencies

### Sequential Flow
```
Step 1 (Open & Orient)  [Human]
  → Step 2 (Review Calendar Blocks)  [AI-Semi-Autonomous]
    → Step 3 (Reconcile Sprint Tickets) ←→ Step 4 (Enrich Ticket Descriptions)  [AI-Semi-Autonomous, in tandem per ticket]
      → Step 5 (Review Sprint State)  [AI-Semi-Autonomous]
```

### Parallel Steps
- Steps 3 and 4 run in tandem — for each ticket, reconcile (Step 3) then enrich (Step 4) before moving to the next ticket.

### Critical Path
1 → 2 → 3/4 → 5

### Dependency Map
| Step | Depends On |
|------|-----------|
| Step 2 | Step 1 — Calendar must be open and red blocks visible |
| Step 3 | Step 2 — Block classification must be complete |
| Step 4 | Step 3 — Ticket must exist before enriching |
| Step 5 | Steps 3+4 — Board must be updated before reviewing state |

---

## Prerequisites

### What Must Be in Place
- Google Calendar red/green color-coding convention adopted and in use
- Jira MCP connected to Claude instance (already in place)
- Story Point Conversion Rule documented and loaded as context
- Block Classification Rules documented and loaded as context
- Ticket Description Template created and loaded as context
- Claude Project created with system instructions containing all three context artifacts above

### External Dependencies
| Dependency | Status | Notes |
|-----------|--------|-------|
| Google Calendar access | Exists | Red/green convention needs to be adopted |
| Jira MCP | Exists | Already connected to Claude instance |
| Google Tasks access | Exists | Used for lightweight reminder close-out only |
| Claude Project (Daily Sprint Sync) | Needs Creation | Houses system instructions + context artifacts |

---

## Context Inventory

| Artifact | Type | Description | Used By Steps | Status | Format | Key Contents |
|----------|------|-------------|---------------|--------|--------|--------------|
| Block Classification Rules | Context MD | Rules for determining whether a calendar block requires a Jira ticket | 2 | Needs Creation | Markdown | Focus/task = ticket; meeting = no ticket; personal = no ticket; ad hoc (PR review, research) = judgment call |
| Story Point Conversion Rule | Context MD | Conversion rule for translating calendar time to Jira story points | 3 | Needs Creation | Markdown | 1 hour = 0.25 story points; always additive — never recalculate existing points |
| Ticket Description Template | Context MD | Standard structure for all Jira ticket descriptions | 4 | Needs Creation | Markdown | `## Why This Matters` + business impact (1-3 sentences) + `---` divider + technical details |
| Google Calendar (color-coded) | Live Data | Time blocks with red (unprocessed) / green (processed) color convention | 1, 2, 3 | Exists — convention needs adoption | Calendar events | Focus blocks, meeting blocks, all-day task events, time durations |
| Jira Sprint Board | Live Data | Active sprint with all tickets, story points, status, and sprint membership | 3, 4, 5 | Exists | Jira (via MCP) | Ticket titles, descriptions, story points, status, sprint membership, linked/parent tickets |
| Google Tasks | Live Data | Lightweight task reminders | 1 | Exists | Google Tasks | Short-lived reminders — not converted to sprint tickets |

---

## Tools and Connectors Required

| Tool | Used By Steps | Status | Purpose |
|------|--------------|--------|---------|
| Jira MCP | 3, 4, 5 | Exists — connected | Create/update tickets, update story points, manage sprint membership, read board state |
| Google Calendar | 1, 2, 3 | Exists — color convention needed | Read time blocks, classify blocks, mark blocks as processed (green) |
| Google Tasks | 1 | Exists | Close out completed task reminders |
| Claude Project | 2, 3, 4, 5 | Needs Creation | Houses system instructions with block classification rules, story point rule, and ticket description template |

---

## Recommended Build Order

1. **Create the three Context MDs** — Block Classification Rules, Story Point Conversion Rule, Ticket Description Template
2. **Set up Claude Project** — Load all three context MDs into project system instructions
3. **Build the Reconcile Skill (Steps 3+4)** — The core repeatable unit: takes a calendar block, finds/creates a ticket, updates story points, and enriches the description
4. **Test end-to-end** — Run one day's blocks through the full flow manually, refine prompts
5. **Adopt red/green calendar convention** — Start color-coding blocks consistently to enable the processed/unprocessed filter
