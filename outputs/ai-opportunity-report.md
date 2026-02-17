# AI Opportunity Report

**Prepared for:** Jennifer Joseph, BI Data Engineer
**Date:** 2026-02-17

---

## Context

Jennifer is a BI Data Engineer (L1) who builds and maintains data pipelines supporting financial engineering and analytics engineering initiatives. Her stack includes Airflow, Fivetran, Snowflake, dbt, and an emerging agentic analytics layer (KBI) using Claude Code agents. She is temporarily covering stakeholder-facing responsibilities (scoping, prioritization) while her manager is on a 6-month leave. She also handles PR reviews, hosts team showcase meetings, and tracks internal AI usage across enterprise tooling.

---

## Summary Table

| # | Opportunity | Category | Impact |
|---|------------|----------|--------|
| 1 | Stakeholder Request Intake & Ticket Structuring | Deterministic Workflow | High |
| 2 | Data Quality Investigation Assistant | Collaborative AI | High |
| 3 | Meeting Agenda Generator | Deterministic Workflow | Medium |
| 4 | dbt Test Case Generator from Edge Cases | Collaborative AI | Medium |
| 5 | Team Showcase Prep Generator | Deterministic Workflow | Medium |
| 6 | Jira Sprint Retro (Daily/Weekly) | Collaborative AI | High |
| 7 | 1:1 Meeting Logs | Autonomous Agent | High |

---

## Deterministic Workflows

---

**[3] Meeting Agenda Generator**

**Category:** Deterministic Workflow

**Why it's a good candidate:**
This is a recurring task with clear inputs (meeting type, attendees, recent context) and a structured output (agenda document). Jennifer does this for multiple meeting types (1:1s, team meetings) but has no standardized template, meaning she reinvents the format each time. The task is highly pattern-based — agendas follow predictable structures per meeting type.

**Current pain point:**
Jennifer creates agendas ad hoc in different formats each time. The work is valuable (she finds well-prepared agendas incredibly helpful) but the lack of standardization means unnecessary time spent on structure rather than substance. This adds up across the multiple meetings she owns weekly.

**How AI helps:**
A deterministic workflow takes the meeting type (1:1, team sync, etc.), attendees, and optionally recent context (open action items, project updates) as inputs and produces a consistently formatted agenda. Templates are defined once per meeting type, and AI fills in the relevant details. The output is a ready-to-share agenda document.

**Getting started:**
Pick your most frequent meeting type (e.g., weekly 1:1). Write down the sections that agenda always needs. Build a prompt that takes the meeting type and a few bullet points of context and outputs a formatted agenda.

---

**[5] Team Showcase Prep Generator**

**Category:** Deterministic Workflow

**Why it's a good candidate:**
Showcase meetings follow a repeatable pattern — current project status, what was accomplished, what's next, any demos. The content changes but the structure doesn't. Jennifer currently spends more time than necessary because she has no consistent template to populate.

**Current pain point:**
Each showcase requires Jennifer to figure out the format from scratch. It's not that the prep is long — it's that it's longer than it should be because the structure isn't standardized. The cognitive overhead of "how should I present this" takes away from "what should I present."

**How AI helps:**
A workflow takes a few bullet points about current project status (or pulls from a sprint board / recent commits / dbt project state) and produces a consistent showcase document with sections like: highlights since last showcase, current work in progress, upcoming priorities, and discussion topics. Same format every time, minimal input required.

**Getting started:**
After your next showcase meeting, capture the format that worked well. Turn it into a template prompt that takes 3-5 bullet points of project status and outputs the full showcase doc.

---

## Collaborative AI

---

**[2] Data Quality Investigation Assistant**

**Category:** Collaborative AI

**Why it's a good candidate:**
Data quality debugging is investigative work — it requires tracing lineage through dbt models, querying Snowflake, and understanding business logic. The process is too variable for full automation, but AI can dramatically accelerate the investigation by helping trace lineage, generate diagnostic queries, and suggest root causes based on patterns.

**Current pain point:**
When a stakeholder reports a data issue (e.g., an account looks off), the investigation is ad hoc with no predictable timeline. Jennifer manually traces through dbt models and Snowflake to find where the data went wrong — bad source data or a mis-modeled dataset. This is skilled detective work, but much of the early investigation (lineage tracing, writing diagnostic queries) is mechanical.

**How AI helps:**
When a data issue is reported, AI acts as an investigation partner. Given the affected model/table and example accounts, it can: (1) trace the dbt lineage graph to identify upstream dependencies, (2) generate diagnostic SQL queries to isolate where the data diverges from expectations, (3) suggest likely root causes (schema changes, null handling, join fanout, etc.), and (4) draft the fix or flag the source data issue. Jennifer drives the investigation; AI accelerates the mechanical parts.

**Getting started:**
Next time a data quality issue comes in, document your investigation steps as you go. Capture the queries you write and the lineage you trace. Use that as the basis for a prompt that takes a reported issue and generates the first round of diagnostic queries.

---

**[4] dbt Test Case Generator from Edge Cases**

**Category:** Collaborative AI

**Why it's a good candidate:**
Jennifer is actively trying to move from manual, one-off testing to scalable test logic in dbt test files. Writing dbt tests (schema tests, data tests, custom generic tests) from a discovered edge case is a well-defined translation task — but it requires understanding both the business logic and dbt's testing framework.

**Current pain point:**
When an edge case is discovered (often from a stakeholder report), the fix is applied but the corresponding test is either manual or takes extra effort to encode as reusable dbt test logic. This slows down the effort to build a robust, scalable testing layer.

**How AI helps:**
Given a description of the edge case (e.g., "accounts with null values in field X are being double-counted in the join") and the relevant dbt model, AI generates the appropriate dbt test — whether it's a schema.yml test, a singular data test, or a custom generic test. Jennifer reviews and refines. Over time this builds a comprehensive test suite much faster than manual authoring.

**Getting started:**
Take the last edge case you fixed and write a prompt that describes the issue and the model involved. Ask AI to generate the dbt test. Compare its output to what you would have written and refine the prompt.

---

**[6] Jira Sprint Retro (Daily/Weekly)**

**Category:** Collaborative AI

**Why it's a good candidate:**
This workflow combines two data sources (Google Calendar time blocks and Jira tickets) that should stay in sync but don't. It requires human judgment to interpret what time blocks map to which tickets, reflect on progress, and identify blockers — making it ideal for a collaborative session rather than full automation. The 4-hour-to-1-story-point conversion provides a clear, rule-based foundation.

**Current pain point:**
Jennifer time-blocks her days in Google Calendar with rough descriptors (e.g., "v2 testing") and separately maintains a Jira board with sprint tasks. These two systems drift apart — tickets don't reflect actual time spent, story points don't match effort invested, and there's no easy way to see a holistic picture of where time went across the sprint. Manually reconciling the two is tedious, and without it, she can't articulate the value she's creating from time spent.

**How AI helps:**
An interactive collaborative session where AI: (1) ingests Google Calendar time blocks for the day/week, (2) maps them to corresponding Jira tickets using descriptions and context, (3) converts time spent to story points (4 hours = 1 point) while respecting existing story totals (additive time tracking, not recalculation of the story estimate), (4) surfaces discrepancies (unplanned work, tickets with no calendar time, calendar blocks with no ticket), and (5) facilitates a retro-style conversation — what moved well, what's blocked, what needs reprioritization. By the end, Jira is accurate and Jennifer has a narrative of sprint value delivered.

**Getting started:**
Export one day's Google Calendar blocks and list your active Jira tickets. Write a prompt that maps the calendar entries to tickets, calculates time-to-story-point conversion, and asks you reflective questions about the day's work. Test it against a real day.

---

## Autonomous Agents

---

**[1] Stakeholder Request Intake & Ticket Structuring**

**Category:** Deterministic Workflow

**Why it's a good candidate:**
The inputs are unstructured (Slack/Zoom conversations) but the output is highly structured (a well-formed ticket with required fields). The translation from conversation to ticket follows repeatable rules — extract the ask, identify timeline, assign priority, map to OKR. Once the rules are defined, this can run deterministically with minimal judgment required.

**Current pain point:**
Stakeholders discuss requirements over Slack or Zoom, then submit a ticket that lacks the details from that conversation — missing timeline, priority, and key context. Jennifer has to manually reconstruct the conversation, update the ticket, potentially break it into sub-tickets, map it to an OKR, and slot it into the sprint. This is a significant time sink that scales with the number of incoming requests, and it's especially burdensome given she's covering this responsibility temporarily.

**How AI helps:**
A deterministic workflow takes a Slack thread or conversation summary as input and produces a structured ticket with all required fields: title, description, priority, timeline, OKR mapping, and acceptance criteria. It follows a defined template and extraction rules — same input format, same output format, every time. Jennifer reviews the output before submission.

**Getting started:**
Copy your last 2-3 Slack threads where a stakeholder made a request. Write a prompt that takes a Slack thread as input and outputs a structured ticket with all the fields your backlog requires (title, description, priority, timeline, OKR, acceptance criteria). Test it against those real examples.

---

**[7] 1:1 Meeting Logs**

**Category:** Autonomous Agent

**Why it's a good candidate:**
This is a multi-step, multi-destination workflow that requires reasoning about sensitive content. An agent must: transcribe, identify sensitive vs. shareable content, produce two different outputs to two different destinations, and make judgment calls about what qualifies as an important quote, action item, or sensitive remark. The dual-output (private Obsidian + public Google Drive) and the security-conscious scrubbing make this a natural fit for an autonomous agent that can plan and execute across steps.

**Current pain point:**
Jennifer's 1:1s and coffee chats are tracked inconsistently — sometimes in Google Docs, sometimes via Granola transcription, sometimes on paper, sometimes not at all. There's no single system of record, no reliable paper trail of important things said, and no separation between sensitive private notes and shareable professional logs. With AI tools in the mix, storing sensitive notes in cloud services raises additional privacy concerns.

**How AI helps:**
An autonomous agent handles the full pipeline after a 1:1: (1) takes raw meeting notes or a transcript as input, (2) produces a detailed private record in Obsidian (local, not cloud) that preserves important quotes, sensitive context, and personal observations — the "defense" layer, (3) scrubs sensitive content and generates a clean, professional 1:1 log for Google Drive with objective callouts, questions raised, action items, and decisions made — the "public" layer, and (4) handles the file creation/routing to both destinations. The agent reasons about what is sensitive vs. shareable, so Jennifer doesn't have to manually curate two versions.

**Getting started:**
After your next 1:1, capture raw notes (even messy ones). Write a prompt that takes those raw notes and produces two outputs: (a) a detailed private log with verbatim quotes and sensitive context, and (b) a scrubbed, professional summary with action items and key decisions. Compare the two to make sure the scrubbing logic works correctly.

---

## Workflow Candidate Summary

- **Workflow:** Stakeholder Request Intake Form
- **Type:** Deterministic
- **Pain point:** Stakeholder conversations over Slack/Zoom produce incomplete tickets missing timeline, priority, and key context, requiring manual reconstruction and structuring.
- **AI opportunity:** A deterministic workflow extracts requirements from conversation threads and produces fully structured tickets with all required fields, ready for review.
- **Why it matters:** Jennifer is temporarily covering her manager's stakeholder-facing responsibilities, making intake volume higher than usual — automating the ticket structuring step directly reclaims time she can spend on her core pipeline and analytics engineering work.

- **Workflow:** Jira Sprint Retro (Daily/Weekly)
- **Type:** Collaborative AI
- **Pain point:** Google Calendar time blocks and Jira sprint tickets drift out of sync, making it tedious to reconcile actual effort with planned work and articulate sprint value.
- **AI opportunity:** An interactive AI session maps calendar time to Jira tickets, converts hours to story points, surfaces discrepancies, and facilitates a reflective retro to keep the board accurate.
- **Why it matters:** As an L1 covering expanded responsibilities, Jennifer needs a reliable way to demonstrate the value she's delivering across the sprint — this workflow gives her both an accurate board and a narrative to communicate impact to leadership.

- **Workflow:** 1:1 Meeting Logs
- **Type:** Autonomous Agent
- **Pain point:** 1:1 notes are captured inconsistently across tools with no separation between sensitive private records and shareable professional logs, leaving no reliable paper trail.
- **AI opportunity:** An autonomous agent produces two outputs from raw meeting notes — a detailed private record in local Obsidian (preserving sensitive quotes) and a scrubbed, professional log in Google Drive with action items and decisions.
- **Why it matters:** With her manager out for 6 months and increased stakeholder exposure, maintaining a reliable, secure paper trail of conversations protects Jennifer professionally and ensures nothing falls through the cracks.
