---
name: loop-code-review
description: >-
  Find and fix material code defects and DX issues through fresh reviewing
  subagents and regression checks. Use for a repeated review/fix loop,
  not a one-pass review or visual QA.
---

You are the lead. Delegate project-file inspection, implementation, and checks
to subagents; do not read project files or their histories. Decide from reported
evidence. Directly spawn all subagents; they must not delegate. Give fresh
subagents necessary task context without parent history (Codex:
`fork_turns: "none"`; Claude Code: a fresh `general-purpose` agent).

Keep the lead's model. Accept a subagent model in ordinary user text; otherwise
use `gpt-5.6-luna` in Codex or `sonnet` in Claude Code. Use it for implementation
and review on every spawn; report unavailable models without substitution.

When called by another skill, keep its existing lead and selected subagent model.

Meet all requirements with the simplest sufficient solution; leave optional
refinements for later. Respect project instructions, user overrides, and unrelated
work. Follow project testing instructions; leave visual acceptance to the user.

Start each review round with a fresh reviewing subagent. Give it the original
requirements, accepted clarifications, repository path, task scope, check results,
and known risks, without previous review conclusions. Have it inspect all current
task changes and affected code; its review must not be limited to the lead's concerns.

Before editing, require concrete findings: failing scenario, violated requirement,
code references, impact, why existing safeguards fail, reproduction or a regression
test, and the smallest fix with its effects on other behavior. Separate facts,
assumptions, and material coverage gaps; continue after finding an issue.

Accept evidenced, material defects introduced or worsened by the task, including
DX problems. Weigh impact and likelihood without inventing probabilities. Reject
unsupported claims, refactoring preferences, and scope expansion with reasons.
Use open-ended questions only where uncertainty about evidence, consequences,
or a proposed fix prevents a decision; have the subagent investigate.

Have the same reviewing subagent fix accepted findings. Where practical, demonstrate
the defect with a failing regression test before fixing it. After fixes and relevant
checks, start a fresh full review. Later changes to the reviewed scope also require
review. Finish when accepted findings and material coverage gaps are resolved
and applicable checks pass.

Before handing off implementation or fixes, have the subagent run checks that
meaningfully verify the changes and any checks required by project instructions.
Skip unrelated or redundant checks; reuse results that remain valid. Fix failures
caused by the changes, and report unrelated failures without expanding scope.

If the reviewing subagent finds no task changes, report that and finish.
Resolve obstacles within the current task; pause only for needed human action.
This skill does not authorize commits, pushes, deployment, or production changes.

Prompt subagents in English. Finish briefly in the user's language with results,
validation evidence, remaining issues, and delivery status.
