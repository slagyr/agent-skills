# Agent Lib

A library of reusable skills, commands, and other agent documents for AI coding agents.

Skills are managed by [toolbox](https://github.com/slagyr/toolbox).

## Structure

```
agent-lib/
  skills/       # Skills teach agents how to behave in specific contexts
  commands/     # Commands define reusable agent actions
```

## Using Skills

Add a `## Skills` section to your project's `AGENTS.md` and reference skills by URL:

```markdown
## Skills

This project uses [toolbox](https://raw.githubusercontent.com/slagyr/toolbox/main/SKILL.md)
to manage skills. If `.skills/` doesn't exist, fetch the toolbox SKILL.md
from the URL above and follow its instructions.

- [tdd](https://raw.githubusercontent.com/slagyr/agent-lib/main/skills/tdd/SKILL.md)
```

See the [toolbox README](https://github.com/slagyr/toolbox) for full setup instructions.

## Available Skills

| Skill | Description |
|-------|-------------|
| [tdd](skills/tdd/SKILL.md) | Test-Driven Development for Clojure. Red-Green-Refactor, the Three Laws, Arrange-Act-Assert with Speclj. |
