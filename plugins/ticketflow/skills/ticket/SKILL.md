---
name: ticket
description: Manage tracked development with branch and change-request conventions, private notes, ticket comments, and end-of-day handoffs. Use when working against a ticket across sessions, preparing a change request, wrapping up ("EOD"), or resuming work. Supports any work source or approved local ticket files.
argument-hint: <ticket-reference>
---

# Ticket — tracked development session

Apply the repository's conventions for tracked work. The formats below are defaults when no
convention is established. "Change request" includes the review mechanism used by the project;
branch and change-request steps apply only where that workflow exists.

## Right-sizing

Full ceremony — notes file, session log, ticket comments, EOD handoff — is for work that spans a change request
or more than one sitting. For a quick fix against a ticket, apply the relevant branch and change-request conventions and
skip the rest; say so in one line rather than silently opening a notes file. Escalate to the full
flow the moment the work grows a second session or a blocker worth recording.

Read `${CLAUDE_PLUGIN_ROOT}/reference/session-context.md` for the shared conventions. Resolve values
only when a step needs them. `<TICKET>` is the canonical reference, and `<TICKET-SLUG>` is its safe,
scope-qualified local derivative. Neither requires a particular ticket-key format.

## 1. Session initialization

Accept a ticket reference as an argument or ask for it. Resolve the work source and scope from
context and metadata; confirm an ambiguity or mismatch rather than guessing from the reference.

1. Read the ticket through the chosen source's supported read operation. Record its title,
   canonical reference, source, scope, and internal ID if provided. If reading is unavailable,
   say so and work only from user-provided content; do not invent ticket state.
2. Resolve the notes directory (see `${CLAUDE_PLUGIN_ROOT}/reference/session-context.md`) and create
   it if missing:

   ```bash
   NOTES="${TICKET_NOTES_DIR:-${XDG_STATE_HOME:-$HOME/.local/state}/claude/tickets}"
   mkdir -p "$NOTES" && echo "$NOTES"
   ```

   `<NOTES>` below means the absolute path this prints. Re-run the snippet any time that path is
   no longer in front of you — it is idempotent.
3. Resolve a safe `<TICKET-SLUG>` using the shared conventions. If `<NOTES>/<TICKET-SLUG>.md`
   exists, confirm its canonical reference matches and read it to resume. Otherwise create it from this
   template:

```markdown
# <TICKET>: <ticket title>

Source: <work source>
Scope: <repository, workspace, or local collection>
Ticket reference: <canonical reference>
Internal ID: <source ID, if provided>
Local slug: <TICKET-SLUG>

## Session Log
<!-- Append timestamped entries below -->

## Changes Made
<!-- Running list: `path/to/file` — what changed -->

## Open Threads
<!-- - [ ] question or unresolved item -->
```

If source metadata is missing from existing notes, resolve it from the source before posting.

## 2. Branch naming

Follow the repository's branch convention. If none exists, confirm this default once:

Format: `<TICKET-SLUG>-<short-description>` (with an optional personal prefix)

- Lowercase, hyphenated description, 2-5 words
- Use the safe slug, not a raw URL or file path. Skip this step when branches are not used.

## 3. Change-request title

Default format: `<type>(<TICKET-SLUG>): description of change`

- Types: `feat`, `fix`, `chore`, `refactor`, `docs`, `perf`
- Suggest the type based on what the work actually is; confirm with the user
- Link the canonical ticket reference in the body when the slug alone does not identify it.
- Avoid closing keywords (`fixes`, `closes`, `resolves`) attached to ticket references by default.
  Integrations may interpret them as automatic status transitions; do not trigger those unless
  the user explicitly approves that transition and the source's behavior has been verified.

## 4. Change-request body

```
1-3 sentence plain English summary of what changed and why.

**Problem**
- point form

**Solution**
- point form

**Caveats**
- point form
```

Omit the **Caveats** section entirely when there is nothing to note.
Apply the same automatic-closure guard to the body. Use the repository's template if one exists,
and include the canonical ticket reference without an automatic-closing directive by default.

## 5. Development log — local notes (tier 1)

Continuously maintain `<NOTES>/<TICKET-SLUG>.md` throughout the session.

**When to write**: after each meaningful action — debugging a failure, making a code change,
discovering something unexpected, or resolving a question.

**Entry format** — append under `## Session Log`:

```
### <timestamp> — <short title>
<what happened, what was tried, what was learned>
```

Get the timestamp by running `date '+%Y-%m-%d %H:%M'` via Bash.

**Maintain the running lists**:

- `## Changes Made`: add `path/to/file` — brief description for every file modified
- `## Open Threads`: add `- [ ] question` for unresolved items; check off when resolved

**Mark posted items**: after a comment's write to the work source is verified (tier 2), append `[posted]` so it is
never double-posted.

These notes are for LLM consumption — be verbose and capture reasoning, not just outcomes.

## 6. Ticket comments — summaries (tier 2)

Draft a comment for the work source on exactly these triggers, using its supported comment or
append operation and the stored canonical reference or internal ID as required:

- **Blocker resolved**: what blocked progress, what was tried, how it was unblocked
- **Assumption made**: the assumption, rationale, and what changes if it's wrong
- **Design decision**: the choice, alternatives considered, and reasoning

Do NOT post routine progress updates. Only post what a teammate picking up the ticket tomorrow would
need to know. Mark posted entries in the local notes with `[posted]`.

**IMPORTANT:** Before posting any comment, display the full comment text in a code block and ask for
explicit confirmation. Never post to the work source without approval. Read the result back before
marking it `[posted]`. If comments are unsupported, offer an approved local handoff or manual draft;
do not silently overwrite the ticket description or mark an unposted draft as posted.

## 7. End-of-day comment

When the user says "EOD" or "wrap up":

1. Read `<NOTES>/<TICKET-SLUG>.md` to gather the full session context.
2. Check for any un-posted blockers, assumptions, or design decisions — draft them for review first.
3. Draft an EOD comment with:
   - What was done today (bullet points)
   - What is left (bullet points)
   - **Pick up here tomorrow**: a single actionable sentence — not "continue work", something specific
     like "wire up the retry counter to the metrics endpoint"
4. Display ALL drafted comments (including any from step 2) in code blocks and ask for explicit
   confirmation for each write before posting anything to the work source. Verify each write.
5. Append a session-end timestamp to the local notes file.

## 8. Morning handoff

When resuming a ticket:

1. Read the latest comments or shared handoffs using the source's available read operation.
   If unavailable, say so; do not assume there are no newer updates.
2. Read `<NOTES>/<TICKET-SLUG>.md` for full local context.
3. Resume from the "Pick up here tomorrow" line in the most recent available EOD handoff.
   If none exists, summarize known context and resolve the next action with the user.
4. Append a new session-start timestamp to the local notes file and continue.

## New work discovered mid-session

If work surfaces that belongs in its own ticket, use the `pm` skill's mid-session capture flow — draft
from session context, confirm, create, drop the identifier inline, and return here.
