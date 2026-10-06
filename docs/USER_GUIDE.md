# Cleaner+ — server owner and player guide

Cleaner+ is a Torch-side world maintenance plugin. Players do not install a client mod. The plugin can evaluate floating objects, grids, corpses, and (in supported builds) whole asteroid voxel maps. Every cleanup family has its own enable switch and run mode: interval, bounded real-time scanning, or command-only. Read the versioned [configuration example](../TROA-CleanerPlus.cfg.example) and root [README](../README.md); settings documented for a newer version do not imply that an older installed DLL implements them.

## Install and begin safely

Install the release ZIP through Torch and start once to generate the server config/data paths. Keep the built-in protections and `GlobalDryRun` enabled while checking the server's rules. The grid preview (`!cleanerplus scan`) reports which rule a grid fails and does not delete anything. Review Torch logs and the scheduled digest before turning dry-run off. Reload changed configuration with `!cleanerplusadmin reload`; ordinary module and mode changes do not need a restart.

## Cleanup families and grid keep rules

- **Floating objects:** age-based cleanup with optional stack-size and player-distance protection.
- **Grids:** policy can check Beacon presence, minimum block count, a non-default name, owner-offline age, ownerless status, and functional blocks. Static grids are excluded unless explicitly included. Mechanically connected grids can be evaluated and backed up as a group.
- **Corpses:** optional removal after a configured age.
- **Asteroids:** supported releases can inspect complete voxel maps after an observed-age period and configured clearances from players and grids. This is permanent world-data deletion, has no grid backup/undo, and must remain disabled until separately opted into and tested on a disposable world.
- **Concealment:** where included in the installed release, eligible idle grids may be hidden from active simulation and later revealed; it is separate from deletion.

Whitelist/name protections, GPS protection zones, the `[NOCLEAN]` tag, grace periods, and configured per-zone/per-faction policy overrides can exempt grids. The specific installed release determines which of these advanced settings exist.

## Backup, restore, and integrations

Before deleting a grid, Cleaner+ requests a GridVault+ backup when the integration is available. If configured to require backup and the backup fails, deletion is skipped. Without GridVault+, the plugin can use its local cleanup vault. Admins can inspect restore candidates, undo the last cleanup where supported, or review and approve player restore requests. Cleaner+ can also report optional Hangar stow and restart integration status; these integrations are not required for basic cleanup.

## Commands and roles

Use the in-game command roots `!cleanerplus` for status, policy, previews, and player actions such as protecting a grid or requesting a restore. Operators use `!cleanerplusadmin` for module toggles, run modes, dry-run, scheduling, webhook/dashboard operations, backup restore, quotas, and (if present) restart and asteroid controls. Exact syntax and permission requirements are in the [command reference in the README](../README.md#commands). Give admin access only to trusted operators.

## Operating checklist

1. Confirm plugin version and its changelog.
2. Copy and review the sample config; keep webhook URLs private.
3. Preview each cleanup family in dry-run and inspect why specific grids match.
4. Test backup and restore in a disposable world.
5. Enable one cleanup family at a time and observe logs, player reports, and digest output.
6. Keep asteroid permanent deletion separately disabled unless you explicitly accept irreversible voxel removal.
