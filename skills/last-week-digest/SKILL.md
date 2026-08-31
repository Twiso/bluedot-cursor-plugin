---
name: last-week-digest
description: Summarizes meetings recorded with Bluedot from the last 7 days where the current user was a participant. Use when the user asks what was discussed last week, wants a weekly meeting digest, or runs /bluedot-last-week.
---

# Last-week digest

Summarize what was discussed in the last 7 days in meetings **recorded with Bluedot** where the current user is a participant. Do not require a "Weekly alignment" title. Participant filter is the source of truth.

Follow `use-bluedot-meetings` for auth, tool routing, transcripts, and links.

## Workflow

1. `get_current_user` → email.
2. `list_meetings` with:
   - `hasParticipantEmail`: that email
   - `uploadedFrom` / `uploadedTo`: last 7 days (ISO-8601, inclusive of now)
   - `sortBy`: `uploadedAt`
   - `order`: `desc`
   - `pageSize`: 16 (page with `pageNumber` until the page is empty or dates fall outside the window)
3. Skip junk recordings per `use-bluedot-meetings` unless the user asks for everything.
4. For remaining meetings, `get_meeting` and read Overview / Topics / Action Items only. Do not load full transcripts unless a meeting has no summary.
5. Reply as a digest grouped by calendar day (newest first):

```markdown
## [Weekday, Mon DD]

### [Meeting title](canonical-url)
2–4 sentences on what was discussed.

**Your action items:** …
```

If there are no matching meetings, say so and stop. Do not pad with older calls.
