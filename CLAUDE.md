# watchfiles: Brain recovery fork

This fork retains upstream v1.2.0 and its locked dependencies. The sole runtime
change forwards notify rescan flags through the existing
`WatchfilesRustInternalError` path, including notifications without paths.
Brain's feeder supervisor restarts and reconciles the affected vault.

## Testing

Run `cargo test --locked` (fast native regressions), `make build-dev`,
`make test` (upstream Python suite), and `make lint-rust` before publishing.
Use `UV_PYTHON=/Library/Frameworks/Python.framework/Versions/3.12/bin/python3.12`
on the Brain Mac. Build/test in this checkout's isolated `.venv`; never use
`maturin develop` against the live Brain environment.

This new fork is not enrolled in myci and its inherited GitHub workflows are
not enabled. Local Rust/Python tests are the delivery gate. Do not enable
upstream publication workflows or contact upstream maintainers without approval.

Commit and push on `main`. Brain pins an immutable fork commit only on Darwin;
other platforms keep the upstream package. Remove the fork pin only when an
upstream release passes the rescan regressions and Brain's integration gate.
