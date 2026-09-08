---
name: simplify
description: Simplify code for clarity without changing behavior, and apply type, naming, comment, and error-handling conventions. Use when refactoring working code that is harder to read, maintain, or extend than it should be, when reviewing code with accumulated complexity, or when cleaning up comments or error handling.
---

# Simplify

## Overview

Simplify code by reducing complexity while preserving exact behavior. The goal is not fewer lines — it's code that is easier to read, understand, modify, and debug. Comments are in scope: add the ones that carry context the code cannot express, and delete the ones that restate it.

## When to Use

- After a feature is working and tests pass, but the implementation feels heavier than it needs to be
- During code review when readability or complexity issues are flagged
- When you encounter deeply nested logic, long functions, or unclear names
- When refactoring code written under time pressure
- When consolidating related logic scattered across files
- After merging changes that introduced duplication or inconsistency

**When NOT to use:**

- Code is already clean and readable — don't simplify for the sake of it
- You don't understand what the code does yet — comprehend before you simplify
- The code is performance-critical and the "simpler" version would be measurably slower
- You're about to rewrite the module entirely — simplifying throwaway code wastes effort

## The Principles

### 1. Preserve Behavior Exactly

Don't change what the code does — only how it expresses it. All inputs, outputs, side effects, error behavior, and edge cases must remain identical, with one sanctioned exception: error handling may be improved to follow the conventions in Error Handling below. If you're not sure a simplification preserves behavior, don't make it.

```
ASK BEFORE EVERY CHANGE:
→ Does this produce the same output for every input?
→ Does this maintain the same error behavior? (Error Handling improvements are the one exception)
→ Does this preserve the same side effects and ordering?
→ Do all existing tests still pass without modification?
```

### 2. Follow Project Conventions

Simplification means making code more consistent with the codebase, not imposing external preferences. Before simplifying:

```
1. Read AGENTS.md / project conventions
2. Study how neighboring code handles similar patterns
3. Match the project's style for:
   - Import ordering and module system
   - Function declaration style
   - Naming conventions
   - Error handling patterns
   - Type annotation depth
```

### 3. Prefer Clarity Over Cleverness

Explicit code is better than compact code when the compact version requires a mental pause to parse.

```typescript
// UNCLEAR: Dense ternary chain
const label = isNew ? 'New' : isUpdated ? 'Updated' : isArchived ? 'Archived' : 'Active';

// CLEAR: Readable mapping
function getStatusLabel(item: Item): string {
  if (item.isNew) return 'New';
  if (item.isUpdated) return 'Updated';
  if (item.isArchived) return 'Archived';
  return 'Active';
}
```

```typescript
// UNCLEAR: Chained reduces with inline logic
const result = items.reduce((acc, item) => ({
  ...acc,
  [item.id]: { ...acc[item.id], count: (acc[item.id]?.count ?? 0) + 1 }
}), {});

// CLEAR: Named intermediate step
const countById = new Map<string, number>();
for (const item of items) {
  countById.set(item.id, (countById.get(item.id) ?? 0) + 1);
}
```

### 4. Maintain Balance

Simplification has a failure mode: over-simplification. Watch for these traps:

- **Inlining too aggressively** — removing a helper that gave a concept a name makes the call site harder to read
- **Combining unrelated logic** — two simple functions merged into one complex function is not simpler
- **Removing "unnecessary" abstraction** — some abstractions exist for extensibility or testability, not complexity. That protection counts only when something exercises the abstraction today — a caller, or a mock in tests; a seam nothing uses is speculative, and Step 2's Necessity table applies.

When a simplification deliberately keeps a weaker implementation with a known
ceiling (an O(n²) scan, a naive heuristic, an in-memory cache), leave a
comment naming the ceiling and the upgrade path — an undocumented corner-cut
is a bug waiting for a ticket.

### 5. Stay in Scope

Simplify only within the scope the user or another agent defines — whether that is recently modified files or older code the task names. Work outside that scope creates noise in diffs and risks unintended regressions.

### 6. Reuse Genuinely Repeated Logic (DRY)

When the same meaning appears in two or more places, extract one helper or
module with a descriptive name and call it from both; prefer the codebase's
existing canonical helper over a new bespoke one. "Genuinely" does the work:
extract only logic that must change together. Similar-looking code with
different reasons to change is not duplication, and a speculative extraction
no call site uses is over-abstraction — see Maintain Balance. Step 2's
redundancy table lists the signals.

### 7. Delete Over Add

The strongest simplification is code removed, not code rewritten. Hunt for
what no longer needs to exist: exports, props, options, and branches nothing
references; speculative generality ("for later" wrappers, config knobs
nothing sets); boilerplate nobody asked for. Prefer the boring approach over
the clever one, and the fewest files that work — deletion beats refactoring,
and refactoring beats rewriting.

### 8. Reuse Before Writing

When a simplification seems to need new code — a parser, a clamp, a
formatter — stop at the first source that already provides it:

```
1. Already in this codebase?  → reuse it, don't rewrite it
2. Standard library?          → use it
3. Installed dependency?      → use it
4. Only then: write the minimum that works
```

Never add a new dependency during a simplification. Do not promote native
browser elements above this ladder either: a native date picker, select, or
dialog behaves differently browser to browser, so swapping an established
external component for a native one buys inconsistency, not simplicity. The
ladder ends at the installed dependency, not the platform.

### 9. Fail Early (Negative-Space Programming)

Check the undefined, invalid, and unauthorized states first and return or
throw immediately, so the valid path comes last and stays straight. Guard
clauses replace nested if/else: a function reads as a list of "if this is
wrong, stop" checks followed by the happy path. The Python example under
Language-Specific Guidance shows the shape.

## Type and Naming Conventions

These conventions are the direction every simplification should point. Each
rule names the simpler form; a change that moves toward it is simplification,
and a change that moves away needs a reason.

### Types

**Infer types from data sources and APIs.** Let inference handle the routine
work and annotate the boundaries instead. When a stable or complex shape
recurs — an API response, a config object, a state model — hoist it into a
named `type` alias near the data source so every caller shares one contract.
Prefer `type` aliases over `interface`s; reach for an `interface` only when it
measurably improves the codebase or an interface-exclusive feature such as
`extends` is genuinely needed — treat those as a last resort.

**Use TypeScript-only syntax sparingly for readable code.** `enum`,
decorators, namespaces, parameter properties, conditional types, and
mapped-type acrobatics are features, not defaults. Reach for them only when
the codebase already uses them or they delete more complexity than they add.
Plain values, plain functions, and plain branches read best.

**Prefer string literals and explicit branches.** A string literal union is
the whole contract; an `enum` creates a type and a value object that must be
kept in sync:

```typescript
// CLEAR: one union is the whole contract
type Plan = 'free' | 'pro' | 'enterprise';

// UNCLEAR: two things to keep in sync for no gain
enum Plan { Free = 'free', Pro = 'pro', Enterprise = 'enterprise' }
```

**When the members must also exist as a runtime value, lock a plain object
with `as const` instead of an `enum`.** The object is the value; the union
derives from it, so one edit covers both — and call sites can map, iterate,
or switch over the real values:

```typescript
const PLAN = { free: 'free', pro: 'pro', enterprise: 'enterprise' } as const;
type Plan = (typeof PLAN)[keyof typeof PLAN];
```

**Use `as const` to stop widening.** In a plain object or array, a literal
widens to `string`, `boolean`, or `number[]`, and the widened type accepts
values the literal one would reject. Locking catches the typo at compile time:

```typescript
// UNCLEAR: state is string, so 'active' compiles
const status = { state: 'active' };

// CLEAR: state is 'active', so 'active' is a compile error
const status = { state: 'active' } as const;
```

**Rewrite nested ternaries; otherwise keep the more readable form.** A single
ternary for one assignment is fine; a second `?` is the signal to rewrite into
explicit branches or a small named function. When an `if/else` block and a
ternary are equally readable, prefer the ternary — the goal is less code at
equal readability. This is the same clarity test as in Prefer Clarity Over
Cleverness above.

**Use specific boundary contracts and parse untrusted data once.** `any`,
`unknown`, `object`, and unsafe dictionaries hide the input contract and let
unchecked assumptions spread. For untyped third-party data or dynamic config,
use the repository's approved boundary parser with a concrete raw-input type,
then return a specific named shape. Keep an unavoidable cast inside that
adapter and document its checked invariant; business logic should receive only
typed values.

```typescript
// UNCLEAR: any silences the compiler and leaks through the app
function getFeatureFlags(config: any): any {
  return config.featureFlags ?? {};
}

// CLEAR: parse at the boundary and pass a specific shape inward
function getFeatureFlags(config: FeatureConfigInput): Record<string, boolean> {
  const parsedConfig = parseFeatureConfig(config);
  return parsedConfig.featureFlags ?? {};
}
```

**Let inference type maps; reach for `Record` only when you must.** A plain
object literal infers its exact keys, so most maps need no annotation. When a
map must cover every key of an `as const` object, derive the keys with
`keyof typeof` instead of hand-writing them — one source of truth, so one
edit covers both:

```typescript
const STATUS = { active: 'active', archived: 'archived' } as const;

// CLEAR: keys derived from STATUS; adding a key updates the contract
const labels: Record<keyof typeof STATUS, string> = {
  active: 'Active',
  archived: 'Archived',
};

// UNCLEAR: a hand-written duplicate of STATUS's keys — adding a key to
// STATUS silently leaves this map incomplete
const labels: Record<'active' | 'archived', string> = {
  active: 'Active',
  archived: 'Archived',
};
```

For genuinely dynamic keys, `Record<string, T>` says the same as
`{ [key: string]: T }` in fewer tokens — reach for it first. To validate a
const object's shape while keeping literal inference, use
`as const satisfies Record<string, T>`.

### Naming

**Use descriptive, familiar names with units.** A well-named value removes
the need for a comment and a second read. Include the unit where it matters —
`timeoutMs`, `maxRetries`, `sizeBytes` — so call sites never guess. Boolean
predicates read as `is` or `has`: `isValid`, `hasPermission`, `isArchived`.
Construction and mutation use familiar verbs: `createSession`, `getUser`,
`updateStatus`. Avoid vague names (`data`, `item`, `result`, `temp`,
`handle`) when a specific one is available, and avoid ornate ones: a name
like `getUserProfileFromCacheIfFresh` is doing too much and should be split,
not kept.

## Comments

Comments are part of the simplification. The goal is for the next reader —
human or agent — to follow the file without re-deriving what each region is
for. Every comment must convey the context the code cannot express: what the
piece of code does or tries to achieve, what it returns, or why it exists.
A comment that restates the code is noise — delete it.

### One-line comments

Above a function or block of code that is not a helper, utility, exported,
or public function, put a one-line comment: minimal, plain English,
top-level — what the code does or tries to achieve, and what it returns:

```typescript
// Fetch the user's team from the DB, or null when the user has no team.
function getTeamForUser(userId: string): Promise<Team | null> {
  ...
}
```

Skip the comment when the name and signature already answer both questions
(`getUser(id: string): Promise<User>` needs nothing).

### JSDoc for helpers and utilities

Use JSDoc on helpers, utilities, and exported or public functions — including
reusable components that live in the same file even when they are not
exported. JSDoc is slightly more technical than a one-liner: it may name
parameter and return types, explain the context and why the function exists,
and note failure behavior. For a component, name what it renders and its key
props. For a small helper, a single-line JSDoc is enough:

```typescript
/** Clamp `value` to `[min, max]` and return the clamped number. */
function clamp(value: number, min: number, max: number): number {
  ...
}
```

### Section comments inside a function

When a function does more than one job, separate the jobs with a one-line
comment that names each group's goal, not its mechanics. In a route handler
that queries the DB, checks authorization, and mutates data, the query and
the ownership check form one section because they share one goal:

```typescript
async function updateInvoice(req: Request, res: Response): Promise<void> {
  // Authorize: fetch the invoice and confirm the caller owns it.
  const invoice = await db.invoices.findById(req.params.id);
  if (!invoice || invoice.ownerId !== req.user.id) {
    res.status(403).json({ error: 'forbidden' });
    return;
  }

  // Mutate: apply the requested fields and persist the new state.
  invoice.assign(req.body);
  await invoice.save();
}
```

### File section comments

Put one single-line comment above each major section of a file — types,
contracts, module-level constants — immediately before the section it labels.
Use plain labels such as `// Types`, `// Global constants`, or `// Request handlers`:

```typescript
// Types
type Plan = 'free' | 'pro';
type CreateUserRequest = {
  email: string;
  plan: Plan;
};

// Global constants
const MAX_RETRIES = 3;

// Request handlers
function handleRequest(request: Request): Response {
  ...
}
```

### Markup comments

In JSX, TSX, HTML, and other markup, use exactly one comment form: a short
`{/* heading */}` separator between major layout regions, immediately before
the section it labels. Keep logic, rationale, accessibility notes, and
debugging notes in surrounding code or component documentation.

### Style

- Write for the next reader, who is often an agent with a limited context
  window: plain words, no jargon, nothing clever.
- Do not comment every line or every function.
- No separator comments: no `// ---`, repeated dashes, boxed banners, or
  multi-line blocks. Move detailed rationale into surrounding documentation
  or code structure.
- Keep comment lines at 80 characters or fewer.

## Error Handling

When a simplification touches an error path, apply these rules — this is the
one sanctioned exception to Preserve Behavior Exactly. Error messages have
two audiences, and each gets different treatment.

### User-facing errors

Assume users are technically illiterate. A user-facing error is one sentence
in plain words that tells them what to do, with the minimum context of what
happened. The instruction is the important part — a concrete action, not a
description of the failure:

- `Payment failed. Check your card details and try again.`
- `We couldn't load the page. Reload to try again.`
- `Something went wrong. Contact support if this keeps happening.`

Keep out stack traces, HTTP codes, internal names, and technical terms. If
details matter for diagnosis, log them internally and leave the user-facing
text minimal.

### Internal errors

Internal and backend error handling should be detailed and graceful.
Detailed: log everything the next developer or agent needs to diagnose — the
failing operation, its inputs, the underlying error, and where it happened.
Graceful: fail at the right layer, leave state intact or rolled back, and
surface a typed, actionable error the caller can handle instead of a raw
stack trace. The details live here, never in the user-facing message.

## The Simplification Process

### Step 1: Understand Before Touching (Chesterton's Fence)

Before changing or removing anything, understand why it exists. This is Chesterton's Fence: if you see a fence across a road and don't understand why it's there, don't tear it down. First understand the reason, then decide if the reason still applies.

```
BEFORE SIMPLIFYING, ANSWER:
- What is this code's responsibility?
- What calls it? What does it call?
- What are the edge cases and error paths?
- Are there tests that define the expected behavior?
- Why might it have been written this way? (Performance? Platform constraint? Historical reason?)
- For recent modifications, check git blame: what was the original context for this code?
```

If you can't answer these, you're not ready to simplify. Read more context first.

### Step 2: Identify Simplification Opportunities

Scan for these patterns — each one is a concrete signal, not a vague smell:

**Structural complexity:**

| Pattern | Signal | Simplification |
|---------|--------|----------------|
| Deep nesting (3+ levels) | Hard to follow control flow | Extract conditions into guard clauses or helper functions |
| Long functions (50+ lines) | Multiple responsibilities | Split into focused functions with descriptive names |
| Nested ternaries | Requires mental stack to parse | Replace with if/else chains, switch, or lookup objects |
| Boolean parameter flags | `doThing(true, false, true)` | Replace with options objects or separate functions |
| Repeated conditionals | Same `if` check in multiple places | Extract to a well-named predicate function |

**Naming and readability:**

| Pattern | Signal | Simplification |
|---------|--------|----------------|
| Generic names | `data`, `result`, `temp`, `val`, `item` | Rename to describe the content: `userProfile`, `validationErrors` |
| Abbreviated names | `usr`, `cfg`, `btn`, `evt` | Use full words unless the abbreviation is universal (`id`, `url`, `api`) |
| Misleading names | Function named `get` that also mutates state | Rename to reflect actual behavior |
| Comments explaining "what" | `// increment counter` above `count++` | Delete the comment — the code is clear enough |
| Comments explaining "why" | `// Retry because the API is flaky under load` | Keep these — they carry intent the code can't express |

**Redundancy:**

| Pattern | Signal | Simplification |
|---------|--------|----------------|
| Duplicated logic | Same 5+ lines in multiple places | Extract to a shared function |
| Dead code | Unreachable branches, unused variables, commented-out blocks | Remove (after confirming it's truly dead) |
| Unnecessary abstractions | Wrapper that adds no value | Inline the wrapper, call the underlying function directly |
| Over-engineered patterns | Factory-for-a-factory, strategy-with-one-strategy | Replace with the simple direct approach |
| Redundant type assertions | Casting to a type that's already inferred | Remove the assertion |
| Widened literal types | `const config = { debug: true }` infers `debug: boolean`, not the literal | Lock with `as const` |

**Necessity (YAGNI):**

| Pattern | Signal | Simplification |
|---------|--------|----------------|
| Dead feature surface | Export, prop, option, or route nothing references | Delete it; re-add when a caller appears |
| Speculative generality | Wrapper "for later", config knob nothing sets | Replace with the direct call |
| Boilerplate nobody asked for | Pass-through files, ritual setup layers | Collapse; fewest files that work |

### Step 3: Apply Changes Incrementally

Make one simplification at a time. Run tests after each change. **Refactoring changes stay separate from feature or bug fix changes** — a PR that refactors and adds a feature is two PRs.

```
FOR EACH SIMPLIFICATION:
1. Make the change
2. Run the test suite
3. If tests pass → continue to the next simplification
4. If tests fail → revert and reconsider
```

Avoid batching multiple simplifications into a single untested change. If something breaks, you need to know which simplification caused it.

**Never commit on your own.** Ask the user for explicit permission before any commit or PR, and follow the user's preference for how changes are split between them.

**The Rule of 500:** A refactoring that would touch more than 500 lines needs a PRD approved before implementation — unless the user declines one — and is split across multiple commits or PRs. Use automation (codemods, AST transforms) rather than hand edits; at that scale manual changes are error-prone and exhausting to review.

### Step 4: Verify the Result

After all simplifications, step back and evaluate the whole:

```
COMPARE BEFORE AND AFTER:
- Is the simplified version genuinely easier to understand?
```

If the "simplified" version is harder to understand or review, revert.

## Language-Specific Guidance

### TypeScript / JavaScript

Type and naming conventions live in Type and Naming Conventions above; the
examples here show structural simplification only.

```typescript
// SIMPLIFY: Unnecessary async wrapper
// Before
async function getUser(id: string): Promise<User> {
  return await userService.findById(id);
}
// After
function getUser(id: string): Promise<User> {
  return userService.findById(id);
}

// SIMPLIFY: Verbose conditional assignment
// Before
let displayName: string;
if (user.nickname) {
  displayName = user.nickname;
} else {
  displayName = user.fullName;
}
// After
const displayName = user.nickname || user.fullName;

// SIMPLIFY: Manual array building
// Before
const activeUsers: User[] = [];
for (const user of users) {
  if (user.isActive) {
    activeUsers.push(user);
  }
}
// After
const activeUsers = users.filter((user) => user.isActive);

// SIMPLIFY: Redundant boolean return
// Before
function isValid(input: string): boolean {
  if (input.length > 0 && input.length < 100) {
    return true;
  }
  return false;
}
// After
function isValid(input: string): boolean {
  return input.length > 0 && input.length < 100;
}
```

### Python

```python
# SIMPLIFY: Verbose dictionary building
# Before
result = {}
for item in items:
    result[item.id] = item.name
# After
result = {item.id: item.name for item in items}

# SIMPLIFY: Nested conditionals with early return
# Before
def process(data):
    if data is not None:
        if data.is_valid():
            if data.has_permission():
                return do_work(data)
            else:
                raise PermissionError("No permission")
        else:
            raise ValueError("Invalid data")
    else:
        raise TypeError("Data is None")
# After
def process(data):
    if data is None:
        raise TypeError("Data is None")
    if not data.is_valid():
        raise ValueError("Invalid data")
    if not data.has_permission():
        raise PermissionError("No permission")
    return do_work(data)
```

### React / JSX

```tsx
// SIMPLIFY: Verbose conditional rendering
// Before
function UserBadge({ user }: Props) {
  if (user.isAdmin) {
    return <Badge variant="admin">Admin</Badge>;
  } else {
    return <Badge variant="default">User</Badge>;
  }
}
// After
function UserBadge({ user }: Props) {
  const variant = user.isAdmin ? 'admin' : 'default';
  const label = user.isAdmin ? 'Admin' : 'User';
  return <Badge variant={variant}>{label}</Badge>;
}
```

Prop drilling through intermediate components is a judgment call — consider
whether context or composition solves it better, and flag it rather than
auto-refactoring.

## Common Rationalizations

| Rationalization | Reality |
|---|---|
| "It's working, no need to touch it" | Working code that's hard to read will be hard to fix when it breaks. Simplifying now saves time on every future change. |
| "Fewer lines is always simpler" | A 1-line nested ternary is not simpler than a 5-line if/else. Simplicity is about comprehension speed, not line count. |
| "I'll just quickly simplify this unrelated code too" | Unscoped simplification creates noisy diffs and risks regressions in code you didn't intend to change. Stay focused. |
| "The types make it self-documenting" | Types document structure, not intent. A well-named function explains *why* better than a type signature explains *what*. |
| "This abstraction might be useful later" | Don't preserve speculative abstractions. If it's not used now, it's complexity without value. Remove it and re-add when needed. |
| "The original author must have had a reason" | Maybe. Apply Chesterton's Fence — and for recent modifications, check git blame. But accumulated complexity often has no reason; it's just the residue of iteration under pressure. |
| "I'll refactor while adding this feature" | Separate refactoring from feature work. Mixed changes are harder to review, revert, and understand in history. |
| "A small new library would make this cleaner" | Simplification never adds dependencies. Check the codebase, the standard library, and installed dependencies first; if none fits, the current version stays. |
| "The browser has a native component for this" | Native UI behavior varies browser to browser. Keep the established external component; a native swap trades cross-browser consistency for a smaller diff. |

## Red Flags

- Simplification that requires modifying tests to pass (you likely changed behavior)
- "Simplified" code that is longer and harder to follow than the original
- Renaming things to match your preferences rather than project conventions
- Removing error handling because "it makes the code cleaner"
- Simplifying code you don't fully understand
- Batching many simplifications into one large, hard-to-review commit
- Refactoring code outside the scope of the current task without being asked
- Adding a new dependency in the middle of a simplification
- Swapping an established external UI component for a native platform element (date picker, select, dialog)

## Verification

After completing a simplification pass:

- [ ] All existing tests pass without modification
- [ ] Build succeeds with no new warnings
- [ ] Linter/formatter passes (no style regressions)
- [ ] Each simplification is a reviewable, incremental change
- [ ] The diff is clean — no unrelated changes mixed in
- [ ] Simplified code follows project conventions (checked against CLAUDE.md or equivalent)
- [ ] Comments added where context was missing; none restate the code
- [ ] Invalid, undefined, and unauthorized states checked first with early returns
- [ ] Genuinely repeated logic shared through one helper; no premature abstraction
- [ ] User-facing errors give one plain instruction; internal errors stay detailed
- [ ] No error handling was removed or weakened
- [ ] No dead code was left behind (unused imports, unreachable branches)
- [ ] A teammate or review agent would approve the change as a net improvement
- [ ] Replacements came from the codebase, standard library, or installed dependencies — no new dependency added
- [ ] No established external UI component was swapped for a native platform one
- [ ] Deliberate corner-cuts carry a comment naming the ceiling and upgrade path
