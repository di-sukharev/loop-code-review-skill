---
name: loop-code-review
description: >-
  Find and fix material code defects and DX issues through fresh reviewing
  subagents and regression checks. Use for a repeated review/fix loop,
  not a one-pass review or visual QA.
---

You are the lead. Own the result; minimize time and token cost, including rework.
When called by another skill, keep the existing lead and its selected subagent
model; do not spawn a separate review coordinator.

Delegate all project-file inspection, implementation, and checks to subagents.
Do not read project files or subagent histories; keep your context on requirements,
decisions, and reported evidence. Directly spawn every subagent; they must not
delegate. Give fresh subagents necessary context without parent history
(Codex: `fork_turns: "none"`; Claude Code: a fresh `general-purpose` agent).

Keep the lead's model. Use the user-selected model for all subagents; otherwise
use `gpt-5.6-luna` in Codex or `sonnet` in Claude Code. Apply the choice on every
spawn; report unavailable models without substitution.

State requirements and acceptance decisions directly. Ask focused, open-ended
questions only to resolve material uncertainty; have subagents investigate and
support conclusions with evidence. Do not prescribe code-level implementation.

Meet all requirements with the simplest sufficient implementation and UX/UI.
Leave optional refinements to follow-up requests; never defer required behavior
as polish. Respect project instructions, explicit user overrides, and unrelated
work. Leave visual QA to the user; do not launch browsers or browser tests unless
explicitly requested.

Start each round with a fresh reviewing subagent. Provide original requirements
and accepted clarifications, repository path, the full task scope, check results,
and questions targeting known risks, without previous review conclusions.
Have it inspect all task-owned changes and affected dependencies, including staged,
unstaged, untracked, and relevant committed changes. Review interactions with
earlier work; the lead's questions must not limit the search. Continue after
finding an issue.

Before editing, require each finding's failing scenario, violated requirement,
precise code references, impact, insufficient safeguards, reproduction or regression
test outline, and smallest fix with its side effects. Separate verified facts,
assumptions, and material coverage gaps. Scale detail to the decision without
a rigid template; say "No findings." when none qualify.

Accept concrete, evidenced defects introduced or worsened by the task, including
material DX friction. For existing defects, require the added impact; intentional
changes qualify only if they violate requirements. Weigh likelihood, severity,
and fix cost without inventing probabilities. Reject unsupported claims,
speculative hardening, refactoring preferences, and scope expansion with reasons.
Keep unresolved material concerns visible.

Have the same reviewing subagent fix accepted findings. Where practical, first
add a regression test that demonstrates the defect, then fix it and verify
the test passes. Start a fresh full review after fixes or any later change
to the reviewed scope. Finish when no unresolved accepted findings or material
coverage gaps remain and required checks pass.

If the reviewing subagent finds no task-owned changes, report that and finish.

Have implementing and fixing subagents run relevant fast checks before handoffs
and remaining required checks before completion. Route failures through evidence
of the cause, lead assessment, fixes by the diagnosing subagent, fresh review
of resulting changes, and affected checks. Reuse valid results until later changes
invalidate them; report unrelated failures without expanding scope.

Have subagents resolve obstacles within the current task's scope. Pause only
when progress requires human action; state exactly what is needed. This skill
does not authorize commits, pushes, deployment, or production data changes.

Prompt subagents in English using standard engineering terminology. Finish briefly
in the user's language with results, validation evidence, and remaining limitations.
Never present unverified work as complete.
