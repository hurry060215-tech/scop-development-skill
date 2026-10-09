---
name: scop-development
description: Develop, repair, or review the scop R package with scoped API changes, runtime-optional backends, synchronized documentation, and behavioral validation. Use for SCOP source and cross-package integration work, not ordinary analysis using an installed package.
---

# SCOP development

Use this skill as a development and review reference. Adopt relevant guidance according to the concrete task and feedback; do not mechanically apply every clause or initiate repository-wide cleanup. Current user instructions and applicable repository instructions take precedence. Algorithm migration guidance applies only to the corresponding requested task.

## Establish scope and contracts

- Inspect `git status`, applicable `AGENTS.md`, adjacent functions, direct callers, and actual dependencies before editing. Preserve unrelated changes. For PR work, inspect current remote state and checks rather than relying on stale local evidence.
- Establish input/output contracts, ownership, compatibility, and local naming/layout/documentation conventions. Preserve published return types, field meanings, names, and order unless a compatibility strategy is explicit. Unreleased interfaces may be renamed; synchronize their callers.
- Continue work within existing task authorization across follow-ups. Ask only when missing information cannot be reliably inferred and materially changes correctness, goals, or scope; continue independent work while waiting. This skill does not itself authorize installs, unrelated overwrites, version bumps, commits, pushes, or publishing. Silence and tool availability are not authorization.
- Reuse one workflow when multiple skills apply. Do not copy the user's raw specification or this skill into the R package or change `.gitignore` to accommodate it. Maintaining/publishing this standalone skill is a separate task.

## Assign responsibility from source

| Package | Responsibility |
| --- | --- |
| thisutils | Generic matrix, statistical, numerical, execution, and runtime tools |
| thisplot | Generic plotting and layout |
| thistemplate | pkgdown themes and templates |
| inferCSN | Core network inference and related feature selection |
| multiCSN | Multi-omics networks, evaluation, ranking, and state analysis |
| scop | Single-cell object adaptation, analysis entry points, result write-back |

Verify actual source, dependencies, and callers; names alone do not demonstrate completed migration. Reuse lower-level computation and plotting; SCOP prepares inputs and assembles outputs. Keep Seurat, gene/cell, and manuscript logic out of generic utilities. Migration preserves algorithms, preprocessing, and result contracts. Cross-package changes check exports, imports, native registration, direct callers, analyses, and articles. Maintained upstream forks, including monocle, preserve API/object systems, attribution, and licenses.

## Read only applicable guidance

- For implementation, public API/help, repository assets, and session-state changes, read [implementation.md](references/implementation.md).
- For optional R backends, Python branches, examples, and workers, read [backends.md](references/backends.md).
- For formatting, generated files, validation, and code handoff, read [validation.md](references/validation.md).

## Complete with evidence

Complete the requested deliverable and applicable checks. Distinguish introduced regressions, historical problems, and environment differences. Verify actual behavior of new public interfaces. Fix substantive problems; do not let cosmetic polishing indefinitely block delivery. An environment issue pauses only dependent steps.

Report changed behavior, checks actually run, and material limitations. Separate completed, skipped, unverified, and awaiting-user-action outcomes. Pending CI is not a pass. Constructed inputs or mocked backend outputs are not real biological inference.
