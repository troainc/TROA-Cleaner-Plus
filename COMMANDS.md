# TROA Cleaner+ Command Reference

This reference covers the v1.12.5 private candidate and earlier public releases. v1.12.5 is not a published binary; command availability and behavior depend on the installed DLL. Commands use Torch chat syntax. Preview before any cleanup.

## Player and moderator commands

| Command | Purpose |
|---|---|
| `!cleanerplus help` | Show Cleaner+ commands. |
| `!cleaner mygrids` | List your sampled grids and risk state. |
| `!cleaner risk <grid>` | Explain why one grid is or is not a cleanup candidate. |
| `!cleaner keep <grid>` | Add a temporary personal keep, subject to server limits. |
| `!cleaner claim <grid>` | Claim a nearby ownerless grid when enabled. |
| `!cleaner request <grid>` | Request restoration of a cleaned grid. |
| `!cleanerplus status` | Show master state, dry-run state, module status, and backup integration state. |
| `!cleanerplus policy` | Show the active grid keep policy. |
| `!cleanerplus scan [module]` | Preview qualifying candidates. This never deletes. |

## Administrator cleanup controls

| Command | Purpose |
|---|---|
| `!cleanerplusadmin help` | Show the administrator command menu. |
| `!cleanerplusadmin asteroidstorage status` | Show pending orphan storage cleanup state. |
| `!cleanerplusadmin asteroidstorage scan` | Read-only scan for orphan recognized asteroid/boulder `.vx2` files in the loaded world folder. |
| `!cleanerplusadmin asteroidstorage prune` | Queue orphan files; after cleanup drains and a save succeeds, each is checked against active voxel maps, archived under `TROACleanerPlusData/RemovedVoxelStorage`, verified, and removed from the world folder. Archives use disk space. |
| `!cleanerplusadmin now [module\|all]` | Queue an immediate pass. In v1.12.4, Cleaner+ master and the requested module must be ON. |
| `!cleanerplusadmin on <module>` / `off <module>` | Enable or disable a module without restart. |
| `!cleanerplusadmin mode <module> <interval\|realtime\|command>` | Set a module execution mode. |
| `!cleanerplusadmin interval <module> <minutes>` | Set an interval-mode cadence. |
| `!cleanerplusadmin dryrun <on\|off>` | Enable or disable global dry-run for ordinary cleanup modules. |
| `!cleanerplusadmin master <on\|off>` | Enable or disable all Cleaner+ cleanup activity. |
| `!cleanerplusadmin reload` | Reload the live config file. |
| `!cleanerplusadmin conceal now\|status` | Run or inspect experimental grid concealment. |
| `!cleanerplusadmin reveal all` | Reveal every currently concealed grid. |
| `!cleanerplusadmin digest now` | Send the cleanup digest immediately. |
| `!cleanerplusadmin simspeed` | Show the SimSpeed value Cleaner+ sees. |
| `!cleanerplusadmin quota` | Recompute quota status. |
| `!cleanerplusadmin schedule list` | List scheduled cleanup events. |
| `!cleanerplusadmin lifecycle status` | Show graduated-abandonment status. |
| `!cleanerplusadmin stow status` | Show TROA-Hangar auto-stow availability. |

Modules: `floatingobjects`, `grids`, `corpses`, `respawnships`, `npcgrids`, and (in supported builds) `asteroids`.

## Manual cleanup — v1.12.4 candidate

Begin with previews:

```text
!cleanerplus status
!cleanerplus scan floatingobjects
!cleanerplus scan corpses
!cleanerplus scan grids
!cleanerplus scan respawnships
!cleanerplus scan npcgrids
!cleanerplus scan asteroids
```

Every `scan` is read-only. A disabled module returns no candidates, so check its enabled state before treating an empty result as proof there is no eligible cleanup.

A targeted manual run uses `!cleanerplusadmin now <module>`. The Cleaner+ master switch and requested module must both be ON. With global dry-run ON, ordinary modules report eligible candidates without deleting. `!cleanerplusadmin now all` queues every enabled module.

**Asteroid exception:** the v1.12.3+ candidate permanently removes eligible whole `Asteroid_` maps and, by default, `Boulder-` maps. Voxel removal has no backup or undo and bypasses global dry-run. Review `!cleanerplus scan asteroids` first. Run `!cleanerplusadmin off asteroids` before `!cleanerplusadmin now all` to exclude voxel cleanup. Do not run `now asteroids` or `now all` unless permanent asteroid/boulder removal is intended.

## Recovery commands

| Command | Purpose |
|---|---|
| `!cleanerplusadmin recovery status` | Show recovery state, master/dry-run state, pending removals, and last status. |
| `!cleanerplusadmin recovery on` | Enable automatic eligible floating-object/corpse sweeps during sustained low SimSpeed. |
| `!cleanerplusadmin recovery off` | Disable recovery sweeps; normal module schedules continue. |
| `!cleanerplus scan floatingobjects` | Preview floating-object candidates without deletion. |
| `!cleanerplusadmin now floatingobjects` | Queue a targeted ordinary-module pass if master and module are ON; dry-run and eligibility gates apply. |
| `!cleanerplusadmin dryrun on` | Preview eligible removals without deleting. |
| `!cleanerplusadmin dryrun off` | Permit real removal under ordinary-module cleanup policies. It does not enable or gate the separate asteroid cleanup path. |

Recovery sweeps are separate from manual `now all`. They apply only to enabled automatic floating-object and corpse modules; they do not clean grids, NPC grids, respawn ships, or asteroids and do not override dry-run. `recovery on` does not enable disabled modules, change command-only modes, turn dry-run off, or enable concealment. No grid is selected for removal solely because SimSpeed is low.

The v1.12.0 release notes describe that version's historical behavior. In the v1.12.3+ candidate, asteroid removal ignores dry-run; dry-run ON is not a global no-deletion guarantee.

## Restore and restart operations

| Command | Purpose |
|---|---|
| `!cleanerplusadmin restore list [steamid]` | List locally retained Cleanup Grids backups. |
| `!cleanerplusadmin restore <gridId> [x y z]` | Restore a retained grid near the caller or at a supplied position. |
| `!cleanerplusadmin undo` | Restore grids from the last pass within the configured undo window. |
| `!cleanerplusadmin requests` | List player restoration requests. |
| `!cleanerplusadmin approve <id> [x y z]` / `deny <id>` | Resolve a player restoration request. |
| `!cleanerplusadmin restart now [minutes] [full\|soft]` | Arm a configured restart. |
| `!cleanerplusadmin restart cancel\|delay <minutes>\|skip\|status\|boot` | Manage an armed restart or review boot status. |

## Discord webhooks and operations dashboard

| Command | Purpose |
|---|---|
| `!cleanerplusadmin webhook` | Show webhook health without exposing the URL. |
| `!cleanerplusadmin webhook test` | Queue a webhook test embed. |
| `!cleanerplusadmin webhook reset` | Reset the webhook failure circuit. |
| `!cleanerplusadmin webhook mirror <on\|off>` | Enable or disable generic safe command audit receipts. |
| `!cleanerplusadmin webhook sensitive <on\|off>` | Control separately approved sensitive administrator output. |
| `!cleanerplusadmin webhook players <on\|off>` | Enable or disable generic player-command receipts. |
| `!cleanerplusadmin webhook resource <status\|scan\|health\|quota\|conceal\|schedule\|restart\|modules> [--count N] [--title "text"] [--note "text"] [--fresh]` | Send a detailed but sanitized operator embed. |
| `!cleanerplusadmin dashboard status` | Show persistent dashboard state. |
| `!cleanerplusadmin dashboard now` | Immediately create or update the one editable Discord operations dashboard. |
| `!cleanerplusadmin dashboard reset` | Clear the local message link; the next update creates a replacement dashboard message. |

Webhook dashboards and resource reports intentionally exclude player names, grid names, IDs, Steam IDs, GPS positions, backup paths, and webhook credentials.

## Reusable command references

| Command | Purpose |
|---|---|
| `!cleanerplusadmin command save <name> <full !cleaner... command>` | Save a copy-ready command reference locally. It is never auto-executed. |
| `!cleanerplusadmin command list` | List saved command references. |
| `!cleanerplusadmin command show <name>` | Retrieve one saved reference. |
| `!cleanerplusadmin command remove <name>` | Delete one saved reference. |

## Source policy

This public repository is limited to operator documentation and configuration examples.
