---
name: "Jira Branch and Ticket Linker"
description: "Creates a correctly formatted git branch for a Jira ticket and updates the ticket in Jira — branch name follows project conventions, ticket gets status update and branch link. Eliminates the need to repeat branch naming rules and Jira update steps in every conversation."
when_to_use: "Use when starting work on a Jira ticket and you need to: (1) create a correctly named git branch, (2) update the Jira ticket status to In Progress, and (3) link the branch to the ticket. Say 'create branch for TICKET-ID' or 'set up branch for this ticket'."
version: "1.0"
author: "Jennifer Joseph"
---

# Jira Branch and Ticket Linker

You are setting up a git branch for a Jira ticket and linking the two together. Execute all steps in order. Do not run git commands without confirmation.

---

## Branch Naming Convention

Format: `type/TICKET-ID-short-description`

| Ticket Type | Prefix | Example |
|-------------|--------|---------|
| Feature / new work | `feat/` | `feat/KBI-42-add-vendor-cost-model` |
| Bug fix | `fix/` | `fix/KBI-88-null-cost-center-id` |
| Data quality / investigation | `fix/` | `fix/KBI-101-revenue-attribution-null` |
| Chore / refactor / maintenance | `chore/` | `chore/KBI-55-cleanup-stg-models` |
| Documentation | `docs/` | `docs/KBI-60-pipeline-runbook` |

**Rules:**
- Ticket ID in ALL CAPS exactly as it appears in Jira (e.g., `KBI-42`, `ANLT-7`)
- Short description in `kebab-case`, 3-6 words, derived from ticket title
- No special characters, no spaces
- Branch off `main` unless told otherwise

---

## Steps

### Step 1 — Identify the Ticket

Ask for or confirm:
- Jira ticket ID (e.g., `KBI-42`)
- Ticket title (used to generate the branch description slug)
- Ticket type (feature, bug, chore, docs) — infer from title if obvious, confirm if ambiguous

### Step 2 — Generate the Branch Name

Construct the branch name following the convention above. Show it to the user:

```
Proposed branch name: feat/KBI-42-add-vendor-cost-model

git checkout -b feat/KBI-42-add-vendor-cost-model
```

Ask: "Does this look right? I'll run it once you confirm."

### Step 3 — Run the Git Command

After confirmation, output the exact command to run. If Bash access is available, run it directly and confirm success.

```bash
git checkout -b <branch-name>
```

### Step 4 — Update Jira Ticket

Via Jira MCP, update the ticket:
1. Set **Status** → `In Progress`
2. Add the branch name to the **Development** or **Branch** field if available
3. Confirm the update completed

If Jira MCP is unavailable, output the update steps for manual execution.

### Step 5 — Confirm and Summarize

Output a summary:

```
✅ Branch created: feat/KBI-42-add-vendor-cost-model
✅ Jira KBI-42 status: In Progress
✅ Branch linked to ticket (if MCP available)

You're set up. Start working on the ticket.
```

---

## Input Format

Minimal: just the ticket ID or title.

```
KBI-42
```
or
```
KBI-42 — Add vendor cost model to stg layer
```
or
```
The ticket about fixing null cost_center_id in the vendor costs feed
```

Claude will infer or ask for anything missing.

---

## Error Handling

| Situation | Action |
|-----------|--------|
| Ticket ID not provided | Ask for it before proceeding |
| Branch already exists | Report it, ask if they want to check it out instead |
| Jira MCP unavailable | Provide the branch command and list the Jira steps for manual update |
| Ambiguous ticket type | Default to `feat/` and confirm with user |
