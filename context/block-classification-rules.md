# Block Classification Rules

Use these rules to classify Google Calendar time blocks during a Daily Sprint Sync session.

## Classification Table

| Block Type | Examples | Needs Jira Ticket? |
|------------|----------|-------------------|
| **Focus / Task Work** | "v2 testing", "pipeline build", "dbt model refactor", "KBI agent work" | Yes |
| **Meeting** | "1:1 with X", "team sync", "sprint retro", "stakeholder call", "stand-up" | No |
| **Personal** | "lunch", "focus time (personal)", "blocked", "OOO", "commute" | No |
| **Ad Hoc** | "PR review", "reading", "research", "Slack catch-up", "documentation review" | Flag for judgment |

## Rules

1. **When in doubt, classify as Focus/Task Work** and ask the user to confirm. It's better to create a ticket and remove it than to miss work that should be tracked.

2. **Ad hoc blocks are judgment calls.** Flag them and ask the user:
   - "Was this work tied to a specific project or deliverable?"
   - If yes → treat as Focus/Task Work, create a ticket
   - If no → skip

3. **Ambiguous titles** (e.g., "v2 testing" — which v2?) — make your best inference based on the sprint board context and note your reasoning. Ask the user to confirm before proceeding.

4. **All-day events** are typically Google Tasks surfaced as calendar items (e.g., "top 3 tasks for today"). Do not create Jira tickets for these — close them out in Google Tasks if completed.

5. **Green blocks are already processed** — skip them entirely. Never re-process a green block.

## Color Convention

| Color | Meaning | Action |
|-------|---------|--------|
| Red | Unprocessed — not yet reconciled into Jira | Process during this sync |
| Green | Processed — already reconciled into Jira | Skip — do not re-process |
