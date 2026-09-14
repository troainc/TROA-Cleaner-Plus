# v1.1.0 - Concealment, Discord Audit, Connected Grids, Zones, Digest, Restore

- **Grid concealment (experimental, off by default):** a reversible layer between "keep active" and "delete". Idle grids far from players are removed from the simulation/update path (not deleted) to reclaim sim speed, and revealed when a player returns. Modernized from the classic TorchAPI/Concealment mechanism (component-update removal, `MyEntities.UnregisterForUpdate`, entity flags, hierarchy recursion, projector handling), with spawn-point safety (never conceals medical/cryo grids), protection/static exclusions, reveal-all on unload, and fail-closed guarding. Config: `Conceal_*`. Commands: `!cleanerplusadmin conceal now|status`, `reveal all`.
- **Discord audit webhook with pretty embeds:** consolidated per-pass embed (removed / backed up / skipped / dry-run), a scheduled digest embed, and a concealment-effectiveness embed with a coverage progress bar. Quiet-failure circuit (3 fails → 10-min pause), ASCII-safe payloads, URL never logged. Config: `EnableAuditWebhook`, `AuditWebhookUrl`, `WebhookName`, `SendStartupWebhookTest`, `WebhookOnPass/Digest/Conceal`. Command: `!cleanerplusadmin webhook [test|reset]`.
- **Connected-grid awareness:** mechanically connected subgrids are treated as one unit — the group is kept if any member passes the keep policy, and every member is backed up before any is removed. Config: `Grids_TreatConnectedAsGroup` (default on).
- **Per-faction / per-zone policy overrides:** different block minimums, beacon requirement, or enable state inside a faction or GPS zone (most-specific wins). Config: `PolicyOverrides`.
- **Owner-offline seeding:** historical last-login data is imported once at first load so `Grids_OwnerOfflineDays` is accurate immediately after install, not only from first run.
- **Scheduled digest:** periodic "world is clean / N grids flagged" report to the log, in-game chat, and Discord. Config: `Digest_*`. Command: `!cleanerplusadmin digest now`.
- **Local restore helper:** `!cleanerplusadmin restore list [steamid]` and `restore <gridId> [x y z]` restore from Cleaner+'s own Cleanup Grids folder when Gridvault+ is not installed.

# v1.0.0-alpha.2 - Fully Configurable Messaging

- Made all player-facing text configurable in `TROA-CleanerPlus.cfg`: `WarningMessageTemplate`, `SummaryMessageTemplate`, `KeepHintMessage`, `RecoveryHintMessage`, and `PolicyMessageOverride`, plus `ShowKeepHintOnScan` and `ScanMaxLines`.
- Templates support placeholders that are substituted at runtime: `{DisplayName}`, `{Module}`, `{Seconds}`, `{Count}`, `{MinBlocks}`, and `{NoCleanTag}`.
- `!cleanerplus scan` now appends the configurable keep hint and recovery hint (toggle with `ShowKeepHintOnScan`) and honors `ScanMaxLines`; `!cleanerplus policy` uses `PolicyMessageOverride` when set, otherwise the auto-generated policy plus the configurable keep hint.
- Interval pre-run warnings and the post-pass summary now use the configurable templates. All keep rules (beacon requirement, block minimum, rename requirement, default-name patterns, ownerless, no-functional, protection lists, GPS zones, and the no-clean tag) remain individually configurable as before.

# v1.0.0-alpha.1 - First Build

- Created TROA Cleaner+, a headless, toggleable, restart-free automated cleanup plugin for Torch Space Engineers servers.
- Added three independently toggleable modules: floating objects, grids, and dead characters.
- Added three per-module run modes: Interval (with broadcast countdown warnings), RealTime (bounded per-tick scanning), and CommandOnly.
- Implemented the TROA grid keep policy: a grid is kept only if it has a Beacon, at least `MinBlocksToKeep` (default 25) blocks, and a custom (renamed) name. Failing any enabled requirement makes the grid eligible; `!cleanerplus scan` shows exactly which rule a grid failed.
- Added protection that always keeps a grid: `[NOCLEAN]` name tag, protected name substrings, owner Steam IDs, faction tags, keep-if-block-present, and spherical GPS protection zones. Added a configurable grace period for newly built grids.
- Added backup-before-delete through Gridvault+: every removed grid is captured into Gridvault+'s "Cleanup Grids" vault via its public `TROAGridVaultCleanupBridge` API before deletion.
- Added a local "Cleanup Grids" fallback (under `TROA-CleanerPlusData`) so servers without Gridvault+ still keep every grid Cleaner+ removes.
- Added `RequireGridvaultBackupBeforeDelete` (default on): a grid that cannot be backed up is skipped, never deleted.
- Added global dry-run (default on), pre-run warning broadcasts, batched game-thread removal bounded by `MaxRemovalsPerTick`, and per-action logging to `Cleanup.log`.
- Added restart-free configuration: safe reload with `!cleanerplusadmin reload` and an automatic config-file save watcher.
- Added moderator commands (`!cleanerplus help|status|policy|scan`) and admin commands (`!cleanerplusadmin now|on|off|mode|interval|dryrun|master|reload`).
- Added a deterministic, dependency-free test harness for the name policy and configuration logic.
