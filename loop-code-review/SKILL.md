---
name: loop-code-review
description: >-
  Fresh reviewer agents find and fix bugs, data model flaws, security gaps,
  leftovers, and needless complexity before release. Use when the user asks for
  loop-code-review or another skill calls it.
---

Run independent code review in a loop. Find and fix material problems before release at the lowest total cost.
Follow project rules. Use English with agents and the user's language with the user.

## Review loop

Start each round with a fresh subagent. Exclude chat history, author explanations, and previous review conclusions.
Default to staged and unstaged Git changes and untracked files unless another scope is specified.
Give it the repository path, requirements, Definition of Done (DoD), and review criteria below.

Choose among available models and effort levels. Follow the user's choices.
Use the most capable model and high effort for high-risk changes.
These include stored data, migrations, security, concurrency, external contracts, and failures across components.
Use a capable general model and medium effort for other changes.

Ask the subagent to review the changes and affected code. Require a location, concrete scenario, impact, and evidence for each finding.
Ask it to score the task implementation from 1 to 10, with one reason. Track scores across rounds without showing previous scores to new reviewers.
Have the reviewer report findings and stop. Accept problems with concrete effects on correctness, data safety, security, release, or maintenance.
Reject preferences, unrelated work, and cases without realistic impact. Mark assumptions.
Save each reviewed code state before fixes. Compare later edits against it and review affected code. Preserve the user's staging choices.
Ask the same reviewer to fix accepted findings and run affected checks.
Where possible, write a failing regression test first. Fix causes with the simplest sufficient change and avoid unrelated cleanup.

Use at most five review rounds unless instructed otherwise. Stop when accepted findings are fixed and no material risk needs further review.

## Review criteria

Assume you will maintain this code for ten years. Refactor each module you change to meet these standards.
Meet the requirements and DoD. Handle invalid input, failures, empty data, repeated actions, and concurrent operations.
Write clear code. Prefer declarative code. Keep modules easy to change, run, and test.
Use clear names, explicit dependencies, and existing project patterns.
Reuse project code and dependencies. Keep one implementation per business rule.
Add widely used, actively maintained libraries in the project's stack when they clearly simplify the solution.
Add abstractions, layers, options, and dependencies only for current needs.
Delete needless code. Remove debug output, temporary files, and obsolete fallbacks within task scope.
Preserve compatibility code still needed by existing callers or stored data.

Model the domain with one source of truth per fact. Use strict types, constraints, and transactions to protect data.
Each table, field, and index must have a current need. Migrations must keep existing data correct.
Use the project's ORM where it supports the operation. Raw SQL needs a concrete functional or performance reason.

Validate external input, enforce access checks, and parameterize database queries. Keep secrets out of code, logs, and responses.
Make failures visible. Check configuration, environment variables, migrations, and external service failures for the target environment.

Tests must catch real failures in changed behavior and stay isolated when run together.
Do not mock the code under test.

## Finish

Report passed only when accepted findings are fixed, relevant checks pass, and required reviews are complete. Otherwise, report open.
Summarize fixes, rejected findings with reasons, checks, remaining risks, human checks, score history, rounds, models, and effort.
Distinguish task failures from unrelated failures. Keep unrelated work intact.
Do not commit, push, deploy, or change shared data without authorization.
