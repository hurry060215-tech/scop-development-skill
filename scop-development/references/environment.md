# Development environments and dependency governance

Use renv where it adds reproducibility; pak is an optional installer. Neither is a reason to expand an unrelated task into environment migration. DESCRIPTION remains the public dependency contract; a lockfile records a selected development/test environment, not a new downstream installation requirement.

## Choose the appropriate scope

- Follow an established repository/CI renv workflow. Inspect R version, active library paths, project activation, lockfile, and settings first. A lockfile alone does not prove that the current session uses its environment.
- Do not automatically initialize renv, restore after every pull/branch switch, upgrade dependencies, or regenerate a lockfile for a documentation-only change or a repair that works in the existing environment.
- Restore when a task needs the locked environment, onboarding/reproduction requires it, or relevant dependency drift prevents validation. Review the target library and changes first; honor installation authorization. Restore is an environment mutation, not a harmless status check.
- Use an isolated library/environment for backend acceptance or dependency upgrades. Keep locked-baseline tests separate from latest-dependency compatibility tests; a lockfile alone does not prove current ecosystem compatibility.
- If a missing environment blocks required checks, report those checks as blocked/unverified. Continue independent work; never claim skipped backend acceptance passed.

## Snapshot deliberately

For an established package project, DESCRIPTION-based snapshots are a useful default:

```r
renv::settings$snapshot.type("explicit")
```

This changes project settings; apply it during authorized environment setup, not incidental documentation work. Decide whether development dependencies are required; explicit snapshots with `dev = TRUE` include Suggests. Do not assume all installed packages or dynamically resolved backends will be captured.

SCOP optional GitHub backends must not be promoted into Imports/Suggests merely to make explicit snapshots complete. For designated backend environments, use an existing environment profile or an intentionally selected package set via `renv::snapshot(packages = ..., ...)`, recording versions and sources. The `packages` argument overrides snapshot type: provide the complete intended set, not just the newly changed package. Keep environment manifests separate from the package's public dependency contract.

After an intentional dependency change, run relevant compatibility tests, review the lockfile diff, and snapshot/commit it when that repository's reproducibility baseline is intentionally being updated. Do not snapshot unrelated packages from a personal library or commit exploratory installs. Record isolated experiment versions with their evidence instead.

Use `renv::status()` for an adopted renv environment at handoff when environment consistency matters. Resolve drift that affects the agreed acceptance environment; explain unrelated drift rather than forcing every task to synchronize an entire library. Extra exploratory packages and separate optional-backend environments need not make the distributed DESCRIPTION enumerate every installed package.

## pak usage

Use pak if available and helpful; its absence does not block work that can use the established installer. Run installs only inside the intended project library and authorized scope.

- `pak::local_install_dev_deps()` installs development dependencies and defaults to `upgrade = TRUE`; use it for an intentional broad update, not every coding task. Inspect the installed signature and use `upgrade = FALSE` when the task only needs missing dependencies.
- Targeted `pak::pkg_install("owner/package")` uses the verified repository/ref, not a literal placeholder. Test the selected lower-level package before updating its SCOP caller.
- Add legitimate new declared dependencies to DESCRIPTION before installation, subject to SCOP's optional-backend exceptions. Avoid unrelated minimum-version changes.
- `renv::settings$pak.enabled(TRUE)` is not a supported setting in the verified renv API. pak integration uses renv's configuration mechanism, such as `renv.config.pak.enabled`. It is optional; verify version support. Prefer direct pak use or a scoped temporary option restored with `on.exit()` in an explicit environment-preparation routine. Do not add package-load global options or hidden runtime switches just to enable it.

## Keep environment assets out of package distributions

When adding or maintaining renv infrastructure, exclude it from source package builds. Preserve equivalent existing patterns rather than adding redundant lines:

```text
^renv$
^renv/.*$
^renv\.lock$
^\.Rprofile$
```

Track the intended `renv.lock`, `renv/activate.R`, and `renv/settings.json`; exclude `renv/library/`, `renv/local/`, and caches from Git. Retain the repository's activation mechanism and verify the built tarball contains no renv infrastructure. Editing ignore rules for authorized environment infrastructure is distinct from modifying `.gitignore` merely to hide a specification file. Do neither as an incidental skill/documentation update.

Official references: [renv package development](https://rstudio.github.io/renv/articles/packages.html), [snapshot semantics](https://rstudio.github.io/renv/reference/snapshot.html), [status](https://rstudio.github.io/renv/reference/status.html), [configuration](https://rstudio.github.io/renv/reference/config.html), [pak development dependencies](https://pak.r-lib.org/reference/local_install_dev_deps.html).
