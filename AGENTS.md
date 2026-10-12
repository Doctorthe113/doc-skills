## Skill source

- This repo is the source of the skill code. Edit skills only here.
- Treat the installed skill folders (`~/.agents/skills/` and `~/.config/opencode/skills/`)
  as read-only. Do not edit or sync them.
- Each skill is a standalone skill. Do not reference other skills.
- Author each skill so its runtime root is the installed copy
  (`~/.agents/skills/<name>` or `{repo}/.agents/skills/<name>`). This tree is
  source; in-tree script checks are fine.
