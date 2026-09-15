# loop-code-review-skill

Fresh independent reviews of code, meaningful DX issues, and realistic edge cases,
followed by tests. Focuses on broken requirements, data integrity, access control,
payments, serious operational failures, and friction that makes correct use or
maintenance difficult. Excludes speculative hardening and cosmetic work; visual
QA stays with the user.

A reviewer inspects the entire scoped diff and new files, then reports concrete
findings, material coverage gaps, and a production readiness score from 1 to 10
as guidance for the lead. Findings must be actionable, supported by code or tests,
and introduced or worsened by the task. The lead decides which findings warrant a
fix and which proposed fixes are overengineering. The same reviewer fixes accepted
issues, and a fresh reviewer checks all updated task changes.
Reports include failure conditions, evidence, existing safeguards, impact, and a
minimal fix with its effects. For each finding, reviewers outline a regression
test when practical: what triggers the bug and the expected behavior.

The lead keeps its context for decisions and coordination, asking neutral,
open-ended questions where a decision remains unresolved and reading code when
that is faster or more reliable.

Focused checks can run during review to verify findings or fixes; routine suites
and required project checks run after review passes. On failure, a subagent
reports the root cause with evidence before editing; after the lead agrees, that
same agent fixes it. Review the changes and retry affected checks until both pass.
Completion requires no unresolved accepted findings, no material review coverage
gaps, and passing relevant checks.

## Install

Ask your agent:

```text
Install this skill globally: https://github.com/di-sukharev/loop-code-review-skill
```

Or copy `loop-code-review` into `~/.codex/skills/` for Codex or
`~/.claude/skills/` for Claude Code. Requires a runtime with subagent support.

## Use

```text
$loop-code-review
```

Use `/loop-code-review` in Claude Code. Subagents use the current session's model
unless you request another. The skill preserves unrelated changes and does not
authorize commits, pushes, or deployment.

## License

[MIT](LICENSE)
