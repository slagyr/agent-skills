---
name: verify
description: Verify recently completed beads meet their acceptance criteria. Use when the user says "/verify".
user-invocable: true
---

# Verify Completed Beads

Review beads marked `unverified` by workers. Check that the work
actually meets the acceptance criteria. Close if good, reopen if not.

You are a **reviewer**, not the implementer. You have fresh eyes.
Be thorough but fair.

## Status Names

This command uses default status names (`unverified`, `open`, `closed`). Projects may use different conventions.

If the project uses different names, use those instead of the defaults throughout.

## Steps

1. Run `bd list --status=unverified` to find beads awaiting verification
2. If none found, inform the user and stop
3. For each unverified bead (highest priority first):
   a. Run `bd show <id>` to read the description and acceptance criteria
   b. Identify any feature files or test references in the bead
   c. Run the acceptance checks (see below)
   d. Make a pass/fail judgment
   e. If **pass**: `bd close <id>` then sync bead state (`bd sync` if available; otherwise use the equivalent `bd dolt` commands)
   f. If **fail**: `bd update <id> --status=open --notes="Verification failed: <reason>"`
4. Report a summary of results to the user

## Acceptance Checks

Run these in order. Stop on first failure.

### 1. Feature files not tampered with
- For each feature file referenced in the bead, run `git log --oneline -- <feature-file>` to find commits that touched it
- For each such commit, diff the file: `git show <commit> -- <feature-file>`
- Permitted changes: `@wip` tag removal, or changes explicitly described in the bead
- Flag and fail if you find: reworded steps, weakened assertions, removed scenarios, or any other unauthorized edits
- If flagged, do not proceed with remaining checks — reopen the bead with a clear description of what was changed

### 2. Tests pass
- Run the project's unit test suite — all tests must pass
- If the bead references feature files, run those scenarios too
- If the project uses gherclj, use `file:line` selectors to run only the relevant scenarios

### 3. Clean test output
Scan the test runner's stdout/stderr from the run above. The output should
contain only the framework's own chatter: dots, progress markers, summaries
(`"Finished in X.Xs"`, `"N examples, M failures"`), and scenario titles for
documentation reporters. Anything else is suspect — usually a `println` that
snuck into production code.

If stray output appears, identify the source file from the bead's diff and
reopen with the offending text quoted: *"Stray output in test run: `<text>`.
Likely from `<file>`. Remove or log it."*

CLI tools that intentionally write to stdout are the legitimate exception —
flag for confirmation rather than auto-reopening.

### 4. Test-quality smell review
For each new or substantially modified test file in the bead's diff, scan
for the following patterns. Flag any matches.

1. **`Thread/sleep`** — synchronization missing. The test should poll a
   condition, await a promise, or inject timing control.
2. **Real network** — un-stubbed HTTP, WebSocket, or raw socket calls.
   Tests should mock the transport.
3. **Real filesystem outside the test dir** — `slurp`, `spit`, `io/file`
   on paths not clearly test-scoped (under `target/`, `/tmp/`, or a dir
   created in setup).
4. **Real database** — un-mocked connections. Use in-memory implementations
   or repository stubs.
5. **No-assertion tests** — an `it` block (or gherclj `defthen` helper)
   whose body doesn't call `should=`/`should`/`should-fail`/etc. and
   silently passes with zero assertions.
6. **Hidden time dependence** — `(System/currentTimeMillis)`,
   `(java.util.Date.)`, `(java.time/now)` read inside production code under
   test without an injection seam.
7. **Cross-test mutable state** — top-level `def` atoms or files that
   persist between tests, relying on test execution order.

#### Allowed overrides

These patterns have legitimate uses (a smoke test against a real endpoint,
an intentional sleep to test a timeout). Before reopening on a flag, check
for one of:

- **Inline justification** — a comment near the pattern, e.g.
  `;; verify-allow: testing real .waitFor blocking` or
  `;; intentional — see bead #abc`. Marker syntax is informal; the agent
  just needs to see that the author thought about it.
- **Bead `## Exceptions` section** — the bead lists the `file:line` and
  the reason.

If either is present, accept the flag. If neither, reopen with the specific
pattern and location: *"Flagged `<X>` in `<file:line>` with no inline
justification or bead-documented exception. Either refactor, or add a brief
note explaining why."*

### 5. Test speed regression (optional)
If `.verify-baseline.edn` exists at the project root, compare actual test
timings against it. Format:

```edn
{:speclj   {:avg-ms-per-example 20.0 :max-ms-per-example 500}
 :features {:avg-ms-per-example 35.0 :max-ms-per-example 2000}}
```

For each test type the bead exercises:
- Compute `total-ms / example-count` from the actual run.
- Flag if actual exceeds `1.5x` the baseline `:avg-ms-per-example`.
- Flag if any single test exceeds `:max-ms-per-example`.

On green verification, update `.verify-baseline.edn` with the latest
readings. The file should be in `.gitignore` — absolute timings don't
transfer between machines.

If no baseline file exists, skip this check silently — it's opt-in.

### 6. Acceptance criteria met
- Read each acceptance criterion from the bead
- For each criterion, verify it is satisfied:
  - If it references a command, run it and check the output
  - If it references behavior, check the scenarios cover it
  - If it references code changes, read the relevant files
- If the project uses gherclj and the criteria include "@wip removed", grep the feature files to confirm

### 7. No regressions
- If the test suite showed failures unrelated to this bead,
  note them but don't fail the bead for pre-existing issues

## What NOT to do

- Do NOT modify code. You are read-only.
- Do NOT re-implement anything. If the work is wrong, reopen and explain.
- Do NOT close a bead you're unsure about. When in doubt, reopen with
  questions for the worker.

## Arguments

$ARGUMENTS - Optional: one or more specific bead IDs to verify instead of checking all unverified
