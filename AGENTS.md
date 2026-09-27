<!--
  - SPDX-FileCopyrightText: 2026 Nextcloud GmbH and Nextcloud contributors
  - SPDX-FileCopyrightText: 2026 XeniaCloud
  - SPDX-License-Identifier: AGPL-3.0-or-later
-->

# Agent Guidelines for Nextcloud Server

This file provides instructions for AI coding agents (Claude Code, GitHub Copilot, Cursor, Windsurf, and others) operating on this repository. Read it before generating any code, commits, or pull requests.

---

## Nextcloud Contribution Policy

> **Fork amendment (Krateos-BV).** This file is inherited from upstream
> `nextcloud/server`. In this fork, work is reviewed on the pull request
> itself rather than before it is opened, so the agent opens its own PRs and
> writes their descriptions (see "What this agent may do in this fork"). Every
> other rule below stands unchanged. **This amendment applies only to pull
> requests targeting branches of `Krateos-BV/xeniacloud-server`.** Anything
> destined for an upstream `nextcloud/*` repository follows the unmodified
> upstream policy, where a human opens the PR and writes it in their own
> words.

All contributions generated or assisted by this agent must fully comply with:

- **[AI Contribution Policy](https://github.com/nextcloud/.github/blob/master/AI_POLICY.md)** - the primary reference for AI-specific rules, covering disclosure, author accountability, communication, security, licensing, code quality, and autonomous agent behavior.
- **[Contribution Guidelines](https://github.com/nextcloud/.github/blob/master/CONTRIBUTING.md)** - covering testing requirements, the Developer Certificate of Origin (DCO), license headers, conventional commits, and translations. These apply in full to all contributions regardless of how they were produced.

### What this agent must always do

- Add an `Assisted-by: AGENT_NAME:MODEL_VERSION` git trailer to every commit containing AI-assisted content.
- Ensure every pull request includes a disclosure of AI tool use in the PR description.
- Produce focused, scoped pull requests that address exactly one concern. Do not touch unrelated files or introduce incidental refactors.
- Verify all dependencies against actual package registries before suggesting them. Do not use hallucinated or unverified package names.
- Write code comments that document the code, never the process that produced it:
  - Comments describe what the code does - method signatures, behavior, and constraints the code itself cannot express (e.g. a non-obvious invariant or workaround).
  - Never add comments that document progress, decisions, or changes (e.g. "changed X to Y", "as requested", "this fixes ...", "previously this did ..."). That belongs in the commit message or PR discussion; in the code it goes stale and becomes misleading.
  - Do not narrate self-explanatory code. If the code is readable without a comment, omit the comment.
  - Keep comments brief - short and simple, matching the comment density of the surrounding code.
- Reuse existing helper functions and utilities instead of re-implementing their logic inline. When fixing a flawed pattern, fix every occurrence of it across the changed code, not only the instance that was pointed out.
- Run permission and access-control checks before the operation they guard, never after it and never only in the UI layer.
- When adding or changing user-facing functionality, wire it up in every context where the affected component is used - the default authenticated view, public share pages, and embedded contexts such as the Smart Picker and reference widgets. When emitting new events, verify that every consumer of the component subscribes to and handles them.
- Explicitly inform the contributor when any action they are about to take, or have taken, would violate the AI Contribution Policy or the Contribution Guidelines. Do not silently proceed. State which rule is at risk and what the contributor should do instead.
- Warn the contributor if a pull request is growing too large. A PR approaching several thousand lines of changed code is a signal that it should be split into smaller, focused PRs. Suggest a logical split before the PR is opened, not after.
- Recommend opening a ticket for discussion before starting implementation whenever a feature or change is sufficiently complex - for example when it touches multiple subsystems, requires architectural decisions, or the right approach is not yet clear. A ticket allows maintainers and the contributor to align on direction before code is written, avoiding wasted effort on a PR that may be rejected or require fundamental rework.
- When adding a new PHP file, run build/autoloaderchecker.sh

### What this agent must never do

- Send security reports autonomously, or submit anything to an upstream `nextcloud/*` repository without a human opening it. (Issues and pull requests *within this fork* are covered by the fork amendment above.)
- Add `Signed-off-by` tags to commits. Only the human contributor can certify the Developer Certificate of Origin.
- Generate or submit security reports without independent human verification. Report verified vulnerabilities via [HackerOne](https://hackerone.com/nextcloud), not as GitHub issues.
- Write PR descriptions, review comments, or issue reports on behalf of the contributor, or put words in the contributor's mouth anywhere. Agent-authored PR descriptions in this fork are the agent's own words, and are labelled as such.
- Fully automate the resolution of issues labeled [`good first issue`](https://github.com/issues?q=org%3Anextcloud+label%3A%22good+first+issue%22) or similar beginner-friendly labels.
- Submit code that has not been reviewed and cleaned up by the contributor. Dead code, redundant logic, excessive comments, malformed or garbled characters (e.g. `�` replacement characters), and unrelated changes must be removed before submission.

### What this agent may do in this fork

- Open issues and pull requests against `Krateos-BV/xeniacloud-server` without
  waiting for a human to do it, and write the PR description itself. The
  description must still disclose AI tool use, and must say plainly what was
  verified and what was not, so the reviewer can tell evidence from assertion.
- This does not relax the DCO rule above. The agent still never adds
  `Signed-off-by` - only the human contributor can certify it.

---

## Repository-Specific Requirements

### Commit format

Use [Conventional Commits](https://www.conventionalcommits.org) for all commit messages:

```
<type>(<scope>): <short description>

[optional body]

Assisted-by: AGENT_NAME:MODEL_VERSION
```

Common types: `feat`, `fix`, `refactor`, `test`, `docs`, `chore`, `perf`, `build`, `ci`.  
The scope should match the affected component or app (e.g. `files_sharing`, `core`, `encryption`).

Example:
```
feat(files_sharing): allow sharing with contacts

Assisted-by: ClaudeCode:claude-sonnet-4-6
```

### Tests

- Every changed or added code segment must be covered by unit tests. Pull requests without tests for new or modified logic will not be accepted.
- In areas where unit testing is currently difficult, refactoring to enable testability is encouraged alongside the bug fix.
- New features must be manually tested on a live Nextcloud instance by the human contributor before submission. Providing test steps for an agent to execute is not a substitute.
- You can run tests with `NOCOVERAGE=0 ./autotest.sh <db> <path>` with db being either sqlite or pgsql

### Developer Certificate of Origin (DCO)

The project uses the DCO as an additional safeguard. Only the human contributor may add the `Signed-off-by` trailer - agents must not add it:

```
Signed-off-by: Random J Developer <random@developer.example.org>
```

Contributors can sign automatically with `git commit -s` after configuring `user.name` and `user.email`.

### License headers

Every new file must include the correct SPDX license header. For AGPL-3.0-or-later (the default for this repository):

```php
/**
 * SPDX-FileCopyrightText: <year> <name>
 * SPDX-License-Identifier: AGPL-3.0-or-later
 */
```

See [HowToApplyALicense.md](https://github.com/nextcloud/server/blob/master/contribute/HowToApplyALicense.md) for details on per-language formats. AI-generated code must not include material from sources incompatible with AGPL-3.0-or-later.

### Security

- Do not open GitHub issues for potential vulnerabilities. Report them via [HackerOne](https://hackerone.com/nextcloud) following the [security policy](https://nextcloud.com/security/).
- AI-generated security reports must be independently verified by the human contributor before submission.
- Manually verify all access control logic, authentication patterns, and dependency names - AI tools are known to hallucinate package names and reproduce vulnerable patterns.

### Scope of this repository

This repository covers the Nextcloud server core and the bundled apps: files, encryption, external storage, sharing, deleted files, versions, LDAP, and WebDAV Auth. Issues and changes for other components belong in their respective repositories under the [Nextcloud GitHub organization](https://github.com/nextcloud/).

---

## Further Reading

- [Local CONTRIBUTING.md](.github/CONTRIBUTING.md)
- [Nextcloud Contribution Guidelines](https://github.com/nextcloud/.github/blob/master/CONTRIBUTING.md)
- [AI Contribution Policy](https://github.com/nextcloud/.github/blob/master/AI_POLICY.md)
- [Developer Certificate of Origin](https://github.com/nextcloud/server/blob/master/contribute/developer-certificate-of-origin)
- [How to Apply a License](https://github.com/nextcloud/server/blob/master/contribute/HowToApplyALicense.md)
- [Developer Manual](https://docs.nextcloud.com/server/latest/developer_manual/)
- [Security Vulnerability Reporting (HackerOne)](https://hackerone.com/nextcloud)
