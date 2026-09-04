---
name: loop-code-review
description: Iterative review-and-fix loop for task-scoped active Git changes using fresh independent reviewer agents. Use when the user invokes the skill or asks for repeated independent review after fixes until validation passes and no actionable findings remain; not for routine one-pass reviews.
---

# Loop Code Review

Review the current task as a handoff to another engineer. Independently test whether the change satisfies its requirements, is safe to modify, and has credible validation evidence.

## Invariants

- **Task scope:** Review only task-owned active changes, including relevant untracked files and mixed-file hunks. Determine ownership from the current task and edits made during it, not from `git status` alone. Read neighboring code for context, but report only problems caused or worsened by the scoped change, including missed requirements. If ownership is ambiguous, ask the user.
- **Independent reviewer:** Each review pass uses a fresh read-only agent without parent conversation history, reasoning, assumptions, or prior review discussion. In Codex, spawn it with `fork_turns: "none"`; elsewhere use the equivalent fresh-context mechanism.
- **Stable state:** Snapshot the scoped patch before review and compare it when the reviewer returns. Any relevant change makes the review stale and requires a fresh pass after validation. For unchanged state, use the same reviewer for clarification or reconsideration.
- **Evidence over scores:** Concrete findings and validation control the outcome. Scores only show progress and never create work, excuse findings, or gate acceptance.
- **Worktree safety:** Preserve unrelated changes. Do not stage, commit, reset, stash, push, or modify out-of-scope files unless the user explicitly asks.

## Workflow

1. **Prepare the scope.**
   - Inspect `git status --short`, staged and unstaged diffs, and relevant untracked files.
   - Record included files or hunks and explicit exclusions.
   - Prepare a factual task brief from the user's request and authoritative specifications. Include requirements and acceptance criteria, but not implementation rationale, suspected issues, or proposed fixes.
   - If there are no task-owned active changes, report `no-changes` and stop.

2. **Validate the scoped state.**
   - Run the smallest meaningful non-destructive tests, typecheck, lint, build, or focused checks for the changed surface. Do not run a repository-wide gate solely to prepare a review; leave the final broad gate to the owning task workflow unless it is the only meaningful scoped check.
   - Fix only failures caused by the scoped task. Record pre-existing, unrelated, flaky, or environmental failures as evidence or blockers without expanding scope.
   - Record exact commands and compact factual outcomes — the failure tail when a command fails, not passing logs. Reuse evidence keyed to the scoped-state identifier while the snapshot is unchanged. If meaningful validation is unavailable, explain why.
   - Refresh the scope and snapshot after any command that changes the worktree.

3. **Run one review pass.**
   - Read [references/reviewer-contract.md](references/reviewer-contract.md) and use it as the canonical reviewer prompt.
   - Give the reviewer the repository path, factual task brief, acceptance criteria, included scope, exclusions, scoped-state identifier, and validation commands with factual outcomes.
   - Require independent repository inspection. The reviewer may rerun only checks that are safe for tracked and user-owned files, and a broad green suite only for a concrete evidence concern that focused inspection cannot resolve.

4. **Triage the complete review before editing.**
   - Require the complete finding set before editing. Ask the same reviewer once to finish an incomplete review, complete missing required output, or explain an unclear understanding summary. If it still cannot, replace it with one fresh reviewer; if that review is also unusable, stop `incomplete`.
   - Accept evidence-backed findings within the contract's review scope. Reject speculative refactors, optional hardening, and subjective polish.
   - For a material disputed finding, read [references/adjudication.md](references/adjudication.md).

5. **Fix, validate, and repeat.**
   - Resolve the complete set of accepted findings as one coherent batch when safe, then rerun affected scoped validation, refresh the full task-owned scope, and start a fresh review pass per the stable-state invariant.
   - If two fix/review cycles reveal the same underlying failure class, stop local patching and re-examine the owning flow before another pass.

## Completion

Pass only when all are true:

- scoped validation is green, or any unavailable check has a concrete acceptable justification;
- the reviewer of the latest current state credibly explains the change and inspected the whole scope;
- no actionable finding remains unresolved;
- test evidence is trustworthy, or its absence is concretely justified.

Unless the user requests another limit or explicitly requires persistence, stop `incomplete` after five completed review passes without acceptance. Explicit persistence removes only that limit: stop `incomplete` when higher-priority constraints intervene or no safe in-scope progress remains. Use `interrupted` when the user or system cancels the workflow.

## Final Report

Report:

- status: `passed`, `no-changes`, `incomplete`, or `interrupted`, with the number of completed review passes;
- what changed and how each finding was resolved, withdrawn, adjudicated invalid, or left unresolved;
- validation commands and factual outcomes;
- the latest score, if a current review pass completed;
- test quality score and basis when tests changed;
- blockers, residual risks, and follow-up work.
