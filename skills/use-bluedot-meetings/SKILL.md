---
name: use-bluedot-meetings
description: Uses Bluedot MCP tools to find, read, and cite meetings without dumping full transcripts. Use when the user mentions Bluedot, meetings, recordings, transcripts, weeklies, or asks about what was discussed on a call.
---

# Use Bluedot meetings

Shared mechanics for every Bluedot workflow. Follow this skill before `last-week-digest`, `meeting-to-plan`, or `meeting-to-tasks`.

## Auth

If Bluedot tools are missing, fail with auth, or the namespace needs login, call `mcp_auth` once, then retry. Do not invent meeting data.

## Tool routing

| Need | Tool |
| --- | --- |
| Who is signed in | `get_current_user` |
| Recency or date range | `list_meetings` |
| Topic / feature / keyword | `search_meetings` |
| Full meeting | `get_meeting` **only after you have an id** |
| Workspace / collection context | `list_workspaces`, `get_workspace`, `list_collections` |

`search_meetings` is the tool name. Never call `search_meeting`.

`list_meetings` `pageSize` max is 16. Page with `pageNumber` if needed.

Date filters on `list_meetings` are `uploadedFrom` and `uploadedTo` (ISO-8601 datetimes). Participant filter is `hasParticipantEmail`.

## Resolving a meeting

| User gave | Do |
| --- | --- |
| Video id (UUID) or a URL that contains one | `get_meeting` with that id |
| Title / topic / feature words | `search_meetings` (`searchBy`: title + transcription) |
| Nothing, and the workflow allows a default | `list_meetings` newest first, current user as participant, skip junk |

Skip obvious non-meetings unless the user asks for everything: duration under ~1 minute, titles like `test`, `media-…`, UUID-only names.

## Transcripts

`get_meeting` can return a huge transcript. Do **not** paste it into the reply or keep it all in working notes.

Read, in order:

1. `summary` — Overview, Topics, Action Items
2. Only then, speaker-filtered utterances if the summary is not enough

To isolate the current user: match `get_current_user` `email` / `name` to `meetingParticipants` and transcription `speakerTag`.

## Links

Always use the canonical `url` from the tool payload. Never construct `app.bluedothq.com/preview/...` from an id.
