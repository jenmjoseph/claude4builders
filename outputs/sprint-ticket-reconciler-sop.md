# SOP: Sprint Ticket Reconciler

**Workflow Type:** Automated (Skill-Powered Prompt — Deterministic)
**Trigger:** Manual — once per red calendar block during Daily Sprint Sync
**Owner:** Jennifer Joseph
**Last Updated:** 2026-02-21

---

## Overview

The Sprint Ticket Reconciler converts a single red (unprocessed) Google Calendar time block into an updated Jira sprint ticket — calculating story points, confirming ticket match, and drafting a business-impact description — then presents the full proposal for review before writing anything to Jira.

---

## Prerequisites

- [ ] Jira MCP connected to Claude (Atlassian official MCP server)
- [ ] Google Calendar MCP connected (nspady/google-calendar-mcp) — for reading events and auto-flipping blocks green
- [ ] `sprint-ticket-reconciler` skill installed at `~/.claude/skills/sprint-ticket-reconciler/`
- [ ] Calendar block is red (unprocessed) — green blocks are skipped automatically
- [ ] Jira sprint board is accessible via MCP

---

## Trigger

**When:** During Daily Sprint Sync, once per red calendar block
**Frequency:** Daily (end of workday) or as needed during sprint

---

## Procedure

### Step 1: Read today's calendar blocks (AI)

Claude reads today's events via Google Calendar MCP (`list-events`) and uses `list-colors` to identify which colorId corresponds to red (unprocessed) and green (processed) in your calendar.

Claude presents the list of red blocks found and asks you to confirm which one to process.

**Fallback — if Google Calendar MCP is unavailable:** Paste the block details manually:
```
Block: [block title]
Date: [date]
Duration: [X hr / X min]
Color: [red / green]
Notes: [optional context]
```

### Step 2: Validate the block (AI)

The skill checks block color from the MCP-read event data.

**Decision point:**
- Block is **green** → skill stops immediately: *"Already processed. Skipping."* Do not proceed.
- Block is **red** → skill extracts title, duration, and any ticket reference, then continues.

### Step 3: Calculate story points (AI)

The skill calculates: `hours × 0.25 = points to add`

The full calculation is stated explicitly:
`"[X hrs] × 0.25 = [Y pts] — adding to existing [Z pts] for new total of [Z+Y pts]"`

Story points are **always additive** — existing points are never recalculated.

> 💡 **Tip:** 15 min = 0.0625 SP · 30 min = 0.125 · 1 hr = 0.25 · 2 hr = 0.5 · 4 hr = 1.0

### Step 4: Confirm ticket match (Human)

The skill proposes a Jira ticket match and states its reasoning.

**Decision point:**
- Match is correct → confirm and continue
- Match is wrong → tell the skill the correct ticket ID
- No ticket exists → skill proposes a new ticket title; confirm before it creates one

### Step 5: Confirm status and sprint membership (Human)

The skill asks:
1. Is this ticket **In Progress** or **Done**?
2. Is it in the current sprint? (If not, skill asks before adding it.)

### Step 6: Review description proposal (Human)

The skill drafts a full description update using the standard template:

```
## Why This Matters
[Business impact — 1-3 plain-language sentences]

---

[Technical details — approach, tools, PRs, decisions]
```

Review the draft. Edit inline or tell the skill what to change. Do not approve until the "Why This Matters" section accurately reflects the business value.

### Step 7: Approve and submit (Human → AI)

Once approved, the skill writes to Jira via MCP:
- Story points updated (additive)
- Status updated
- Sprint membership confirmed or updated
- Description written

Each update is confirmed individually.

### Step 8: Mark block processed (AI)

After Jira updates are confirmed, Claude automatically flips the calendar block from **red → green** via Google Calendar MCP (`update-event`). Claude confirms the color change was applied.

**Fallback — if Google Calendar MCP is unavailable:** Flip the block manually in Google Calendar.

---

## Outputs

| Output | Destination | Format |
|--------|-------------|--------|
| Updated story points | Jira ticket | Numeric (additive) |
| Updated status | Jira ticket | In Progress / Done |
| Enriched description | Jira ticket | Markdown (Why This Matters template) |
| Green calendar block | Google Calendar (via MCP) | Color change (automated via `update-event`) |

---

## Quality Checks

- [ ] Story point calculation is stated explicitly and math is correct (`hours × 0.25`)
- [ ] "Why This Matters" section is present and written in plain language (not generic)
- [ ] Calendar block has been flipped green after submission
- [ ] No green blocks were re-processed (double-counting prevention)

---

## When Things Go Wrong

| Problem | Solution |
|---------|----------|
| Jira MCP unavailable | Skill outputs the full proposal as structured text — apply updates manually |
| Google Calendar MCP unavailable | Fall back to manual block input (paste format); flip block green manually after submit |
| Block title is ambiguous | Skill infers from context and asks for confirmation — correct if wrong |
| No matching ticket exists | Skill proposes a new ticket — confirm title and description before creating |
| "Why This Matters" is generic | Provide more context about the business impact; skill will redraft |
| Same ticket appears in multiple blocks | Tell the skill — it will sum durations and process as a single update |

---

## Automation Notes

**Skill:** `sprint-ticket-reconciler` at `~/.claude/skills/sprint-ticket-reconciler/SKILL.md`
**GitHub:** https://github.com/jenmjoseph/claude4builders/blob/assignment/build-agent-skills/skills/sprint-ticket-reconciler/SKILL.md

**MCPs used:**
- Jira MCP (Atlassian official) — Steps 3, 4
- Google Calendar MCP (nspady/google-calendar-mcp) — Steps 1, 8

**Human checkpoints:**
1. Block selection confirmation (Step 1)
2. Ticket match confirmation (Step 4)
3. Status + sprint membership confirmation (Step 5)
4. Description approval (Step 6)
5. Final submit approval (Step 7)

**No Jira writes occur without explicit human approval.**
