# Baseline Workflow Prompt: Daily Sprint Sync

**Workflow:** Daily Sprint Sync
**When to use:** At the end of each workday (or at minimum, before your bi-weekly sprint retro) to reconcile Google Calendar time blocks with your Jira sprint board and keep sprint state accurate.
**Outcome:** Jira sprint tickets updated with accurate story points, status, and enriched descriptions — with calendar blocks marked as processed.

---

## Where to Run

**Recommendation: Claude Project workspace**

**Why:** This workflow runs daily, requires 3 context files that stay consistent across every run, and benefits from conversation memory (Claude will remember ticket decisions from earlier in the sprint).

**Set up the project once:**
- Pre-load **Block Classification Rules** (once created)
- Pre-load **Story Point Conversion Rule** (once created)
- Pre-load **Ticket Description Template** (once created)

Each session, provide:
- Today's unprocessed (red) calendar blocks as a pasted list or screenshot description
- Your Jira sprint board state (or let Claude pull it via Jira MCP if connected)

---

## Baseline Workflow Prompt

> Copy and paste the prompt below to start a Daily Sprint Sync session.

---

You are helping me run my Daily Sprint Sync — a recurring end-of-day session to reconcile my Google Calendar time blocks with my Jira sprint board.

**Your role:** Work through each step with me interactively. Ask for what you need. Flag decisions that require my judgment. Do not skip steps.

---

### Context

**Story point conversion rule:**
1 hour of work = 0.25 story points. Always additive — never recalculate existing story points on a ticket. Only add the new time logged.

**Block classification rules:**
Classify each calendar block I give you as one of:
- **Focus/task work** → needs a Jira ticket
- **Meeting** → no ticket needed, skip
- **Personal block** → no ticket needed, skip
- **Ad hoc** (PR review, reading, research, etc.) → usually no ticket; flag for my judgment

**Processed block convention:**
- Red calendar block = not yet processed into Jira
- Green calendar block = already processed (do not re-process — this prevents double-counting)
Only process red blocks.

**Ticket description template:**
Every Jira ticket I touch today should use this structure in the description field:
```
## Why This Matters
[Business impact — plain language, 1-3 sentences. What value does this work deliver? Why does it matter to the business or stakeholders?]

---

[Technical details — approach, implementation notes, AI tools used, decisions made]
```
- OKR and epic context come from linked/parent tickets in Jira — do not repeat them in the description.
- Note AI tool usage (e.g., "Used Claude Code to generate dbt test logic") in the technical details section.

---

### Step 1 — Open & Orient (Human)

I will open Google Calendar, my Jira sprint board, and Google Tasks before we begin.

- I am running this as a: **[daily check-in / end-of-sprint cleanup]** ← I will tell you which
- For a daily check-in: we process today's red blocks only
- For end-of-sprint cleanup: we process all red blocks from the sprint window

Once I confirm I'm ready, proceed to Step 2.

---

### Step 2 — Review Calendar Blocks (AI)

I will paste my unprocessed (red) calendar blocks below. For each block, classify it using the block classification rules above.

Present your classifications as a table:

| Block | Time | Duration | Classification | Needs Ticket? |
|-------|------|----------|----------------|---------------|

For any **ad hoc** blocks, flag them and ask me to decide before we proceed.
For any ambiguous block titles, make your best inference and note your reasoning — I will confirm or correct.

Wait for my confirmation on the classified list before proceeding to Step 3.

**[PASTE CALENDAR BLOCKS HERE]**

---

### Step 3+4 — Reconcile and Enrich Tickets (AI) — run in tandem per ticket

For each focus block confirmed in Step 2, work through the following in sequence. Complete one ticket fully before moving to the next.

**For each focus block:**

1. **Find the matching Jira ticket.**
   - If a ticket exists: name it and confirm with me before updating.
   - If no ticket exists: propose a ticket title and description, then ask me to confirm before creating it via Jira MCP.

2. **Calculate story points to add.**
   - Use the conversion rule: 1 hour = 0.25 story points.
   - State the calculation explicitly: "[X hrs] × 0.25 = [Y points] to add"
   - Add to the existing story point total — never recalculate from scratch.

3. **Update ticket status.**
   - Ask me: is this ticket In Progress or Done?

4. **Update sprint membership.**
   - If the ticket is not in the current sprint, ask me to confirm before adding it.
   - If the ticket is in the sprint but I did not work on it today, flag it — do not remove it mid-sprint.

5. **Enrich the ticket description.**
   - Draft the "Why This Matters" section based on what you know about the work and its business context.
   - Draft the technical details section based on the block title and any context I provide.
   - Show me the full draft and wait for my approval before writing it to Jira.

6. **Confirm block is processed.**
   - After the ticket is updated and description is approved, remind me to flip the calendar block from red to green.

Repeat for each focus block.

---

### Step 5 — Review Sprint State (AI)

After all blocks are processed, scan the Jira sprint board for tickets that have no story points added in this session.

Present a table:

| Ticket | Story Points | Last Updated | Status | Recommendation |
|--------|-------------|--------------|--------|----------------|

**For a daily check-in:** Flag candidates for removal — do not remove them. I will act on these at end of sprint.
**For end-of-sprint cleanup:** Ask me to confirm removal or deferral for each flagged ticket.

---

### Input Requirements

Provide the following at the start of each session:

| Input | Format | Notes |
|-------|--------|-------|
| Session type | "daily check-in" or "end-of-sprint cleanup" | Determines scope and actions |
| Unprocessed calendar blocks | Pasted list or description | Include block title, start time, duration |
| Jira sprint board state | Pulled via Jira MCP or pasted | Ticket titles, story points, status |

---

### Output Format

By the end of each session, the following will be complete:

1. **Classified block list** — every red block classified and confirmed
2. **Updated Jira tickets** — story points added, status updated, sprint membership confirmed, descriptions enriched
3. **Processed calendar blocks** — list of blocks to flip green
4. **Sprint state review** — flagged tickets with no time logged, with recommendations

---

## Recommended Implementation Order

### Tier 1 — Quick Wins (Start Here)
Build these first — clear inputs/outputs, no external integrations required.

| Step | What to Build |
|------|--------------|
| Step 2 — Review Calendar Blocks | Write the Block Classification Rules as a Context MD. The prompt logic is already in the Baseline Prompt above. |
| Step 4 — Enrich Ticket Descriptions | Write the Ticket Description Template as a Context MD. Already embedded in the prompt above; formalize it as a standalone reference. |

**Action:** Create two markdown files — `context/block-classification-rules.md` and `context/ticket-description-template.md`. Load them into a Claude Project.

---

### Tier 2 — High-Value Semi-Autonomous Steps (Build Next)
Steps where AI does most of the work but needs a human review gate.

| Step | What to Build |
|------|--------------|
| Step 3 — Reconcile Sprint Tickets | Write the Story Point Conversion Rule as a Context MD. Consider packaging Steps 3+4 as a reusable Skill: takes a calendar block as input, outputs a Jira update proposal for review. |
| Step 5 — Review Sprint State | Prompt is already in the Baseline above. Once Jira MCP is confirmed, test the board scan against a real sprint. |

**Action:** Create `context/story-point-rule.md`. Test Steps 3+4 manually against 3-5 real calendar blocks. Refine the "Why This Matters" draft quality before packaging as a Skill.

---

### Tier 3 — Complex / Integration Steps (Tackle Last)
Requires confirmed MCP connectivity and real data testing.

| Step | What to Build |
|------|--------------|
| Steps 3+4 as a packaged Skill | Once the prompt is tested and refined, package the Reconcile + Enrich logic as a Claude Code Skill for repeatable invocation |
| Jira MCP validation | Confirm the Jira MCP can: read sprint board, create tickets, update story points, update status, update description. Test each action separately before running end-to-end. |
| Red/green calendar convention | Adopt and consistently apply the red/green color-coding in Google Calendar. This is a habit, not a technical build — but it's a prerequisite for the processed/unprocessed filter to work. |

**Action:** Validate Jira MCP capabilities. Package Skill after 2-3 successful manual runs.
