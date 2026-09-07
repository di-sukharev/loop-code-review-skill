# Reviewer Contract

Fill this template for each review pass.

```text
Independently review the scoped changes below. Stay read-only: do not edit, stage, commit, reset, stash, or push files. Run only checks that cannot alter tracked or user-owned state.

Repository: <path>
Task brief and acceptance criteria: <requirements or authoritative specification>
Included scope: <files, untracked files, and mixed-file hunks>
Excluded active changes: <files or hunks>
Scoped-state identifier: <snapshot or fingerprint>
Validation evidence: <commands and factual outcomes>

Inspect applicable repository instructions, repository state, relevant diffs, every scoped file or hunk, and neighboring code needed to understand local contracts. Verify the change against the task requirements.

Reconstruct the changed responsibility, control or data flow, state transitions, invariants, failure behavior, and non-obvious rationale. For anything still unclear after reasonable inspection, identify the exact symbol or flow and the future modification or diagnosis it makes risky.

Report only substantiated issues caused or worsened by the change, including missed requirements. Check relevant correctness, security, privacy, data integrity, failure handling, UX, operations, maintainability, architecture, and project conventions. Reuse recommendations must name a better existing solution and its practical benefit. Exclude unaffected pre-existing issues, speculative refactors, optional hardening, and subjective polish.

Assess whether tests exercise changed behavior, catch plausible regressions, assert observable contracts, and mock only real boundaries. Explain whether coverage gaps are justified. Run a focused reproduction when a concrete concern needs proof; rerun a broad green suite only for an evidence concern that focused inspection cannot resolve.

Finish the whole review before replying. Reply concisely, as to a colleague: explain the changed responsibility and key flow, report all findings in severity order with file/line references, evidence, and impact, and summarize inspected or rerun checks and test-evidence quality. State explicitly when none are actionable. Use whatever structure makes this clear.
```
