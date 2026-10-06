# Cleaner+ Roadmap

Cleaner+ owns cleanup policy, cleanup schedules, preservation before deletion, and cleanup reporting. Admin Overseer owns the administrative save-confirmed restart workflow. Monitor+ only transports authorized commands and does not own either feature.

## Current public release

The published package remains v1.7.0. Its legacy optional restarter is disabled by default, but remains present in that package. For server restarts, use Admin Overseer's `!ova restart` workflow. Retirement of Cleaner+'s duplicate restart execution is a release-gated follow-up; do not enable both restart authorities.

Asteroid removal described in the README is a v1.8.0 development candidate and is not part of the published v1.7.0 package.

## Priorities

1. Close the v1.8 asteroid safety and disposable-world acceptance gates before publication. Keep permanent deletion disabled by default and require explicit owner opt-in.
2. Add a read-only policy simulator that lists candidates, rule outcomes, protection reasons, and whether recovery is available.
3. Retire duplicate restart execution in a future Cleaner+ release while preserving cleanup schedules and backup-before-delete behavior.
4. Validate backup/restore recovery when GridVault+ is missing, unavailable, or on an incompatible API version.

These priorities are not release promises. See [CHANGELOG.md](CHANGELOG.md) for published behavior.
