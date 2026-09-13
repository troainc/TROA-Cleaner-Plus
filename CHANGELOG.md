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
