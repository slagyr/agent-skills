# Agent Lib

A library of reusable skills, commands, and other agent components for AI coding agents.

## Structure

```
agent-lib/
  skills/       # Skills teach agents how to behave in specific contexts
  commands/     # Commands define reusable agent actions
```

## Available Skills

| Skill | Description |
|-------|-------------|
| [clojure](skills/clojure/SKILL.md) | Clojure coding conventions. Naming, formatting, function design, state management, threading, cross-platform patterns. |
| [c3kit](skills/c3kit/SKILL.md) | Guide to the c3kit ecosystem (apron, bucket, wire, scaffold). Schemas, database API, web layer, app lifecycle. |
| [tdd](skills/tdd/SKILL.md) | Test-Driven Development for Clojure. Red-Green-Refactor, the Three Laws, Arrange-Act-Assert with Speclj. |
| [gherkin](skills/gherkin/SKILL.md) | Writing good Gherkin feature files. Scenario structure, step clarity, tag conventions. |
| [gherclj](skills/gherclj/SKILL.md) | Implementing gherclj step definitions. defgiven/defwhen/defthen conventions, assertions, state management, running scenarios. |

For [toolbox](https://github.com/slagyr/toolbox) managed projects, add to your boot file:

```markdown
- [tdd](https://raw.githubusercontent.com/slagyr/agent-lib/main/skills/tdd/SKILL.md)
```

## Available Commands

| Command | Description |
|---------|-------------|
| [plan](commands/plan.md) | Planning mode using the beads issue tracker. Gathers context, discusses requirements, and creates beads. |
| [plan-with-features](commands/plan-with-features.md) | Extends /plan with a feature-first workflow using Gherkin and gherclj. |
| [todo](commands/todo.md) | Convert TODO comments in the code into beads. |
| [work](commands/work.md) | Pick up the next ready bead and work on it. |
| [verify](commands/verify.md) | Review beads marked unverified by workers. Runs tests, checks acceptance criteria, closes or reopens. |

For [toolbox](https://github.com/slagyr/toolbox) managed projects, add to your boot file:

```markdown
- [plan](https://raw.githubusercontent.com/slagyr/agent-lib/main/commands/plan.md)
- [plan-with-features](https://raw.githubusercontent.com/slagyr/agent-lib/main/commands/plan-with-features.md)
- [todo](https://raw.githubusercontent.com/slagyr/agent-lib/main/commands/todo.md)
- [work](https://raw.githubusercontent.com/slagyr/agent-lib/main/commands/work.md)
- [verify](https://raw.githubusercontent.com/slagyr/agent-lib/main/commands/verify.md)
```
