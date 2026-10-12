---
name: lessons
description: "Review a repo's merged work since the last review, record each lesson in LESSONS.md with its evidence, and promote lessons seen on 3 separate days into the code standards."
disable-model-invocation: true
---

Agents that change code in this repo read `LESSONS.md` first. You keep it. You review the
commits merged since the last review, turn each mistake into a **lesson**, and **promote** a
lesson into the code standards once reviews have seen it on 3 separate days. A lesson written
down reaches every later session. A promoted lesson is checked by every review.

## Steps

1. **Find the range.** Run `git fetch`, then read the first line of `LESSONS.md`:
   `<!-- reviewed-through: <sha> (<date>) -->`. The range is `<sha>..origin/main`. If the repo has
   no `LESSONS.md`, set it up first:
   - Find the standards file: the code-standards doc the repo's `AGENTS.md` names. If there is
     none, copy this skill's [docs/code-standards.md](docs/code-standards.md) to the repo's
     `docs/code-standards.md`. If there is one, add each rule from this skill's copy that it does
     not already state.
   - Copy this skill's [LESSONS.md](LESSONS.md) to the repo root, and put the standards file's
     path in place of `<standards file>`. Step 5 sets its reviewed-through line.
   - Add both pointer lines to the repo's `AGENTS.md` (or `CLAUDE.md`).
   - Review the last 24 hours.

   Done when you can name the range's first and last sha and its commit count.

2. **Review every commit in the range.** Read each commit's diff and body. Then `git log` past it
   for the files and issue numbers it touches. Also read the PRs merged and the issues closed or
   reopened in the range (`gh`). The lessons already in `LESSONS.md` are the patterns to look
   for. Give each commit one mark:
   - **redo**: a later commit fixed it again, reverted it, or covered a sibling case it missed.
   - **should not have merged**: it shipped past a red test, past a problem a review named, or
     with no record of what was run.
   - **thin**: its verification can't show the fix works. It has no test that fails without it,
     no real-use check where one was possible, or it left a check out instead of listing it as
     not run.
   - **done right**: it did well what a lesson asks. Worth copying.
   - **clean**: none of these.

   Done when every commit in the range has a mark, and each redo names the commit that redid it.

3. **Write the lessons.** Group the findings by cause. For each group, look for the lesson that
   already states its rule:
   - In `LESSONS.md`: add the evidence, and add the review's date to its Seen list if it is new.
     An inherited lesson becomes Seen 1×.
   - In the standards file: the rule was missed again. Add the evidence under its bullet in
     "Moved to the standards".
   - Nowhere: add a new lesson, Seen 1×, under the heading that fits.

   Add each **done right** commit as a "Done right" bullet under its lesson. Done when every
   redo, should-not-have-merged and thin mark is evidence under some lesson.

4. **Promote.** Move each lesson with 3 or more dates in its Seen list into the standards file,
   under the section that fits. Write the rule there with its strongest evidence. In
   `LESSONS.md`, leave one bullet under "Moved to the standards": the rule in bold and its three
   dates. Done when no lesson in `LESSONS.md` has 3 dates.

5. **Check and commit.** Run `git cat-file -e` on every sha you cited, and fix each one that
   fails. Set the reviewed-through line to the range's last sha and its date. Commit only
   `LESSONS.md`, the standards file and the pointer lines, as `lessons: review of <dates>`. The
   body gives the commit count and the lessons that are new, seen again and promoted, then
   "Ran: every cited sha checked with git cat-file". Push if the repo's instructions say to.

## A lesson

```markdown
**<The rule, as the action to take.>**
- <What happened: sha or #issue, what went wrong, which commit fixed it.>
- <How to apply it: the check or command that would have caught it.>
- Done right on <date>: <sha>, <what it did>.
- Seen 2× (2026-10-05, 2026-10-06).
```

- Write the rule as the action to take: "Reproduce the reporter's exact case before you call
  it fixed."
- Every claim from this repo cites a sha or issue number a reader can open.
- Seen counts separate review days. Six commits on one day are Seen 1×.

## Pointer lines

Add to the repo's `AGENTS.md`, with the standards file's real path:

```markdown
Write and review changes against the [code standards](docs/code-standards.md). A change is merged
or released only when it meets their acceptance criteria.

Before changing code, read [LESSONS.md](LESSONS.md): what recent merged work got wrong and the
rule that would have caught it.
```
