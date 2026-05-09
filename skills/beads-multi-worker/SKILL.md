---
name: beads-multi-worker
description: Use this skill when multiple workers (parallel checkouts, separate machines, or human + agent) share a single beads tracker. Covers push-after-every-write, pull-at-handoff, session close protocol, and avoiding stranded state.
---

# Beads Multi-Worker Sync

## When This Skill Applies

Use this skill when multiple worker checkouts (`project-main`, `project-worker-1`, …), separate machines, or a mix of human and agent collaborators share one beads tracker (with DoltHub or another sync backend). Each worker's view of source and beads is stale by default. The cost of skipping a sync at a handoff point is silent divergence: a verifier reviewing stale source, a worker missing reviewer notes.

## The Rule

**Before acting on another worker's output, pull. After producing your own, push.**

Pushes are cheap. Pulls are situational. The asymmetry matters — push proactively, pull at moments where you're about to act on what someone else produced.

## Push after every bead write

Pushes are cheap. They prevent stranding state where another worker can't see it.

- After `bd create`, `bd update`, `bd close`, `bd dep add` → `bd dolt push`
- After `git commit` that signals work another worker may consume (e.g. marking a bead `unverified`, closing a dependency, finishing a refactor) → `git push`

This supersedes any "after a batch of writes" guidance. Push every time.

## Pull at handoff points

Pulls are situational — only at moments where you're about to act on what someone else produced. Do **not** pull on every `bd show`/`bd ready`/`bd list` — that's too aggressive and wastes time.

### Verification handoff

Running `/verify` or otherwise reviewing a bead marked `unverified`:

```bash
git pull --rebase
bd dolt pull
```

…**before** reading source or bead state.

Verification is the canonical worker→reviewer handoff. Skipping the source pull risks reopening a bead against stale code; skipping the bead pull risks overwriting concurrent reviewer notes.

### Resuming after external change

If you've been told "the bead was reopened," "the verifier left notes," "the user closed a dependency," or any signal that another actor touched your bead since you last looked:

```bash
bd dolt pull
```

…before `bd show <id>`. Your in-memory model of the bead is stale.

### Session start

```bash
git pull --rebase
bd dolt pull
```

You don't know what landed while you were away.

## Session Close Protocol

When ending a work session, you MUST complete ALL steps below. Work is NOT complete until `git push` succeeds.

```
[ ] 1. git status              (check what changed)
[ ] 2. git add <files>         (stage code changes)
[ ] 3. git commit -m "..."     (commit code)
[ ] 4. bd dolt push            (push beads)
[ ] 5. git pull --rebase       (rebase on remote)
[ ] 6. git push                (push to remote)
[ ] 7. git status              (must show "up to date with origin")
```

### Critical rules

- Work is NOT complete until `git push` succeeds.
- NEVER stop before pushing — that leaves work stranded locally.
- NEVER say "ready to push when you are" — YOU must push.
- If push fails, resolve and retry until it succeeds.
- File issues for remaining work before pushing — capture follow-ups in beads, not in your head.

### What gets pushed

- Code changes — `git push`
- Bead state changes — `bd dolt push`

These are independent push targets. Pushing one without the other strands the other. The session-close protocol pushes both, every time.

## Conflict resolution

When two workers update the same bead concurrently, dolt may report a merge conflict.

```bash
bd dolt pull           # detects conflict
dolt conflicts resolve --theirs <table>   # accept incoming
dolt conflicts resolve --ours <table>     # keep local
```

Use `--theirs` when accepting another worker's status change (e.g. they marked a bead `in_progress` while you were updating its description — you want their status, plus your description).

Use `--ours` when your edit should win (e.g. you have richer notes than they did). After `--ours`, you may need to manually re-apply selected fields from theirs via `dolt sql` UPDATE.

After resolving, commit the merge and push.
