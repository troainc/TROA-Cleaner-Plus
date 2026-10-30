# Cleaner+ Roadmap

Cleaner+ owns cleanup policy, cleanup schedules, preservation before deletion, and cleanup reporting. Admin Overseer owns the administrative save-confirmed restart workflow. Monitor+ only transports authorized commands and does not own either feature.

## Release status

Current Cleaner+ release: v1.12.7. Cleaner+ owns cleanup; Admin Overseer owns the save-confirmed restart workflow. Do not enable both restart authorities in builds where Cleaner+'s legacy restarter is still present.

The v1.12.6 release adds online-player clearance and deep orphan asteroid `.vx2` archival/pruning, gated by a successful save and active-reference checks.

The v1.12.6 asteroid behavior is irreversible and bypasses global dry-run; operators should review the safety documentation before enabling it.

The v1.12.7 Bifrost integration supports explicit read-only aggregate cleanup previews. It does not enqueue or execute cleanup and does not return candidate identity or position data.

## Priorities

1. Complete deployment-side runtime acceptance for v1.12.7 and its Admin Overseer integration.
2. Validate backup/restore recovery when GridVault+ is missing, unavailable, or on an incompatible API version.

These priorities are not release promises. See [CHANGELOG.md](CHANGELOG.md) for published behavior.


## Release history

Version-specific historical notes are under releases/. The current Cleaner+ package and behavior are documented in v1.12.7 release notes.
