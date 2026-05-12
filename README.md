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
| [architecture](skills/architecture/SKILL.md) | Architecture principles: Screaming Architecture, domain-as-interface seams (Ports & Adapters at the directory level), implicit/explicit API pairs. |
| [beads-multi-worker](skills/beads-multi-worker/SKILL.md) | Multi-worker discipline for shared beads trackers. Push-after-every-write, pull-at-handoff, session close protocol, conflict resolution. |
| [clojure](skills/clojure/SKILL.md) | Clojure coding conventions. Naming, formatting, function design, state management, threading, cross-platform patterns. |
| [c3kit](skills/c3kit/SKILL.md) | Guide to the c3kit ecosystem (apron, bucket, wire, scaffold). Schemas, database API, web layer, app lifecycle. |
| [c3kit-schema](skills/c3kit-schema/SKILL.md) | Deep reference for c3kit.apron.schema. Four operations, spec anatomy, nested types, entity-level specs, error handling, silent-failure trap. |
| [gherkin](skills/gherkin/SKILL.md) | Writing good Gherkin feature files. Scenario structure, step clarity, tag conventions, common traps. |
| [logging](skills/logging/SKILL.md) | Structured logging discipline. Levels, event-keyword conventions (:domain/action), feature-test assertions on log entries. |
| [refactor](skills/refactor/SKILL.md) | Refactoring lessons. Extract method/namespace, rename, magic-number-to-constant, the discipline of rewiring callers immediately. |
| [smells](skills/smells/SKILL.md) | Code smells. Dead code, unused bindings, duplication, magic numbers, long parameter lists, god namespaces, sleeping in tests, testing smells. |
| [tdd](skills/tdd/SKILL.md) | Test-Driven Development for Clojure. Red-Green-Refactor, the Three Laws, Arrange-Act-Assert with Speclj. |

For [toolbox](https://github.com/slagyr/toolbox) managed projects, add to your boot file:

```markdown
- [tdd](https://raw.githubusercontent.com/slagyr/agent-lib/main/skills/tdd/SKILL.md)
- [c3kit-schema](https://raw.githubusercontent.com/slagyr/agent-lib/main/skills/c3kit-schema/SKILL.md)
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
