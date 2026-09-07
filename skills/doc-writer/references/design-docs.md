# Design docs

## Document headers

1. Start every Markdown design doc and PRD with its title and useful content.
2. Do not add repository paths, `Repo:` lines, dates, `Date:` lines, timestamps, author names, or generated metadata at the top unless the user explicitly requests them.
3. When editing an existing document, preserve its existing header unless the user asks to remove it, but never add a new metadata header.

## Markdown design docs

Use this branch only when documentation is needed for a new feature or a massive refactor. A design doc records the problem, decision, boundaries, and validation plan.

1. Decide whether a doc is needed before creating or editing one. If the need for documentation or the correct existing doc is unclear, ask the user and wait for an answer. For a fix, bug correction, or change to an existing feature, do not create a new doc. If a relevant doc already exists, edit it; otherwise leave the docs unchanged.
2. For a new feature, create the doc under `docs/features/`.
3. For a massive refactor of an existing feature, create a new doc under `docs/refactors/` only when the work spans multiple surfaces, such as database migration or backfill together with UI and API changes. Smaller refactors and ordinary changes do not create a new doc.
4. Use the next numeric prefix in the selected directory only. `docs/features/`, `docs/prds/`, and `docs/refactors/` each have their own sequence. Start at `001` with three digits when the selected directory has no numbered doc, and preserve its existing width.
5. Use `NNN_UPPERCASE_SNAKE_CASE.md`. Recheck the number immediately before writing.
6. Cover the problem, outcome, scope, non-goals, important behavior or invariants, design boundary, and validation. For features, include user-visible behavior and acceptance criteria. For refactors, state what behavior remains unchanged and any migration work.

Done when the file has a unique next number, the required name, clear scope, and enough context for implementation or review.
