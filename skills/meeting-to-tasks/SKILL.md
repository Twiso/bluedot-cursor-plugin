---
name: meeting-to-tasks
description: Extracts tasks from a Bluedot recording and offers to create them in a connected ticket system (Linear, Jira, Trello, GitHub, Asana, and similar). Use when the user wants action items from a call, asks to create tickets from a meeting, or runs /bluedot-tasks.
---

# Meeting to tasks

Turn a recording into a **proposed ticket list**, then sync only the tasks the user confirms into whatever tracker is already connected in Cursor.

This plugin does **not** ship Linear, Jira, Trello, or GitHub. It uses trackers the user already has (other Cursor plugins / MCP servers).

Follow `use-bluedot-meetings` for auth, tool routing, transcripts, links, and junk-recording filters. For tracker detection and create-tool mapping, see [ticket-systems.md](ticket-systems.md).

## Workflow

1. **Resolve the meeting**
   - Argument / `$input` is a meeting name, URL, or video id. If empty, take the latest non-junk meeting from `list_meetings` where the current user is a participant (`get_current_user` → `hasParticipantEmail`).
   - State the chosen title and canonical `url`. If several meetings are plausible, list them and wait.
2. **`get_meeting`**. Read Overview, Topics, and Action Items first. Use speaker-filtered utterances only to turn vague action items into concrete tickets.
3. **Draft tickets** (do not create yet). Split:
   - **Proposed** — concrete work someone committed to
   - **Skipped** — already done, decisions with no work, or too vague to ticket
4. **Detect trackers** (see [ticket-systems.md](ticket-systems.md)). Do this in parallel with meeting fetch when possible.
5. **Reply with the proposal** (template below) and **stop**.
6. **Create only after confirmation** of which tasks. Then:
   - **One** tracker connected → create there (still ask team/board/project if the tool requires it and there is more than one).
   - **Several** → ask which tracker, then create.
   - **None** → do not pretend a tracker exists. Leave the list as copyable markdown and say they can install Linear, Jira, Trello, or GitHub in Cursor Plugins.
7. After create, list each new ticket URL. On failure, keep the proposed task and report the error; do not retry in a loop.

If the user already said which tasks and where (e.g. "create 1–3 in Linear"), skip the wait.

Never create tickets the user did not confirm. Never invent meeting or tracker data.

## What counts as a task

Include:

- Explicit Action Items from the summary
- Clear commitments ("I'll ship X", "let's add Y to the sprint")
- Follow-ups with an owner or a deadline

Skip:

- FYI / decisions with no follow-up work
- Work already completed on the call
- Vague notes ("stay aligned", "keep an eye on it")
- Personal reminders that are not team work — mention them under Skipped

Title: imperative, ≤80 characters. Owner: meeting participant name if stated; only assign in the tracker when the name/email matches a tracker user. Suggested due date only if the recording named one.

Dedup: before creating, search the tracker for a similar open item by title. If one exists, show it as **already exists** with a link instead of creating a duplicate.

## Proposal template

```markdown
# Tasks from [meeting title](canonical-url)

Tracker: **Linear** (only one connected) — I'll create the ones you pick there.
<!-- or: Tracker: Linear, Jira — say which to use. -->
<!-- or: No ticket plugin connected. Install Linear, Jira, Trello, or GitHub, or copy the list below. -->

## Proposed

1. **[title]** — one-line why
   - Owner: [name or Unassigned]
   - Due: [date or —]
2. …

## Skipped
- [item] — [reason]

Reply with `all`, numbers (`1,3`), or `none`.
```

Cap is not a reason to drop items. If there are more than ~12 proposed tasks, still list them all and suggest starting with the ones that have owners or dates.
