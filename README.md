# ticketflow

A Claude Code plugin marketplace for tool- and project-agnostic ticket workflows, owned by mniehe.

## Install

In Claude Code, register this local marketplace:

```
/plugin marketplace add /path/to/ticketflow
/plugin install ticketflow@ticketflow
/reload-plugins
```

Once published to GitHub, register it with `/plugin marketplace add mniehe/ticketflow` instead.

## Plugin

- `/ticketflow:pm` — turn discovery into tickets, capture follow-up work, and groom the backlog.
- `/ticketflow:ticket <ticket-reference>` — manage tracked development, branch and change-request conventions,
  development notes, and end-of-day handoffs.

Uses the available integration, CLI, API, or approved local ticket files. No particular tracker,
ticket-key format, team, or project is required. Drafting works without a connected source.
Repository conventions take precedence over defaults. Work-source writes require explicit confirmation.

See [the plugin README](plugins/ticketflow/README.md) for requirements and usage.

## Validate

```
claude plugin validate .
claude plugin validate ./plugins/ticketflow
```
