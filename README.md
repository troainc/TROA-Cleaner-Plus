# TROA Cleaner+

TROA Cleaner+ is a headless, fully toggleable, **restart-free** automated cleanup plugin for Space
Engineers dedicated servers running Torch. Every feature is an on/off switch, every switch can be
changed live (no server restart, no world reload), and every grid it removes is backed up first.

It was built for hassle-free operation: turn on the sweeps you want, pick how each one runs, and let
it keep the world clean without downtime.

## Current Release

- Version: `v1.0.0-alpha.1`
- Package: `TROA-CleanerPlus-v1.0.0-alpha.1.zip`
- SHA-256: `D6E468B1A23B217E5CC3563D268C630F8140E25BD2838D8F3B2569616FD3B8AC`
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
| Grids | `grids` | on | Grids that fail the keep policy, plus (optionally) ownerless grids and grids with no functional blocks. Static grids are excluded unless enabled. |
| Dead characters | `corpses` | off | Dead character bodies older than `Corpses_MaxAgeMinutes`. |

## Commands

### Players (moderator/read)

| Command | Use |
|---|---|
| `!cleanerplus help` | Lists commands. |
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
