# Agent Skills

Portable, versioned skills for AI coding agents.

**The problem:** Agent skills live at machine-specific paths (`~/.config/agents/skills/tdd/SKILL.md`). When agents run on different machines, CI environments, or cloud workers, those paths don't exist. Your carefully crafted instructions break.

**The solution:** Declare skills by URL. Agents fetch and cache them automatically. Works everywhere.

## Quick Start

Add a `## Skills` section to your project's `AGENTS.md`:

```markdown
## Skills

This project uses [toolbox](https://raw.githubusercontent.com/slagyr/agent-skills/main/toolbox/SKILL.md)
to manage skills. If `.skills/` doesn't exist, fetch the toolbox SKILL.md
from the URL above and follow its instructions.

- [tdd](https://raw.githubusercontent.com/slagyr/agent-skills/main/tdd/SKILL.md)
```

That's it. When an agent reads your `AGENTS.md`, it fetches the toolbox skill, learns how to resolve skill URLs, and caches everything locally in `.skills/`.

Add `.skills/` to your `.gitignore` — it's a cache, not source.

## How It Works

1. Agent reads `AGENTS.md`, finds the `## Skills` section
2. On first run, fetches each skill's `SKILL.md` (and any linked references) into `.skills/`
3. Creates `.skills/toolbox.json` manifest with content hashes for change detection
4. On subsequent runs, uses the cached copies instantly
5. When you ask "check for skill updates", it compares remote content against stored hashes and reports what changed

Skills are never auto-updated. You decide when to pull new versions.

## Using Skills From Other Repos

Skills don't have to live in this repo. Any `SKILL.md` accessible via URL works:

```markdown
## Skills

This project uses [toolbox](https://raw.githubusercontent.com/slagyr/agent-skills/main/toolbox/SKILL.md)
to manage skills. If `.skills/` doesn't exist, fetch the toolbox SKILL.md
from the URL above and follow its instructions.

- [tdd](https://raw.githubusercontent.com/slagyr/agent-skills/main/tdd/SKILL.md)
- [braids](https://raw.githubusercontent.com/slagyr/braids/main/braids/SKILL.md)
```

### Local Skills

For skills under active development or private skills, use `file://` URLs:

```markdown
- [my-skill](file:///Users/me/Projects/my-skill/SKILL.md)
```

`file://` URLs aren't portable across machines, but they're great for local development. Switch to `https://` when you publish.

### Pinning Versions

Use a commit SHA instead of a branch name to pin a skill to a specific version:

```markdown
- [tdd](https://raw.githubusercontent.com/slagyr/agent-skills/a1b2c3d/tdd/SKILL.md)
```

Branch names (like `main`) always fetch the latest.

## Skill Anatomy

A skill is a directory containing a `SKILL.md` and optional reference files:

```
tdd/
  SKILL.md

braids/
  SKILL.md
  references/
    worker.md
    orchestrator.md
    ...
```

`SKILL.md` is the entry point — it has YAML frontmatter (`name`, `description`) and markdown content that teaches the agent how to behave.

Reference files are discovered automatically: toolbox parses relative markdown links in `SKILL.md` and fetches them alongside it. No manifest needed from skill authors.

## Available Skills

| Skill | Description |
|-------|-------------|
| [toolbox](toolbox/SKILL.md) | Skill manager. Resolves, caches, and updates skill dependencies declared by URL. |
| [tdd](tdd/SKILL.md) | Test-Driven Development for Clojure. Red-Green-Refactor, the Three Laws, Arrange-Act-Assert with Speclj. |

## Writing Your Own Skills

Create a directory with a `SKILL.md`:

```markdown
---
name: my-skill
description: One-line description of what this skill does and when to use it.
---

# My Skill

Instructions for the agent...
```

Link to reference files using relative paths:

```markdown
See: [references/details.md](references/details.md)
```

Toolbox will discover and fetch these automatically. Push to any git repo and reference it by URL.
