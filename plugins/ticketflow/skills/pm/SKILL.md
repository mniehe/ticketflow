---
name: pm
description: Create and manage tickets from discovery, exploration, or development sessions using any work source or approved local files. Use when turning findings into tickets, capturing work mid-session ("track this", "make a ticket for that"), running a ticket-writing session, or grooming a backlog. Complements the ticket skill for active development.
---

# PM — discovery to tickets

Discover, define, and create tickets from inside a Claude Code session.

**CRITICAL RULE: Never write anything to a work source (tickets, groupings, comments, updates, or local ticket files) without first
displaying the full content in a code block and receiving explicit confirmation. This applies to ALL
flows — no exceptions.**

Read `${CLAUDE_PLUGIN_ROOT}/reference/session-context.md` for the shared conventions. Resolve the
source, scope, and supported assignee when a ticket is actually being created — not as an opening questionnaire — and
reuse whatever the conversation has already established.

Adapt operations and fields to the chosen source's discovered capabilities. Drafting does not
require a connected integration. If a write is unavailable, provide a draft or offer approved local
files instead; do not claim an item was created. Repository and user conventions override defaults.

## When this skill is relevant

- Exploring a codebase and discovering work that needs to be tracked
- A discovery session reveals scope, unknowns, or follow-up tasks worth capturing
- Creating tickets without switching out of the current session
- Working in a `ticket` dev session and new work surfaces mid-session
- Pre-ticket discovery: researching a problem before formalizing it

This skill complements the `ticket` skill. `ticket` manages an active development session against a
known ticket. This one handles the upstream work of discovering and defining what the tickets should be.

## Ticket fields

| Field | Behavior |
|---|---|
| Title | Provided by the user or derived from discovery context |
| Description / Requirements | Synthesized from discovery findings if available, asked upfront if not |
| Priority | Suggest with a one-sentence rationale using the source's supported scale, user confirms; omit if unsupported |
| Structure | Suggest with rationale, user confirms (see Structure decision) |
| Grouping | Suggest a supported project, parent, collection, or equivalent based on context; ask if unclear |
| Labels | Suggest valid source labels if relevant, omit if unsupported or none are obvious |
| Assignee | Stated assignee or a discoverable authenticated user — shown in the draft, not set silently; omit if unsupported |
| Source / Scope | Destination and repository, workspace, board, or collection resolved from context or metadata — shown in the draft |
| Estimate / Due date | Only set if mentioned by the user and supported by the source |

## Structure decision

Before drafting anything, suggest the structure with a one-sentence rationale. This is a discussion —
the user can override and the draft adjusts.

| Signal | Suggestion |
|---|---|
| Single clear outcome, no sub-work | Standalone ticket |
| Multiple distinct workstreams or deliverables | Group of related tickets under a supported project or parent |
| Clearly lives under an existing ticket | Child ticket or explicit relationship, if supported |
| Spans weeks, touches multiple areas, or has phases | Grouping with milestones or phased tickets, if supported |
| Quick fix, no dependencies | Ticket, no project needed |
| Ambiguous | Flag it: "This could go either way — here's the tradeoff" |

## Draft format

Used consistently before writing anything to the work source. Include only supported fields that
will actually be written; show the full destination and any proposed fallback representation.

```
**[TICKET / GROUPING / CHILD of <TICKET>]**
**Title:** ...
**Source / Scope:** ...
**Assignee:** ... (omit if unsupported or unset)
**Priority:** [supported value] — [one sentence rationale] (omit if unsupported)
**Grouping / Parent:** ... (omit if unsupported or unset)
**Labels:** ... (omit the line if none)

**Description**
[1-3 sentences of context — the why]

**Requirements / Goals**
- ...
- ...

**Assumptions made:** [only if something non-obvious was inferred]
```

For groupings with supported milestones, also include:

```
**Suggested milestones:**
- ...
```

End every draft with: `Create this? (yes / edit / discard)`

## Flow 1: Discovery to ticket

**Triggered by:** finishing an exploration session, "let's capture what we found", "turn this into
tickets", "what should we track from this"

This is the primary flow. After exploring a codebase, reading documentation, or investigating a
problem, synthesize findings into well-formed tickets rather than starting from scratch.

1. Summarize what was discovered in plain language before proposing any tickets:
   *"Here's what I think we found — [2-4 sentences]. Does that match your read?"*
2. Propose a ticket structure from the findings:
   - What's clearly one ticket vs. what needs a grouping or parent
   - What's well-understood enough to write now vs. what needs more discovery first
   - Any dependencies or sequencing that affects how things should be structured
3. Draft tickets one at a time, in priority order, each following the draft format
4. Confirm each write before creating — do not batch-create without per-write confirmation.
   Read back each created item and verify its approved fields; do not blindly retry uncertain writes.
5. Output the canonical references of verified creations. Distinguish drafts, failed writes,
   and unverified results from created tickets.

## Flow 2: Mid-session capture

**Triggered by:** "track this", "make a ticket for that", "we should capture this" during any session

1. Draft immediately from existing session context — do not ask for information already present in
   the conversation
2. Present a compact draft in a code block (title, 2-3 bullet requirements, supported priority,
   structure, destination, and every field to be written)
3. One round of edits if needed, then confirm
4. Create through the chosen source, read it back, drop the canonical reference inline, continue without
   ceremony

Keep this lightweight. The session context is the source — don't re-ask for things already established.

## Flow 3: Dedicated PM session

**Triggered by:** "PM session", "let's create some tickets", "I need to track some work"

1. Ask: *"What are we creating today? Give me as much or as little as you have."*
2. **If title only** — ask all clarifying questions in one message before drafting:
   - What's the goal or desired outcome?
   - What are the requirements or acceptance criteria?
   - Any context worth knowing? (related work, constraints, why now)
   - Standalone ticket, part of a larger project, or related to something that already exists?
3. **If rough description** — shape it into a draft immediately, flag assumptions, ask for corrections
   in one pass
4. Suggest structure with rationale before presenting the draft
5. Present the full draft for confirmation
6. On approval — create through the chosen source, read back to verify, confirm with the canonical reference
7. Ask: *"Anything else to create or adjust?"*

## Flow 4: Backlog grooming

**Triggered by:** "let's groom", "backlog review", "tidy up the tracker"

1. Resolve the requested scope, then list open tickets through the chosen source using its actual
   statuses. Follow pagination; disclose filters or access limits. If listing is unavailable,
   request a user-provided backlog and clearly label the assessment as based on that input.
2. Present a full assessment before taking any action:

   **In Progress**
   - Tickets with no recent comments that look stale
   - Tickets that have grown large enough to warrant splitting or grouping

   **Backlog — Needs Attention**
   - Unprioritized tickets (suggest a priority for each)
   - Tickets that belong in a supported grouping but aren't linked to one
   - Tickets that look like duplicates or are no longer relevant

   **Backlog — Looks Fine**
   - Brief list, no action needed

3. End with a proposed action list: *"Here's what I suggest we do — want to go through these?"*
4. Work through the action list together, showing the full payload and confirming each change
   before applying it; read back the exact item to verify each write
5. End with a brief summary of what changed

## Discovery best practices

- **Prefer reading over assuming** — check actual file contents, configs, and existing code before
  describing what something does
- **Note what you don't know** — unknowns that need more investigation should surface as discovery
  sub-tickets, not be papered over in requirements
- **Flag dependencies** — if ticket B can't start until ticket A is done, say so explicitly in both
  drafts
- **Separate the problem from the solution** — ticket descriptions explain why the work matters;
  implementation details belong in comments after the ticket is created and work begins
- **Cross-reference related tickets** — before creating a new ticket, check whether related work
  already exists to avoid duplication or a missed sub-issue opportunity

## Relationship to the ticket skill

The two skills cover different phases of the same loop:

```
Discovery / planning     ->  pm skill (this file)
                                |
                         Tickets created in the chosen source
                                |
Active development       ->  ticket skill
                                |
                         Comments + EOD written to ticket
```

In a `ticket` session where new tickets need to be created, Flow 2 (mid-session capture) applies —
keep it lightweight and return to the development session after.
