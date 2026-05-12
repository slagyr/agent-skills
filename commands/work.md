---
name: work
description: Pick up the next ready bead and work on it. Use when the user says "/work" or asks to start working on the next task.
user-invocable: true
---

# Work on Next Bead

Pick up the next ready bead and work on it.

## Steps

1. Pull the latest code with `git pull`
2. Sync the latest bead state with `bd dolt pull`
3. Branch on `$ARGUMENTS`:
   - **If a bead ID was provided** → follow "Targeted bead" below. Do not fall back to the ready queue.
   - **If no argument was provided** → follow "Pick from ready queue" below.

### Targeted bead (a specific ID was passed)

The user named this bead deliberately. Treat it as a hard constraint: never substitute a different bead, never auto-pick a dependency, never silently fall back to the queue. If the named bead cannot be worked right now, **stop and report** — let the user decide what to do.

1. Run `bd show <id>` to load the bead. If the ID does not exist, stop and tell the user.
2. Check status:
   - If `closed` / done / already complete → **stop**. Report the status and ask whether the user meant a different bead or wants to reopen this one. Do not pick another bead.
   - If `in_progress` → **stop**. Report that the bead is already claimed (likely by another worker) and ask whether to proceed anyway. Do not silently take it over.
   - If `blocked` or has unmet dependencies → **stop**. List the blocking beads and ask the user which one they want to work on. Do not auto-pick the dependency.
   - If `ready` (or otherwise workable) → continue.
3. Show the issue details to the user with `bd show <id>`.
4. Run `bd update <id> --status=in_progress` to claim it.
5. Push the claim with `bd dolt push` so other workers see it as taken.
6. Implement the issue, following any applicable project skills and conventions.

### Pick from ready queue (no argument)

1. Run `bd ready` to find issues with no blockers.
2. If no issues are ready, inform the user and stop.
3. Select the highest priority issue (lowest P number) that is NOT in_progress.
4. If the only ready issues are in_progress, select the highest priority issue and ask the user if they'd like to proceed. If they decline, stop.
5. Show the issue details to the user with `bd show <id>`.
6. Run `bd update <id> --status=in_progress` to claim it.
7. Push the claim with `bd dolt push` so other workers see it as taken.
8. Implement the issue, following any applicable project skills and conventions.

## When Complete

1. Ensure all unit tests/specs pass
2. If the bead references approved feature scenarios, ensure those scenarios pass and are not pending
3. If the bead references approved feature scenarios, do not close the bead while those scenarios remain pending
4. If the bead references approved feature scenarios, do not change approved feature direction without review; if feature text and implementation diverge, stop and raise it
5. Check boot files (`AGENTS.md`, `CLAUDE.md`, etc.), existing beads, or `bd` config for the project's status flow. Do not skip this step. Use whatever status the project defines as the next step after `in_progress`. If no conventions are documented, default to `bd close <id>`.
6. Push bead state with `bd dolt push`
7. Commit code changes with a descriptive message

## Common Traps

### Premature close

Beads get closed before the work is actually done. Always verify with
the project's feature/test runner before trusting "it's done." Always
check that `@wip` was removed AND the scenarios pass. If the project
uses a verifier (separate `/verify` flow), mark the bead `unverified`
rather than `closed` — let the verifier confirm.

### Dated defers bite back

`bd defer <id> --until=2026-05-01` on a backlog bead quietly ticks
down and surprises whoever's triaging when the date arrives. For
"not now, no specific deadline," use `bd defer <id>` with **no**
`--until` flag. Un-defer with `bd update <id> --status=open` — NOT
`--defer=""`, which clears the date but leaves the status deferred.

Rule of thumb: only use dated defers when there's a real external
dependency (a release date, an upstream fix, an infra upgrade).
Everything else is undated backlog.

## Arguments

$ARGUMENTS - Optional: a specific issue ID. When provided, this is a hard constraint: work that exact bead or stop. Never substitute a different bead (not its dependencies, not the next ready bead, not anything else). If it can't be worked, surface the reason to the user and let them decide.
