# macos-tcc-automation-permissions

**Make unattended macOS automation survive permission resets: AppleScript -1743/-1728, launchd jobs, screen recording, and real vs synthetic clicks.**

[![test](https://github.com/Kein-chronicle/macos-tcc-automation-permissions/actions/workflows/test.yml/badge.svg)](https://github.com/Kein-chronicle/macos-tcc-automation-permissions/actions/workflows/test.yml) ![license](https://img.shields.io/badge/license-MIT-blue.svg)

## Why

macOS ties Automation, Accessibility and Screen Recording grants to the launching app, and a Homebrew or Python upgrade silently drops them. This decodes the cryptic errors and shows the launcher-app pattern that keeps grants stable.

> **Status: doc-only.** Observed on recent macOS releases; permission UI wording changes. Grants are human-only; nothing here lets a program grant itself permission.

## Install

**As a Claude Code skill** (Claude uses it when the task matches the skill description):

```sh
git clone https://github.com/Kein-chronicle/macos-tcc-automation-permissions.git ~/.claude/skills/macos-automation-permissions
```

For a single project, clone into `<project>/.claude/skills/macos-automation-permissions` instead, then start a new Claude Code session.

**Just read it:** the whole skill is [SKILL.md](SKILL.md). It is plain Markdown; copy any part into your own `CLAUDE.md` or prompts.

**Requires:** nothing to read it; Python 3.10+ only to run `validate.py`.

## What it covers

- Error decoder: `-1743` automation denied, `-1728` accessibility, wallpaper-only screenshots.
- Make permissions stick for launchd jobs with a small signed launcher that spawns the worker as a child.
- Real OS input versus synthetic input (CDP clicks vs OS-level clicks), typing into a console under a CJK input method, and how to diagnose before reporting.

## Limits

- Observed on recent macOS releases; permission UI wording changes.
- Grants are human-only; nothing here lets a program grant itself permission.

## Related projects

These solve neighbouring problems; use them when they fit better.

- **Apple's TCC documentation**: the official description of the permission system

## More from the same collection

Small, tested tools that grew out of running AI agents and game/app pipelines for months:

- [**safe-delete-sole-copy-check**](https://github.com/Kein-chronicle/safe-delete-sole-copy-check): Before you delete a project folder: is any of it the only copy? Checks the live remote, unpushed commits and tags, untracked and ignored files, and verifies copies by hash.
- [**exif-orientation-guard**](https://github.com/Kein-chronicle/exif-orientation-guard): Find and fix images whose stored size differs from their displayed size because of an EXIF orientation tag.
- [**ios-simulator-ui-test-hygiene**](https://github.com/Kein-chronicle/ios-simulator-ui-test-hygiene): Keep iOS simulator UI tests and crash-fuzz runs from dying on a laptop: the 16 GB memory wall and the crash-report dialog that never goes away.

## Development

```sh
python3 validate.py
```

This only validates the skill file (frontmatter and referenced paths). See [CONTRIBUTING.md](CONTRIBUTING.md).

## License

MIT. See [LICENSE](LICENSE).

If this saved you a debugging session, a star helps other people find it. Bug reports and failing test cases are even better.

<sub>Keywords: macos, tcc, applescript, launchd, automation, accessibility, claude-code, claude-skills</sub>
