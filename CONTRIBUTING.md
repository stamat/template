# Contributing to <project>

Issues and pull requests are welcome. Taking part means keeping to the
[Code of Conduct](CODE_OF_CONDUCT.md).

<One paragraph on what this project is for and what it refuses to become. This
is the most useful thing on the page: it is what tells someone whether the
feature they are about to build has a home here, before they build it.>

## Getting set up

```bash
git clone https://github.com/<owner>/<project>.git
cd <project>
script/bootstrap
```

```bash
script/server    # run it locally
script/build     # produce the artifacts
script/test      # run the tests
script/lint      # run the linters
```

<Anything non-obvious about the layout: which directory is the source of truth,
which is generated, what the one command is that proves a change works.>

## Reporting a bug

[Open an issue](../../issues/new/choose) — the form asks for what you ran, what
you expected, the version and the environment, because those are the four things
every fix starts from. A reproduction is worth more than a description of one.

## Pull requests

- **Add a test.** A bug fix gets a test that fails without the fix.
- **Match the surrounding style.** `script/lint` is the authority, and CI runs it.
- **Add a changelog entry** under `## [Unreleased]` in
  [CHANGELOG.md](CHANGELOG.md) — that file explains the format.
- **Keep the diff about one thing.** A rename bundled with a fix is two reviews
  wearing one hat.

Commit messages are freeform, write something that says what changed.

## How a release works

Maintainer flow, recorded here so the automation isn't a mystery:

`script/publish [version]` takes the current version from the last `v*` tag,
writes the new one with `script/version`, runs `script/changelog` to cut
`[Unreleased]` into a released entry, builds, commits, tags and pushes. Pushing
the tag triggers [publish.yml](.github/workflows/publish.yml), which publishes
via trusted publishing — OIDC, no tokens stored anywhere. The changelog entry
becomes the body of the GitHub release verbatim.
