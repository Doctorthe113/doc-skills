---
name: doc-writer
description: Create or revise useful code comments, numbered Markdown docs for features and refactors, and PRDs after requirements are clear. Use when a change needs documentation or a user asks for comments, a design doc, or a PRD.
---

# Doc writer

Write concise, specific documentation for the next reader.

## Process

1. Inspect the README, applicable `AGENTS.md` or `CLAUDE.md`, target code, nearby tests, and existing docs. Done when the audience, purpose, repository rules, and documentation location are clear.
2. Choose the requested branch below and draft only what the change needs. Done when every comment or section explains a decision, behavior, constraint, or action.
3. Review the result, check the diff, and run available documentation checks. Done when the output is accurate, correctly named, free of filler, and limited to the requested scope.

## Prose rules

Apply these rules to comments, design docs, and PRDs:

- State facts, decisions, constraints, and tradeoffs directly.
- Use plain words and specific examples. Cut puffery, vague attributions, filler, excessive hedging, stock AI language, and generic conclusions.
- Use sentence-case headings, straight quotes, and purposeful emphasis. Use periods, commas, or parentheses for breaks.
- Keep prose free of decorative emoji, forced "not just X, it is Y" framing, forced groups of three, synonym cycling, false ranges, and em dash punctuation.
- Self-audit once as a skeptical teammate. Replace anything padded, vague, evasive, or more certain than the evidence.

## Document headers

1. Start every Markdown design doc and PRD with its title and useful content.
2. Do not add repository paths, `Repo:` lines, dates, `Date:` lines, timestamps, author names, or generated metadata at the top unless the user explicitly requests them.
3. When editing an existing document, preserve its existing header unless the user asks to remove it, but never add a new metadata header.

## Code comments

1. **Function context.** Add one concise comment directly above every function, method, or assigned function. Skip only the program's main loop and main component. State what the function does and why it exists in simple words.

```ts
// Add an item to the user's cart after checking ownership and stock.
async function addItemToCart(userId: string, itemId: string): Promise<Cart> {
  return addToCart(userId, itemId);
}
```

2. **Large function bodies.** Use short comments to separate large chunks of related work. Group validation, database queries, mutations, and the response together. Name the purpose of each group.

```ts
// Add an item to the user's cart after validation and lookup.
async function addItemToCart(req: Request, res: Response): Promise<Response> {
  // Verify the user.
  await verifyUser(req);

  // Read the cart and item.
  const cart = await getCart(req.user.id);
  const item = await getItem(req.body.itemId);

  // Add the item to the cart.
  await addToCart(cart, item);

  // Return the result.
  return res.status(200).json(cart);
}
```

3. **Comment size and quality.** Keep comments small, concise, and informative. A good comment gives context. A weak comment repeats the function name, next line, or obvious syntax; uses a vague label such as `// Handle edge case`; records temporary history; or describes behavior the code does not enforce.

4. **File sections.** Use one single-line comment above each major section of a file, immediately before the section it labels. Use plain labels such as `// Types`, `// Constants`, or `// Request handlers`.

```ts
// Global constants
const GLOBAL_VARIABLE = 2;

// Request handlers
function handleRequest(request: Request): Response {
  return createResponse(request);
}
```

5. **Separator style.** Do not use `// ---`, repeated dashes, boxed banners, or multi-line separator blocks. Keep function context comments, chunk comments, and file section comments to one line and at most 80 characters. Move detailed rationale, long explanations, and implementation notes into surrounding documentation or code structure.

6. **Markup.** In JSX, TSX, HTML, and other markup, use exactly one comment form: a short `{/* heading */}` separator between major layout regions, immediately before the section it labels. Keep logic, rationale, accessibility notes, implementation details, and temporary debugging notes in surrounding code or component documentation.

Done when every eligible function has a context comment, every large function has clear chunk boundaries, every major file section has a single-line label, no large or `// ---` separator comments exist, and markup uses only the allowed short separator form.

## Markdown design docs

Use this branch only when documentation is needed for a new feature or a massive refactor. A design doc records the problem, decision, boundaries, and validation plan.

1. Decide whether a doc is needed before creating or editing one. If the need for documentation or the correct existing doc is unclear, ask the user and wait for an answer. For a fix, bug correction, or change to an existing feature, do not create a new doc. If a relevant doc already exists, edit it; otherwise leave the docs unchanged.
2. For a new feature, create the doc under `docs/features/`.
3. For a massive refactor of an existing feature, create a new doc under `docs/refactors/` only when the work spans multiple surfaces, such as database migration or backfill together with UI and API changes. Smaller refactors and ordinary changes do not create a new doc.
4. Use the next numeric prefix in the selected directory only. `docs/features/`, `docs/prds/`, and `docs/refactors/` each have their own sequence. Start at `001` with three digits when the selected directory has no numbered doc, and preserve its existing width.
5. Use `NNN_UPPERCASE_SNAKE_CASE.md`. Recheck the number immediately before writing.
6. Cover the problem, outcome, scope, non-goals, important behavior or invariants, design boundary, and validation. For features, include user-visible behavior and acceptance criteria. For refactors, state what behavior remains unchanged and any migration work.

Done when the file has a unique next number, the required name, clear scope, and enough context for implementation or review.

## PRDs

Create a PRD only after a grilling session or complete context has resolved the feature's material decisions. Verify the problem, users, outcome, goals, non-goals, requirements, acceptance criteria, constraints, dependencies, risks, rollout, and measurement.

1. Write the PRD as an extremely detailed implementation handoff, not a high-level product summary. Cover the end-to-end flow, data model and schema changes, API contracts, UI behavior and states, permissions, validation, errors, edge cases, migrations or backfills, rollback, observability, rollout, and tests when they apply.
2. Include relevant pseudocode or code snippets for important flows, state transitions, queries, schemas, payloads, and failure handling. Use snippets to remove ambiguity, not as decoration, and base them on the repository's actual conventions.
3. Keep the PRD easy to read. Use short sections, direct sentences, concrete examples, labeled code fences, and familiar words. Define unavoidable technical terms and acronyms when first used, and replace jargon with a plain explanation.
4. If a material answer is missing or two interpretations remain, ask focused questions and do not create the PRD. Create PRDs under `docs/prds/` and use the location and numbering rules above with that directory as the selected directory. Make the in-scope and out-of-scope boundary explicit, keep acceptance criteria observable, and set `Open questions` to `None`.

Done when the PRD has no material ambiguity that could change its scope, design, or acceptance criteria.
