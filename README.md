# lessons

A `LESSONS.md` ledger for repos that agents maintain. A review of the work merged since the last review turns each mistake into a lesson, and a lesson seen on 3 separate days becomes a code standard.

It ships as an **[Agent Skill](https://agentskills.io)**: pure markdown, no scripts. It runs on **Claude Code, Codex, Gemini CLI, Cursor**, and any other skills-compatible agent.

## Install

### Universal (any skills-compatible agent)

```text
npx skills add OpenSWE/lessons
```

### Claude Code (plugin)

```text
/plugin marketplace add OpenSWE/lessons
/plugin install lessons@lessons
```

## What it does

Run `/lessons` in a repo, by hand or from a nightly job. The skill ([`skills/lessons/SKILL.md`](skills/lessons/SKILL.md)) does this:

- **Reviews the range.** It reads every commit since the `reviewed-through` marker in `LESSONS.md`, plus the PRs merged and the issues closed or reopened in that range. It marks each commit as *redo*, *should not have merged*, *thin* (weak verification), *done right* or *clean*.
- **Writes lessons.** Each lesson is a rule, then why, then evidence (shas and issue numbers), then a *Seen N×* count of the separate days a review found it.
- **Promotes.** A lesson seen on 3 separate days moves into the repo's code standards, which every later review checks.
- **Seeds a new repo.** The first run creates `LESSONS.md` with about 30 [starter lessons](skills/lessons/LESSONS.md) at *Seen 0×*. It sets up the code standards from the [starter standards](skills/lessons/docs/code-standards.md), including five acceptance criteria such as a test that fails without the change, real use, and "a check that wasn't run is listed as not run". It adds pointer lines to `AGENTS.md`, so every agent reads both files before it changes code.

## Links

- **Inspiration**: [LESSONS.md](https://github.com/yetone/magpie/blob/main/LESSONS.md), the lessons file of a repo whose code agents write, review, merge and release. This skill generalizes its review loop.

## License

MIT. The starter lessons and starter standards are adapted from MIT-licensed work; see [THIRD-PARTY-NOTICES](THIRD-PARTY-NOTICES).
