---
name: loop-code-review
description: >-
  Fresh reviewer sub-agents find and fix material problems in code changes through independent review rounds. Verify task fulfillment and find material bugs, regressions, and risks. Use this skill before committing changes.
---

Follow project rules. Use English with the sub-agents and the user's language with the user.

## Scope and reviewer

In every round, review the full diff against HEAD and untracked files. Use a different scope when the user specifies one. Do not change staging.

Start each round with a fresh sub-agent that has no chat history. Give it the repository path, requirements, completion criteria, and the review criteria below.
Do not pass author explanations, implementation plans, or earlier review findings and scores.

Follow the user's model and effort choices. Otherwise, choose by risk:

- For high-risk changes, use the strongest available Sol or Opus model at high effort. High risk includes stored data, migrations, security, concurrency, external contracts, and failures across multiple modules.
- For other changes, use a capable general model at medium effort.

## Review loop

1. Ask the reviewer to inspect the changes and affected code, then report without changing code. Each finding must include a location, a realistic failure scenario, its impact, and supporting evidence. State any assumptions. Also ask for an implementation score from 1 to 10 based on task fulfillment and material issues, with a short reason. Record each round's score.
2. Accept findings for unmet requirements, bugs, regressions, data safety or security issues, build or deployment failures, material performance problems, and unnecessary migrations.
3. Ask the same reviewer to fix only the accepted findings and run the relevant checks. Use the simplest sufficient fix and follow the testing rules below. Reuse project code and widely used, maintained libraries when they simplify an accepted fix. Use the project's ORM when it supports the operation. Use raw SQL only for a concrete functional or performance reason.
4. After fixes, start another round to review the updated code. Stop when a round has no accepted findings and no material risk needs further review.

Use at most five rounds unless the user specifies another limit.

## Review criteria

- Check changed behavior against the requirements and completion criteria. Consider relevant cases: invalid input, failures, empty data, retries, and concurrent operations.
- Check that constraints and transactions prevent invalid or partially saved data. Report duplicate business rules or stored values when they can produce conflicting results.
- If the implementation adds a migration, verify that the existing data schema cannot meet the requirements and the migration is necessary. Check that it preserves existing data.
- Check validation, access checks, and query parameterization in changed input and data flows. Look for exposed secrets.
- Check configuration, environment variables, migration execution, and external service failures in the target environment. Failures must be visible to callers or operators.
- Check compatibility with existing callers and stored data unless the task explicitly changes those contracts.

## Testing

Tests must catch meaningful behavioral failures. Do not add unit tests for their own sake, and do not test cosmetic details.
Prefer simple integration tests against real endpoints and a test database. Tests must exercise the real code under test and remain isolated when run together.

When a regression test is useful and practical, add it before fixing the bug and confirm that it fails for the reported reason.

## Finish

Report `passed` only when all accepted findings are fixed, relevant checks pass, and required independent reviews are complete.
Otherwise, report `open`, including when the round limit leaves work unfinished.

Summarize fixes, rejected findings and reasons, checks run, unresolved risks, and any checks left for the user.
Include the score history, number of rounds, and models and effort used. Distinguish failures caused by the task from unrelated failures.

Keep unrelated work intact. Do not commit, push, deploy, or change shared data without authorization.
