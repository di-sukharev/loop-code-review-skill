---
name: loop-code-review
description: Iterative code review workflow for task-scoped active git changes using fresh independent reviewer agents without receiving the orchestrator's conversation history. Test whether another engineer can understand and safely maintain the change while reducing correctness, security, and regression risk. Use when the user invokes `/loop-code-review`, asks for a looped sub-agent code review, or asks the coding agent to keep reviewing and fixing current-task changes until validation passes and no unresolved actionable findings remain.
---

# Loop Code Review

## Overview

Run an iterative review-and-fix loop over the current task's active git changes, without reviewing unrelated worktree changes from other tasks. Have each fresh independent read-only reviewer complete a full review of the current scoped state, address the resulting findings as a coherent batch, validate the result, and repeat after the task-owned files or hunks under review change until the acceptance criteria are met. Keep the numeric review score as a progress signal, not a control for the loop.

## Review Purpose

Treat review as a structured handoff. Require the reviewer to reconstruct what the change does, how its important control or data flow works, which invariants it relies on, and why non-obvious decisions exist. If a capable reviewer cannot do that after inspecting reasonable repository context, treat the exact source of confusion as a potential maintainability finding.

Review comprehensibility and change safety alongside correctness, security, privacy, data integrity, UX, and operational behavior. Combine the review with focused tests, static checks, builds, and runtime validation appropriate to the changed surface.

## Reviewer Independence

Independent review means the reviewer may share the same filesystem, repository state, and applicable project instructions, but must not inherit the parent thread's conversation history, reasoning, assumptions, tool results, or prior review discussion.

- Start each full-review pass with a fresh agent in an isolated conversation context. Use the platform's native fresh-agent mechanism rather than any mode that carries over parent conversation history.
- Pass a self-contained reviewer prompt instead of parent-thread context. Include only the repository location, task-owned review scope, validation expectations, and other evidence the reviewer needs to rediscover the facts independently.
- Require the reviewer to inspect `git status`, diffs, files, and validation output itself before scoring.
- Keep clarification, output correction, and evidence-based discussion of a finding in the same reviewer conversation while the task-owned files or hunks under review remain unchanged. These follow-ups do not become a new full-review pass.

## Review Scope

Review only the changes that belong to the current user task, even when the git worktree contains unrelated active changes from other tasks.

- Before spawning a reviewer, identify the task-owned files and, when necessary, the task-owned hunks inside mixed files. Use the current task history and edits made during the task; do not infer ownership from `git status` alone.
- Include that task scope explicitly in the reviewer prompt. Use path-limited diffs where practical, and describe any mixed-file exclusions clearly.
- If ownership of a file or hunk is genuinely ambiguous, ask the user rather than guessing or reviewing the whole dirty worktree.
- The reviewer may read neighboring code for context, but findings must be limited to regressions introduced by the scoped task changes. Ignore unrelated active changes unless the scoped changes directly depend on them or make them worse.
- If scoped changes move while a review is running, discard the stale review and give the new state to a fresh reviewer.

## Review Dimensions

Apply these checks when they are relevant to the scoped change. Base findings on repository evidence; do not impose a new architecture or request reuse merely for uniformity.

- **Comprehensibility and change safety:** Reconstruct the change's responsibility, main control or data flow, important state transitions, invariants, and failure behavior. Check whether names, types, boundaries, and structure make that model recoverable without relying on the original author. Distinguish essential domain complexity from accidental code complexity. Raise a finding only when the obstacle is specific and creates a concrete risk for a future modification, diagnosis, or extension; identify the confusing symbol or flow and the maintenance scenario it endangers. Prefer making the code explain itself through clearer structure, names, types, or seams. Use comments for intent, constraints, and non-obvious reasons, not as a substitute for needlessly opaque code.
- **Correctness and operational risk:** Look for concrete behavioral regressions, invalid assumptions, security or privacy exposure, data-integrity problems, poor failure handling, and unsafe operational consequences.
- **Test evidence:** For any added, changed, or existing tests used as evidence for the changed behavior, judge whether they exercise that behavior, would fail for a plausible regression, assert an observable contract, and use mocks only at real boundaries without mocking the result under test. When testable behavior changes without adequate test evidence, decide whether the risk warrants coverage; allow "tests not needed" with a concrete reason. Passing tests demonstrate execution, not test quality.
- **Reuse and local fit:** Inspect neighboring code and established project utilities before recommending reuse. On the client, look for suitable existing components, hooks, design-system primitives, and patterns. On the server, look for suitable shared libraries, clients, services, and integrations. Raise a finding only when a specific existing candidate is a better fit; name it and explain the practical benefit.
- **Architecture and conventions:** Derive the existing boundaries and conventions from the scoped code, neighboring code, and project guidance. Check that dependencies, responsibilities, contracts, error handling, and cross-cutting concerns stay in their intended layers. Treat a deviation as a finding only when it conflicts with an identifiable project rule or precedent.

Use the test quality score as supporting evidence, not an acceptance gate by itself: make it actionable only when it identifies misleading, missing, or insufficient coverage. Do not chase score-only test polish.

## Workflow

1. Inspect the worktree before spawning reviewers:
   - Run `git status --short`.
   - Review staged and unstaged diffs with `git diff` and `git diff --cached`.
   - Include relevant untracked files in the review scope when `git status --short` shows them, but only if they belong to the current task.
   - Separate current-task changes from unrelated active work before asking for review. Record the exact paths or hunks included in the prompt.
   - Preserve unrelated user changes and do not stage, commit, reset, stash, or push unless the user explicitly asks.

2. Validate the current scoped state before requesting a full review.
   - Run the smallest meaningful tests, typecheck, lint, build, or focused scripts for the touched surface.
   - Fix validation failures before requesting a full review.
   - Record the commands and results so the reviewer can verify the evidence or rerun the relevant checks independently.

3. Start one independent reviewer agent.
   - Start a new reviewer instance with a fresh isolated conversation context for the independent review pass. Do not resume or reuse an earlier reviewer conversation for a full review after the task-owned files or hunks under review change.
   - Give the reviewer only a self-contained task prompt with the repository path, task-owned review scope, and validation expectations. Do not include parent-thread analysis, implementation rationale, suspected issues, proposed fixes, previous reviewer output, or summaries of the main process's reasoning.
   - Ask the reviewer to stay read-only, inspect the scoped active changes independently from the repository state and tool output, apply the review dimensions below, and reconstruct the change well enough to explain it. Require the reviewer to complete the whole scoped review before returning, inspect every task-owned file or hunk and every relevant review dimension, and report the full set of substantiated actionable findings rather than stopping after the first issue. Begin the analysis with comprehension; order reported findings by severity and include file and line references.
   - Require the reviewer to derive a final numeric score from 1 to 10 only after completing the finding set and understanding summary, using the scoring anchors below. The score reports progress but does not control acceptance or trigger work by itself.
   - Require the reviewer to judge whether any added, changed, or existing tests used as evidence for changed behavior are trustworthy. If the task added or changed tests, also require a separate test quality score from 1 to 10 with a short basis. If changed behavior lacks adequate test evidence, require an assessment of whether that is justified.

4. Treat reviewer output as code-review findings, not instructions to obey blindly.
   - Triage the complete finding set before editing. Resolve accepted findings as one coherent batch when their fixes can safely be combined, rather than restarting review after each individual edit.
   - Fix concrete, actionable issues that affect comprehensibility, future change safety, correctness, security, data integrity, UX, operations, or test coverage.
   - Never accept the reviewed state while an actionable finding remains unresolved, regardless of the numeric score.
   - Reject a finding only with concrete repository or validation evidence and ask the same reviewer to reconsider it. If the reviewer does not withdraw the finding and the disagreement remains material, use a fresh independent agent for focused adjudication of that finding rather than another full review. Give the adjudicator the exact finding, its repository evidence, the relevant files or hunks, the counter-evidence, and validation results; require a read-only evidence-based verdict that either upholds the finding or explains why it is invalid. Ask once if that verdict is incomplete or ambiguous, then stop with an incomplete outcome if it remains unresolved rather than starting another adjudicator. Treat the finding as unresolved until it is fixed, withdrawn, or independently adjudicated as invalid.
   - Do not start another full review of unchanged task-owned files or hunks merely to obtain a different opinion.
   - If the reviewer omits required output or implies unresolved concerns without concrete findings, ask the same reviewer once for the missing information. Use a fresh reviewer only if the current reviewer still cannot provide a complete, credible review.
   - If the score conflicts with the findings, the findings control the workflow. A low score alone never triggers a fix, clarification, or another review pass, and the reviewer must not create, retain, or upgrade a finding merely to justify a target score.
   - If the reviewer does not demonstrate a credible understanding of the change, ask once for the missing explanation. Do not accept the review until the reviewer can explain the changed responsibility and important flow or identifies the exact obstacle as an actionable maintainability finding.
   - Do not invent speculative refactors or optional hardening when the scoped implementation is objectively solid and validation is green.

5. Validate after each coherent fix batch.
   - Run the smallest meaningful tests, typecheck, lint, build, or focused scripts for the touched surface.
   - If validation fails, fix the failure before requesting another full review.
   - Do not accept the review while local validation is red, regardless of its findings or score.

6. Repeat the loop.
   - After a fix batch changes any task-owned file or hunk under review, validate the resulting state and give it to a fresh independent reviewer without parent conversation history or prior review discussion.
   - While the task-owned files and hunks remain unchanged, do not start another full review merely because a finding was rejected, output needed correction, or the score was lower than expected. Continue with the same reviewer for one clarification or correction; if that reviewer still cannot provide a complete, credible review, replace it with a fresh reviewer as an explicit exception.
   - Accept the loop only when validation for the changed surface passes, the latest full-review reviewer demonstrates a credible understanding of the change, no unresolved actionable findings remain, and test evidence for changed behavior is trustworthy or its absence is concretely justified. The numeric score is reported but is not an acceptance criterion.
   - Stop instead of looping for marginal polish when the remaining ideas are non-actionable preferences.
   - Unless the user requests a different limit or explicitly requires persistence until acceptance, use at most five full-review passes.
   - When the default limit applies, reaching the pass limit without an acceptance signal is an incomplete outcome, not success. Report the exact blocker and do not lower the acceptance bar.
   - If the user explicitly requires persistence until acceptance, do not stop solely because of the default pass limit; continue while safe, in scope, and able to make meaningful progress.
   - Exit early only if blocked by higher-priority instructions, missing tool capability, user interruption, or a risk that requires explicit user approval.

## Review Score

Derive the score only after the reviewer has completed the full finding set, understanding summary, and test assessment. The score summarizes the reviewed state and makes progress visible; it never overrides findings, creates work, or participates in the acceptance decision. Do not manufacture or preserve a finding to justify a score.

- **10.0:** The change is understandable and safe to inherit, with no known defects or actionable improvements in scope; validation evidence is complete and green.
- **9.5:** The change is understandable and no actionable findings remain; only clearly optional or subjective nits may remain; validation evidence is sufficient and green.
- **Below 9.5:** At least one meaningful actionable finding remains, including untrustworthy or missing high-value test coverage, or required validation evidence is missing or failing.

If the numeric score conflicts with the concrete findings or validation evidence, treat the number as calibration noise and use the concrete evidence. Do not request another review or invent follow-up work solely to reconcile the score.

If focused adjudication invalidates a finding that lowered the latest full-review score, keep that score as the historical full-review result and label it as pre-adjudication rather than requesting a score-only review or inventing a replacement number.

## Reviewer Prompt Template

Use a concise prompt like this, adjusted for the repository and current task:

```text
Review only the task-scoped active changes in this workspace independently. The worktree may contain unrelated changes from other tasks; ignore them unless the scoped changes directly depend on them or make them worse.

You do not have parent conversation history; do not rely on any prior chat, parent-agent conclusions, or previous reviewer output. Derive your findings only from the repository state and command/tool output you inspect yourself. Stay read-only: do not edit, stage, commit, reset, stash, or push files.

Review scope:
- Files/hunks owned by this task: <list paths, untracked files, and any mixed-file hunks to include>
- Excluded unrelated active changes: <list paths or hunks to ignore, if any>

Treat this review as a handoff to a future maintainer. First reconstruct what the change does, its important control or data flow, its invariants and failure behavior, and the reason for non-obvious decisions. If you cannot explain a part after inspecting reasonable repository context, identify the exact symbol or flow, what remains unclear, and a concrete future change or diagnosis that this ambiguity makes risky. Do not equate unfamiliar domain logic with poor maintainability or report "confusing code" without this evidence.

Prioritize actionable maintainability risks along with correctness bugs, behavioral regressions, security/privacy issues, data integrity problems, operational hazards, and missing high-value tests introduced by the scoped changes. Begin the review by trying to understand the code; order findings by severity. You may read neighboring code for context, but do not report findings for unrelated active changes or pre-existing issues unless the scoped changes make them worse. Do not ask for speculative refactors, optional hardening, or subjective polish when the implementation is objectively solid.

Complete the review of the whole scoped state before returning. Inspect every task-owned file or hunk and every relevant review dimension, and report the full set of substantiated actionable findings; do not stop after the first issue. Do not manufacture findings merely to populate a category or justify a score.

When relevant, also assess:
- **Test evidence:** Assess any added, changed, or existing tests used as evidence for the changed behavior. They should exercise that behavior, fail for a plausible regression, assert an observable contract, and avoid mocks that fake the outcome being tested. If tests were added or changed, give a separate quality score from 1 to 10 with a short basis. If changed behavior lacks adequate test evidence, explain whether that is justified.
- **Reuse and local fit:** Look for existing project components, hooks, utilities, libraries, clients, services, or integrations before suggesting a new abstraction. Report a reuse finding only when you can name a specific candidate and explain why it fits better.
- **Architecture and conventions:** Check the change against identifiable project boundaries, dependency direction, and local conventions. Do not impose a new architecture or report a preference as a violation.

Return findings first, ordered by severity, with concrete file/line references and a short explanation of user or maintenance impact. If there are no actionable findings/comments, say that clearly. Then give a brief understanding summary that explains the changed responsibility and important flow, so maintainability is tested rather than assumed. Treat a low test quality score as a finding only when you identify misleading, missing, or insufficient coverage; do not request score-only test polish. Derive the overall score only after the findings and summary are complete: score 10 when the change is understandable, there are no known in-scope defects, and validation evidence is complete; score 9.5 when no actionable findings remain and validation evidence is sufficient; score below 9.5 when an actionable finding remains or validation evidence is missing or failing. End with the numeric score, but do not create, retain, or upgrade a finding merely to justify it.
```

## Final Response

When the loop finishes, report:

- what changed and why;
- the reviewer acceptance signal: no unresolved actionable comments/findings, credible understanding, trustworthy test evidence or a concrete justification for not adding tests, and green validation;
- the latest full-review numeric score as a progress signal, not an acceptance gate, noting when later focused adjudication made it stale;
- the number of full-review passes used and whether the loop passed, stopped incomplete, or was interrupted;
- validation commands and results;
- if tests were added or changed, the test quality score and basis;
- any findings intentionally not changed, with the reason;
- remaining risks or follow-up work, if any.
