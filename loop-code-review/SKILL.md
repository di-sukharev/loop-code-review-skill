---
name: loop-code-review
description: >-
  Fresh reviewer agents find and fix bugs, data model flaws, security gaps,
  leftovers, and needless complexity before release. Use when the user asks for
  loop-code-review or another skill calls it.
---

## Goal

A fresh reviewer without the chat history finds the mistakes that the author misses.
If no rule fits a case, aim for a production-ready change at the lowest total cost. Count retries and your own turns in the cost.

## Rules

- You are the lead. Do not review or edit code. To verify a claim, read only the code that it cites. For more, ask the agent.
- Lead the review yourself, even if another skill calls this skill.
- Follow project rules. Write to agents in English and to the user in the user's language.

## Risk

Use the calling skill's risk. If there is none, choose it.
High risk: migrations, stored data, security, concurrency, APIs or data formats that code outside this repository uses, or an unclear failure across components. Other tasks have normal risk.

## Agents

- Claude Code: `subagent_type: general-purpose`, `model: opus`.
- Codex: Sol, `reasoning_effort: medium` (`high` at high risk), `fork_turns: "none"`. Use the longest `wait_agent` timeout.
- The user's model and effort choices override these settings.
- Send each new agent the Reviewer brief section verbatim, then the task context. Do not poll agents.
- As a Claude Code subagent, start agents in the foreground. Do not continue an agent, because its replies do not reach you. Start a new one with the same settings. Give it the brief, the task context, the last report, and one request only.
- If checks still fail after two fix attempts, escalate: replace the reviewer with a stronger one. Give it the brief, the task context, the accepted findings, the changed ranges, and the check results. Tell it to fix, not to review. This is not a new round.
- Escalate in this order: `high` effort, then a stronger model. Claude Code can only change the model. If no step is left or an agent cannot start, stop and report. The status is open.

## Steps

If there are no task changes, report this and finish.

1. Start a reviewer for a full round.
2. Judge the findings. Accept material problems with evidence. Reject the others with a reason, but not only because they contradict your earlier decisions. An important review gap is a finding. If the evidence is weak, verify it. If you accept nothing, go to step 5.
3. Tell the same reviewer to fix the accepted findings and run the relevant checks.
4. Take the first row that matches the fix report.

   | Fix report | Next action |
   | --- | --- |
   | Only tests or docs changed | Step 5 |
   | Normal risk, all fixes trivial | Step 5 |
   | Third round done | Step 5. Report the last fixes as not re-reviewed. |
   | Normal risk | New reviewer, delta round, step 2 |
   | High risk | New reviewer, full round, step 2 |

   A trivial fix is local and has a regression test or only deletes unused code, commented-out code, or debug output. A fix to a contract, shared code, a schema, a migration, or stored data is never trivial.
5. Make sure that the last report shows that all applicable checks pass. A check failure that the task does not cause is not applicable. Report it as an unresolved issue. Return other failed checks to the same reviewer, then go to step 4.

The task context is the requirements, accepted clarifications, Definition of Done (DoD), repository path, assigned changes, check results, and known risks.
A full round gets all task changes. A delta round gets the ranges that the last fix report changed.
Do not add earlier review conclusions or the implementer's reasoning.

## Finish

The status is passed if all applicable checks pass and no accepted finding is unfixed. At high risk, each fix to production code also needs a re-review. Otherwise, the status is open.
Report the status, fixes, rejected findings with reasons, checks, human checks, unresolved issues, and the last score.
Also report the cost: rounds, models, and efforts.
Pause only for a necessary human action, and name it. Human checks do not pause the work or change the status.

## Reviewer brief

Review this change as if you release it to production and answer for it.

### Work

- Examine the assigned changes and the code that they affect. Look beyond the checklist and the known risks.
- Search before you read. Read only the ranges that you need.
- Report the findings and stop. Fix only the findings that the lead accepts.
- Fix the root cause with the simplest sufficient change. Where possible, first write a failing regression test.
- Reuse valid check results. Run only the checks that are missing, stale, or affected by your fixes.
- If a check still fails after two fix attempts, stop and report.

### Checklist

Apply each item only to the code that the assigned changes touch.

1. Requirements: the change does what the requirements and the DoD ask, and no more. It handles failures, empty data, and repeated actions.
2. Data: the model fits the domain, with one source of truth for each fact. Each table, field, and index has a current need. Types and constraints are as strict as the data allows. Migrations keep existing data correct.
3. Simplicity: the design is the simplest that meets the requirements, and a new developer can understand it. Each abstraction, option, layer, and dependency has a current need. The change reuses project code.
4. Leftovers: no debug output, commented-out code, temporary files, file copies, placeholder data, or unused code. Replaced code, data, and fallbacks are gone, unless existing callers, clients, or stored data need them.
5. Security: input from users and external systems is validated. Access checks protect data. Secrets stay out of code, logs, and responses.
6. Production: errors are visible. Configuration, environment variables, and migrations work outside a developer's computer. Report what only a human can verify as human checks, not as findings.
7. Tests: each changed behavior with a realistic regression risk has a test that fails when it breaks. Tests do not mock the code under test.

### Findings

Report only material problems that the task causes or makes worse. A problem is material if it can cause wrong behavior, lost or exposed data, or a failed release. Code that the next change must work around or edit in two places is also material. So are leftovers, needless complexity, and data model flaws.
Do not report style, preferences, work outside the task, or edge cases without a realistic failure.

For each finding, give:

- the location and the failing scenario;
- the violated requirement or checklist item, and the impact;
- the root cause and its evidence, with assumptions marked;
- the simplest fix and its side effects;
- a regression test or reproduction steps.

### Limits

- Change only the files that your work needs. Do not stash, reset, or check out files.
- Do not use a browser for visual checks.
- Do not commit, push, create branches, start agents, deploy, or write to shared or production data.

### Report

Report briefly and in English, with `file:line` references. Do not paste code or full logs.

- Review report: findings, important review gaps, check status, human checks, and a readiness score from 1 to 10 with one reason.
- Fix report: changed files and line ranges, and check results. For each fix, say if it has a regression test, only deletes code, or changes a contract or shared code.
