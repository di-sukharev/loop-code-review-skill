---
name: loop-code-review
description: Iteratively review and fix task-owned active Git changes with fresh independent reviewers. Use when invoked or when the user requests a repeated review/fix loop; not for one-pass reviews.
---

# Loop Code Review

Review the current task as a handoff: verify requirements, maintainability, and validation evidence. Communicate concisely, as to a colleague.

## Scope and safety

- Review only task-owned active changes, including untracked files and mixed-file hunks. Determine ownership from the task and its edits; ask the user if ambiguous.
- Preserve unrelated changes. Do not stage, commit, reset, stash, push, or modify out-of-scope files unless the user explicitly asks.

## Workflow

1. **Scope.** Inspect `git status --short`, staged and unstaged diffs, and relevant untracked files. Record included files or hunks, exclusions, and a snapshot of the scoped patch. Stop `no-changes` if the scope is empty.

2. **Validate.** Run the smallest meaningful non-destructive checks for the changed surface. Leave the repository-wide gate to the owning task workflow unless it is the only meaningful check.
   - Fix only task-caused failures; record pre-existing, unrelated, flaky, or environmental failures as evidence or blockers.
   - Record exact commands and compact outcomes, including failure tails, against the snapshot. Reuse evidence while unchanged; explain unavailable validation.
   - Refresh scope and snapshot after any command that changes the worktree.

3. **Review.** Use [references/reviewer-contract.md](references/reviewer-contract.md) as the prompt for a fresh read-only agent without parent history or prior review discussion. In Codex, use `fork_turns: "none"`; elsewhere use equivalent fresh context.
   - Fill the template with factual context. Derive requirements and acceptance criteria from the user's request and authoritative specifications; omit parent-agent conclusions, implementation rationale, suspected issues, and proposed fixes.
   - Compare the snapshot when the reviewer returns. Any relevant change invalidates the review: validate and start a fresh pass. For unchanged state, use the same reviewer for clarification or reconsideration.

4. **Triage before editing.** Require a complete review with a credible explanation of the change and validation evidence. Ask once to fill substantive gaps or clarify reasoning, then replace the reviewer once if needed. If the replacement is also unusable, stop `incomplete`.
   - Accept only findings supported by evidence and the reviewer contract.
   - For material disputes, follow [references/adjudication.md](references/adjudication.md).

5. **Fix and repeat.** Resolve all accepted findings as one coherent batch when safe. Refresh the full task-owned scope, rerun affected validation, and obtain a fresh review of the changed state. If two fix/review cycles reveal the same underlying failure class, re-examine the owning flow before further patching.

## Completion

Return `passed` only when:

- the latest review covers the whole current scope and credibly explains the change;
- no actionable finding remains unresolved;
- scoped validation is green, or unavailable checks have a concrete acceptable justification;
- test evidence is trustworthy, or its absence is concretely justified.

Stop `incomplete` after five completed review passes without acceptance unless the user sets another limit or explicitly requires persistence. Persistence removes only this limit; stop `incomplete` if higher-priority constraints intervene or no safe in-scope progress remains. Use `interrupted` for user or system cancellation.

## Final Report

Briefly report status, completed pass count, changes, and how each finding was resolved, withdrawn, adjudicated invalid, or left unresolved. Include validation commands and outcomes, test-evidence quality, and any blockers, residual risks, or follow-up work. Use whatever structure makes this clear.
