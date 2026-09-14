---
name: loop-code-review
description: Review and fix material systemic defects in active task changes through fresh independent reviewers. Use for a repeated review/fix loop, not a one-pass review or visual QA.
---

Review for sufficient reliability in real use with minimal time and token cost.
Write all subagent prompts in English using standard engineering terminology.
Keep user-facing communication in the user's language.
Focus on material systemic defects introduced or worsened by this task: broken
core behavior or requirements, data loss or corruption, meaningful access violations,
incorrect payments, and serious operational failures. Weigh likelihood, impact, and
fix cost together. Require a concrete scenario and meaningful consequences.
Do not invent stronger product guarantees or report speculative hardening,
refactoring preferences, or tiny race windows without a convincing practical risk.
Rare but severe failures still matter.

Leave visual bugs, styling, and cosmetic polish to the user's browser review.
Do not launch a browser or run browser tests unless explicitly requested.

Identify the task-owned staged, unstaged, and untracked changes, including relevant
hunks in shared files. Preserve unrelated work and respect project instructions
and user authorization; this skill does not authorize Git publication or deployment.
If there are no task changes, report that and stop.

Start a fresh reviewer without parent history or previous review conclusions
(`fork_turns: "none"` in Codex). Honor any requested model through the spawning tool.
Give it these review criteria, requirements, repository path, scoped changes, and
available check results. Ask it to inspect the full scope and relevant surrounding code,
then report only substantiated issues with location, impact, and evidence. No
mandatory architecture retelling, report template, or agent-history reading.

The reviewer reports before editing. Assess its findings, resolve uncertainty with
focused inspection or follow-up, and ask that same reviewer to fix the issues you
agree with and verify them. Reject unsupported findings with evidence; leave real
unresolved concerns visible. Choose the simplest useful way to resolve disputes.
After fixes, start another fresh reviewer for all current task changes. A review
cannot clear relevant changes made after it inspected them.

Use the smallest meaningful checks and regression coverage for the behavior at risk.
Reuse passing evidence for unchanged code; rerun affected checks after fixes. Report
unrelated failures without expanding the task. Finish when a fresh review finds no
remaining material issues and relevant checks support the result. Do not present
blocked or unverified behavior as passing. If the loop stalls, reconsider the cause
or report the blocker instead of repeating it indefinitely. Briefly report the
result, fixes, checks, and any remaining limitations.
