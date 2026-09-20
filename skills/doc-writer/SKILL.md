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

Write all comments, design docs, and PRDs in ASD-STE100 Simplified Technical
English. This standard is mandatory and overrides a conflicting project style
guide or glossary.

## Branches

One branch fires per task; load only its guide:

- **Code comments** (adding or revising comments in code): [references/code-comments.md](references/code-comments.md)
- **Design docs** (a new feature or a massive refactor needs a numbered doc): [references/design-docs.md](references/design-docs.md)
- **PRDs** (the user asks for a PRD): [references/prds.md](references/prds.md)
