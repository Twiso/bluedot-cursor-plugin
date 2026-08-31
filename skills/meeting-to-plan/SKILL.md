---
name: meeting-to-plan
description: Turns a feature discussed in a Bluedot meeting into an implementation plan. Use when the user wants to plan a feature from a call, or asks what was decided about a feature.
---

# Meeting to plan

Turn a feature that was discussed in a meeting into an implementation plan — not a recap of the whole call.

Follow `use-bluedot-meetings` for auth, tool routing, transcripts, and links.

## Workflow

1. Feature name or meeting hint is the user's argument. If it is empty, ask for the feature name and wait.
2. `search_meetings` with that query, `searchBy`: `["title", "transcription"]`, `sortBy`: `date_created`, `order`: `desc`.
3. Pick the best meeting. If several are plausible, list them with canonical `url`s and confirm before planning.
4. `get_meeting`. Use Topics, Action Items, and only the utterances that are about this feature (current user and counterparts). Skip unrelated agenda.
5. Reply with a plan, not minutes:

```markdown
# Implementation plan: [feature]

Source: [meeting title](canonical-url)

## Objective
One paragraph: what we are building and why, from the discussion.

## Assumptions
- Explicit guesses. Ask the user to correct them.

## Tasks
1. **[title]** — what to do
   - Acceptance: testable condition
2. …

## Likely touchpoints
Files, services, or systems in the open workspace if they match the discussion. Omit this section if the workspace is unrelated.

## Open questions
- Unresolved decisions from the meeting
```

Do not implement code unless the user asks. The deliverable is the plan.
