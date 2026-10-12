# Code standards

These rules come from what reviews keep asking for. A reviewer checks each one, and a change that
misses one gets a comment that names it. Lessons seen on 3 separate days in
[LESSONS.md](../LESSONS.md) are added here.

## What a change contains

| Rule | What a reviewer looks at |
| --- | --- |
| A change does only what its goal needs. | Every change in behavior has a reason. Unrelated refactors, extra features and policy changes go in a change of their own. |
| A fix covers exactly the cases it means to. | Which cases change, and which neighboring ones stay the same. Different errors are handled as different errors. |
| A change holds on the whole path. | Follow it from the user's input to the end result, through the callers, the state it changes and the side effects. A setting saved under one id and read under another, or an update button that updates a binary other than the one that runs, passes every unit test. |
| A fix reaches every sibling of the bug. | Grep for the same pattern. Every command, integration, guard of the same shape and caller of a changed predicate gets the fix in the same change. So do the other branches of the operation and the other messages that name the same rule. |
| A change re-derives everything built on what it changes. | When a formula, a list, a layout breakpoint or the timing of an async step changes, find every caller, saved copy and test that assumed the old meaning, and run it. |
| Checks cover the edges the change touches. | For numbers, the boundaries. For paths, each supported OS. For concurrency, races and stale state. Pick checks by risk. |

## Tests

| Rule | What a reviewer looks at |
| --- | --- |
| A fix comes with a test that fails without it. | The reviewer puts the old code back and runs the test, and it must fail. The description says what it failed with. |
| Tests use real inputs. | Fixtures look like real requests, real serialized output and real files on disk, with missing fields, empty values and defaults. |
| Tests check behavior, not implementation. | Assert what the user or caller needs. A format change or an internal refactor leaves the tests green, unless exact bytes are the requirement. |
| A test that fails on `main` is a bug to fix now. | "Fails the same on main" and "passes alone" are not baselines. Fix it at its cause, or open an issue that names the cause, in a commit of its own, before you ship on top of it. Run a test many times under the race detector before you call it a flake. |
| Tests stay out of the real machine. | Run them under a temporary HOME with the toolchain's caches pinned. A test never reads or writes the user's real config. |
| Whatever a test starts ends when the test does. | Each fake process, thread or async refresh a test starts is stopped or waited for before the test returns. Otherwise it writes into the next test's folder, or runs for days. |
| A timing test leaves room on both sides. | Bound a test by events, never by wall-clock time a loaded runner can exceed. Keep a threshold's fixture far from the threshold. Reproduce a timing failure by injecting the delay, and list every timing assumption in the test when you fix one. |

## Acceptance criteria

A change is merged or released only when it shows each of the following. Say which ones were
run and what they printed. A check that wasn't run is listed as not run, never left out.

1. **A test that fails without the change.** Put the old code back and run the new test. It must
   fail with a real failure that names the behavior. A compile error or a skipped test doesn't
   count. Then restore the change and check the test passes.
2. **Tests in a sandbox.** Run them under a temporary HOME, with the toolchain's caches pinned
   from before HOME is swapped. Remove the HOME afterwards.
3. **The build and the suite.** The formatter prints nothing on the changed files. The linter
   and the build pass for every supported OS, and the full test suite passes. A cross-OS build
   may skip test files; run the linter for each OS too. In a chain of commands, set
   `set -o pipefail`, so `test | tail && git push` stops on a failure.
4. **UI tests in every engine and language.** A UI change runs the UI tests it touches in every
   supported browser engine and every supported language. Run the whole suite when the change
   adds an API the UI calls, a selector other pages share, a CSS feature an older engine lacks,
   or a layout breakpoint. If CI doesn't run this suite, run it locally.
5. **Real use where possible.** A change to an integration is also checked against the real
   thing: a real account or key, the real config format, the real reply. Use a sandbox with
   copies of the credentials, and leave the live service alone. If a real check isn't possible,
   say so. What wasn't tried for real isn't built on, and isn't told to users as working. A
   platform report is checked on that platform; a cross-compile is not a test.

A PR is merged only after the reviewer has pulled it, merged it onto current `main` and run the
checks above on the result. Write on the PR what was run and at which head, then merge with
`gh pr merge --match-head-commit <reviewed sha>`. A push during the review then stops the merge.
A head pushed after the review is reviewed again. A Draft PR is merged only after it is marked
Ready for review.

A release counts only when all of these hold:

- A CI run on the tagged commit, or a later one containing it, finished green. A run cancelled
  by the next push is not a pass.
- The tag is on `main`, an ancestor of `origin/main`.
- The release has all its assets, and is marked as the latest.

Check `git tag` for the next free version first, because releases can be made from more than one
place. A change that only adds tests doesn't get a release of its own.

## Reviewing a PR

A reviewer checks each pattern that applies:

- **Run it.** Pull the head, merge it onto current `main` and run the acceptance checks. Reading
  the diff alone isn't a review.
- **Break it on purpose.** Revert the fix, or break the code it guards in several ways, and check
  that the new tests catch each break. A reviewer may add tests of their own.
- **Check the vendor, not the PR's account of it.** Read the upstream source for a vendor's
  behavior, and try the change against the real upstream.
- **Check each platform and architecture.** An integer conversion safe on one architecture can
  wrap on another. Error codes differ by OS.
- **Follow the whole path.** A pick is saved and read under the same id. A button works on the
  thing that actually runs. Names don't collide with another component's.
- **Classify narrowly.** A mark or error class covers only the case it describes.
- **A preview writes nothing.** A dry run, plan or preview has no side effects.
- **Leave deliberate decisions to the owner.** A PR that changes a product decision the owner
  made waits for the owner.
- **Compare test pass counts with `main`.** A test that fails on `main` too gets fixed or filed
  before the merge.
- **Docs match the source.** A doc is checked against the code it links, and every link resolves.
- **Keep the PR to its goal.** Unrelated tests and refactors go in a PR of their own. Comments
  name functions, not line numbers. Fixture names match what they stand for.

## Commits

A commit message names the areas it changes, then says what the user now sees, as plain
behavior: `api: a 429 that says the balance is spent reads as out of credit, not a rate limit`.
Give the issue number or the reporter. The body says how the change was verified, and which test
fails without it.
