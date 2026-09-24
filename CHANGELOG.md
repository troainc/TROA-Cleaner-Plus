# v1.7.0 - Persistent Discord operations dashboard

- **One editable dashboard message:** `Dashboard_Enabled` now creates a single Discord operations embed, then updates it in place instead of posting a new health message every interval.
- **Operator controls:** `!cleanerplusadmin dashboard status|now|reset` reports dashboard state, forces an immediate update, or clears the local message link so the next update creates a replacement.
- **Service coverage:** the dashboard reports aggregate master/dry-run state, SimSpeed, cleanup candidates, concealment, quota flags, restart/watchdog state, webhook health, and module activity.
- **Safe local state:** `TROA-CleanerPlusData/OperationsDashboard.state` retains only Discord's numeric message ID. Webhook URLs, player identities, grid identities, IDs, positions, backups, and credentials are not included.
# v1.6.0 - Owner operations webhooks and command presets

- **Mixed command webhook delivery:** every command can emit a safe Discord audit receipt when enabled; player receipts and explicitly routed restore/ownership/GPS administrator output are independently opt-in.
- **Sanitized resource webhooks:** `!cleanerplusadmin webhook resource` supports `status`, `scan`, `health`, `quota`, `conceal`, `schedule`, `restart`, and `modules`, plus trailing `--count`, `--title`, `--note`, and `--fresh` options.
- **Saved command presets:** bounded local `command save|list|show|remove` references help operators reuse standard health and cleanup workflows without automatic command execution.
- **Privacy boundary:** detailed resource panels intentionally omit player names, grid names, IDs, Steam IDs, GPS coordinates, backup paths, and webhook credentials.

# v1.5.0 - Restarter, boost fast-restart, and startup watchdog

- **Restarter** (`Restart_Enabled`, off by default): scheduled (`RestartSchedule` HH:mm + days),
  interval (`Restart_EveryHours`), performance (low sim for N min / `Restart_MaxMemoryMb`), uptime
  (`Restart_MaxUptimeHours`), empty-server (with a hard deadline), and manual triggers, each with a
  broadcast + Discord countdown.
- **Three execution modes** (`Restart_Mode`): TorchNative (`ITorchServer.Restart`), ProcessExit (for
  AMP/Wine wrappers), and ExternalCommand — all via guarded reflection.
- **Pre-shutdown pipeline** (startup-speed win): trim the world (`Restart_TrimBeforeSave`) then a
  verified save (`Restart_SaveBeforeRestart`, aborts on save failure) so the next boot loads a smaller
  world faster.
- **Boost / tiered restart** (`Restart_BoostEnabled`): prefers a fast in-process **soft session
  reload** and forces a full process restart every `Restart_FullEveryNth` to clear memory; optional
  mod-cache prewarm; Cleaner+ state is flushed pre-restart so it resumes instantly. (A plugin cannot
  keep the loaded world in RAM across a full process restart; the speed comes from soft-reload + a
  smaller save.)
- **Startup watchdog** (`Startup_WatchdogEnabled`): an off-thread timer auto-recovers a boot that
  hangs past `Startup_HangTimeoutMinutes`, and a **boot-readiness report** logs/Discords load time +
  grids loaded.
- Commands: `!cleanerplusadmin restart now [min] [full|soft] | cancel | delay <min> | skip | status | boot`.
- Built on verified Torch API (`ITorchServer.Restart`, `Torch.Save`/`SaveResult`, `GameStateChanged`,
  `UnloadSession`/`LoadSession`). Everything is off by default and dry-run testable (`Restart_DryRun`).

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
