# Ticket Description Template

Every Jira ticket touched during a Daily Sprint Sync should use this structure in the **description field**.

## Template

```
## Why This Matters
[Business impact — plain language, 1-3 sentences. Answer: what value does this work deliver? Why does it matter to the business, stakeholders, or the team's goals?]

---

[Technical details — approach, implementation notes, decisions made, tools used]
```

## Rules

1. **Lead with business impact.** The "Why This Matters" section comes first, always. A stakeholder or manager reading this ticket should immediately understand the value without reading the technical details.

2. **Keep "Why This Matters" to 1-3 sentences.** Specific and concrete beats vague and general.
   - Good: "This model fix corrects account-level revenue attribution for Q1 reporting, resolving a discrepancy flagged by Finance."
   - Weak: "This is important for data quality."

3. **OKR and epic context belong in linked/parent tickets** — do not repeat them in the description. The description is for the work itself.

4. **Technical details are free-form** — length and format vary by ticket. Include what's useful: approach taken, key decisions, edge cases handled, SQL or dbt logic notes, PRs referenced.

5. **Note AI tool usage** in the technical details when Claude, Claude Code, or other AI tools contributed meaningfully to the work. Example: "Used Claude Code to generate dbt generic test from edge case description."

6. **If updating an existing description:**
   - If a "Why This Matters" section already exists: update or extend it, don't replace it wholesale unless the work direction changed significantly.
   - If no "Why This Matters" section exists: add the full template structure.
   - If context is insufficient to write a meaningful "Why This Matters": leave a placeholder (`[TODO: add business context]`) and flag for follow-up. Never skip the section entirely.

## Example

```
## Why This Matters
This pipeline fix ensures the financial engineering team receives accurate daily cost attribution data, unblocking their Q1 budget variance analysis that was stalled due to null values in the source feed.

---

Root cause: upstream source table `raw.vendor_costs` began sending nulls for `cost_center_id` after a schema change on Feb 14. Fixed by adding a COALESCE fallback in `stg_vendor_costs.sql` and adding a not-null dbt test to catch future regressions.

AI usage: Used Claude Code to trace dbt lineage and generate the singular data test from the edge case description.

PR: [link]
```
