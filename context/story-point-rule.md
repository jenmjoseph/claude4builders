# Story Point Conversion Rule

## The Rule

**1 hour of work = 0.25 story points**

| Hours Worked | Story Points to Add |
|-------------|-------------------|
| 0.25 hr (15 min) | 0.0625 |
| 0.5 hr (30 min) | 0.125 |
| 1 hr | 0.25 |
| 2 hrs | 0.5 |
| 4 hrs | 1.0 |
| 8 hrs | 2.0 |

## Critical: Always Additive

**Never recalculate story points from scratch.**

- A ticket with 1.0 story points already logged means 4 hours have already been spent on it.
- If you log 2 more hours today, add 0.5 — the new total is 1.5, not 0.5.
- Existing story points represent real work already done. Do not reduce or overwrite them.

## How to Apply

When reconciling a calendar block to a ticket:

1. Read the duration of the calendar block (in hours)
2. Multiply by 0.25 to get the points to add
3. State the calculation explicitly before updating: `"[X hrs] × 0.25 = [Y pts] — adding to existing [Z pts] for new total of [Z+Y pts]"`
4. Update the ticket's story point field with the new total

## Edge Cases

- **Split blocks** (same ticket worked across multiple blocks in one day): sum the durations first, then convert. Example: two 1-hour blocks on the same ticket = 2 hrs total = 0.5 pts to add.
- **Partial hours**: round to the nearest 15 minutes for simplicity.
- **Already-green blocks**: skip entirely. They have already been converted and added.
