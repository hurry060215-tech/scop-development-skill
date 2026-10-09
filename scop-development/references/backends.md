# Optional R backends and Python runtime

Inspect actual helpers and signatures before making calls. Current user and repository instructions override older conventions.

## R dependencies and examples

- Treat DESCRIPTION Remotes other than `mengxu98/thisplot` and thisutils as runtime-optional backends. Do not add GitHub backends to Imports/Depends/Suggests just to silence check warnings. thisplot/thisutils are exceptions. thistemplate belongs only in `Config/Needs/website`. Do not apply SCOP's dependency restriction to other packages' established dependencies.
- Before first backend execution, use `check_r("owner/repo-or-package", verbose = FALSE)` or an existing method checker. Use `install = FALSE` when supported. Verify actual read-only behavior if that parameter is absent; installation belongs to an explicitly authorized preparation flow, not an analysis side effect.
- Resolve optional functions through `get_namespace_fun("pkg", "fun")`, then call the resolved function. Prefer public APIs. If internal API use is essential, establish a fixed supported version and verify its signature; never use `:::`.
- Executable optional-backend code must not use `pkg::fun()`, `pkg:::fun()`, `library(pkg)`, `require(pkg)`, or `requireNamespace("pkg")`. Direct thisplot/thisutils `::` is allowed; other `::` use is limited to CRAN/Bioconductor dependencies. Non-Remotes read-only `requireNamespace()` checks may be used, but must not silently skip requested analysis.
- Reuse existing check/resolution helpers; do not routinely add another helper layer. If applicable AGENTS.md explicitly requires centralized `<backend>_check_r()`/`<backend>_get_fun()` for a new backend, follow it and centralize repo/package mapping, errors, and resolution. Do not proliferate wrappers per method.
- Analysis functions, examples, and PSOCK workers do not attach packages with `library()`/`require()`. Workers receive objects already resolved by the main process; verify actual worker execution where relevant.
- Optional-backend examples use the existing `check_r` availability mechanism with verified read-only behavior. No `SCOP_RUN_*` guards, optional-package `requireNamespace()` guards, or direct optional-package calls in executable blocks. Prose may mention `pkg::fun()`. Synchronize generated Rd after roxygen/example edits.

## Python branches

- Only Python branches call `PrepareEnv(modules = ...)`. Use `check_python()`, `resolve_conda()`, and `reticulate::import()` as needed by the actual path.
- Preparation reuse decisions stay in `PrepareEnv()`; interpreter configuration/validation stays in established shared runtime code. Do not add `prepare_*_if_needed()`, method-specific environment helpers, or readiness caches. An importable module alone does not satisfy the runtime contract.
- Preserve `assert_python_runtime_switchable()`. An initialized mismatched interpreter requires restarting R. Preparation and analysis use the same `envname` and `conda`.
- Access attributes with `reticulate::py_get_attr()` and generators/views with `reticulate::iterate()`. Read only necessary fields; distinguish execution failure from result read-back failure.

Do not add hidden `Sys.getenv()` switches, `options()`/`getOption()` behavior, or environment variables to bypass backends, examples, tests, or CI warnings. A genuinely necessary temporary option change is local, explained, and restored via `on.exit()`. Permanent or unconditional package-load `options()`/`Sys.setenv()` changes require an already established explicit design.
