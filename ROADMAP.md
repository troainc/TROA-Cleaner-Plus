# Cleaner+ Roadmap

Cleaner+ owns cleanup policy, cleanup schedules, preservation before deletion, and cleanup reporting. Admin Overseer owns the administrative save-confirmed restart workflow. Monitor+ only transports authorized commands and does not own either feature.

## Release status

The latest published binary is v1.10.0. v1.12.0 through v1.12.4 are documented candidates; candidate docs do not mean those packages are published. Cleaner+ owns cleanup; Admin Overseer owns the save-confirmed restart workflow. Do not enable both restart authorities in builds where Cleaner+'s legacy restarter is still present.

The v1.12.3+ asteroid behavior is irreversible and bypasses global dry-run; its release gate requires clear operator documentation and owner validation before public binary publication.

## Priorities

1. Complete v1.12.4 manual command, dry-run, and asteroid safety acceptance on an owner-controlled test server before publication.
2. Add a read-only policy simulator that lists candidates, rule outcomes, protection reasons, and whether recovery is available.
3. Validate backup/restore recovery when GridVault+ is missing, unavailable, or on an incompatible API version.

These priorities are not release promises. See [CHANGELOG.md](CHANGELOG.md) for published behavior.


## Candidate history

v1.12.0 is available under releases/ with version-specific notes. v1.12.4 is the latest documented candidate; its named package remains local until owner validation and publication.
