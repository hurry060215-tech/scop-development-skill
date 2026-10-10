# Formatting, generated files, and validation

## Synchronize the scoped change

1. Edit sources and direct callers. For documentation-only tasks, confirm zero function-body changes and do not change versions. Clean obsolete references for intentionally retired interfaces. Check exports/imports and native registration across packages.
2. After R changes, run `styler::style_file()` on changed R source and tests using package configuration. Do not add styler to runtime dependencies. Use `style_pkg()` only on explicit request after checking the whole working-tree scope.
3. Regenerate C++ export changes with `Rcpp::compileAttributes()`; roxygen changes with `devtools::document()`. Never hand-edit or separately format generated `man/`, `NAMESPACE`, or Rcpp files. Exclude unrelated generated changes while preserving pre-existing work.
4. For an adopted renv environment when consistency affects acceptance, run `renv::status()`, resolve relevant drift, and report unrelated differences; do not require a full environment rebuild for every task. Follow [environment.md](environment.md). After formatting, parse and run relevant behavioral verification; check stale references, help links, and `git diff --check`. Regression tests exercise substantive failures with small deterministic inputs, not implementation mirrors. Verify actual behavior of new public APIs.
5. Do not incidentally change dependency minimum versions. DESCRIPTION Version/Date changes require explicit version-bump authorization; a feature, fix, commit, or publication request does not imply it. NEWS updates go under the top `# <package> (development version)` section. Reorganize into a release only on an explicit version/release task.

## Minimum validation by change type

| Change | Required evidence |
| --- | --- |
| Documentation/API help | For roxygen edits, regenerate and parse Rd; check affected contracts, links, and rendering. Keep function bodies unchanged in documentation-only work. Run new/changed executable examples; prose-only edits do not require a full backend environment |
| R behavior | Relevant testthat tests and affected structural invariants; cover empty/singleton/NA/invalid inputs where the changed contract makes them meaningful |
| Resource preparation/dispatch | First initialization, cache hit, invalidation, and equivalence of shared and independent entry points |
| C++/native/parallel | Recompile; numerical assertions in a fresh R process; check platform compatibility and disclose untested platforms |
| Algorithm migration or backend numerical behavior | Fixed-version official-reference comparisons for affected calculations, end-to-end smoke test, recorded acceptance metrics |
| Skill/specification itself | Check code references, parameter naming, links, and rule consistency; do not initialize an R environment in a Markdown-only skill repository |

Choose checks for changed behavior rather than the presence of a backend name in a file. Do not use brittle wall-clock limits in unit tests. Mocks use actual upstream formals; changes to argument forwarding require a real-backend minimal comparison. For backend execution, preprocessing, numerical output, or write-back changes, use at least one designated environment with known versions and run the affected key assertions there. Prose, labels, and error-message-only changes use focused checks unless they alter control flow or error semantics; they do not automatically require full backend provisioning. Ordinary optional tests may skip unavailable dependencies; critical acceptance cannot be reported as passed on skips. If installation is unauthorized or unavailable, report blocked acceptance instead of installing silently.

Package-specific exceptions:

- **thisplot:** do not add tests at present; render and inspect isolated plotting cases.
- **thistemplate:** build and preview affected pkgdown pages for template/static-resource changes; do not impose numerical acceptance when algorithms are unchanged.
- **scop:** prefer built-in pancreas data, raw counts, and clear grouping for analysis/plot examples. Keep CI `--no-examples`; validate optional environments separately using the existing `.github/workflows/optional-backends.yaml` when present, without inventing a workflow or claiming it was run.

## SCOP optional-backend checks

Before submitting optional/Remotes-related modifications, run at least these checks plus applicable behavioral tests/examples. These commands exclude tests/examples and do not establish that they pass.

```sh
Rscript -e 'pkgload::load_all(".", quiet=TRUE, compile=FALSE)'
_R_CHECK_FORCE_SUGGESTS_=false Rscript -e 'rcmdcheck::rcmdcheck(args=c("--no-manual","--as-cran","--no-examples","--no-tests"), error_on="never")'
```

For PowerShell, temporarily set and restore the check variable:

```powershell
Rscript -e 'pkgload::load_all(".", quiet=TRUE, compile=FALSE)'
if ($LASTEXITCODE -ne 0) { throw 'pkgload validation failed' }
$scopHadForceSuggests = Test-Path Env:_R_CHECK_FORCE_SUGGESTS_
$scopOldForceSuggests = $env:_R_CHECK_FORCE_SUGGESTS_
try {
    $env:_R_CHECK_FORCE_SUGGESTS_ = 'false'
    Rscript -e 'rcmdcheck::rcmdcheck(args=c("--no-manual","--as-cran","--no-examples","--no-tests"), error_on="never")'
    if ($LASTEXITCODE -ne 0) { throw 'R check process failed' }
} finally {
    if ($scopHadForceSuggests) {
        $env:_R_CHECK_FORCE_SUGGESTS_ = $scopOldForceSuggests
    } else {
        Remove-Item Env:_R_CHECK_FORCE_SUGGESTS_ -ErrorAction SilentlyContinue
    }
}
```

This standard R check setting is not a package behavior bypass. Use the actual installed Rscript path and shell-compatible quoting. Keep generated check output isolated when supported.

Inspect full results even if the process succeeds: `error_on="never"` does not make warnings/errors a pass. Specifically inspect:

- `checking dependencies in R code ... WARNING`
- `checking for unstated dependencies in examples ... WARNING`

Resolve new source problems in scope. Do not hide warnings by adding optional dependencies or hidden switches. Report unavailable dependency, network, compiler, locale, and platform limitations honestly. Timed-out or incomplete checks remain unverified.

For other changes, select checks proportionate to the change and repository requirements. A standalone skill edit does not require checking an unrelated R package. Once appropriate checks pass, repeat or broaden them only for new changes, failures, or unresolved concerns.

## Handoff

Report scope, scientific-result impact, platform/species coverage, checks and actual pass/fail/skip/unverified outcomes, and remaining limitations. Split independently deliverable lower-level refactors and business features into separate PRs; deliver lower-level packages before upper-level callers in cross-package work. Keep isolated validation evidence out of package commits. Inspect the exact staged files before an authorized commit. Verify remote commit/PR state after authorized publication; report pending CI separately from passed local checks.
