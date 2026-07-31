# template

The scaffolding an open source project of mine gets on day one, with the
project taken out of it. Copy what you need, delete what you don't.

Nothing here is tied to a language or a build tool — Node, Python, Ruby, PHP,
Go, a pile of shell scripts, all the same. The one idea holding it together is
that **every verb is a script with a fixed name**:

```
script/bootstrap install what the project needs, from a fresh clone
script/server    run it locally
script/build     produce the artifacts
script/test      run the tests
script/lint      run the linters
script/publish   cut a release
script/version   write the version wherever it lives (called by publish)
script/changelog turn the CHANGELOG entry into release notes (called by publish)
```

A contributor clones the repo and runs `script/test`. CI runs `script/test`.
Neither has to know whether that means `npm test`, `pytest`, `bundle exec rspec`
or `vendor/bin/phpunit` — the script knows, and it is the only place that does.
Rewrite the bodies, keep the names. This is
[Scripts to Rule Them All](https://github.blog/engineering/scripts-to-rule-them-all/),
and it is why the CI workflow here is language-agnostic: it calls `script/*` and
nothing else, so the only thing you change per project is the setup step.

## What's in the box

| Path | What it is |
| --- | --- |
| [script/](script/) | The entry points above. The stubs exit 1 until you fill them in — a check that passes without running anything is worse than no check. `publish` and `changelog` are written, and work as they are. |
| [.github/workflows/ci.yml](.github/workflows/ci.yml) | Bootstrap, lint, test, build. Uncomment the setup step for your toolchain and the push/pull request triggers. |
| [.github/workflows/publish.yml](.github/workflows/publish.yml) | Publishes on a `v*` tag. npm is wired up; the commented blocks are the same thing for PyPI, RubyGems and Packagist. Keep the shape: **a tag triggers the release, over OIDC, with no token stored anywhere.** |
| [.github/dependabot.yml](.github/dependabot.yml) | Monthly updates for your ecosystem and for the pinned action majors. |
| [.github/ISSUE_TEMPLATE/](.github/ISSUE_TEMPLATE/) | Bug report and feature request as [issue forms](https://docs.github.com/en/communities/using-templates-to-encourage-useful-issues-and-pull-requests/syntax-for-issue-forms) — required fields, so "it doesn't work" arrives with a version and a reproduction. `config.yml` holds the chooser links. |
| [.github/PULL_REQUEST_TEMPLATE.md](.github/PULL_REQUEST_TEMPLATE.md) | What and why, how to check it, and the checklist that keeps the changelog entry from being the thing everyone forgets. |
| [CHANGELOG.md](CHANGELOG.md) | [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) format, with the house rules for writing an entry. |
| [CONTRIBUTING.md](CONTRIBUTING.md) | Setup, how to report a bug, what a pull request needs, how a release works. Fill in the blanks marked `<…>`. |
| [LICENSE](LICENSE) | MIT. Change the name and year, or replace it wholesale. |
| [.editorconfig](.editorconfig) | UTF-8, LF, two spaces — four where the language settled on four. |
| [.gitignore](.gitignore) | OS and editor noise, with the per-language lines commented out below it. |

## Wiring it to your language

Four files, one line each, and then nothing else in the repository knows what
you are writing in:

| | Node | Python | Ruby | PHP |
| --- | --- | --- | --- | --- |
| `script/bootstrap` | `npm ci` | `uv sync` | `bundle install` | `composer install` |
| `script/test` | `npm test` | `uv run pytest` | `bundle exec rspec` | `composer test` |
| `script/lint` | `npm run lint` | `uv run ruff check .` | `bundle exec rubocop` | `composer lint` |
| `script/version` | `npm version "$1" --no-git-tag-version` | `uv version "$1"` | `sed` into `version.rb` | `composer config version "$1"` |

`script/build` and `script/server` are whatever those mean here — for a library
with nothing to compile, `build` can be `exit 0` with a comment saying why.

## How a release works

```bash
script/publish          # or: script/publish 2.1.0
```

It reads the current version off the last `v*` tag, offers the patch bump, then
runs `script/version` and `script/changelog` if they exist, commits, runs
`script/build` if it exists, commits that, tags, and pushes. The tag is what
triggers publishing, and the changelog entry becomes the body of the GitHub
release — nothing to paste in by hand, nothing to remember.

Both scripts are bash, and know nothing about your project beyond the names
above. `script/changelog` cuts `## [Unreleased]` out of `CHANGELOG.md` into a
dated entry and leaves the entry in the temp directory for `gh release create
--notes-file`; a repository without a `CHANGELOG.md` skips it, silently and
successfully.

## What is deliberately missing

- **A code of conduct.** Add one when the project has contributors who are not
  you; [Contributor Covenant](https://www.contributor-covenant.org/) is the
  default and takes a minute.
- **A security policy.** Add `SECURITY.md` when there is something to disclose
  responsibly — anything handling untrusted input, auth, or user data. The
  issue chooser already points at private advisories.
- **Branch protection, CODEOWNERS, a release drafter.** Repository settings and
  process, not files to copy. Turn them on when more than one person merges.

## Adopting it

```bash
git clone https://github.com/stamat/template.git my-project
cd my-project
rm -rf .git && git init
```

Then: fill in the `script/` bodies, uncomment the setup step and the triggers in
`ci.yml`, replace the `<…>` placeholders in `CONTRIBUTING.md` and
`.github/ISSUE_TEMPLATE/config.yml`, and write the first `[Unreleased]` entry.
Delete this README — it documents the template, not your project.
