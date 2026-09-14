# v1.4.0 - Lifecycle, Hangar stow, Respawn/NPC cleanup, persistent age, claim

- **Graduated abandonment lifecycle** (`Lifecycle_Enabled`, owned grids): instead of instant deletion,
  a flagged grid is warned (+GPS) -> depowered (reactors/thrusters/tools off) -> disposed (stow or
  delete) on `Lifecycle_DepowerMinutes` / `Lifecycle_DisposeMinutes`; state persists across restarts.
  `!cleanerplusadmin lifecycle status`.
- **TROA-Hangar auto-stow** (`Stow_Enabled`): move an owned flagged grid into its owner's hangar via a
  reflection soft-bridge (like Gridvault+) instead of deleting; falls back to backup+delete when
  TROA-Hanger is absent. `Stow_OnlyOwnerOffline`; `!cleanerplusadmin stow status`.
- **Respawn-ship module** (`respawnships`, off by default): removes unclaimed default respawn ships/pods
  by name pattern + age.
- **NPC-grid module** (`npcgrids`, off by default): removes stale NPC/pirate/cargo/encounter grids by
  age + distance, sparing grids with a keep block (Store/SafeZone).
- **Persistent grid age** (`PersistGridAge`): first-seen times persist to `GridAges.xml` so grace/idle/
  offline stay accurate across restarts.
- **Owner GPS markers** (`Warn_SendGps`): a flagged grid drops a temporary GPS to its owner.
- **Claim window** (`Claim_Enabled`): `!cleaner claim <grid>` lets a player take a nearby ownerless grid.
- **Bugfix**: content-defaulted config lists (WarningLeadSeconds, DefaultGridNamePatterns, respawn/NPC
  lists) no longer double on load (XmlSerializer append behavior); now filled only when empty.

# v1.3.0 - Backlog: keep-alive action, PCU rules, safe-zone protect, localization, CI

- **PB / Remote-Control keep-alive action** — a `Cleaner+ Keep Alive` terminal action on Programmable Blocks and Remote Controls holds a grid hot (exempt from concealment) for `Conceal_KeepAliveMinutes` (`Conceal_KeepAliveAction`).
- **PCU keep rule** — `RequirePcuToKeep` + `MinPcuToKeep`: a grid under the PCU minimum fails the keep policy (shown as "under N PCU" in `!cleanerplus scan`). Also honored per-zone/faction.
- **Safe-zone / faction-HQ auto-protect** — `Protect_SafeZoneGrids`: any grid containing a Safe Zone block is protected automatically, no manual GPS zone needed.
- **Localization packs** — set `LocaleFile` to a `key=value` locale file to override all player-facing messages; ships `docs/locales/en.lang` + a German sample.
- **Concealment reveal-on-spawn** — `Conceal_RevealOnSpawn`: allows concealing medbay/cryo grids and reveals grids near players when someone logs in/spawns.
- **CI workflow** — `.github/workflows/build-and-release.yml` builds on push and, on a `v*` tag, publishes the plugin zip to the public release repo (needs `TORCH_BINARIES_TOKEN` + `PUBLIC_REPO_TOKEN`).
- **Player-ready config example** — `TROA-CleanerPlus.cfg.example` rewritten with real, populated sample entries (protection lists, zones, policy overrides, scheduled events, quiet hours) using open/close tags — no more empty self-closing tags to guess at.

# v1.2.0 - Self-healing performance, player-fair retention, scheduling, analytics

Self-healing performance:
- Adaptive intervals scale each module's cadence by server sim ratio (healthy = longer, degraded = shorter). Off by default (`Adaptive_*`).
- Emergency concealment: when sim ratio stays below `Conceal_EmergencyThreshold`, concealment runs aggressively (more per tick, no dwell) then backs off.
- Concealment keep-alive: grids with an antenna owned by an online player are never concealed (`Conceal_KeepAliveAntenna`).
- Sim-speed source (`Util/SimSpeed`) reads the engine directly or, if `ProfilerMetricsFile` is set, TROA Profiler+'s metrics file.

Player-fair retention:
- Owner warnings before cleanup: a grid must be flagged (and its owner warned in chat/Discord) for `Warn_LeadMinutes` before it is removed; a login grants `Warn_LoginGraceMinutes`.
- Self-service commands: `!cleaner mygrids`, `!cleaner risk <grid>`, `!cleaner keep <grid>` (limited, persisted), `!cleaner request <grid>`.
- Per-player quotas (`Quota_*`): warn owners over `Quota_MaxGrids`/`Quota_MaxPcu` and optionally flag their smallest grids for cleanup.

Admin control & scheduling:
- Scheduled cleanup events at a local time-of-day (`ScheduledEvents`) and quiet-hours suppression (`QuietHours`).
- Undo the last pass (`!cleanerplusadmin undo`) and a player restore-request workflow (`!cleanerplusadmin requests|approve|deny`).

Insight & analytics:
- Per-pass sim-speed delta in the log + Discord embed; rolling `History/*.csv`; digest now shows clean-reason and top-flagged-owner breakdowns.
- Prometheus/JSON metrics export (`Metrics_Enabled`) and a periodic Discord server-health dashboard (`Dashboard_Enabled`).

All new features are off by default except owner warnings and history. 54 unit tests pass.

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
