# template

The scaffolding an open source project of mine gets on day one, with the
project taken out of it. Copy what you need, delete what you don't.

Nothing here is tied to a language or a build tool. The one idea holding it
together is that **every verb is a script with a fixed name**:

```
script/bootstrap install what the project needs, from a fresh clone
script/server    run it locally
script/build     produce the artifacts
script/test      run the tests
script/lint      run the linters
script/publish   cut a release
script/changelog turn the CHANGELOG entry into release notes (called by publish)
```

A contributor clones the repo and runs `script/test`. CI runs `script/test`.
Neither has to know whether that means `npm test`, `cargo test`, `pytest` or a
shell loop over fixtures — the script knows, and it is the only place that does.
Rewrite the bodies, keep the names. This is
[Scripts to Rule Them All](https://github.blog/engineering/scripts-to-rule-them-all/),
and it is the reason the CI workflow here is eleven lines and language-agnostic.

## What's in the box

| Path | What it is |
| --- | --- |
| [script/](script/) | The entry points above. The stubs exit 1 until you fill them in — a check that passes without running anything is worse than no check. |
| [.github/workflows/ci.yml](.github/workflows/ci.yml) | Lint, test, build — eleven lines that call `script/*` and nothing else. Add the setup step your toolchain needs, and uncomment the push and pull request triggers once the scripts are real. |
| [.github/workflows/publish.yml](.github/workflows/publish.yml) | Publishes on a `v*` tag, via npm trusted publishing (OIDC). Swap the last two steps for your registry, keep the shape: **a tag triggers the release, and no long-lived token is stored anywhere.** |
| [.github/dependabot.yml](.github/dependabot.yml) | Monthly dependency updates. Set the ecosystem. |
| [CHANGELOG.md](CHANGELOG.md) | [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) format, with the house rules for writing an entry. |
| [CONTRIBUTING.md](CONTRIBUTING.md) | Setup, how to report a bug, what a pull request needs, how a release works. Fill in the blanks marked `<…>`. |
| [LICENSE](LICENSE) | MIT. Change the name and year, or replace it wholesale. |
| [.editorconfig](.editorconfig) | UTF-8, LF, two spaces, trailing newline. Editor-agnostic, no plugin required for most. |
| [.gitignore](.gitignore) | OS and editor noise only. Add your build output and dependency directory. |

## How a release works

`script/publish [version]` bumps the version, calls `script/changelog` to cut
`## [Unreleased]` into a released entry, builds, commits, tags and pushes. The
tag is what triggers publishing, and the changelog entry becomes the body of the
GitHub release — nothing to paste in by hand, and nothing to remember.

Both scripts are Node and assume a `package.json` and a `CHANGELOG.md`. In a
project that has neither, they are the two files worth rewriting rather than
deleting: the flow — one command, tag-triggered publish, generated release notes
— is the part that carries.

## What is deliberately missing

- **Issue and pull request templates.** They pay off when strangers file issues
  in volume. Before that they are a form standing between you and a bug report.
- **A code of conduct.** Add one when the project has contributors who are not
  you; [Contributor Covenant](https://www.contributor-covenant.org/) is the
  default and takes a minute.
- **A security policy.** Add `SECURITY.md` when there is something to disclose
  responsibly — anything handling untrusted input, auth, or user data.
- **Branch protection, CODEOWNERS, a release drafter.** Repository settings and
  process, not files to copy. Turn them on when more than one person merges.

## Adopting it

```bash
git clone https://github.com/stamat/template.git my-project
cd my-project
rm -rf .git && git init
```

Then: fill in the `script/` bodies, add the setup step to `ci.yml`, replace the
`<…>` placeholders in `CONTRIBUTING.md`, and write the first `[Unreleased]`
entry. Delete this README — it documents the template, not your project.
