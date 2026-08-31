# Ticket systems

How to find a connected tracker and create issues. Inspect the live tool schema before every create call — names and arguments differ by plugin version.

Bluedot (`search_meetings`, `get_meeting`, …) is **not** a ticket system.

## Detect

1. Catalog MCP namespaces and/or available tool names.
2. A tracker is **connected** when its create tool is present and the namespace is not in `needsAuth` / `error` / `loading`. If it needs auth, call that namespace's `mcp_auth` once, then retry.
3. Ignore namespaces that only search docs or have no create-item tool.

Match on namespace **or** tool names (case-insensitive):

| Tracker | Detect | Typical create path |
| --- | --- | --- |
| Linear | `linear`, `save_issue` | `list_teams` → `save_issue` (no `id`) |
| Jira | `jira`, `atlassian` | create issue; needs `project` / project key |
| Trello | `trello` | create card; needs board + list |
| GitHub Issues | `github` (issues, not only PRs/code) | `create_issue` or `gh issue create` |
| Asana | `asana` | create task; needs project |
| ClickUp | `clickup` | create task; needs list/space |
| Monday | `monday` | create item; needs board |
| Notion | `notion` **and** a tasks/issues database the user names | create page/item in that DB |

If a namespace looks like a tracker but is not in this table, still use it: find the create-item tool, read its schema, then follow **Create rules**.

## Create rules

- Read the create tool's schema first. Do not guess required fields.
- Attach the Bluedot meeting: use a native `links` / URL field when the tool has one; otherwise put `Source: [title](canonical-url)` at the top of the description.
- Description is markdown. Do not escape newlines.
- Assign only on a confident name/email match. Otherwise leave unassigned and keep Owner on the proposal.
- Required container (team, project, board, list, repo): if exactly one exists, use it and say so. If several, ask before creating.
- Search for duplicates with the tracker's search/list tool and a short title query before each create.

## Linear (reference)

When Linear is connected:

1. `list_teams`. One team → use it. Several → ask, unless the user already named a team.
2. Optional: `list_issues` with `query` set to a distinctive title fragment; skip create if a strong open match exists.
3. `save_issue` **without** `id`:
   - `title`, `team` (required)
   - `description` — context from the call
   - `assignee` — user id, name, email, or `"me"` (not `assigneeId`)
   - `dueDate` — ISO date when the recording named one
   - `links`: `[{ "url": "<canonical meeting url>", "title": "<meeting title>" }]`
4. Reply with the issue identifier and URL from the result.

## After create

Reply with a short table: proposed title → ticket URL (or "already exists" / error). Do not dump tracker API payloads.
