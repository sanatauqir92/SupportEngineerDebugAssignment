# Follow-up Ticket

**Title:** Return descriptive 400 error when task title is numbers-only
**Priority:** P3
**Owner:** Sana Tauqir

## Description

Submitting a task title that contains only numbers (e.g. `"1234"`) results in an unhandled 500 server error rather than a meaningful validation response. The API should reject this input with a 400 and a clear message, and the UI should display that message to the user instead of a generic failure.

## Acceptance criteria

- [ ] `POST /api/tasks` returns `400 Bad Request` when `title` is numbers-only, with a descriptive `message` in the response body
- [ ] The UI surfaces the API error message rather than a generic "Add failed" string
- [ ] Existing valid inputs (alphanumeric titles, titles with special characters) are unaffected
- [ ] A test covers the numbers-only rejection case

## Notes / context

- Validation should live server-side in `TaskEndpoints.cs` alongside the existing `userId`/`title` empty-string check
- The UI error display is handled in `main.js` `addTask()` — it already reads the response body on failure, so the message just needs to be passed through correctly
- Screenshot of the 500 in context: `onlynumbers.png` (see `Debugging.md`)
