# SCOP Development Skill

A Codex skill for developing, fixing, and reviewing the [scop](https://github.com/mengxu98/scop) R package.

It covers API compatibility, optional R backends, renv/pak environments, documentation, testing, and algorithm validation. It favors direct implementations, boundary validation, and checks matched to the change. Apply the guidance to the task at hand; current user and repository instructions take precedence.

## Setup

Copy the `scop-development` folder into your Codex skills directory (usually `~/.codex/skills/`).

## Usage

```text
Use $scop-development to review this SCOP change, check optional backend
handling and API compatibility, and run the relevant validation.
```

## Files

- [SKILL.md](scop-development/SKILL.md) — scope and development workflow.
- [implementation.md](scop-development/references/implementation.md) — code, public documentation, and repository assets.
- [backends.md](scop-development/references/backends.md) — optional R dependencies and Python runtime rules.
- [environment.md](scop-development/references/environment.md) — development isolation, dependency updates, and lockfiles.
- [migration.md](scop-development/references/migration.md) — algorithm parity and performance evidence.
- [validation.md](scop-development/references/validation.md) — formatting, generated files, checks, and handoff.

This skill supports package development; it is not a single-cell analysis tutorial.
