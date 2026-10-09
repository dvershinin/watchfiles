# watchfiles fork: preserve FSEvents rescan kind and path

Working directory: /Users/danila/Projects/watchfiles
Reminder ID: 3F5EEB1F-126D-4242-A1DC-A2F33B2FA7B2

## Missing capability
The pinned fork (commit 6bf42ca, "Surface native rescan requests") turns any notify
`Flag::Rescan` event into the fixed error string "filesystem events lost; rescan required".
notify's rescan info (which FSEvents flag fired: UserDropped vs KernelDropped vs
MustScanSubDirs) and the event path are discarded, so Brain's production logs cannot say
which kind of FSEvents drop happened or under which directory.

## Manual workaround it replaces
The Brain feeder session (commit 705f2fa, in-process rescan recovery) had to write an ad-hoc
Swift FSEvents monitor in scratch to observe which drop kind occurs. Reusable need: diagnose
future feeder rescan storms from logs alone.

## Proposed fix
- Carry flag detail and path in the raised error (or a structured exception attribute):
  e.g. "filesystem events lost; rescan required (kernel_dropped, /path)".
- Keep the existing message prefix so Brain's matcher (project-docs recovery) still works.
- Add Rust unit tests for each flag kind with and without a path; run Brain's feeder
  integration gate against the rebuilt fork before re-pinning.
- Then re-pin Brain to the new commit and log the kind in the feeder's rescan_recovered event.

## Done condition
A forced FSEvents drop produces a log line naming the drop kind and path; tests cover it.
