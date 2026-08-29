# Skills

This is my skills repo. Each skill lives in `skills/<name>/SKILL.md`.

## Skill inventory

| Skill | Invocation | Summary |
| --- | --- | --- |
| [`btw`](skills/btw/SKILL.md) | Manual | Answer a side question briefly without changing files or the active task. |
| [`code-reviewer`](skills/code-reviewer/SKILL.md) | Automatic | Review a small set of local changes for bugs, project-rule violations, and scope creep. It requires deep reading, two analysis passes, reproduction scenarios, and reports only findings with confidence of at least 80. |
| [`doc-writer`](skills/doc-writer/SKILL.md) | Automatic | Write useful code comments, numbered Markdown docs for features and refactors, and PRDs after requirements are clear. It includes the repository's comment guidance and a built-in prose standard. |
| [`simplify`](skills/simplify/SKILL.md) | Automatic | Reduce code complexity without changing behavior, with guidance for clarity, types, naming, comments, error handling, and verification. |
| [`thermo-nuclear-code-quality-review`](skills/thermo-nuclear-code-quality-review/SKILL.md) | Manual | Run an unusually strict maintainability review focused on structural simplification, abstraction quality, file size, and spaghetti-condition growth. |

## Code review skills

`code-reviewer` and `thermo-nuclear-code-quality-review` both produce code review feedback, but they answer different review needs.

| Concern | `code-reviewer` | `thermo-nuclear-code-quality-review` |
| --- | --- | --- |
| Primary question | Does this small change work, follow project rules, and avoid clear bugs or scope creep? | Does this implementation make the codebase materially harder to maintain, and is there a simpler structural design? |
| Review depth | Reads each touched file, at least one caller, and shared-state read/write paths when relevant. | Audits the branch broadly and challenges the surrounding architecture, boundaries, abstractions, and orchestration. |
| Analysis method | Uses a broad pass and an adversarial edge-case pass. It checks inputs, failures, races, cache invalidation, tests, and swallowed exceptions. | Looks aggressively for "code judo": removing branches, layers, special cases, wrappers, and other sources of structural complexity. |
| Reporting bar | Requires a concrete reproduction scenario and reports only findings scored at 80 or higher. | Prioritizes structural regressions and treats major maintainability problems as presumptive blockers. No confidence cutoff is specified. |
| Output | Findings include severity, file and line, evidence, reproduction scenario, and a concrete fix or test suggestion. | A prioritized, direct review with ambitious restructuring recommendations and an approval bar. |
| Invocation | Model-invoked, so another skill or the agent can reach it when a focused review is needed. | User-invoked, so it is reserved for an explicitly requested deep or especially strict audit. |

Use `code-reviewer` for a focused correctness and guideline review of a small change set. Use `thermo-nuclear-code-quality-review` when the main risk is architecture, maintainability, file growth, or accumulated complexity. The second review is intentionally harsher and may recommend restructuring code that is functionally correct.
