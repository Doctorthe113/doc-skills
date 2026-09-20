---
name: simplify
description: Simplify changed or named working code when a cleanup is requested for avoidable complexity, duplication, nesting, types, names, or comments. Preserve observable behavior.
---

# Simplify

Make the requested code easier to read, change, and debug. Prefer a smaller
mental model, not fewer lines. Preserve inputs, outputs, side effects, ordering,
performance constraints, and error behavior.

## Workflow

### 1. Set the boundary

Use the scope named by the user or calling agent. If none is named, use only
the files changed for the current task. Do not clean nearby code unless it is
required to simplify that scope.

Read the target code, its callers, nearby conventions, and its tests. Check
history only when the reason for unusual code is still unclear.

The step is complete when you can state:

- what behavior the code must preserve;
- which files and interfaces are in scope;
- which tests or checks cover that behavior.

If you cannot establish those facts, leave the uncertain code unchanged and
report the uncertainty.

### 2. Find high-confidence simplifications

Use this order. Stop when no change is a clear improvement.

| Priority | Look for | Prefer |
| --- | --- | --- |
| Delete | Dead branches, unused values, pass-through wrappers, speculative options | Remove verified dead code and empty layers |
| Straighten | Deep nesting, repeated checks, long mixed-purpose functions | Guard clauses and small single-purpose functions |
| Reuse | Logic with the same meaning in multiple places | An existing canonical helper, then one shared helper |
| Clarify | Misleading names, hidden units, dense expressions, nested ternaries | Familiar names, explicit branches, useful intermediate values |
| Tighten types | Duplicated shapes, unsafe casts, types wider than the data | Inferred local types and named boundary contracts |
| Prune comments | Comments that restate code or describe old behavior | Intent, constraints, and non-obvious reasons only |

Similar syntax is not enough reason to extract a helper. Share logic only when
the copies must change together. Keep an abstraction when a current caller,
test seam, platform constraint, or measured performance need gives it value.

Reuse code already in the repository before the standard library, then use an
installed dependency. Write the minimum missing code only after those checks.
A simplification does not add a dependency.

### 3. Make the smallest coherent edit

Follow local conventions over preferences in this skill. Keep public contracts
stable. Keep error behavior and user-facing messages stable. If a behavior or
error fix is needed, separate it from the simplification and obtain the scope
required for that change.

Use these rules while editing:

- Check invalid states first when guard clauses make the valid path clearer.
- Keep functions single-purpose.
- Improve a name only when it is vague or misleading. Match local vocabulary,
  and include units such as `timeoutMs` when they prevent ambiguity.
- Replace a nested ternary with branches or a named function. Keep a single
  ternary when it is the clearest form.
- Remove redundant type syntax. Keep explicit types at reused or untrusted
  boundaries. Do not spread `any` or unchecked casts into business logic.
- Follow repository comment rules. Keep comments that explain intent or a
  constraint. Remove comments that translate the next line of code.

Do not replace an established component, abstraction, or algorithm only
because another form is shorter. Compare behavior, portability, testability,
and performance before changing it.

### 4. Verify and review

Run the narrowest relevant checks after each coherent edit or small batch.
Then run broader checks when the change affects shared code or public
boundaries. Do not weaken test assertions to make the refactor pass.

Review the final diff and confirm all of these conditions:

- Observable behavior is unchanged.
- The diff contains no unrelated cleanup.
- Each remaining abstraction has a present use.
- Repeated logic is shared only when it has one meaning.
- Names, types, branches, and comments reduce the reader's mental work.
- Tests, type checks, lint, and build checks relevant to the scope pass.

If the result is not clearly easier to understand, revert that simplification.
Report the changed files, checks run, and any limits in verification.

## Short examples

Use a guard clause to keep the valid path straight:

```python
if data is None:
    raise TypeError("Data is required")
if not data.is_valid():
    raise ValueError("Data is invalid")
return process(data)
```

Give a dense decision a name:

```typescript
function getStatusLabel(item: Item): string {
  if (item.isNew) return 'New';
  if (item.isArchived) return 'Archived';
  return 'Active';
}
```

Keep two similar operations separate when they have different reasons to
change. Extract one helper only when both operations implement the same rule.
