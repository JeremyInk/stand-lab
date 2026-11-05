# Stand Lab

Personal Stand Lab v27 release.

## Load

Paste this into your compatible Lua runner:

```lua
loadstring(game:HttpGet("https://raw.githubusercontent.com/JeremyInk/stand-lab/main/main.lua"))()
```

The runner must support `game:HttpGet` and `loadstring`, plus the capabilities used by the full script. This is not a standard Roblox Studio LocalScript loader.

The loader downloads `main.lua` from this repository. Updating that file updates what the loader runs next time. A short loader reduces editor text; the complete script still downloads and executes, so it does not itself reduce runtime memory or lag.

## Skin targets

Stand Rolls now has a searchable multi-select list of 147 Stand skin names from the [YBA wiki](https://your-bizarre-adventure.fandom.com/wiki/Skins), also available in [skin-catalog.json](skin-catalog.json). The list contains limited/evolved skins and is not a guarantee that a skin can roll from the selected item or exists under the same name in a modified game.

- **Keep any skin** is the default and preserves every skin.
- **Keep selected skins** rerolls unselected skins, including a current skin you already own. Select at least one skin before starting; an empty selection cannot run.
- **Add skin name** accepts custom names; **Add current skin to picker** reads its exact game name. Select the added name in the keep list.
- A selected Stand remains an additional stop condition. Fresh server metadata is rechecked before Rokakaka and again before confirming its dialogue. Changed or unverifiable server data stops the reset.

## Saved preferences

Common preferences now autosave to a stable per-Roblox-account file in the runner's StandLab folder. This includes Stand/skin selections, roll item/limit, Auto-Farm filters, travel preferences, speeds/delays, candy threshold, supported visual options, performance recording and hide/show key. Saving is debounced, writes are read back for verification when possible, and a previous valid file is retained as a backup.

**Settings → Settings save** reports whether changes are saved or only retained in session memory. Without runner file APIs, settings cannot persist across rejoining; reruns can still use session memory. **Save settings now** flushes pending changes. Farming, rolling, selling, prestige, candy turn-in and automatic item hopping do not automatically start from a saved toggle. A deliberate server-travel continuation remains separate.

## Candy turn-in audit

The inspected Halloween turn-in path had no intentional points-purchase code, but used an unverified numbered option. It now selects only Jack's exact candy turn-in caption and explicit leave choices. It will not fall back to a points/purchase choice or a numbered option if the menu changes. No general-purpose dialogue-closing automation is used in this path.

The points reader handles both “You now have” and “You currently have.” Choice captions, turn-in inventory/points observations and observed balance decreases appear in the local report. A decrease alone does not identify its cause. The reader scans player stat folders rather than repeatedly walking the entire player UI.

## Servers

The integrated **Servers** tab replaces the separate Item Serverhop loader:

- **Server hop** finds a different public server with space and handles failed teleport attempts.
- **Rejoin** targets your current server, or uses normal matchmaking for a solo session.
- **Collect + auto hop** uses the existing Auto-Farm item filters (or active candy collector) and changes servers after the configurable empty interval. It starts off.
- **Reload hub after travel** queues this same Stand Lab loader and restores the requested item collection settings when the runner supports teleport queueing. Without queue support, manual travel is available and you run the usual loader after joining. Automatic item hopping requires queue support.

Travel pauses competing automation. Automatic selling and bulk skill allocation remain off by default. The existing collector and original UI are reused; the separate obfuscated payload is not loaded.

## Loading and automatic Play after travel

Queued travel gives the game's initial replication up to five seconds before handing off to the normal hub loader, or proceeds earlier when a Play menu or settled live game is detected. Download and UI construction time can add to that interval. The previous 90-second pre-hub wait is removed so Logs can be opened while still at the intro. Collection and automatic hopping remain paused until the game is actually ready. No extra automatic escape-hop loop is started for a broken client load.

Automatic Play is retained and also starts when the hub is loaded directly at the intro without teleport metadata; this does not invent a collection restart. Queued travel carries entry intent even when collection was off. The title and Main Game Play buttons are identified within the inspected game's LoadingScreen/LoadingScreen1 UI, with Main Game preferred when both are visible. A visible incomplete asset counter blocks entry. Selection now reads actual Play captions within decorative descendants, supports buttons after the old 500-descendant cutoff, and does not reject mouse-event buttons solely because Active is false. Explicitly non-interactable buttons are excluded. Roblox documents the distinction between [Active](https://create.roblox.com/docs/reference/engine/classes/GuiObject#Active) and the [button input events](https://create.roblox.com/docs/reference/engine/classes/GuiButton).

The runner must expose connected handlers. Available Click, press/release and Activated handlers are inspected; press/release handlers receive button-center coordinates. A dispatched callback is an attempt, not proof of successful entry. If the same button/phase does not advance, completed attempts retry with increasing delays (2, 3, 5, 8 and 12 seconds) and rotate among available methods. A running attempt on that button/phase is not overlapped. There are at most six attempts per observed button/phase and twelve overall within the 90-second entry window. Changed menus and a stable ready HUD are the progress checks. This path does not send remote-only PressedPlay, destroy loading screens, clone a substitute HUD, write a synthetic LOADED attribute, or change input/control properties to force entry.

Collection and queued avoidance resume only after the live HUD/Main UI is visible, loading screens are closed or hidden, and the live character in Living has both remotes, a healthy humanoid with movement enabled and an unanchored root. These observations must remain ready for two seconds; replacing the character, root or PlayerGui restarts settlement. Current PlayerGui is reacquired instead of trusting the startup reference. Item farming and its collision changes also pause while this readiness check fails.

**Servers → Retry game entry** retries the actual menu and can resume the pending collection intent only after readiness succeeds. Turning off Collect + auto hop during the wait cancels its pending collection restart. Closing the hub cancels the wait. A pending v27 entry intent can be retained through a later hub reload; closed runtimes do not expose it. The older kick-rejoin route delegates to the same travel/entry path.

**Logs → Copy report → GAME ENTRY / HUD / MOVEMENT** contains bootstrap stages, asset counts, GUI roots, loading-UI descendant counts, visible input captions, Play text paths, Active/Interactable values, connected handler counts, selected methods, retry/progress results, HUD state, character/remotes, root anchoring, humanoid movement flags, camera state and the readiness timeout. Candidate and history lists are bounded. The normal snapshot compares cached and current PlayerGui. Where supported, a bounded list of recent/live client errors is collected using Roblox's [LogService](https://create.roblox.com/docs/reference/engine/classes/LogService); repeated messages are coalesced, ordinary output is excluded, and the listener disconnects on close. Error capture is best effort and may be truncated or unavailable; messages are observations, not proof of the cause. They are not used to decide readiness or uploaded automatically.

This prevents the hub from claiming a partial game load is Ready. It cannot guarantee recovery of a game client that never creates its loading menu or HUD. After an upgrade, leave the game and join fresh once before running the usual loader, so an old queued bootstrap is cleared.

## Optional server avoidance

**Servers → Server avoidance** adds two independent, initially disabled hop rules:

- **Hop on unusual movement** observes other players' loaded character positions four times per second. Two qualifying samples within six seconds trigger travel. Defaults are a jump of at least 150 studs or movement of at least 300 studs/second over at least 20 studs. Counted samples are at least one second apart.
- **Avoid usernames** accepts comma-, space-, or semicolon-separated account usernames. Turn on **Hop for listed usernames** to leave when any exact listed account is present. Matching ignores case and an optional leading @. Display names and partial names are not used. Your own account is always excluded, so an alt can list your main account.
- **Avoidance cooldown** defaults to 60 seconds and carries through deliberate server travel. Baselines reset for new characters, changed/missing roots, dead characters, and long local sampling gaps. New baselines receive an eight-second grace period; resuming after a long local gap receives two seconds.

Both rules require **Reload hub after travel** and runner teleport-queue support. They reuse the existing server selector, teleport retry handling and collector continuation. An active Stand roll, candy turn-in, quest/NPC farm, prestige task or sale defers automatic avoidance. Only one travel operation runs at a time. Turning off a rule updates pending continuation; closing the old UI during an already-started teleport preserves the queued rules.

The username list and thresholds autosave; the two active rule toggles do not auto-start from saved preferences. Enabled rules resume only through a deliberate queued travel continuation. Observations and hop reasons are included in **Logs → Copy report**, with bounded history. Sampling uses player roots rather than world scans and makes no HTTP requests until a hop is requested.

Unusual replicated movement is an observation, not proof of cheating: game abilities and network corrections can produce similar jumps. Adjust thresholds or use exact usernames if the rule fires on normal play.

## Performance diagnostics

Local performance recording is included in **Logs → Copy report**. Keep **Record performance** on, reproduce a spike, and copy the report after the game responds again.

- Frame/scheduler gaps of 100 ms or more, average sampled FPS, and worst observed gap.
- Client memory, Lua heap, instance count, network ping, network throughput, and available engine rendering/physics measurements. Unsupported readings are listed as unavailable. CPU/GPU rendering values remain in engine-native units; they are not whole-machine utilization.
- Before/after samples, active hub features and pending server request count around a stall.
- Slow item-world scans, Stand preview builds and report snapshots, with elapsed time. Elapsed time can include yields and is not a per-script CPU profile.

Normal frame callbacks update counters only. Metrics are read once per second; recording does not initiate world scans or HTTP requests. Storage is capped at 90 samples, 20 stall windows and 24 slow-work records. Reports include the latest 10 samples, 6 stall windows and 8 slow-work records; spike event output is throttled. Closing the hub disconnects the monitor, and **Clear log** clears its history.

Stalls can only be recorded after execution resumes. Cause hints are correlations, not proof of a specific script, garbage collector, driver or hardware fault. The metrics use Roblox's [Stats service](https://create.roblox.com/docs/reference/engine/classes/Stats) and [local-player network ping](https://create.roblox.com/docs/reference/engine/classes/Player#GetNetworkPing). Data stays in local memory until you choose to copy it; nothing is uploaded automatically.

The v22 retained-Rib correction is preserved: a confirmed Stand result proceeds to fresh skin inspection even when the game leaves the rib equipped. Normal unequip is attempted once without destroying or reactivating the item.

Fresh skin validation before Rokakaka and again before its confirmation remains unchanged. The replacement-character, root, both-remotes and reset-Worthiness checks after Rokakaka remain in place. Servers, item collection, and existing diagnostics are preserved.

Full-script compilation, missed Play selection cases, ineffective and yielding handlers, bounded progress retries, press/release fallback, short bootstrap handoff, direct intro entry, pending-intent retention, and the existing join/server/avoidance regression checks passed for v27. Rolling, skin-policy, preference, candy, readiness gates and movement-detection code is preserved from v26. Live game behavior still needs testing in the user's runner.

The client teleport calls match those in the inspected hub. Roblox documents asynchronous teleport failure handling in [TeleportService](https://create.roblox.com/docs/reference/engine/classes/TeleportService) and notes that the modern TeleportAsync method is server-only in its [teleport guide](https://create.roblox.com/docs/projects/teleport).

## Attribution

Stand Lab customizes the recovered Methion gameplay/UI integration credited in the source to Diablo/redikXrbs and bundles the inspected Methion Airflow UI library. Existing source attribution is retained. The inspected UI source was https://raw.githubusercontent.com/Methions/modf/refs/heads/main/cva.

Only release code, this README, the loader, and checksums are included. No local session reports, screenshots, or credentials are included.
