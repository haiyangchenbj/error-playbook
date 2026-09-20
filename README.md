# Agent Error Playbook

An error-knowledge base for AI-agent execution, indexed by **operation type** rather than by date.

## Why by operation type

Daily logs are organised by date; the retrieval key for an error is the operation that failed.
The next time a JSON write breaks, nothing leads back to a file named after a day in September.
Experience organised by date is unretrievable experience — which is exactly why agents
"find the error, announce the fix, then make it again next week".

## Layout

| Section | Contents |
|---|---|
| §0 | How to use it, the accumulation rule, the unattended-run gate sentence |
| §1 | Recurrence board — errors seen 2+ times. Read this first when context is tight |
| §2 | Pre-write gates, grouped by operation type (9 groups) |
| §3 | Post-write verification + the six false-success receipts |
| §4 | Attribution discipline |
| §5 | Why the same error recurs — seven structural causes |
| §6 | Provenance index |

## Install

```bash
mkdir -p ~/.workbuddy
cp ERROR-PLAYBOOK.md ~/.workbuddy/ERROR-PLAYBOOK.md
```

The companion process-host skill is `workflow-guard-rails`, which queries this file at its
pre-execution, result-validation, and rule-accumulation guards.

## Companion pieces

- **Resident layer** — a pointer in `~/.workbuddy/MEMORY.md` plus a gate sentence inlined into
  each automation prompt. This is the layer that actually covers unattended runs: a skill that
  is never loaded guards nothing.
- **Process host** — `workflow-guard-rails`. Decides *when* to check and *where* a confirmed
  failure is written. Holds no error knowledge of its own.
- **Knowledge base** — this file. Holds the concrete entries. Knows nothing about timing.

## Update policy

This mirror is refreshed irregularly from the private canonical copy. Content is sanitized;
structure and operation-type indexing stay identical so the two copies remain diffable.
