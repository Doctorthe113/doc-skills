---
name: btw
description: Answer a user's side question briefly without changing files or the active task.
disable-model-invocation: true
---

# BTW

Handle only the question in `$ARGUMENTS`.

- Answer directly in simple words and as few sentences as needed.
- Use the context already available. If it is not enough, say so briefly or ask one focused question.
- Keep the active task unchanged: preserve its plan, files, decisions, and result.
- Use this as a read-only side channel: make no edits, run no commands, and invoke no other skills.
- After answering, resume the active task without carrying this side question into it.

Completion means the response contains only the concise answer to `$ARGUMENTS`.
