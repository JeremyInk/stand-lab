# Stand Lab

Personal Stand Lab v20 release.

## Load

Paste this into your compatible Lua runner:

```lua
loadstring(game:HttpGet("https://raw.githubusercontent.com/JeremyInk/stand-lab/main/main.lua"))()
```

The runner must support `game:HttpGet` and `loadstring`, plus the capabilities used by the full script. This is not a standard Roblox Studio LocalScript loader.

The loader downloads `main.lua` from this repository. Updating that file updates what the loader runs next time. A short loader reduces editor text; the complete script still downloads and executes, so it does not itself reduce runtime memory or lag.

## Release status

v20 waits for Rokakaka's Worthiness reset before learning the node and using another arrow. It also handles late dialogue listeners, stops repeated Continue dispatches caused by typewriter text, reports the game's unworthy rejection immediately, preserves the Stop reason during skin validation, and logs item lookup duration.

The release passed compilation and 39 offline check groups during preparation; these corrections still need live testing. Existing diagnostic logging remains available inside the UI. Rejoin before loading a new release to clear the previous session's game/UI state.

## Attribution

Stand Lab customizes the recovered Methion gameplay/UI integration credited in the source to Diablo/redikXrbs and bundles the inspected Methion Airflow UI library. Existing source attribution is retained. The inspected UI source was https://raw.githubusercontent.com/Methions/modf/refs/heads/main/cva.

Only release code, this README, the loader, and checksums are included. No local session reports, screenshots, or credentials are included.
