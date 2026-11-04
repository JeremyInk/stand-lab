# Stand Lab

Personal Stand Lab v21 release.

## Load

Paste this into your compatible Lua runner:

```lua
loadstring(game:HttpGet("https://raw.githubusercontent.com/JeremyInk/stand-lab/main/main.lua"))()
```

The runner must support `game:HttpGet` and `loadstring`, plus the capabilities used by the full script. This is not a standard Roblox Studio LocalScript loader.

The loader downloads `main.lua` from this repository. Updating that file updates what the loader runs next time. A short loader reduces editor text; the complete script still downloads and executes, so it does not itself reduce runtime memory or lag.

## Servers

The integrated **Servers** tab replaces the separate Item Serverhop loader:

- **Server hop** finds a different public server with space and handles failed teleport attempts.
- **Rejoin** targets your current server, or uses normal matchmaking for a solo session.
- **Collect + auto hop** uses the existing Auto-Farm item filters (or active candy collector) and changes servers after the configurable empty interval. It starts off.
- **Reload hub after travel** queues this same Stand Lab loader and restores the requested item collection settings when the runner supports teleport queueing. Without queue support, manual travel is available and you run the usual loader after joining. Automatic item hopping requires queue support.

Travel pauses competing automation. Automatic selling and bulk skill allocation remain off by default. The existing collector and original UI are reused; the separate obfuscated payload is not loaded.

## Release status

v21 also waits for Rokakaka's replacement character, character root, both character remotes, and reset Worthiness before learning the node and activating the next Arrow/Rib Cage. Logs identify the exact item-settlement blocker and include server-control state.

Compilation and offline dialogue, respawn, server-controller, arrival-bootstrap and UI-adapter checks passed. Live game timing, server-list access and teleports still need testing in the user's runner. Rejoin before loading a new release to clear previous session state.

The client teleport calls match those in the inspected hub. Roblox documents asynchronous teleport failure handling in [TeleportService](https://create.roblox.com/docs/reference/engine/classes/TeleportService) and notes that the modern TeleportAsync method is server-only in its [teleport guide](https://create.roblox.com/docs/projects/teleport).

## Attribution

Stand Lab customizes the recovered Methion gameplay/UI integration credited in the source to Diablo/redikXrbs and bundles the inspected Methion Airflow UI library. Existing source attribution is retained. The inspected UI source was https://raw.githubusercontent.com/Methions/modf/refs/heads/main/cva.

Only release code, this README, the loader, and checksums are included. No local session reports, screenshots, or credentials are included.
