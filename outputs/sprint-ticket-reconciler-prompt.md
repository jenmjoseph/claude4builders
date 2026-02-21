# Baseline Workflow Prompt: Sprint Ticket Reconciler

**Workflow:** Sprint Ticket Reconciler
**Pattern:** Skill-Powered Prompt
**Interaction Mode:** Interactive
**When to use:** During Daily Sprint Sync — once per red (unprocessed) calendar block that needs to be logged against a Jira ticket.
**Outcome:** Jira ticket updated with story points, status, sprint membership, and an enriched description — calendar block marked green.

---

## Where to Run

**Claude Code** (with both MCPs connected) or a **Claude Project workspace** preloaded with sprint context.

**Required:**
- `sprint-ticket-reconciler` skill at `~/.claude/skills/sprint-ticket-reconciler/`
- Jira MCP (Atlassian official)
- Google Calendar MCP (nspady/google-calendar-mcp) — reads red blocks automatically, flips to green after submit

---

## Baseline Workflow Prompt

> Copy and paste to start a Sprint Ticket Reconciler run. Replace the bracketed fields with your actual block data.

---

You are helping me reconcile a calendar block into my Jira sprint board.

**Rules — follow these exactly:**

1. **Block validation first:** Only process red (unprocessed) blocks. If I tell you a block is already green, stop immediately and say: "This block is already processed (green). Skipping to prevent double-counting."

2. **Story point math:** 1 hour of work = 0.25 story points. Always additive — never recalculate existing story points. State the calculation explicitly before every update: `"[X hrs] × 0.25 = [Y pts] — adding to existing [Z pts] for new total of [Z+Y pts]"`

3. **No Jira writes without approval:** Present the full proposal first. Wait for my explicit confirmation before writing anything to Jira.

4. **Description template:** Every ticket description must use this structure:
```
## Why This Matters
[Business impact — plain language, 1-3 sentences]

---

[Technical details — approach, implementation notes, tools used]
```

---

**Step 1 — Read & Validate**
Use the Google Calendar MCP to read today's calendar events (`list-events`). Use `list-colors` to identify the colorId for red (unprocessed) and green (processed) in this calendar. Present the list of red blocks found and ask me to confirm which one to process before continuing.

If Google Calendar MCP is unavailable, fall back to manual input:
```
Block: [block title]
Date: [date]
Duration: [X hr / X min]
Color: [red / green]
Notes: [any additional context — optional]
```

---

**Run the Sprint Ticket Reconciler skill** — work through the following steps in order for the confirmed block:

**Step 2 — Calculate Story Points**
Multiply duration by 0.25. State the full calculation explicitly. Do not update Jira yet.

**Step 3 — Build Ticket Update Proposal**
- Find the matching Jira ticket. State your match and reasoning. Ask me to confirm before proceeding.
- If no match: propose a ticket title and description. Ask me to confirm before creating.
- Confirm ticket status: ask if this is In Progress or Done.
- Confirm sprint membership: is the ticket in the current sprint?
- Draft the full description update using the template above.
- Present the complete proposal — ticket match, story point math, status, sprint membership, and full description draft — and wait for my approval.

**Step 4 — Submit (after my approval)**
Write story points, status, sprint membership, and description to Jira via Jira MCP. Confirm each update. Then automatically flip the calendar block from red to green using Google Calendar MCP (`update-event`). Confirm the color change was applied.

---

### Error Handling

| Situation | What to do |
|-----------|-----------|
| Block is already green | Stop. Say: "Already processed (green). Skipping." |
| Ambiguous block title | Make best inference, state reasoning, ask for confirmation |
| No matching Jira ticket | Propose new ticket. Do not create without my confirmation. |
| Jira MCP unavailable | Produce full update as structured text I can apply manually |
| Google Calendar MCP unavailable | Fall back to manual block input; remind me to flip block green manually after submit |
| Insufficient context for "Why This Matters" | Insert `[TODO: add business context]`, flag it explicitly |
| Same ticket in multiple blocks today | Sum all durations, process as a single update |

---

## Input Requirements

| Field | Required | Format | Example |
|-------|----------|--------|---------|
| Block title | Yes | Text | "Fivetran connector debugging" |
| Date | Yes | Any date format | "Feb 18" or "2026-02-18" |
| Duration | Yes | Hours or minutes | "1.5 hr" or "90 min" |
| Color | Auto-detected via MCP | Auto-read from Google Calendar; manual fallback: "red" or "green" | "red" |
| Notes | No | Free text | "Fixed the auth token issue, opened PR #42" |

---

## Output Format

At the end of each run, the following will be confirmed:

1. **Block status** — validated as red (processed) or green (skipped)
2. **Story point calculation** — explicit math shown
3. **Jira ticket** — matched or created, confirmed by user
4. **Ticket status** — In Progress or Done, confirmed by user
5. **Sprint membership** — confirmed or updated
6. **Description** — "Why This Matters" + technical details, approved by user
7. **Jira updates written** — confirmation of each MCP write
8. **Block processed** — auto-flipped green via Google Calendar MCP (or manual reminder if MCP unavailable)

---

## Sample Inputs for Testing

### Test Input 1 — Straightforward match
```
Block: Fivetran connector debugging
Date: 2026-02-18
Duration: 1.5 hr
Color: red
Notes: Tracked down auth token expiry issue with the Salesforce connector. Fixed and tested.
```

### Test Input 2 — Longer session, different work type
```
Block: dbt model refactor — accounts table
Date: 2026-02-18
Duration: 3 hr
Color: red
Notes: Refactored the accounts base model to fix the null handling issue causing double-counts in downstream reports.
```

### Test Input 3 — Already processed (green) — should be skipped
```
Block: Sprint retro prep
Date: 2026-02-17
Duration: 1 hr
Color: green
Notes: Already logged yesterday.
```
