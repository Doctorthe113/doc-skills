---
name: code-reviewer
description: Review a small set of local code changes. Use when reviewing an unstaged diff, a user-specified file, function, or commit range, or when dispatched as a feature-dev quality-review sub-task.
---

# Code Reviewer

## Scope

By default, review unstaged changes from `git diff`. The user may specify different files, a commit range, or a specific function to review.

## Required reading depth

For each function or class touched by the change:

1. Read the **entire file** containing it, not just the changed hunks.
2. Identify and read at least **one caller** of the changed code.
3. If the change touches shared state (caches, globals, locks, queues, modules with module-level data), trace at least one path that mutates and one path that reads that state.

## Core review responsibilities

Check every changed function against:

- **Project-guidelines** — explicit rules in `AGENTS.md` or `CLAUDE.md`, and Type and Naming when TypeScript is in the change set
- **Bugs** that will impact functionality
- **Code quality** — scope creep: speculative abstractions, configurability, or features that do not trace to the change's goal

### Type and Naming

When the change set includes TypeScript, apply every rule below as a project-guideline and record a candidate for each violation.

**Types**

- Infer routine types; annotate boundaries. Hoist a recurring shape (API response, config, state model) into a named `type` alias near the data source. Prefer `type` aliases over `interface`s unless the codebase already uses interfaces.
- `enum`, decorators, namespaces, parameter properties, conditional types, and mapped-type acrobatics only when the codebase already uses them or they delete more complexity than they add.
- A string literal union is the whole contract; flag an `enum` that is two things to keep in sync.
- When members must exist at runtime, lock a plain object with `as const` and derive the union from it.
- Flag a literal that widens to `string`, `boolean`, or `number[]` where `as const` would catch a typo.
- A second `?` in a ternary is a candidate: rewrite as `if` branches or a small named function.
- Use specific named input types and parser-backed boundary adapters for
  untrusted data, then return a specific typed shape. Record a violation for
  `any`, `unknown`, `object`, or unsafe dictionary types at a function
  boundary. Keep any unavoidable cast inside the adapter and require a
  comment documenting its checked invariant.
- Let inference type maps; when a map must cover every key of an `as const` object, derive them with `keyof typeof` — flag hand-written key unions that duplicate the source and go stale. Reserve `Record<string, T>` for genuinely dynamic keys.

**Naming**

- Descriptive, familiar names with units (`timeoutMs`, `maxRetries`, `sizeBytes`).
- Boolean predicates: `is` or `has` (`isValid`, `hasPermission`, `isArchived`).
- Construction and mutation: `create`, `get`, `update` (`createSession`, `getUser`, `updateStatus`).
- Flag vague names (`data`, `item`, `result`, `temp`, `handle`) when a specific one is available, and ornate names that do too much (`getUserProfileFromCacheIfFresh`).

## Multi-pass analysis

Do **two analysis passes**. The first pass is broad; the second is adversarial.

### Pass 1 — Broad scan

Walk through every changed function and check it against the three review categories above. Produce a candidate list with initial confidence scores.

### Pass 2 — Adversarial / edge-case pass

For every candidate from pass 1, AND for every changed function regardless of whether it raised a flag in pass 1, ask the following questions explicitly. Each one should produce either a "no issue here" line or a new candidate.

- What happens with empty / `None` / zero-length input?
- What happens with the maximum input size or boundary value?
- What happens if a downstream call fails or times out?
- Is there shared mutable state? Can two callers race?
- Does the cache (or memoization, or singleton) invalidate on every relevant change, or only some? Could it serve a stale value?
- Is there a comparison or check that uses a length, count, or hash where the underlying values can change while preserving that key? (Common cache-invalidation bug pattern.)
- For each new branch, is there a test that exercises it? If not, that is a candidate.
- Could an exception silently swallow a real failure?

For every candidate from either pass, write a one-sentence **reproduction scenario**: for a Type and Naming violation, name the identifier or type and the rule it breaks; for every other candidate, name a concrete input or condition triggering the failure. If you cannot write one, drop the candidate before scoring.

## Confidence scoring

Rate each potential issue on 0–100:

- **0** — Not confident at all. False positive, or pre-existing.
- **25** — Somewhat confident. Might be real, might be a false positive. If stylistic and not named in project rules or Type and Naming, lower.
- **50** — Moderately confident. Real issue, but possibly a nitpick or rare in practice. Not very important relative to the rest of the changes.
- **75** — Highly confident. Verified twice. Likely to be hit in practice. The existing approach is insufficient. Important and impacts functionality.
- **100** — Absolutely certain. Confirmed this will happen frequently, or a clear violation of an explicit project rule or Type and Naming rule. Direct evidence.

**Only report issues with confidence ≥ 80.**

## Output

Start by stating what you are reviewing (files, scope, branch).

For each high-confidence issue, provide:

- Clear description with confidence score
- File path and line number
- Specific project-guideline reference or bug explanation
- **Reproduction scenario** from the multi-pass analysis
- Concrete fix suggestion (and where to add a test if none exists)

Group issues by severity (Critical vs. Important).

If no high-confidence issues exist, confirm the code meets standards with a brief summary that lists the three categories you checked, Type and Naming when TypeScript was in scope, and the multi-pass questions you asked.

A thorough review of a non-trivial change set will take multiple minutes. If you finish in under a minute on a non-trivial change set, you have skipped the required reading depth or the multi-pass analysis.

---

**User arguments:** $ARGUMENTS
