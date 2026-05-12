---
name: handoff
description: Create a saved Markdown handoff document for continuing work in a new session. Use when the user asks for a handoff, checkpoint, context summary, session summary, resume document, continuation note, or new-thread context; when context compaction or long conversation history may have made details stale; or when Codex should preserve current work before ending or switching tasks. Captures verified current state, git status, files touched, decisions, tests, running processes, pitfalls, and immediate next steps.
---

# Handoff

## Core Rule

Create a handoff that a fresh Codex session can use without relying on the prior conversation. Always save the document to disk; do not only display it.

Favor verified facts over remembered narrative. Context compaction and long threads can preserve false or stale arguments, so re-check local state before writing anything operational.

## Workflow

### 1. Determine Scope

Identify the work being handed off:

- User goal and current task.
- Repository or workspace root.
- Explicit filename, if the user provided one.
- Whether the handoff is for immediate continuation, task switching, review, or end-of-session archival.

If the user did not provide a filename, generate:

`HANDOFF_[TOPIC]_[MM_DD]_[HH_MM].md`

Use uppercase topic words, replacing non-alphanumeric characters with underscores. Use the user's local timezone when available.

### 2. Verify Current State

In a git repo, gather:

- `git branch --show-current`
- `git status --short`
- `git diff --stat`
- `git diff --cached --stat`
- `git log --oneline --decorate -10`

Only label commits as made during the session when that is actually known from the conversation or tool history. Otherwise call them "recent commits".

Inspect relevant files before summarizing modified behavior. If a file is changed but unrelated or clearly user-owned, note it as unrelated/unknown instead of claiming authorship.

Capture running processes only when relevant to the current work, such as dev servers, watchers, background workers, daemons, test runs, or API servers. Include ports and restart commands when known.

### 3. Reconstruct Work Context

Extract and verify:

- Work completed and partial work.
- Files created, modified, deleted, or used as reference.
- Important commands run and their outcomes.
- Tests or checks run, including failures and skipped checks.
- Decisions and rationale.
- Blockers, gotchas, assumptions, and known risks.
- The most useful immediate next step.

When memory and filesystem disagree, trust the filesystem and git state. If something cannot be verified, say `Unknown` or `Not verified` rather than inventing.

### 4. Choose Save Location

Save the handoff in the first available location:

1. `docs/handoffs/` if `docs/` exists or `docs/handoffs/` already exists.
2. `docs/` if `docs/` exists and a handoffs subdirectory would be inappropriate.
3. `.claude/handoffs/` if `.claude/` exists.
4. Project root as fallback.

Create the selected directory if needed. For local Codex workspace edits, prefer `apply_patch` for the new Markdown file.

### 5. Write the Document

Keep the handoff comprehensive but concise. Target under 2000 words unless the work truly requires more detail. Use concrete paths, function names, line numbers, commands, and test names where they help continuation.

Use this structure:

```markdown
# Handoff: [Brief Title]

**Created:** [timestamp with timezone]
**Workspace:** [absolute path]
**Branch:** [branch or Not a git repo]
**Purpose:** [immediate continuation/task switch/review/archive]

---

## Summary

[2-3 sentences covering goal, current state, and what a new session should do first.]

## Current State

- **Done:** [verified completed work]
- **In progress:** [partial work]
- **Blocked:** [blockers or None]
- **Working tree:** [clean/dirty plus staged/unstaged summary]

## Files Affected

### Created

- `path` - [purpose]

### Modified

- `path` - [what changed and why]

### Deleted

- `path` - [why removed]

### Read / Reference

- `path` - [why referenced]

## Key Decisions

| Decision | Rationale | Alternatives / Notes |
| --- | --- | --- |
| [decision] | [why] | [tradeoffs] |

## Verification

- **Commands run:** [command -> result]
- **Tests:** [passing/failing/not run, with reason]
- **Manual checks:** [what was checked]

## Git / Process Notes

- **Branch:** [branch]
- **Uncommitted changes:** [staged/unstaged/untracked summary]
- **Recent commits:** [relevant recent commits or Not checked]
- **Running processes:** [servers/watchers/ports or None known]

## Gotchas, Assumptions, And Risks

- [edge cases, stale-context warnings, non-obvious behavior]

## Immediate Next Steps

1. [first exact action]
2. [second exact action]
3. [third exact action]

## Useful Commands

```bash
[command]
```

## Search Queries

- `[pattern]` - finds [what]

## Open Questions

- [ ] [question or None]

## New-Session Bootstrap

Start the next session with:

`Continue from [handoff path]. First verify git status and the files listed there, then proceed with the Immediate Next Steps. Do not assume stale conversation context is correct.`
```

Omit empty subsections only when they add no signal, but always include `Summary`, `Current State`, `Verification`, `Immediate Next Steps`, and `New-Session Bootstrap`.

### 6. Final Response

After saving, respond with:

- The saved handoff path.
- A one-sentence summary of what it captures.
- Any notable caveat, such as tests not run or unknown running processes.

Do not paste the full handoff unless the user asks.
