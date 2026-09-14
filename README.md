# loop-code-review-skill

Fresh independent reviews and fixes until the task is sufficiently reliable for
real use. Focuses on material systemic defects: broken core behavior, data integrity,
access control, payments, and serious operational failures. Excludes speculative
hardening and cosmetic work; browser checks require an explicit request.

A reviewer examines the task's active changes and reports concrete findings. The
lead evaluates them, then the same reviewer fixes accepted issues and checks the
result. A fresh reviewer checks the updated changes. Reports stay short, passing
checks are reused where applicable, and there is no fixed dispute procedure.

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

Use `/loop-code-review` in Claude Code. You can specify a reviewer model in your
request. The skill preserves unrelated changes and does not authorize commits,
pushes, or deployment.

## License

[MIT](LICENSE)
