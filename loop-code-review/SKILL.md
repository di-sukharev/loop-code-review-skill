---
name: loop-code-review
description: >-
  Find and fix material code defects and DX issues through a review loop
  with subagents and regression checks.
---

## Roles

You coordinate the review. Assign review rounds and assess reports.
You are responsible for quality and delivery time.

## Subagent setup

Before each review round, honor explicit model and effort choices (user > calling skill).
Otherwise, choose the cheapest reliable model and effort for the full task scope and likely fixes.

Give each new subagent the task context without the parent history.

- In Codex, set `fork_turns: "none"`.
- In Claude Code, start a new `general-purpose` agent.

## Rules

Meet all requirements with the simplest sufficient solution.
Keep the UX thoughtful, simple, and elegant.
Keep the UI minimal.
Avoid unnecessary clicks, modals, and controls.
Give these expectations to every subagent.

Do not open a browser for visual inspection.
Do not click through the app for visual inspection.
Subagents use code and check results to assess the changes.
The user checks the visuals.

Subagents run checks that are useful for the task.
They also run checks required by the project.
Subagents do not run unrelated or redundant checks.
They reuse valid check results.
They fix failures caused by the task.
They report unrelated failures.

Follow project instructions and user overrides.
Leave unrelated changes untouched.
Exclude unrelated changes from the task's work, review, and commits.
Unless instructed otherwise, continue on the current branch.
Do not deploy to production without user authorization.
Do not create branches or worktrees without user authorization.

## Process

1. Start a new reviewer. Give the reviewer:

   - The original requirements.
   - The accepted clarifications.
   - The task scope.
   - The repository path.
   - The check results.
   - The known risks.

   Do not give the reviewer previous review conclusions.

2. The reviewer examines all current task changes, including new files.
   The reviewer also examines affected code.
   The reviewer looks for problems beyond the risks you listed.
   The reviewer continues the review after it finds a problem.
   Before any edits, the reviewer reports material defects and DX problems caused or worsened by this task.
   For each finding, the reviewer reports:

   - The scenario that fails.
   - The requirement that the code violates.
   - The code location.
   - The impact.
   - Why the existing safeguards do not prevent the problem.
   - A reproduction procedure or a regression test.
   - The root cause and evidence for it.
   - The simplest fix that resolves the root cause across the affected code.
   - The effects of the fix on related behavior.

   The reviewer separates facts from assumptions.
   The reviewer reports important gaps in the review.
   The reviewer gives the task changes a production readiness score from 1 to 10.
   The reviewer briefly explains the score.
   The score is advisory.

3. Assess the findings. Inspect the relevant code when the report does not give enough evidence.
   Accept concrete problems supported by evidence.
   Reject unsupported claims, personal preferences, and proposals that expand the task scope.
   Give reasons for each rejection.
   Do not complicate a sufficient implementation for hypothetical edge cases without evidence of a realistic failure scenario.
   Consider impact and likelihood.
   Do not invent probabilities.
   Ask open-ended questions only if uncertainty about evidence, consequences, or the proposed fix prevents a decision.
   The same reviewer investigates.

4. Tell the reviewer to fix the accepted findings.
   Where practical, the reviewer writes a regression test that fails before the fix.
   The same reviewer fixes the problem.
   The reviewer verifies the result.
   The reviewer runs suitable checks before it reports the results to you.

5. After the fixes, start a new reviewer.
   Repeat the review for all updated task changes.
   Include any later changes to the reviewed scope in a review.
   Finish when all of these conditions are true:

   - All accepted findings are resolved.
   - All important gaps in the review are resolved.
   - All applicable checks pass.

If the reviewer finds no task changes, report this result and finish.

## Finish

Resolve obstacles within the current task.
Pause only if human action is necessary.
If you pause, state the human action that is necessary.
Give instructions to subagents in English.
Write the final report in the user's language.
Include fixes, check results, and remaining issues.
If a review is complete, include its latest score and brief explanation.

Use the fewest steps needed to complete the task.
Each step must advance the task or resolve a real uncertainty.
