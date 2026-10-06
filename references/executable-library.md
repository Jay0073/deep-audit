# Executable and library audit

Use the relevant section only.

## CLI and maintenance executables

- Validate realistic bad and missing arguments and verify useful failure text and exit codes.
- Exercise paths with spaces, traversal attempts where input is untrusted, partial files, and interrupted runs.
- Trace destructive actions, dry-run behavior, confirmation, rollback or resume, and the state left after mid-run failure.
- Check secrets in arguments, logs, errors, and output. Keep stdout machine-readable when the command is designed for piping and send diagnostics to stderr.
- Run against disposable inputs and inspect actual outputs rather than inferring success from a zero exit code.

## Reusable libraries and SDKs

- Compare the public surface with actual caller expectations and maintained documentation.
- Exercise defaults used by real callers, supported edge inputs, and compatibility with currently supported versions.
- Verify error types and messages allow callers to distinguish retryable, invalid-input, permission, and terminal failures when that distinction matters.
- Check resource cleanup on success, exception, cancellation, and early return.
- Test concurrency and reentrancy only when callers use them or the public contract implies they are supported.
- Treat breaking changes as current when an existing consumer is affected, not merely because an internal signature changed.

## Priority discipline

Prioritize data loss, unsafe destructive behavior, misleading success, unusable defaults, and broken published contracts. Internal cleanup and hypothetical compatibility belong outside the active report unless they have a demonstrated consumer consequence.
