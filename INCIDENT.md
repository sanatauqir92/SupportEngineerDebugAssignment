# Incident Summary

**Title:** Duplicate Tasks on Refresh
**Date:** 2026-05-13
**Severity:** P2 — poor user experience but not critical

## Impact

- **Who/what was impacted:**
  Customers
- **Symptoms observed by customers/internal users:**
  Tasks appearing multiple times in the list after refreshing. The root cause is that tasks were being appended to the existing list rather than replacing it.

## Detection

- **How did we learn about this?**
  Via customer report: "After I refresh, some tasks show up twice. Also the order looks random sometimes."

## Timeline (UTC)

- HH:MM — ...
- HH:MM — ...

## Root cause

- **What happened and why:**
  The `refresh()` function was concatenating new results onto `state.tasks` instead of replacing it with fresh data, causing duplicate entries to accumulate with each refresh.

## Mitigation / resolution

- **What did we change?**
  Updated `main.js` to replace `state.tasks = state.tasks.concat(items)` with `state.tasks = items`, so each refresh overwrites the previous list rather than appending to it.

## Verification

- **How did we verify the fix worked?**
  Refreshed the application, added a task, and clicked refresh multiple times. No duplicate tasks were appended.

## Follow-ups / action items

- [ ] Test the change in dev, push to production
