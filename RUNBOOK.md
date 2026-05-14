# Runbook — SupportEngineerChallenge

> Update this file as part of the exercise.

## Service overview
- **Service:** SupportEngineerChallenge.Api
- **Purpose:** Minimal task tracker (create + list tasks)
- **Data store:** SQLite (`app.db` in the API working directory)

## Common commands

**Run locally**
```bash
cd src/SupportEngineerChallenge.Api
dotnet run
```

**Run tests**
```bash
dotnet test
```

## Key endpoints
- `GET /api/tasks?userId={id}&limit={n}`
- `POST /api/tasks`

## Using log artifacts

- **Create-task 500:** Inspect `artifacts/sample_api_log.txt` (or production logs). Look for the `CreateTask request` line — `X-Client-Timestamp present=False` or `length=0` indicates missing/invalid header. The stack trace shows `FormatException` at `DateTime.Parse`.
- **Slow list:** Look for `ListTasks completed` lines with high `elapsedMs` (e.g. `artifacts/sample_slow_list_log.txt`). Correlate `userId` and `limit` with slow requests.

## Troubleshooting checklist (starter)

### “Create task fails with 500”
- Check API logs for `X-Client-Timestamp present=False` or `length=0`.
- **Root cause:** `DateTime.Parse` on an empty or missing header throws `FormatException`.
- **Fix:** `TaskEndpoints.cs` — use `DateTime.TryParse` with `RoundtripKind` style; fall back to `DateTime.UtcNow` when the header is absent or invalid.

### “Tasks list is slow”
- Look for `ListTasks completed` log lines with high `elapsedMs`.
- **Root cause:** `ToListAsync()` was called before `Where`, loading every row into memory before filtering.
- **Fix:** `TaskEndpoints.cs` — push `Where`, `OrderByDescending`, and `Take` into the EF Core query so only matching rows are fetched from the database.

### “Duplicates / wrong order after refresh”
- Replicate by clicking Refresh several times in a row; task count will grow with each press.
- **Root cause:** `main.js` used `state.tasks.concat(items)`, appending new results to the existing list instead of replacing it.
- **Fix:** `main.js` — replace `concat` with direct assignment: `state.tasks = items`.

## Verification steps (starter)
- Create tasks from UI and via Swagger.
- Refresh tasks repeatedly; confirm no duplicates and ordering is correct.
- Validate list endpoint returns only requested user's tasks.

## Rollback / mitigation ideas (starter)
- Roll back to last known good version.
- Temporarily disable problematic client behavior (feature flag / UI change).
- Add guardrails (e.g. input validation, error handling) to prevent unhandled exceptions.
