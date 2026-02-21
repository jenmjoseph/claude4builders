---
name: "Stakeholder Request Intake"
description: "Transforms a stakeholder conversation (Slack thread, Zoom notes, or verbal summary) into a fully structured Jira ticket — extracting the ask, timeline, priority, OKR mapping, and acceptance criteria — ready for Jennifer to review and submit."
when_to_use: "Use when a stakeholder has communicated a request via Slack, Zoom, or verbally and you need to turn that conversation into a structured, complete Jira ticket. Especially valuable when covering manager responsibilities or handling high intake volume."
version: "1.0"
author: "Jennifer Joseph"
workflow: "Stakeholder Request Intake"
---

# Stakeholder Request Intake

You are running the **Stakeholder Request Intake** workflow — transforming an unstructured stakeholder conversation into a complete, ready-to-submit Jira ticket.

Your job is to extract signal from noise, apply consistent structure, and produce a ticket that a developer could pick up without needing to re-read the original conversation. Do not submit anything to Jira until the user explicitly approves the output.

---

## Ticket Structure

Every output uses this format:

```
**Title:** [Concise, action-oriented ticket title — verb + object + context]

**Description:**

## Why This Matters
[Business impact — plain language, 1-3 sentences. What does this request unlock for the stakeholder? What breaks or stalls without it?]

---

[Technical scope — what needs to be built, changed, or investigated. Include: affected models/pipelines/tables, key constraints, known edge cases]

**Priority:** [Critical / High / Medium / Low]
**Requested By:** [Stakeholder name and team]
**Requested Timeline:** [Deadline or urgency signal from the conversation]
**OKR Mapping:** [Which company or team OKR does this support? If unclear, flag it]
**Acceptance Criteria:**
- [ ] [Condition 1 — specific and verifiable]
- [ ] [Condition 2]
- [ ] [Add as many as needed]

**Open Questions:**
- [Any clarifying questions that must be answered before work starts]

**Sub-tickets needed:** [Yes / No / Maybe — note if the request should be broken into subtasks]
```

---

## Extraction Rules

When reading a stakeholder conversation, extract the following:

### 1. The Core Ask
- What does the stakeholder actually want? (Not what they said literally — what they need)
- Distinguish between: a new data model, a pipeline fix, a report change, a data quality investigation, an ad hoc query, or a definition/methodology question
- If the conversation contains multiple asks, identify each one — they may need separate tickets

### 2. The Business Driver
- Why do they need this? What decision, deadline, or process depends on it?
- Look for: reporting deadlines, audits, leadership asks, blocked workflows, compliance requirements
- This becomes the "Why This Matters" section

### 3. Timeline and Priority
- Any explicit deadline mentioned → use it
- Urgency signals without explicit dates: "end of quarter," "before the board meeting," "ASAP" → flag as High priority and ask for a specific date
- No timeline mentioned → flag as Open Question

### 4. Acceptance Criteria
- What does "done" look like?
- If the stakeholder didn't define it, infer from the ask and make it explicit
- Each criterion should be specific and verifiable — not "the data is correct" but "the report matches the Finance team's expected Q1 revenue total within 0.1%"

### 5. OKR Mapping
- Map to the most relevant team or company OKR if known
- If unclear: leave `[OKR: TBD — flag for clarification]`

### 6. Sub-ticket Assessment
- Is this a single deliverable or a project?
- If the request has 3+ distinct deliverables or spans multiple systems/models, recommend breaking it into sub-tickets

---

## Output Sequence

Work through these steps in order:

### Step 1 — Parse the Conversation

Read the provided conversation thread or notes. Identify:
- The primary ask(s)
- The business driver
- Any stated timeline or deadline
- The requester's name and team
- Any technical details mentioned (specific tables, models, systems)

If the input is too vague to extract a clear ask, stop and ask: "What is the core deliverable the stakeholder is expecting?"

### Step 2 — Flag Ambiguities

Before drafting the ticket, surface anything that needs clarification:
- Multiple conflicting asks
- Missing timeline
- Unclear acceptance criteria
- Unknown OKR mapping
- Technical scope that could expand significantly

List these as Open Questions in the ticket. Do not block ticket creation waiting for answers — make your best inference and flag it.

### Step 3 — Draft the Full Ticket

Produce the complete ticket using the structure above. Show the full draft to the user.

### Step 4 — Review and Refine

Present the draft and ask:
> "Here's the structured ticket. Does this accurately capture the request? Let me know what to adjust before I submit to Jira."

Do not submit to Jira until the user gives explicit approval.

### Step 5 — Submit (After Approval)

Once approved:
1. Create the ticket in Jira via Jira MCP
2. Set priority, assignee (if specified), and sprint membership as directed
3. Report the ticket ID and URL to the user

---

## Input Format

Provide any of the following:

**Option A — Slack thread:**
Paste the full thread directly. Include timestamps and participant names if available.

**Option B — Conversation notes:**
Paste bullet-point notes from a Zoom call or verbal conversation.

**Option C — Summary:**
Describe the request in 3-5 sentences if you don't have the original thread.

You may also add context that was not in the original conversation:
```
Additional context: [anything the stakeholder didn't say but you know — their actual deadline, the downstream report affected, the OKR this maps to]
```

---

## Quality Standards

A ticket is ready when:
- A developer could pick it up without reading the original conversation
- The business impact is stated in plain language that a non-technical stakeholder would recognize as accurate
- Acceptance criteria are specific enough to be verified
- Open questions are clearly flagged rather than hidden in vague language
- The scope is bounded — it's clear what is and is not included

---

## Error Handling

| Situation | Action |
|-----------|--------|
| Multiple unrelated asks in one thread | Create one ticket per distinct deliverable. Notify the user. |
| No timeline stated | Flag as Open Question. Default to Medium priority unless urgency signals suggest otherwise. |
| Technical scope unclear | State what you can infer and mark the rest as `[TBD — scope clarification needed]` |
| Jira MCP unavailable | Deliver the formatted ticket as a document for manual entry |
| Request appears to be a data question, not a ticket | Flag it: "This looks like an ad hoc data question rather than a development request. Do you want me to answer it directly or structure it as a ticket anyway?" |
