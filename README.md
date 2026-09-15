# loop-code-review

Fix material defects and DX issues introduced or worsened by a task:
fresh reviewing subagent → evidenced findings → lead assessment →
fixes by the same subagent → fresh full review.
Keep UX thoughtful, simple, and elegant, and UI minimal: no unnecessary clicks,
modals, or controls.

The lead directly coordinates subagents without reading project files or their
histories; open-ended questions resolve material uncertainty only.
The lead owns quality and delivery time. Each step advances the task or resolves
uncertainty. Each round uses original requirements, accepted clarifications, check results, and known risks,
not previous review conclusions. Review current task changes and affected code.
Findings cover failing scenarios, evidence,
impact, reproduction/tests, and minimal fixes.

Before handoffs, run checks useful for the changes and those required by the project;
skip unrelated or redundant checks. Finish with accepted findings and material
coverage gaps resolved and applicable checks passing. Pause only for human action.
Preserve unrelated work; skip speculative hardening and cosmetics. Subagents work
from code; they do not open browsers or click through the app for visual inspection.
The user checks the visuals. No authorization for commits, pushes, deployment, or production
data changes. [Full protocol](loop-code-review/SKILL.md).

## Install and use

Ask your agent:

```text
Install this skill globally: https://github.com/di-sukharev/loop-code-review-skill
```

Or copy `loop-code-review` into `~/.codex/skills/` or `~/.claude/skills/`.
Requires subagents.

Run `$loop-code-review` in Codex or `/loop-code-review` in Claude Code.
Choose one subagent model in natural language; defaults: `gpt-5.6-luna` in Codex,
`sonnet` in Claude Code. When called by another skill, use its selected subagent
model; the calling agent coordinates the review. No silent model substitution.

[MIT](LICENSE).
