---
name: loop-code-review
description: >-
  Find and fix material code defects and DX issues through fresh independent
  reviewers, then validate the result with tests. Use for a repeated review/fix
  loop, not a one-pass review or visual QA.
---

You are the lead. Own the result. Use fresh reviewers to find and fix material
defects and DX issues introduced or worsened by this task. Be demanding about
correctness; keep the work, time, and token cost minimal, including rework.

Use subagents as your eyes and hands. Keep your context focused on requirements,
decisions, and coordination; leave implementation detail with them. Delegate code
inspection by default and assess their evidence against the requirements. Read
the relevant code yourself when that resolves uncertainty faster or more reliably.
Subagents write the fixes.

Identify all task-owned changes: staged, unstaged, and untracked, including
relevant hunks in shared files. Preserve unrelated work; respect project
instructions and user authorization. If there are no task changes, report that
and stop.

Start each pass with a fresh reviewer, without parent history or previous review
conclusions (Codex: `fork_turns: "none"`; Claude Code: a new `general-purpose`
agent). Use the current session's model for all subagents unless the user requests
another. Apply model choices through the spawning tool's supported settings;
report unavailable choices without silent substitution. Provide requirements,
repository path, scoped changes, these criteria, and check results. Tailor the
brief to the implementation's actual risks. Have the reviewer inspect the entire
scoped diff and new files, trace affected callers and dependencies, and report
material coverage gaps. Continue after finding issues.

Require the initial report to include, for each finding: what fails and under
which conditions, the affected requirement, relevant safeguards and why they fall
short, practical consequences, precise code references, and the smallest sufficient
fix with its effects on other behavior. Have reviewers distinguish verified facts
from assumptions and unknowns. Scale detail to the decision; do not require a
rigid report template. For each finding, outline a regression test when practical:
what triggers the bug and what correct behavior it should assert.

Accept only discrete, actionable findings introduced or worsened by this task,
with a concrete affected scenario supported by code or tests. For existing issues,
identify the added impact. Intentional changes are defects only if they violate
requirements.

Focus on meaningful impact: broken requirements, data loss or corruption, incorrect
payments, access violations, serious operational failures, or DX friction that
makes correct use or maintenance difficult. Weigh likelihood, severity, and fix
cost; rare but severe failures still matter. Skip speculative hardening,
refactoring preferences, stronger product guarantees, and tiny race windows
without practical consequences. Leave visual bugs and cosmetic polish to the user.

Before editing, each reviewer reports findings, material coverage gaps, and a
production readiness score from 1 to 10 with brief reasoning. Say "No findings."
when none qualify; keep unverified concerns separate. The score is advisory.
Completion requires no unresolved accepted findings, no material review coverage
gaps, and passing relevant checks. Ask neutral, open-ended follow-up questions
only where the report leaves a decision unresolved.

Unknown frequency does not mean low risk; do not invent percentages. Decide which
findings warrant a fix and which proposed fixes are overengineering. Reject
unsupported claims with reasons and keep unresolved material concerns visible.
Have the same reviewer fix accepted issues, then get a
fresh review of all current task changes. Any later change to the reviewed scope
requires another pass.

Defer routine test suites and project checks until code review passes. Run focused
checks earlier when needed to verify a finding or fix. After review passes, run
relevant tests and required checks. If a check fails, send a subagent to reproduce
the failure and report its root cause with evidence before editing. Assess the
diagnosis, then have that same agent fix the cause and add useful regression
coverage. Review the changes with a fresh reviewer, then rerun affected checks.
Repeat until review is clean and checks pass. Reuse valid results for unchanged
code; report unrelated failures without expanding scope. If the loop stalls,
identify the cause or report the blocker.

Prompt reviewers in English using standard engineering terminology. Do not read
subagent histories. Report fixes, validation, and remaining limitations in the
user's language.

Deliver an absolutely sufficient result: every requirement met, material defects
fixed, nothing unnecessary added.
