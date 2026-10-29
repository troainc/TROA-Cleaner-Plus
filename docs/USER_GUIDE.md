# Cleaner+ — server owner and player guide

Cleaner+ is a Torch-side world maintenance plugin. Players do not install a client mod. The plugin can evaluate floating objects, grids, corpses, and (in supported builds) whole asteroid voxel maps. Every cleanup family has its own enable switch and run mode: interval, bounded real-time scanning, or command-only. Read the versioned [configuration example](../TROA-CleanerPlus.cfg.example) and root [README](../README.md); settings documented for a newer version do not imply that an older installed DLL implements them.

## Install and begin safely

Install the v1.12.6 release and start once to generate the server config/data paths. Keep built-in protections and `GlobalDryRun` enabled while checking server rules. `!cleanerplus scan [module]` previews qualifying cleanup candidates and never deletes. Manual cleanup requires Cleaner+ master and the requested module to be ON. Asteroid/boulder voxel cleanup bypasses global dry-run. Review Torch logs before turning dry-run off. Reload changed configuration with `!cleanerplusadmin reload`; ordinary module and mode changes do not need a restart.

## Cleanup families and grid keep rules

- **Floating objects:** age-based cleanup with optional stack-size and player-distance protection.
- **Grids:** policy can check Beacon presence, minimum block count, a non-default name, owner-offline age, ownerless status, and functional blocks. Static grids are excluded unless explicitly included. Mechanically connected grids can be evaluated and backed up as a group.
- **Corpses:** optional removal after a configured age.
- **Asteroids:** v1.12.6 removes recognized `Asteroid_` and optional `Boulder-` voxel maps beyond the 5 km default grid clearance and 10 km default online-player clearance, regardless of age or global dry-run. If player positions cannot be read, live-map cleanup fails closed. Voxel removal has no backup/undo. Preview with `!cleanerplus scan asteroids`; disable live asteroid cleanup with `!cleanerplusadmin off asteroids` if it must not run.
- **Orphan asteroid storage:** `!cleanerplusadmin asteroidstorage scan` previews orphan `.vx2` files; `prune` waits for cleanup work to drain and a successful save, verifies active references and player clearance, archives and verifies a copy, then removes the world-folder file. Archived files remain recoverable under `TROACleanerPlusData/RemovedVoxelStorage` and use disk space.
- **Concealment:** where included in the installed release, eligible idle grids may be hidden from active simulation and later revealed; it is separate from deletion.

Whitelist/name protections, GPS protection zones, the `[NOCLEAN]` tag, grace periods, and configured per-zone/per-faction policy overrides can exempt grids. The specific installed release determines which of these advanced settings exist.

## Backup, restore, and integrations

Before deleting a grid, Cleaner+ requests a GridVault+ backup when the integration is available. If configured to require backup and the backup fails, deletion is skipped. Without GridVault+, the plugin can use its local cleanup vault. Admins can inspect restore candidates, undo the last cleanup where supported, or review and approve player restore requests. Cleaner+ can also report optional Hangar stow and restart integration status; these integrations are not required for basic cleanup.

## Commands and roles

Use `!cleanerplus` for status, policy, preview, and player actions. Operators use `!cleanerplusadmin` for module toggles, run modes, dry-run, scheduling, webhook/dashboard operations, restore, quotas, recovery, and (if present) asteroid controls. For a manual run use `!cleanerplusadmin now <module>`; `now all` includes every enabled module. In v1.12.4, master and module must be ON. Asteroids bypass global dry-run, so turn that module OFF before `now all` if permanent voxel cleanup is not intended. Recovery sweeps only cover eligible floating objects and dead characters; they are not a full cleanup pass. See the [command reference](../COMMANDS.md). Give admin access only to trusted operators.

## Operating checklist

1. Confirm plugin version and its changelog.
2. Copy and review the sample config; keep webhook URLs private.
3. Preview each cleanup family in dry-run and inspect why specific grids match.
4. Test backup and restore in a disposable world.
5. Enable one cleanup family at a time and observe logs, player reports, and digest output.
6. Review asteroid candidates separately; disable asteroid cleanup unless you explicitly accept irreversible voxel removal.
