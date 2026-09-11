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

- Use one idea per sentence. Use a clear subject and an active verb. Use
  passive voice only when the actor is unknown or does not matter.
- Keep instructions to 20 words or fewer and descriptive sentences to 25 words
  or fewer. Split longer sentences when they contain more than one action,
  condition, or result.
- Write one instruction per sentence. Start safety instructions with a clear
  command or condition.
- Keep one topic per paragraph. Use no more than six sentences per paragraph.
  Use vertical lists when a paragraph contains several items or steps.
- Use plain, specific words. Give each concept one consistent term and meaning.
  Follow the repository or domain glossary when one exists. Use each approved
  term with its defined part of speech and meaning. Define unavoidable
  technical terms and acronyms at first use.
- Write complete sentences for prose. Include the subject, verb, and articles
  when they improve clarity. Use fragments only for clear labels and headings.
- Prefer infinitive, imperative, and simple present, past, or future verb forms.
  Use auxiliary verbs only when needed, and use `-ing` only as a technical noun
  or modifier.
- Keep general compound nouns to three words or fewer. Preserve established
  identifiers and product names when they require more words.
- These rules use Simplified Technical English (STE) principles. Use them with
  the project's style guide and glossary when those exist.
- State facts, decisions, constraints, and tradeoffs directly.
- Use plain words and specific examples. Cut puffery, vague attributions, filler, excessive hedging, stock AI language, and generic conclusions.
- Use sentence-case headings, straight quotes, and purposeful emphasis. Use periods, commas, or parentheses for breaks.
- Keep prose free of decorative emoji, forced "not just X, it is Y" framing, forced groups of three, synonym cycling, false ranges, and em dash punctuation.
- Self-audit once as a skeptical teammate. Replace anything padded, vague, evasive, or more certain than the evidence.

## Branches

One branch fires per task; load only its guide:

- **Code comments** (adding or revising comments in code): [references/code-comments.md](references/code-comments.md)
- **Design docs** (a new feature or a massive refactor needs a numbered doc): [references/design-docs.md](references/design-docs.md)
- **PRDs** (the user asks for a PRD): [references/prds.md](references/prds.md)
