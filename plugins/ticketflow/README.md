# ticketflow

Tool- and project-agnostic ticket workflow. Two skills covering the two halves of the loop: turning
discovery into well-formed tickets, and running a tracked development session against one.

```
Discovery / planning     ->  /ticketflow:pm
                                |
                         Tickets created in the chosen source
                                |
Active development       ->  /ticketflow:ticket <ticket-reference>
                                |
                         Comments + EOD handoff on the ticket
```

## What it does

**`pm`** — four flows:

1. **Discovery to ticket** — after exploring a codebase, synthesize findings into tickets rather than
   starting from a blank title. Suggests supported structure (standalone / child / grouping / phases)
   with a rationale before drafting.
2. **Mid-session capture** — "track this" during any session produces a compact draft from context
   already in the conversation, no re-asking.
3. **Dedicated PM session** — clarifying questions batched into one message, then a full draft.
4. **Backlog grooming** — assessment of in-progress and backlog tickets, then a proposed action list
   worked through together.

**`ticket`** — the conventions for tracked work: branch naming, change-request title and body (no
automatic-closing directives by default), a two-tier development log (verbose local notes in
`${XDG_STATE_HOME:-~/.local/state}/claude/tickets/`, selective ticket comments only for blockers,
assumptions, and design decisions), an end-of-day comment ending in a specific "pick up here tomorrow" line, and the morning
handoff that resumes from it.

**Nothing is written to a work source without the full content shown in a code block and explicit
confirmation first.** Every flow, including local ticket files and batch operations. Private
development notes are separate and can be maintained throughout the session. Writes are read back
before reporting success or marking a comment posted.

## Source, scope, and conventions

The skills resolve context only when needed and reuse it within the session:

| Value | Default offered |
|---|---|
| Work source | The established integration or local ticket collection |
| Scope | Repository, workspace, team, board, or project from context or metadata |
| Assignee | Stated assignee or a discoverable authenticated user, if supported |
| Branch convention | Repository convention; otherwise a safe ticket slug plus description |

Ask only when ambiguous, not as an opening questionnaire. Repository and user conventions override
the defaults. References may be identifiers, numbers, URLs, or local paths; no particular key pattern
is assumed. Safe, scope-qualified slugs keep local notes and branches separate from canonical references.

## Requirements

- For live ticket operations: an available MCP integration, CLI, API, or approved local files.
  Operations and fields are discovered from that source; no specific tool names are assumed.
- Without an integration: draft in the conversation or offer approved local ticket files.
- Branch and change-request steps apply only if the project uses that workflow.

Unsupported features are disclosed, not invented. Groupings can be represented with approved links
or parent tickets where appropriate; unavailable comment operations yield a manual handoff draft.

## Where notes go

`${XDG_STATE_HOME:-~/.local/state}/claude/tickets/<TICKET-SLUG>.md`, deliberately outside `~/.claude/` so
writing them never trips Claude Code's "editing its own settings" prompt. Set `TICKET_NOTES_DIR` to
put them anywhere else — a synced notes repo, a per-machine path.

It is still outside the project directory, so the first write of a session may prompt for permission.
If that gets tiresome, allowlist the directory once — in a sandboxed setup, add it to
`sandbox.filesystem.allowWrite` in your settings.

## Install

One-time marketplace registration (per repo):

```
/plugin marketplace add mniehe/ticketflow
```

Install the plugin:

```
/plugin install ticketflow@ticketflow
/reload-plugins
```

Or add to a repo's `.claude/settings.json`:

```json
{
  "enabledPlugins": {
    "ticketflow@ticketflow": true
  }
}
```

## Usage

- `/ticketflow:ticket <ticket-reference>` — start or resume a tracked development session.
- `/ticketflow:pm` — open a ticket-writing or grooming session.
- Conversational: "let's capture what we found", "track this", "let's groom the backlog", "EOD" — both
  skills match on their triggers without the slash command.
