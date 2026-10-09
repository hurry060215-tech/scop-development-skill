# Implementation, documentation, and assets

Apply these conventions to the authorized change, not as a reason to refactor unrelated files.

## APIs and code

- Search parameters and callers first. Reuse semantically matching names, values, defaults, and subset-selection conventions. Add parameters only when necessary and explain why; do not overload a different meaning or hide duplicate parameters in `...` or configuration lists. Update callers, help, and tests; preserve published compatibility.
- Follow local naming and S3/S4 dispatch. Name interfaces/results by purpose or method; avoid new implementation suffixes such as `cpp`, vague `dynamic`, and unnecessary package affixes. Preserve names required for compatibility or tooling; do not bulk-rename existing interfaces.
- Common entry points own shared validation, dispatch, and assembly; method interfaces own specialized logic. Centralize shared conversions and caches. Inline one-use thin helpers without an independent contract; meaningful algorithm stages, complex validation, and cleanup may remain functions.
- Keep R implementations in flat responsibility-based `R/` files. Group resource interfaces by source; do not create one file for each thin download, preparation, forwarding, or conversion helper. Preserve the one-method-per-file convention for independent public analysis methods.
- Prefer joint-column matching for compound identifiers. When a single key is necessary, use unambiguous encoding. Do not split naive concatenations or delimiter substitutions to recover original fields. Check delimiter-containing values, missing/empty values, and order-sensitive inputs.
- Code-authored progress, success, warnings, and errors use `log_message()` with `message_type` and existing `verbose` control. Verify the actual logger signature: errors terminate and warnings retain warning semantics. Do not directly emit these through `message()`, `warning()`, `stop()`, `cat()`, `print()`, another logger, or a per-method logging helper. Preserve dependency exceptions; never swallow errors or silently return to standardize output.
- Ordinary analysis does not permanently modify session configuration. Restore temporary options and their originally unset state through `on.exit()`, including `future::plan()`. Explicit initialization validates before modification. Do not add environment variables or hidden options to conceal failures.
- Define cache invalidation for asset versions, changed contents, additions, and removals. Construct sparse results directly when compatible with the algorithm; avoid an unnecessary dense `n × n` intermediate. Do not use `gc()` to conceal a structural problem.

## Public documentation

- Use English for public help and necessary comments. Public functions have a concise explicit `@title`; `@description`/`@details` add only useful information. Parameters describe type, meaning, and constraints; use `@inheritParams` when appropriate.
- `@return` documents structure, matrix orientation, ordering, NA behavior, thresholds, and interval meaning. Do not fill API help with migration history, optimization claims, architecture, or manuscript background. Do not create help pages for internal helpers just to satisfy a template.
- Shared help owns the generic contract and supported categories/values. Put backend-specific settings, provenance, limitations, examples, and workflows in method/resource help, linked from the common entry point as needed. Avoid backend-specific details or return subsections in generic help. Reuse existing dispatch/traversal/assembly rather than duplicating the entry point or adding unnecessary restrictions for one category.
- Examples are short, representative, runnable, and free of private paths. Resource, query, and conversion interfaces do not need a full analysis chain. Keep large real-data validation outside the package. Preserve citations, copyright, licenses, and tool instructions.
- Synchronize only task-required API docs, README, and website indexes. Inspect actual `_pkgdown.yml`, `DESCRIPTION`, and `.Rbuildignore` build behavior. Detailed usage belongs in function help.

## Repository assets

Restrictions follow purpose regardless of filename, location, extension, renaming, or repackaging.

- `inst/` contains only non-R-source resources needed after installation. Do not commit standalone demos, trial scripts, benchmarks, result capture/comparison scripts, or validation records. Usage goes in formal API examples/articles; temporary verification goes in an existing ignored or isolated directory.
- Formal unit tests use existing `tests/testthat/test-*.R`. Do not add loose testing scripts, testing manuals, fixtures, or test/demo/validation asset directories.
- No independent test data files: real, cropped, synthetic, serialized objects, CSV/TSV, JSON, images, or caches. Construct small deterministic inputs in code and assert necessary scalars or small structures. Do not evade the rule through large hard-coded arrays, encoded blobs, or generated files committed afterward.
- Run real-data and official-output comparisons in isolation; retain evidence outside the package distribution. Unit tests do not download assets or install dependencies. Explicitly skip tests missing optional dependencies.
- `vignettes/` contains formal articles in the configured buildable format (SCOP currently uses `.Rmd`; verify the checkout) and index convention. Do not substitute plain `.md` articles. Reproduction, repair, review, and numerical acceptance records stay in isolation.
