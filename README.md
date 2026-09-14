# TROA Cleaner+

TROA Cleaner+ is a headless, fully toggleable, **restart-free** automated cleanup plugin for Space
Engineers dedicated servers running Torch. Every feature is an on/off switch, every switch can be
changed live (no server restart, no world reload), and every grid it removes is backed up first.

It was built for hassle-free operation: turn on the sweeps you want, pick how each one runs, and let
it keep the world clean without downtime.

## Current Release

- Version: `v1.4.0`
- Package: `TROA-CleanerPlus-v1.4.0.zip`
- SHA-256: `4ABBA05A7B049F382F4E69E18E460046FC05B86ABE38BEB3F7AA52FBAB96BAF4`
- Runtime: Torch / .NET Framework 4.8
- Hosting: Windows and Linux-hosted AMP/Wine servers
- UI: none; all operation is command-, config-, and file-based

## The TROA grid keep policy

On TROA, a grid is **kept** only when it satisfies **all** of these:

1. **Has a Beacon** block, and
2. **At least 25 blocks** (`MinBlocksToKeep`), and
3. **Has been renamed** — not a Keen default name such as `Large Grid`, `Small Grid`, `Static Grid`
   (a trailing number like `Large Grid 4471` still counts as a default name).

A grid that is missing **any** of these becomes eligible for cleanup. Each of the three rules is an
individual toggle, so other servers can relax or tighten the policy. `!cleanerplus scan` shows, per
grid, exactly which rule a grid failed, so players understand why something is flagged.

Whitelist rules, GPS protection zones, the `[NOCLEAN]` name tag, and the grace period always win and
keep a grid regardless of the policy.

## Highlights

- **Everything is a toggle.** Floating objects, grids, and dead characters are separate modules, each
  independently enabled/disabled.
- **You choose how each module runs.** Per module: **Interval** (every N minutes with a broadcast
  countdown), **RealTime** (continuous, bounded per-tick scanning), or **CommandOnly** (manual).
- **No restarts.** Edit the config and save it, or run `!cleanerplusadmin reload`. Changes apply on
  the next tick. Toggling a module on/off, changing a mode, or changing an interval never needs a
  restart.
- **Backup before delete.** Every grid removed is first captured into **Gridvault+**'s "Cleanup
  Grids" vault through its bridge API. No Gridvault+? Cleaner+ writes to its **own** local
  "Cleanup Grids" folder instead. A grid that cannot be backed up is **skipped, never deleted**
  (`RequireGridvaultBackupBeforeDelete`, default on).
- **Safe by default.** Dry-run ships **on** — nothing is deleted until you turn it off. Newly built
  grids are protected by a grace period. Pre-run warnings are broadcast to players.
- **Server friendly.** Removals are batched across ticks; whole-world scans run only on the module's
  schedule, never every tick. Headless — works the same on Windows, Linux, and AMP/Wine.

## Modules

| Module | Key | Default | What it removes |
|---|---|---|---|
| Floating objects | `floatingobjects` | on | Dropped ore/component stacks older than `FloatingObjects_MaxAgeMinutes`, optionally sparing large stacks and objects near players. |
| Grids | `grids` | on | Grids that fail the keep policy, plus (optionally) ownerless grids and grids with no functional blocks. Static grids are excluded unless enabled. Connected subgrids are treated as one unit. |
| Dead characters | `corpses` | off | Dead character bodies older than `Corpses_MaxAgeMinutes`. |

## v1.1.0 features

- **Grid concealment (experimental, off by default).** A reversible layer between "keep active" and
  "delete": idle grids far from players are removed from the simulation/update path (not deleted) to
  reclaim sim speed, and revealed when a player returns. Never conceals static, protected, or
  spawn-capable (medical/cryo) grids, and reveals everything on world unload. Enable with
  `Conceal_Enabled`; drive it with `!cleanerplusadmin conceal now|status` and `reveal all`.
- **Discord audit with pretty embeds.** Consolidated per-pass embed (removed / backed up / skipped),
  a scheduled digest embed, and a **concealment-effectiveness** embed with a coverage progress bar.
  Quiet-failure circuit (3 fails → 10-min pause); the webhook URL is never logged. Configure
  `EnableAuditWebhook` + `AuditWebhookUrl`; check with `!cleanerplusadmin webhook [test|reset]`.
- **Connected-grid awareness.** Mechanically connected subgrids are evaluated and backed up as one
  unit (`Grids_TreatConnectedAsGroup`, default on).
- **Per-faction / per-zone policies.** Different block minimums, beacon requirement, or enable state
  inside a faction or GPS zone (`PolicyOverrides`; most-specific wins).
- **Owner-offline seeding.** Historical last-login data is imported once at first load so
  `Grids_OwnerOfflineDays` is accurate immediately after install.
- **Scheduled digest.** Periodic "world is clean / N grids flagged" report to log, chat, and Discord
  (`Digest_*`; `!cleanerplusadmin digest now`).
- **Local restore helper.** `!cleanerplusadmin restore list [steamid]` and `restore <gridId> [x y z]`
  restore from Cleaner+'s own Cleanup Grids folder when Gridvault+ is not installed.

## v1.4.0 features

- **Graduated abandonment lifecycle** (`Lifecycle_Enabled`) — owned grids go warn(+GPS) -> depower ->
  dispose on timers instead of instant deletion; state persists. `!cleanerplusadmin lifecycle status`.
- **TROA-Hangar auto-stow** (`Stow_Enabled`) — move an owned grid into its owner's hangar instead of
  deleting (falls back to backup+delete when TROA-Hanger is absent). `!cleanerplusadmin stow status`.
- **Respawn-ship module** (`respawnships`) — remove unclaimed default respawn ships/pods.
- **NPC-grid module** (`npcgrids`) — remove stale NPC/pirate/cargo/encounter grids, sparing trade
  stations (Store/SafeZone).
- **Persistent grid age** (`PersistGridAge`) — grace/idle/offline stay accurate across restarts.
- **Owner GPS markers** (`Warn_SendGps`) + **claim** (`!cleaner claim <grid>` for nearby ownerless grids).

## v1.3.0 features

- **PB / Remote-Control keep-alive** — a `Cleaner+ Keep Alive` terminal action on Programmable Blocks
  and Remote Controls holds a grid hot (exempt from concealment) for `Conceal_KeepAliveMinutes`.
- **PCU keep rule** — `RequirePcuToKeep` + `MinPcuToKeep` add PCU as a first-class keep requirement.
- **Safe-zone auto-protect** — `Protect_SafeZoneGrids` protects any grid with a Safe Zone block.
- **Localization packs** — `LocaleFile` points at a `key=value` file to translate all player messages
  (`docs/locales/en.lang` + a German sample ship in the repo).
- **Concealment reveal-on-spawn** — `Conceal_RevealOnSpawn` lets medbay/cryo grids be concealed and
  reveals them when a player logs in/spawns.
- **CI** — `.github/workflows/build-and-release.yml` builds and publishes releases on a `v*` tag.
- **Player-ready config example** — every list in `TROA-CleanerPlus.cfg.example` now shows real,
  editable sample entries instead of empty tags.

## v1.2.0 features

- **Self-healing performance.** Adaptive intervals scale by server sim ratio; emergency concealment
  kicks in on sustained lag; antenna keep-alive spares grids of online owners. Sim readings come from
  the engine or a TROA Profiler+ metrics file (`ProfilerMetricsFile`). `!cleanerplusadmin simspeed`.
- **Player-fair retention.** Owners are warned before their grid is cleaned (chat/Discord, with a
  lead + on-login grace); players self-serve with `!cleaner mygrids / risk / keep / request`; and
  per-player grid/PCU quotas can warn and trim (`Quota_*`).
- **Admin control & scheduling.** Scheduled cleanup events (`ScheduledEvents`), quiet-hours
  suppression (`QuietHours`), `!cleanerplusadmin undo`, and a restore-request workflow
  (`requests` / `approve` / `deny`).
- **Insight & analytics.** Per-pass sim-speed delta, rolling `History/*.csv`, clean-reason +
  top-offender breakdown in the digest, Prometheus/JSON metrics export for Grafana
  (`Metrics_Enabled`), and a periodic Discord server-health dashboard (`Dashboard_Enabled`).

## Commands

### Players (moderator/read)

| Command | Use |
|---|---|
| `!cleanerplus help` | Lists commands. |
| `!cleaner mygrids` | Your grids and their cleanup risk. |
| `!cleaner risk <grid>` | Why one of your grids is (not) flagged. |
| `!cleaner keep <grid>` | Protect your grid from cleanup for a while (limited count). |
| `!cleaner claim <grid>` | Claim a nearby ownerless grid so it isn't cleaned. |
| `!cleaner request <grid>` | Ask staff to restore a cleaned grid (local fallback). |
| `!cleanerplus status` | Shows the master switch, dry-run state, Gridvault+ link state, and each module's mode/interval/removed totals. |
| `!cleanerplus policy` | Explains the current grid keep policy. |
| `!cleanerplus scan [module]` | Previews exactly what would be cleaned right now, with the reason each entity failed. **Never deletes.** |

### Administrators

| Command | Use |
|---|---|
| `!cleanerplusadmin help` | Lists admin commands. |
| `!cleanerplusadmin now [module\|all]` | Runs a cleanup pass immediately (respects dry-run). |
| `!cleanerplusadmin on <module>` / `off <module>` | Toggles a module live. |
| `!cleanerplusadmin mode <module> <interval\|realtime\|command>` | Changes a module's run mode live. |
| `!cleanerplusadmin interval <module> <minutes>` | Changes a module's interval live. |
| `!cleanerplusadmin dryrun <on\|off>` | Global dry-run switch. |
| `!cleanerplusadmin master <on\|off>` | Enables or disables all cleanup. |
| `!cleanerplusadmin reload` | Re-reads the config file with no restart. |
| `!cleanerplusadmin conceal now\|status` | Runs an experimental concealment pass / shows coverage. |
| `!cleanerplusadmin reveal all` | Reveals every concealed grid. |
| `!cleanerplusadmin digest now` | Sends the cleanup digest immediately. |
| `!cleanerplusadmin webhook [test\|reset]` | Discord audit status, test embed, or reset the failure circuit. |
| `!cleanerplusadmin restore list [steamid]` | Lists local Cleanup Grids backups (no-Gridvault fallback). |
| `!cleanerplusadmin restore <gridId> [x y z]` | Restores a grid from the local Cleanup Grids folder near you or at GPS. |
| `!cleanerplusadmin undo` | Restores the grids removed by the last cleanup pass (within the undo window). |
| `!cleanerplusadmin requests` / `approve <id> [x y z]` / `deny <id>` | Player restore-request workflow. |
| `!cleanerplusadmin quota` | Recomputes per-player quotas and reports flagged grids. |
| `!cleanerplusadmin schedule list` | Lists scheduled cleanup events. |
| `!cleanerplusadmin simspeed` | Shows the current server sim ratio Cleaner+ sees. |
| `!cleanerplusadmin lifecycle status` / `stow status` | Lifecycle stage counts / TROA-Hanger stow availability. |

Modules are `floatingobjects`, `grids`, and `corpses`.

## Install

1. Download the newest `TROA-CleanerPlus-v*.zip` from this repository's releases.
2. Install the plugin package using your host's normal Torch plugin process.
3. Start Torch and load the world. `TROA-CleanerPlus.cfg` and the `TROA-CleanerPlusData` folder are
   created in the plugin storage path.
4. **Dry-run is on by default.** Run `!cleanerplus scan` to preview what would be cleaned.
5. Tune the config (see below), save it (auto-reloads) or run `!cleanerplusadmin reload`.
6. When you are satisfied, set `GlobalDryRun` to `false` (or `!cleanerplusadmin dryrun off`).

## Backups: Gridvault+ and the local fallback

Cleaner+ never throws grids away. Before removing a grid it captures it:

- **With Gridvault+ installed:** the grid is stored in Gridvault+'s
  `Cleanup Grids/Players/<steam-id>/<timestamp>/Grids/` vault through Gridvault+'s public bridge, so
  it is recoverable with the normal `!gridvault` restore commands.
- **Without Gridvault+:** the grid is serialized into Cleaner+'s own
  `TROA-CleanerPlusData/Cleanup Grids/Players/<steam-id-or-Unowned>/<timestamp>/Grids/` folder, as a
  standard Space Engineers `.sbc` blueprint plus a small metadata file.

If neither backup can be made and `RequireGridvaultBackupBeforeDelete` is on (the default), the grid
is **skipped and logged** rather than deleted.

## Configuration reference

The live file is `TROA-CleanerPlus.cfg`. An annotated `TROA-CleanerPlus.cfg.example` ships with the
plugin. All settings are re-read on save or `!cleanerplusadmin reload`.

| Setting | Default | Purpose |
|---|---:|---|
| `Enabled` | `true` | Master switch for the whole plugin. |
| `DisplayName` | `Cleaner+` | Name used in chat broadcasts and logs. |
| `GlobalDryRun` | `true` | When on, modules only report what they would remove; nothing is deleted. |
| `GracePeriodMinutes` | `15` | Grids/objects newer than this are never touched. |
| `MaxRemovalsPerTick` | `2` | Removals applied per simulation tick (protects sim speed). |
| `BroadcastWarnings` | `true` | Broadcast a countdown before each interval pass. |
| `BroadcastSummary` | `true` | Broadcast/log a summary after a pass. |
| `WarningLeadSeconds` | `60, 30, 10` | Seconds-before-run at which warnings are sent. |
| `WarningMessageTemplate` | `{DisplayName}: {Module} cleanup in {Seconds}s.` | Pre-run warning text. Placeholders: `{DisplayName}` `{Module}` `{Seconds}`. |
| `SummaryMessageTemplate` | `{DisplayName}: cleaned {Count} {Module}.` | Post-pass summary text. Placeholders: `{DisplayName}` `{Count}` `{Module}`. |
| `ShowKeepHintOnScan` | `true` | Append the keep hint + recovery hint to `!cleanerplus scan`. |
| `ScanMaxLines` | `25` | Max lines listed by `!cleanerplus scan` before summarizing the rest. |
| `KeepHintMessage` | (see cfg) | How players keep a grid. Placeholders: `{MinBlocks}` `{NoCleanTag}` `{DisplayName}`. |
| `RecoveryHintMessage` | (see cfg) | Where removed grids can be recovered. Placeholder: `{DisplayName}`. |
| `PolicyMessageOverride` | empty | Overrides `!cleanerplus policy` text when set; empty auto-generates it. |
| `EnableGridvaultIntegration` | `true` | Capture removed grids into Gridvault+'s Cleanup Grids. |
| `RequireGridvaultBackupBeforeDelete` | `true` | Never delete a grid that could not be backed up. |
| `GridvaultCleanupReasonPrefix` | `TROA Cleaner+` | Text written into the Gridvault+ audit reason. |
| `EnableLocalBackupFallback` | `true` | Capture to a local Cleanup Grids folder when Gridvault+ is absent. |
| `RequireBeaconToKeep` | `true` | Keep policy: a kept grid must have a Beacon. |
| `MinBlocksToKeep` | `25` | Keep policy: minimum block count to survive. |
| `RequireRenamedToKeep` | `true` | Keep policy: a kept grid must have a custom name. |
| `DefaultGridNamePatterns` | Keen defaults | Names treated as "not renamed". |
| `NoCleanTag` | `[NOCLEAN]` | Put this in a grid name to always protect it. |
| `ProtectedNameSubstrings` | empty | Keep grids whose name contains any of these. |
| `ProtectedSteamIds` | empty | Keep grids owned by these Steam IDs. |
| `ProtectedFactionTags` | empty | Keep grids owned by these faction tags. |
| `KeepIfBlockPresent` | empty | Keep grids containing a block whose type/subtype matches. |
| `ProtectionZones` | empty | Spherical GPS regions that are never cleaned. |
| `FloatingObjects_Enabled` | `true` | Enable the floating-objects module. |
| `FloatingObjects_Mode` | `Interval` | `Interval`, `RealTime`, or `CommandOnly`. |
| `FloatingObjects_IntervalMinutes` | `15` | Interval-mode period. |
| `FloatingObjects_MaxAgeMinutes` | `30` | Minimum age before an object is removed. |
| `FloatingObjects_KeepStackAtLeast` | `0` | Keep stacks of at least this size. 0 = age only. |
| `FloatingObjects_MinDistanceFromPlayers` | `0` | Spare objects within this many meters of a player. 0 = ignore. |
| `Grids_Enabled` | `true` | Enable the grid module. |
| `Grids_Mode` | `Interval` | `Interval`, `RealTime`, or `CommandOnly`. |
| `Grids_IntervalMinutes` | `30` | Interval-mode period. |
| `Grids_IncludeStatic` | `false` | Include static (anchored) grids. |
| `Grids_RemoveOwnerless` | `true` | Also remove ownerless grids. |
| `Grids_RemoveNoFunctional` | `true` | Also remove grids with no functional blocks. |
| `Grids_MinDistanceFromPlayers` | `0` | Spare eligible grids within this many meters of a player. 0 = ignore. |
| `Grids_OwnerOfflineDays` | `0` | Only clean an eligible owned grid if its owner has been offline this long. 0 = ignore. |
| `Corpses_Enabled` | `false` | Enable the dead-character module. |
| `Corpses_Mode` | `Interval` | `Interval`, `RealTime`, or `CommandOnly`. |
| `Corpses_IntervalMinutes` | `20` | Interval-mode period. |
| `Corpses_MaxAgeMinutes` | `30` | Minimum age before a dead body is removed. |
| `Grids_TreatConnectedAsGroup` | `true` | Treat connected subgrids as one unit for policy + backup. |
| `PolicyOverrides` | empty | Per-faction / per-zone overrides of block min, beacon, interval, enable. |
| `EnableAuditWebhook` | `false` | Enable Discord audit embeds. |
| `AuditWebhookUrl` | empty | Discord webhook URL (kept private; never logged). |
| `WebhookName` | `Cleaner+` | Discord sender name. |
| `SendStartupWebhookTest` | `true` | Post a test embed when the webhook is first configured. |
| `WebhookOnPass` / `WebhookOnDigest` / `WebhookOnConceal` | `true`/`true`/`false` | Which events post embeds. |
| `Digest_Enabled` | `false` | Enable the periodic digest report. |
| `Digest_IntervalMinutes` | `60` | Digest period. |
| `Digest_ToAdminsInGame` / `Digest_ToWebhook` | `true` | Digest destinations (chat / Discord). |
| `Conceal_Enabled` | `false` | Enable experimental grid concealment. |
| `Conceal_Mode` | `Interval` | Concealment run mode. |
| `Conceal_IntervalMinutes` | `5` | How often the concealment cycle runs. |
| `Conceal_InactiveMinutes` | `30` | Idle time before a grid is concealed. |
| `Conceal_RevealRadiusMeters` | `2000` | Player proximity that conceals/reveals grids. |
| `Conceal_MaxPerTick` | `2` | Grids concealed per cycle (protects sim speed). |
| `Conceal_SkipStatic` | `true` | Never conceal static grids. |
| `Adaptive_Enabled` | `false` | Scale module intervals by server sim ratio. |
| `Adaptive_Min/MaxIntervalMinutes` | `5`/`120` | Bounds for adaptive intervals. |
| `Adaptive_Healthy/DegradedSimSpeed` | `0.95`/`0.80` | Sim thresholds for scaling. |
| `ProfilerMetricsFile` | empty | TROA Profiler+ metrics file to read sim speed from (blank = engine). |
| `Conceal_EmergencyEnabled` | `false` | Conceal aggressively when sim ratio stays low. |
| `Conceal_EmergencyThreshold` | `0.60` | Sim ratio that triggers emergency concealment. |
| `Conceal_EmergencyMaxPerTick` | `8` | Grids concealed per tick in emergency mode. |
| `Conceal_KeepAliveAntenna` | `true` | Never conceal a grid with an antenna owned by an online player. |
| `Warn_Enabled` | `true` | Warn a grid's owner before cleanup and gate removal behind the lead. |
| `Warn_LeadMinutes` | `10` | Warning lead before a flagged grid is actually removed. |
| `Warn_ToOwnerChat` / `Warn_ToWebhook` | `true`/`false` | Warning destinations. |
| `Warn_LoginGraceMinutes` | `15` | Grace after a player logs in before their grids can be cleaned. |
| `Warn_MessageTemplate` | (see cfg) | Owner warning text. Placeholders `{DisplayName}` `{Grid}` `{Minutes}` `{Reason}` `{NoCleanTag}`. |
| `SelfKeep_Enabled` | `true` | Allow `!cleaner keep`. |
| `SelfKeep_MaxPerPlayer` / `SelfKeep_DurationHours` | `2`/`72` | Self-keep limits. |
| `Quota_Enabled` | `false` | Enable per-player grid/PCU quotas. |
| `Quota_MaxGrids` / `Quota_MaxPcu` | `0`/`0` | Caps (0 = unlimited). |
| `Quota_FlagOverLimitGrids` | `false` | Flag a player's smallest grids when over quota. |
| `Schedule_Enabled` / `ScheduledEvents` | `false` / empty | Scheduled cleanup events at a local time. |
| `QuietHours_Enabled` / `QuietHours` | `false` / empty | Suppress automatic passes in these windows. |
| `Undo_Enabled` / `Undo_WindowMinutes` | `true`/`30` | Allow `!cleanerplusadmin undo` within the window. |
| `RestoreRequests_Enabled` | `true` | Allow `!cleaner request` / admin approve-deny. |
| `History_Enabled` / `History_RetainDays` | `true`/`30` | Rolling pass history CSV. |
| `Metrics_Enabled` | `false` | Write Prometheus/JSON metrics for Grafana. |
| `Dashboard_Enabled` / `Dashboard_IntervalMinutes` | `false`/`30` | Periodic Discord server-health dashboard. |
| `RequirePcuToKeep` / `MinPcuToKeep` | `false`/`0` | PCU keep rule: a grid under this PCU fails the policy. |
| `Protect_SafeZoneGrids` | `true` | Auto-protect any grid containing a Safe Zone block. |
| `LocaleFile` | empty | Path to a locale pack that overrides all message templates. |
| `Conceal_RevealOnSpawn` | `false` | Conceal medbay/cryo grids and reveal them on player spawn/login. |
| `Conceal_KeepAliveAction` / `Conceal_KeepAliveMinutes` | `true`/`30` | PB/RC keep-alive terminal action + how long it holds. |
| `PersistGridAge` | `true` | Persist grid first-seen across restarts. |
| `Warn_SendGps` | `true` | Send a temporary GPS marker to a flagged grid's owner. |
| `Claim_Enabled` / `Claim_MaxDistanceMeters` | `true`/`200` | Allow `!cleaner claim` for nearby ownerless grids. |
| `RespawnShips_Enabled` (+ `_Mode/_IntervalMinutes/_MaxAgeMinutes`, `RespawnShip_NamePatterns`) | `false` | Respawn-ship module. |
| `NpcGrids_Enabled` (+ `_Mode/_IntervalMinutes`, `Npc_MaxAgeMinutes`, `Npc_MinDistanceFromPlayers`, `Npc_KeepIfBlockPresent`) | `false` | NPC-grid module. |
| `Stow_Enabled` / `Stow_OnlyOwnerOffline` | `false`/`true` | Auto-stow owned grids into TROA-Hanger instead of deleting. |
| `Lifecycle_Enabled` / `Lifecycle_Depower` | `false`/`true` | Graduated abandonment for owned grids. |
| `Lifecycle_DepowerMinutes` / `Lifecycle_DisposeMinutes` | `60`/`1440` | Lifecycle stage timers. |

## "Why was my grid cleaned?"

Run `!cleanerplus scan` — for every eligible grid it lists the reason, e.g. `no beacon`,
`under 25 blocks`, `default (un-renamed) name`, `ownerless`, or `no functional blocks`. To keep a
grid: give it a Beacon, build it past the block minimum, rename it, or add `[NOCLEAN]` to its name.
If a grid was removed, it is recoverable — check Gridvault+ (`!gridvault find <name>`) or the local
`TROA-CleanerPlusData/Cleanup Grids` folder.

## Data and logs

- `TROA-CleanerPlus.cfg` — the live configuration.
- `TROA-CleanerPlusData/Cleanup.log` — every scan, removal, dry-run preview, and skip.
- `TROA-CleanerPlusData/LastSeen.xml` — per-identity last-online times for the owner-offline rule.
- `TROA-CleanerPlusData/Cleanup Grids/…` — local grid backups used when Gridvault+ is not installed.

## Safety model

- Dry-run defaults on; nothing is deleted until you turn it off.
- Backup-before-delete; a grid that cannot be backed up is skipped.
- Grace period spares newly built grids.
- Whitelist, GPS zones, and the `[NOCLEAN]` tag always keep a grid.
- Static grids are excluded unless explicitly enabled.
- NPC-owned grids are left to Keen's own encounter/cargo-ship despawn.
- All removals run on the game thread, bounded by `MaxRemovalsPerTick`.

## Support

Report issues with the Cleaner+ version, Torch version, Space Engineers version, host OS, the command
used, and the relevant `Cleanup.log` excerpt. Do not include private server data in public reports.
