# Session context

Resolve context **only when it is first needed**, then reuse it for the session. Never re-ask for
something already established. Repository instructions and the user's conventions take precedence
over the defaults in these skills.

| Value | Needed when | How to resolve |
|---|---|---|
| Work source | Reading or writing tickets | Use the source already established in context; otherwise inspect available integrations or local ticket files. Ask only if the destination is ambiguous |
| Scope | Creating or listing tickets | Resolve the repository, workspace, team, board, or project from source metadata or context. Do not infer scope solely from an identifier's format |
| Assignee | Creating a ticket | Use the stated assignee, or suggest the authenticated user if discoverable. Omit if unsupported; never guess identity from Git configuration |
| Branch convention | Creating a branch | Follow repository instructions or an established convention. Otherwise offer `<ticket-slug>-<short-description>` and confirm once; a personal prefix is optional |

Lazy resolution matters: an unambiguous ticket URL may already establish the source and scope.
A quick "track this" should draft immediately, not open with a questionnaire.

When you do have to ask, ask for everything still unknown in **one** message with the detected
defaults pre-filled. Ask only for values needed for the next action.

Do not write a config file and do not persist these. A new session re-detects, which is the intended
behaviour: people move between projects, and stale config is worse than a one-line confirmation.

## Ticket references and safe local names

`<TICKET>` means the source's exact ticket reference: an identifier, number, URL, or local file path.
Do not require a particular pattern, rewrite it, or assume it is globally unique. Resolve a bare
number against the established scope; ask if multiple sources could match.

Keep the canonical reference, source, scope, and any internal ID returned by the integration in
the notes. Use the integration's actual required identifier for subsequent operations; some sources
have no separate internal ID.

`<TICKET-SLUG>` is a filename- and branch-safe derivative, not the canonical reference. Derive it
from the resolved source, scope, and identifier, using only letters, digits, hyphens, and underscores.
Never interpolate a raw URL, user-provided path, or identifier into a shell command or notes path.
Check existing notes for the same canonical reference before reusing a slug; disambiguate collisions
rather than overwriting a different ticket. Store the selected slug in the notes and reuse it.

## Notes directory

Session notes live outside Claude Code's own config tree, so writing them never trips a
"Claude is editing its own settings" prompt. Resolve the directory with:

```bash
NOTES="${TICKET_NOTES_DIR:-${XDG_STATE_HOME:-$HOME/.local/state}/claude/tickets}"
mkdir -p "$NOTES" && echo "$NOTES"
```

`<NOTES>` in these skills means the absolute path that snippet prints — substitute it, it is not a
variable you can reference. Shell state does not survive between Bash calls, so **re-run the snippet
any time the literal path is no longer in front of you** (after a long session, after context is
compacted). It is idempotent and costs nothing. Never reconstruct the path from memory — it has moved
before, and old notes exist under the previous location.

A ticket's notes are `<NOTES>/<TICKET-SLUG>.md`. Quote all shell paths.

`TICKET_NOTES_DIR` overrides the location entirely: point it at a synced notes repo, a per-machine
path, or anywhere else notes should accumulate.

Writing outside the project directory can prompt for permission on first use. That is expected once
per machine; allowlist the notes directory if the prompts become tiresome.

## Work-source capabilities

These skills prescribe operations, not API names. Use the available MCP integration, CLI, API,
or local files for the chosen work source. Inspect its tool descriptions, schemas, or help before
acting; never invent commands or assume similarly named tools target the same source.

Determine the capabilities needed for the current flow: read or list tickets; create or update
tickets; read or append comments; resolve identities and scope; and manage grouping, relationships,
or milestones. Do not require capabilities that the current flow does not use.

Use only supported fields and valid values discovered from the source. Priority scales, statuses,
labels, assignees, and hierarchy differ. A missing grouping feature can be represented by explicit
links or a parent ticket, but show that alternative and obtain approval before changing the draft.

If no source is connected, drafting still works. Offer to keep drafts in the conversation or in
user-approved local files. Do not claim to have fetched, created, or updated a ticket until the
chosen source was actually accessed. If an operation is unavailable, explain the limitation and
provide a draft or manual handoff instead of silently selecting another destination.

When listing a backlog, follow pagination and state the scope and any limits. Do not present a
partial list as complete. Verify every write by reading back the exact record or file; only then
report its canonical reference as created or updated.

## Confirmation gate

**Nothing is written to a work source — ticket, grouping, comment, status change, or local ticket file — without first showing the
full content in a code block and receiving explicit confirmation.** This applies to every flow in both
skills, including batch operations. One confirmation covers one write; approving a draft does not
approve the next one.

Everything that will be written must appear in the draft, including fields resolved automatically
(scope, assignee, labels). A field the user never saw is a field they never approved.

Routine private development-note updates are separate from work-source writes and do not require
this per-write gate. A local file used as the actual ticket or shared handoff is a work source,
not an exception. Do not mark a comment `[posted]` until its write has been verified.
