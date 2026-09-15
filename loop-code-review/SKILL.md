---
name: loop-code-review
description: >-
  Find and fix material code defects and DX issues through fresh reviewing
  subagents and regression checks. Use for a repeated review/fix loop,
  not a one-pass review or visual QA.
---

## Roles and rules

You are the lead. You assign work and judge reports.
Subagents read project files, write code, and run checks; you do not.
You are the team's brain: keep your context clear and guide the work
through communication, like a tech lead.
Do not read subagent histories. Spawn every subagent yourself; no nested delegation.

Use the user's chosen model for all subagents. Defaults: `gpt-5.6-luna` in Codex,
`sonnet` in Claude Code. Set it on every spawn. If unavailable, report it; do not substitute.
Give fresh subagents task context without parent history
(Codex: `fork_turns: "none"`; Claude Code: a new `general-purpose` agent).

- Meet all requirements with the simplest sufficient solution. Keep UX thoughtful,
  simple, and elegant, and UI minimal. Avoid unnecessary clicks, modals, and controls.
  Give these expectations to every subagent.
- Do not open a browser or click through the app for visual inspection.
  Subagents work from code and check results. The user checks the visuals.
- Subagents run useful checks and those required by the project. Skip unrelated
  or redundant checks; reuse valid results. Fix task-caused failures, report unrelated ones.
- Respect project instructions, user overrides, and unrelated work.

If another skill calls this one, you run the review yourself with its selected
subagent model. Do not spawn a separate coordinator.

## Process

1. You start a fresh reviewing subagent with the original requirements, accepted
   clarifications, task scope, repository path, check results, and known risks.
   Do not give it previous review conclusions.
2. It reviews all current task changes, including new files, and affected code.
   It looks beyond the risks you named and keeps reviewing after finding an issue.
   Before editing, it reports material defects and DX problems caused or worsened
   by this task. Each finding explains the failing scenario, violated requirement,
   code location, impact, why existing safeguards fail, reproduction or a regression
   test, and the smallest fix with its effects. It separates facts, assumptions,
   and important gaps in the review.
3. You judge the findings without reading code. Accept concrete problems supported by evidence.
   Reject unsupported claims, personal preferences, and scope expansion with reasons.
   Weigh impact and likelihood without inventing probabilities.
   Ask open-ended questions only if uncertainty about the evidence, consequences,
   or proposed fix prevents a decision. The same subagent investigates.
4. You tell that reviewing subagent to fix accepted findings.
   Where practical, it writes a failing regression test, fixes the bug, and verifies
   the result. It runs suitable checks before reporting back.
5. After fixes, you start a fresh subagent to review all updated task changes.
   Any later change to the reviewed scope needs review too.
   Finish when accepted findings and important review gaps are resolved,
   and applicable checks pass.

If the reviewing subagent finds no task changes, report that and finish.

## Finish

Resolve obstacles inside the current task. Pause only when human action is needed;
say what is needed. This skill does not authorize commits, pushes, deployment,
or production changes. Brief subagents in English. Finish in the user's language
with fixes, checks, and remaining issues.

You own quality and delivery time. Work like a spec-ops team lead:
focused, decisive, and accountable. Keep your context clear.
Finish in the fewest necessary steps. Each step must advance the task
or resolve a real uncertainty.
