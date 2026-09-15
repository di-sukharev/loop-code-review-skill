# loop-code-review-skill

Find and fix material defects and DX issues introduced or worsened by the task.
Meet requirements without speculative hardening or cosmetic polish; visual QA
stays with the user.

The lead delegates project-file inspection and checks, assessing reported evidence
without reading project files or subagent histories. It directly spawns each
reviewing subagent; there is no nested delegation or separate review coordinator.

Each fresh reviewing subagent receives original requirements and accepted
clarifications, all task-owned changes, check results, and risk-focused questions.
It inspects the full scope and affected dependencies, including relevant committed
changes, without previous review conclusions. Findings explain failing scenarios,
evidence, impact, reproduction or regression tests, and minimal fixes.

The lead asks open-ended questions only where material uncertainty remains.
The same reviewing subagent fixes accepted findings and verifies them; a fresh
subagent reviews all updated changes. Fast checks run before handoffs, remaining
required checks before completion. Done means no unresolved accepted findings
or material coverage gaps and passing checks. Pause only for needed human action.

Full workflow: [SKILL.md](loop-code-review/SKILL.md).

## Install

Ask your agent:

```text
Install this skill globally: https://github.com/di-sukharev/loop-code-review-skill
```

Or copy `loop-code-review` into `~/.codex/skills/` for Codex or
`~/.claude/skills/` for Claude Code. Requires subagent support.

## Use

```text
$loop-code-review
```

Use `/loop-code-review` in Claude Code. Keep the lead's model; select one model
for all subagents in natural language. Defaults: `gpt-5.6-luna` in Codex,
`sonnet` in Claude Code. When called by another skill, preserve its selected
subagent model and existing lead. Unavailable models are not silently substituted.

The skill preserves unrelated work and does not authorize commits, pushes,
deployment, or production data changes.

License: [MIT](LICENSE).
