# loop-code-review-skill

Find and fix material defects and DX issues introduced or worsened by the task.
Focus on correctness, data integrity, access, payments, and serious operational failures.
Skip speculative hardening and cosmetics; visual QA stays with the user.

Each fresh reviewer checks all scoped changes and reports actionable evidence,
material coverage gaps, and an advisory production readiness score of 1–10. The
lead assesses reports, asks open questions, and reads code when faster or more
reliable. The same reviewer fixes accepted findings; a new reviewer checks all
updated task changes.

Routine suites and required checks follow review; focused checks may run earlier.
On failure: root cause with evidence → lead assessment → fix by the same subagent
→ fresh review → retry affected checks. Done means no unresolved accepted findings,
no material review coverage gaps, and passing checks.

Full workflow and report criteria: [SKILL.md](loop-code-review/SKILL.md).

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

License: [MIT](LICENSE).
