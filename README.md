# loop-code-review-skill

An agent skill that turns code review into a loop with a verifiable exit condition. Works in Claude Code, OpenAI Codex CLI, and any runtime that supports the [Agent Skills](https://agentskills.io) format.

## [`loop-code-review`](loop-code-review/SKILL.md)

The skill reviews the current task's active git changes — not the whole dirty worktree — through fresh independent reviewer agents that start without the orchestrator's conversation history and rediscover the facts from the repository themselves.

Review is framed as a handoff: the reviewer must reconstruct what the change does, its control flow, its invariants, and its failure behavior, and a specific comprehension obstacle becomes an actionable maintainability finding. Alongside that it checks correctness, security, data integrity, test evidence, reuse of established project solutions, and architecture fit.

The loop fixes actionable findings, validates the touched surface, and repeats with a new reviewer until validation is green and the latest reviewer has no unresolved actionable findings and either scores the result at least **9.5/10** or explicitly reports no actionable findings. A high score never overrides an unresolved finding or red validation, and pass-limit exhaustion or stagnation is reported as an incomplete outcome rather than success.

## Install

Copy the skill directory into your agent's skills folder.

Claude Code:

```sh
git clone https://github.com/di-sukharev/loop-code-review-skill.git
mkdir -p ~/.claude/skills
cp -R loop-code-review-skill/loop-code-review ~/.claude/skills/
```

Codex CLI:

```sh
git clone https://github.com/di-sukharev/loop-code-review-skill.git
mkdir -p ~/.codex/skills
cp -R loop-code-review-skill/loop-code-review ~/.codex/skills/
```

Codex also reads `~/.agents/skills` and, per project, `.agents/skills`; Claude Code also reads a project's `.claude/skills`. Copy into whichever scope you want the skill in, and copy again to update.

Or hand the repository to your agent and ask it to install the skill into your skills directory — the layout is the standard one, so it can place the folder itself.

## Use

- `/loop-code-review` in Claude Code, `$loop-code-review` in Codex.
- Or just ask the agent to keep reviewing and fixing the current task's changes until an independent reviewer signs off.

## License

[MIT](LICENSE)
