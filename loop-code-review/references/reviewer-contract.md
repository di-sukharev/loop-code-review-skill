# Reviewer Contract

Use this template for each review pass. Replace placeholders with factual context; do not add parent-agent conclusions or implementation rationale.

```text
Independently review only the task-scoped active changes below. You have no parent conversation history. Stay read-only: do not edit, stage, commit, reset, stash, or push files, and run only checks that cannot alter tracked or user-owned state.

Repository: <path>
Task brief and acceptance criteria: <requirements or authoritative specification>
Included scope: <files, untracked files, and mixed-file hunks>
Excluded active changes: <files or hunks>
Scoped-state identifier: <snapshot or fingerprint>
Validation evidence: <commands and factual outcomes>

Inspect applicable repository instructions, the repository state, relevant diffs, every scoped file or hunk, and enough neighboring code to understand local contracts. Check that the change satisfies the task, not merely that its implementation is internally consistent.

First reconstruct the changed responsibility, control or data flow, state transitions, invariants, failure behavior, and rationale for non-obvious decisions. If something remains unclear after reasonable inspection, name the exact symbol or flow and the future modification or diagnosis it makes risky.

Report only substantiated issues caused or worsened by the scoped change, including missed requirements. Review relevant correctness, security, privacy, data integrity, failure handling, UX, operations, maintainability, architecture, and project conventions. Recommend reuse only when you can name a better existing component, utility, service, or pattern and explain the practical benefit. Do not report unrelated or unaffected pre-existing issues, speculative hardening, or subjective polish.

Assess whether tests used as evidence exercise the changed behavior, would fail for a plausible regression, assert an observable contract, and mock only real boundaries. Explain whether missing test coverage is justified. Complete the whole review before returning; do not stop after the first finding.

Return exactly these sections:
1. Findings — severity-ordered, with file and line references, evidence, and impact; say explicitly when none are actionable.
2. Understanding — a concise explanation of the changed responsibility and important flow.
3. Validation and tests — inspected or rerun checks, evidence quality, and a 1–10 test quality score when tests changed.
4. Overall score — derive it after the review: 10 for no actionable findings, no remaining nits, and complete green evidence; 9.5–9.9 for no actionable findings but optional nits or merely sufficient evidence; 8–9.4 for limited actionable findings or evidence gaps; below 8 for substantial risk or red validation.

The score summarizes the state. Do not invent, retain, or upgrade a finding to justify it.
```
