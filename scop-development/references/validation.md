# Formatting, generated files, and validation

## Synchronize the scoped change

1. Edit sources and direct callers. Clean obsolete references for intentionally retired interfaces. Check exports/imports and native registration across packages.
2. After R changes, run `styler::style_file()` on changed R source and tests using package configuration. Do not add styler to runtime dependencies. Use `style_pkg()` only on explicit request after checking the whole working-tree scope.
3. Regenerate C++ export changes with `Rcpp::compileAttributes()`; roxygen changes with `devtools::document()`. Never hand-edit or separately format generated `man/`, `NAMESPACE`, or Rcpp files. Exclude unrelated generated changes while preserving pre-existing work.
4. After formatting, parse and run relevant behavioral verification; check stale references, help links, and `git diff --check`. Regression tests exercise substantive failures with small deterministic inputs, not implementation mirrors. Verify actual behavior of new public APIs.
5. Do not incidentally change dependency minimum versions. DESCRIPTION Version/Date changes require explicit version-bump authorization; a feature, fix, commit, or publication request does not imply it. NEWS updates go under the top `# <package> (development version)` section. Reorganize into a release only on an explicit version/release task.

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

Report changed behavior, checks and actual outcomes, and remaining limitations. Keep isolated validation evidence out of package commits. Inspect the exact staged files before an authorized commit. Verify remote commit/PR state after authorized publication; report pending CI separately from passed local checks.
