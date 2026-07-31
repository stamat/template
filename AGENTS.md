# <project> — agent notes

<One line on what this project is. Then leave the scope paragraph to
CONTRIBUTING.md and point at it — one source of truth.>
Read [CONTRIBUTING.md](CONTRIBUTING.md) first — it defines what belongs in this
project and what a pull request needs.

## Commands

```bash
script/bootstrap # install what the project needs, from a fresh clone
script/server    # run it locally
script/build     # produce the artifacts
script/test      # run the tests
script/lint      # run the linters (the authority; CI runs it)
```

## Layout

<Only what an agent cannot infer from the tree: which directory is the source
of truth, which is generated, where tests live, what else a new module must
touch (an export map, a manifest, a registry).>

## Principles

- **Test-driven.** The test is the spec; write it first. A failing test means
  the code is wrong — never weaken, skip, or delete a test to make it pass. If
  the test itself is wrong, say so and let review decide.
- **YAGNI.** Build only what the task needs — no speculative options,
  abstractions, or "for later" scaffolding.
- **Native / stdlib first.** In order: what's already in this repo → the
  platform → the standard library → new code. A new dependency is a last
  resort and needs a reason.
- **Root cause over symptom.** Fix where all callers route through, not the
  one path the bug report names.
- **Delete dead code.** No commented-out blocks, no "for later" exports — git
  remembers.

## Boundaries

- **Always:** run `script/lint` and `script/test` before calling work done;
  pair every fix or feature with a test; add a changelog entry under
  `## [Unreleased]`.
- **Ask first:** changing a public API; adding a dependency; <the project's
  own "ask first" — a schema migration, a new top-level module, a config
  format change>.
- **Never:** edit generated directories <name them>; weaken, skip, or delete
  a test to make it pass; bump the version or publish — a tag does that.

## Before adding a feature

Run this checklist before writing any code; stop at the first "no".

1. **Does the platform or standard library already do it?** If so, there is
   no feature.
2. **Search for prior art.** How do similar projects do it? What interface do
   they expose? Cite what you found — a URL per fact, no guesses. How can we
   improve on it? If the answer is "we can't", would we benefit from having it
   here at all?
3. **Does it fit the project?** CONTRIBUTING.md says what this project is for
   and what it refuses to become — check against that paragraph, before
   building, not after.
4. **Still yes?** Build the smallest version that works.

## Non-obvious rules

<The traps only a maintainer knows: the doc comment a build step parses, the
file that looks editable but is generated, the field two systems read and
disagree on. One bullet each. If you catch an agent — or yourself — making the
same mistake twice, the correction goes here.>
