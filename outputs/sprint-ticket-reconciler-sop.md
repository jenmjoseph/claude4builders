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
- [ ] `sprint-ticket-reconciler` skill installed at `~/.claude/skills/sprint-ticket-reconciler/`
- [ ] Calendar block is red (unprocessed) — green blocks are skipped automatically
- [ ] Jira sprint board is open and tickets are visible (for confirming matches)

---

## Trigger

**When:** During Daily Sprint Sync, once per red calendar block
**Frequency:** Daily (end of workday) or as needed during sprint

---

## Procedure

### Step 1: Provide the calendar block (Human)

Paste the block details into Claude using this format:

```
Block: [block title]
Date: [date]
Duration: [X hr / X min]
Color: [red / green]
Notes: [optional context]
```

Or describe the block in plain language — the skill extracts what it needs.

### Step 2: Validate the block (AI)

The skill checks block color automatically.

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

### Step 8: Mark block processed (Human)

Flip the calendar block from **red → green** in Google Calendar to mark it as processed.

> 💡 **The skill reminds you to do this** — it cannot change calendar colors directly.

---

## Outputs

| Output | Destination | Format |
|--------|-------------|--------|
| Updated story points | Jira ticket | Numeric (additive) |
| Updated status | Jira ticket | In Progress / Done |
| Enriched description | Jira ticket | Markdown (Why This Matters template) |
| Green calendar block | Google Calendar | Color change (manual) |

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
| Block title is ambiguous | Skill infers from context and asks for confirmation — correct if wrong |
| No matching ticket exists | Skill proposes a new ticket — confirm title and description before creating |
| "Why This Matters" is generic | Provide more context about the business impact; skill will redraft |
| Same ticket appears in multiple blocks | Tell the skill — it will sum durations and process as a single update |

---

## Automation Notes

**Skill:** `sprint-ticket-reconciler` at `~/.claude/skills/sprint-ticket-reconciler/SKILL.md`
**GitHub:** https://github.com/jenmjoseph/claude4builders/blob/assignment/build-agent-skills/skills/sprint-ticket-reconciler/SKILL.md

**Human checkpoints:**
1. Ticket match confirmation (Step 4)
2. Status + sprint membership confirmation (Step 5)
3. Description approval (Step 6)
4. Final submit approval (Step 7)
5. Calendar block flip (Step 8)

**No Jira writes occur without explicit human approval.**
