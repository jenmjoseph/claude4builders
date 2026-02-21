# AI Building Block Spec: Sprint Ticket Reconciler

**Workflow Name:** Sprint Ticket Reconciler
**Extracted From:** Daily Sprint Sync — Steps 3 + 4
**Type:** Deterministic Workflow
**Status:** Ready to Build
**Owner:** Jennifer Joseph
**Pattern:** Skill-Powered Prompt
**Interaction Mode:** Interactive
**Model:** Sonnet (claude-sonnet-4-6)

---

## Execution Pattern Recommendation

**Pattern:** Skill-Powered Prompt — Interactive

**Reasoning:** The workflow has two fully deterministic steps (block validation and story point math) and one semi-autonomous step (ticket matching and description drafting) that already exists as a reusable skill. The skill encapsulates all reconcile + enrich logic; the baseline prompt provides the session wrapper. Interactive mode is required — the human reviews the full update proposal before every Jira write. No autonomous execution is appropriate for a workflow that modifies shared sprint state.

---

## Architecture Decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Execution pattern | Skill-Powered Prompt | Core logic is a reusable skill; prompt wraps it |
| Interaction mode | Interactive | Human review gate before every Jira write |
| Platform | Claude Code | User's primary AI development environment |
| Model | Sonnet | Deterministic math is lightweight; "Why This Matters" drafting needs writing quality |
| Context delivery | Inline in skill | Rules are stable and workflow-specific; no separate context files needed for MVP |
| Tool access (Jira) | Jira MCP | Official Atlassian MCP server — already connected |
| Tool access (Calendar) | Google Calendar MCP | Reads today's events (list-events, list-colors) and auto-flips processed blocks to green (update-event) — replaces manual paste input |

---

## Scenario Summary

| Field | Value |
|-------|-------|
| **Name** | Sprint Ticket Reconciler |
| **Description** | Takes a single calendar block (red/unprocessed) and produces a complete Jira ticket update proposal — story points calculated, status confirmed, description enriched using the standard template — for human review before writing |
| **Process Outcome** | Jira ticket updated with accurate story points, current status, sprint membership, and a business-impact description — calendar block marked as processed (green) |
| **Trigger** | Manual — invoked per red calendar block during Daily Sprint Sync |
| **Type** | Deterministic |
| **Business Objective** | Ensure every hour of work is accurately reflected in the sprint board with context stakeholders can understand — without double-counting or manual calculation |

---

## Step-by-Step Decomposition

| # | Step | Action | Autonomy | Building Block(s) | Skill Candidate | MCPs |
|---|------|--------|----------|-------------------|-----------------|------|
| 1 | Read & Validate Block | Read today's events via Google Calendar MCP. Filter for red (unprocessed) blocks using list-colors to confirm the red colorId. Extract title, duration, any ticket reference. Green blocks → skip automatically. | AI-Deterministic | Prompt (inline rule) + Google Calendar MCP | No | Google Calendar MCP (`list-events`, `list-colors`) |
| 2 | Calculate Story Points | Apply conversion: hours × 0.25. State math explicitly. Confirm additive total (never recalculate from scratch). | AI-Deterministic | Prompt (inline rule) | No | — |
| 3 | Build Ticket Update Proposal | Find or create matching Jira ticket. Confirm ticket match. Confirm status + sprint membership. Draft "Why This Matters" + technical details using template. Present full proposal for review. | AI-Semi-Autonomous | **sprint-ticket-reconciler skill** + Jira MCP | ✓ Exists | Jira MCP (read) |
| 4 | Human Review + Submit | User reviews full proposal. On approval: write story points, status, sprint membership, and description to Jira. Auto-flip block to green via Google Calendar MCP. | Human gate + AI | Skill (Step F) + Jira MCP + Google Calendar MCP | — | Jira MCP (write) + Google Calendar MCP (`update-event`) |

---

## Autonomy Spectrum Summary

### AI-Deterministic Steps
- **Step 1 — Validate Block:** Binary rule — red = proceed, green = skip. No judgment required.
- **Step 2 — Calculate Story Points:** Pure math (hours × 0.25). Additive, never recalculate. Result stated explicitly.

### AI-Semi-Autonomous Steps (Human Review Gates)
- **Step 3 — Build Ticket Update Proposal:** AI finds/creates ticket and drafts the description. Human confirms ticket match, status, sprint membership, and description before any write occurs.

### Human Steps
- **Step 4 — Approve and Submit:** Human makes the final call on every field before Jira is updated. After approval, Google Calendar MCP automatically flips the block to green — no manual color change needed.

---

## Skill Candidates

### `sprint-ticket-reconciler` — Already Built

**Location:** `~/.claude/skills/sprint-ticket-reconciler/SKILL.md`

**Purpose:** Takes a single calendar block and executes the full reconcile + enrich sequence — find/create ticket, calculate story points, confirm status and sprint membership, draft description using template, present full proposal, and submit to Jira on approval.

**Inputs:**
- Calendar block title
- Duration (hours or minutes)
- Optional: ticket reference, notes, additional context

**Outputs:**
- Confirmed Jira ticket match (or new ticket created)
- Story point calculation (explicit math stated)
- Ticket status (In Progress / Done)
- Sprint membership confirmation
- Full description draft ("Why This Matters" + technical details)
- Jira updates written after human approval
- Block automatically flipped green via Google Calendar MCP (`update-event`)

**Decision Logic:**
- Green block → skip entirely (prevent double-counting)
- No matching ticket → propose new ticket, do not create without confirmation
- Same ticket across multiple blocks → sum durations, process as single update
- Existing description → update/append, never replace
- Insufficient context for "Why This Matters" → insert `[TODO: add business context]`, flag it

**Failure Modes:**

| Situation | Handling |
|-----------|----------|
| Block already green | Skip. Explicit message to user. |
| Ambiguous block title | Best inference + reasoning stated. Ask for confirmation. |
| No matching Jira ticket | Propose new ticket. No creation without confirmation. |
| Jira MCP unavailable | Produce full manual update output. User applies manually. |
| Insufficient description context | Insert placeholder. Flag explicitly. Never skip section. |
| Same ticket in multiple blocks | Sum durations. Single update. |

---

## Step Sequence and Dependencies

```
Google Calendar MCP → list-events (today) + list-colors
  │
  ▼
Step 1 — Read & Validate Block  [AI-Deterministic]
  │ Green colorId? → STOP (already processed)
  │ Red colorId? → extract title, duration, ticket ref
  ▼
Step 2 — Calculate Story Points [AI-Deterministic]
  │ Output: hours × 0.25 = points to add
  ▼
Step 3 — Build Proposal         [AI-Semi-Autonomous, sprint-ticket-reconciler skill]
  │ Jira MCP: find/create ticket → confirm with user
  │ Draft description → present for review
  ▼
Step 4 — Human Review + Submit  [Human gate]
  │ Approved → Jira MCP: write story points, status, sprint, description
  │ Google Calendar MCP: update-event → flip block green (automated)
  │ Output: updated Jira ticket + block automatically marked green
```

### Dependency Map

| Step | Depends On |
|------|-----------|
| Step 2 | Step 1 — block must be validated as red before calculating |
| Step 3 | Step 2 — story point calculation must be complete before proposing |
| Step 4 | Step 3 — full proposal must be drafted before human can review |

---

## Prerequisites

### What Must Be in Place
- Jira MCP connected to Claude instance (official Atlassian MCP server)
- Google Calendar MCP connected (workspacemcp.com) — not yet set up; manual paste fallback available
- `sprint-ticket-reconciler` skill installed at `~/.claude/skills/sprint-ticket-reconciler/`
- Google Calendar red/green color-coding convention in use (red = unprocessed)
- Claude Project set up for Daily Sprint Sync sessions

### Setup Commands
```bash
# Jira MCP (if not already connected)
claude mcp add --transport sse atlassian https://mcp.atlassian.com/v1/sse

# Google Calendar MCP (workspacemcp.com)
# Cloud plan (no credentials to manage): schedule setup at https://workspacemcp.com/workspace-mcp-cloud
# Local install: uvx workspace-mcp  (requires Google OAuth credentials from Google Cloud Console)
```

> Google Calendar MCP not yet configured. Use the manual block paste fallback until setup is complete.

---

## Context Inventory

| Artifact | Type | Description | Used By | Status | Location |
|----------|------|-------------|---------|--------|----------|
| Story Point Conversion Rule | Inline | 1 hr = 0.25 SP; always additive; explicit math required | Steps 2, 3 | Exists — inline in skill | SKILL.md |
| Ticket Description Template | Inline | `## Why This Matters` + business impact + `---` + technical details | Step 3 | Exists — inline in skill | SKILL.md |
| Block Validation Rules | Inline | Red = process; green = skip; green blocks prevent double-counting | Step 1 | Exists — inline in skill | SKILL.md |
| Error Handling Rules | Inline | Table of failure modes and responses | All | Exists — inline in skill | SKILL.md |

---

## Tools and Connectors Required

| Tool | Steps | Status | Purpose |
|------|-------|--------|---------|
| Jira MCP (Atlassian official) | 3, 4 | Exists — connected | Read sprint board, find/create tickets, update story points, update status, update descriptions, manage sprint membership |
| Google Calendar MCP (workspacemcp.com) | 1, 4 | Needs setup — not yet configured | `list-events`: read today's calendar blocks; `list-colors`: identify red/green colorIds; `update-event`: flip processed block to green |

---

## Integration Research

| Tool | Platform Availability | Notes |
|------|----------------------|-------|
| Jira MCP | Available with setup — official Atlassian MCP server | `claude mcp add --transport sse atlassian https://mcp.atlassian.com/v1/sse` — OAuth via SSE; already connected |
| Google Calendar MCP | Available with setup — workspacemcp.com | Cloud plan (managed, no credentials to self-manage): workspacemcp.com/workspace-mcp-cloud — local install via `uvx workspace-mcp` requires Google Cloud OAuth credentials; supports `list-events`, `list-colors`, `update-event` — not yet configured |

---

## Model Recommendation

**Recommended model:** `claude-sonnet-4-6`

**Rationale:** Steps 1–2 are pure rule-following (deterministic math and validation) — no reasoning depth needed. Step 3 requires writing quality for the "Why This Matters" section, which needs Sonnet-level output to produce clear, stakeholder-facing business impact statements. Opus is not warranted; Haiku would underperform on the description drafting.

---

## Recommended Implementation Order

### Tier 1 — Already Done (Start Here)
| What | Status |
|------|--------|
| `sprint-ticket-reconciler` skill | ✓ Built |
| Jira MCP connection | ✓ Connected |
| Google Calendar MCP connection | Needs setup — workspacemcp.com (cloud plan or local install with credentials) |

### Tier 2 — Build Now
| What | Action |
|------|--------|
| Baseline workflow prompt | Write `sprint-ticket-reconciler-prompt.md` |
| Test with 2–3 real calendar blocks | Verify consistent output |

### Tier 3 — Optional Enhancements
| What | Action |
|------|--------|
| Standalone context MDs | Extract rules from skill into `context/` files for reuse across workflows |
| Claude Project setup | Load skill into project instructions for persistent session context |

---

## Where to Run

**Recommendation: Claude Code (inline) or Claude Project workspace**

**Why:** The skill is already installed in `~/.claude/skills/`. It runs best in Claude Code where the Jira MCP is connected. For daily use, a Claude Project preloaded with the sprint context keeps ticket history across sessions.

**Each run, provide:**
- Calendar block: title, date, duration
- Optional: any context about what was worked on

**The skill handles everything else.**
