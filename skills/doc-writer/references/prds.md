# PRDs

Create a PRD only after a grilling session or complete context has resolved the feature's material decisions. Verify the problem, users, outcome, goals, non-goals, requirements, acceptance criteria, constraints, dependencies, risks, rollout, and measurement.

Apply the document header rules in [design-docs.md](design-docs.md) to the PRD file.

1. Write the PRD as an extremely detailed implementation handoff, not a high-level product summary. Cover the end-to-end flow, data model and schema changes, API contracts, UI behavior and states, permissions, validation, errors, edge cases, migrations or backfills, rollback, observability, rollout, and tests when they apply.
2. Include relevant pseudocode or code snippets for important flows, state transitions, queries, schemas, payloads, and failure handling. Use snippets to remove ambiguity, not as decoration, and base them on the repository's actual conventions.
3. Keep the PRD easy to read. Apply the STE-aligned prose rules in the parent skill. Use short sections, direct sentences, concrete examples, labeled code fences, and familiar words. Write each requirement and acceptance criterion as one observable statement. Define unavoidable technical terms and acronyms when first used, and replace jargon with a plain explanation.
4. If a material answer is missing or two interpretations remain, ask focused questions and do not create the PRD. Create PRDs under `docs/prds/` and use the location and numbering rules in [design-docs.md](design-docs.md) with that directory as the selected directory. Make the in-scope and out-of-scope boundary explicit, keep acceptance criteria observable, and set `Open questions` to `None`.

Done when the PRD has no material ambiguity that could change its scope, design, or acceptance criteria.
