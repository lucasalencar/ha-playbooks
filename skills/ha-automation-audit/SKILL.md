---
name: ha-automation-audit
description: Audit and simplify Home Assistant automations. Use when the user asks to review automations, reduce automation count, merge duplicates, remove dead automations, or check best practices.
---

# HA Automation Audit

Audit the current automations and propose simplifications. Open-ended by design: the output is a prioritized suggestion list, not an auto-rewrite.

## Steps

1. **Inventory.** List every automation with alias, area, triggers, and enabled state. Every automation gets a disposition, no exceptions.
2. **Health check.** For each, check trace recency, trigger reachability (entities still exist?), and error traces. Mark as `keep`, `merge-candidate`, or `dead-candidate`.
3. **Cluster.** Group merge-candidates by same area, same trigger domain, or overlapping conditions. A cluster is one future automation.
4. **Propose.** For each cluster and dead-candidate, output:
   - what to do: `merge` / `remove` / `simplify`
   - why: which rule from `references/good-practices.md` it violates
   - risk: `low` / `medium` / `high` and what breaks if wrong
   - sketch of the resulting automation (`trigger_id` + `choose` when merging)

   Order by impact first, risk second. Do not create, edit, disable, or delete anything. Stop at the proposal and wait for explicit user approval, even if the fix looks obvious.

Completion: every automation inventoried in step 1 appears in step 4 with a disposition.

## Rules

- One automation per intent. Same trigger set in N automations is a merge signal, not a coincidence.
- Prefer `trigger_id` + `choose` over N near-identical automations.
- Prefer helpers (group, schedule, input_boolean) over duplicated conditions.
- Flag `time_pattern` polling, missing `mode`, and missing `alias`/`description` as fragility. See [good-practices.md](../../docs/good-practices.md).
