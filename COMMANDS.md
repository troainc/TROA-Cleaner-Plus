# TROA Cleaner+ Command Reference

This is the operator-facing reference for v1.7.0. Commands use Torch chat syntax. Start in dry-run mode and use `!cleanerplus scan` before enabling live removal.

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
| `!cleanerplus scan [module]` | Preview candidates and reasons. This never deletes. |

## Administrator cleanup controls

| Command | Purpose |
|---|---|
| `!cleanerplusadmin help` | Show the administrator command menu. |
| `!cleanerplusadmin now [module\|all]` | Queue an immediate cleanup pass. |
| `!cleanerplusadmin on <module>` / `off <module>` | Enable or disable a module without restart. |
| `!cleanerplusadmin mode <module> <interval\|realtime\|command>` | Set a module execution mode. |
| `!cleanerplusadmin interval <module> <minutes>` | Set an interval-mode cadence. |
| `!cleanerplusadmin dryrun <on\|off>` | Enable or disable the global dry-run safeguard. |
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

Modules: `floatingobjects`, `grids`, `corpses`, `respawnships`, and `npcgrids`.

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

This public repository contains operator documentation and configuration examples only. Cleaner+ implementation source and internal frameworks are maintained in TROA's private repository and are not published here.
