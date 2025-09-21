# PreUpdateMovement (CounterStrikeSharp plugin)

Reintroduces the pre-update "timestep-dependent" movement acceleration feel after Valve's change: Improved timestep-independence of subtick movement acceleration.

Reference: https://store.steampowered.com/news/app/730/view/498332997840273548

This plugin offers two approaches:

1. ScaleInput: Scales CUserCmd forward/side movement per processed command to emulate tick-interval-dependent acceleration (approximate revert).
2. CVarProfile: Applies a curated convar profile to approximate legacy feel without touching input.

Because Valve’s change is engine-side, exact 1:1 parity is not guaranteed. This aims to be a practical, tunable approximation.

## Requirements
- CounterStrikeSharp (latest stable compatible with your server)
- .NET 8 SDK for building

## Build
```bash
dotnet build -c Release
```

## Install
Copy the built DLL to your server and place the config file:

- Plugin DLL:
  - from: `src/PreUpdateMovement/bin/Release/net8.0/PreUpdateMovement.dll`
  - to: `game/csgo/addons/counterstrikesharp/plugins/PreUpdateMovement/PreUpdateMovement.dll`

- Config file:
  - from: `configs/PreUpdateMovement.json`
  - to: `game/csgo/addons/counterstrikesharp/configs/PreUpdateMovement.json`

Create destination folders if they do not exist.

## Configuration (configs/PreUpdateMovement.json)
- Enabled: true/false
- Mode: "ScaleInput" or "CVarProfile"
- TargetTickRate: 64 or 128 (used by ScaleInput)
- FixedScaleOverride: null or a float (if your CSS build doesn’t expose per-cmd frametime, set a manual scale like 0.015625 for 64 tick)
- ApplyOnMapStart: true to reapply cvars at map load if in CVarProfile mode
- CVarProfile: tweak sv_accelerate, sv_airaccelerate, friction, etc.

## Commands (server console or RCON)
- css_preupdate_mov_enable [0/1]
- css_preupdate_mov_mode [ScaleInput|CVarProfile]
- css_preupdate_mov_targettick [64|128|custom]
- css_preupdate_mov_scale [float|clear] (sets FixedScaleOverride)
- css_preupdate_mov_applyprofile (reapplies CVars now)

## Quick tuning
- 64-tick: `css_preupdate_mov_mode ScaleInput`, then `css_preupdate_mov_scale 0.015625`
- 128-tick: `css_preupdate_mov_scale 0.0078125`
- If your API exposes per-cmd dt (e.g., FrameTime), leave FixedScaleOverride null and set TargetTickRate to your server tick.
- If you prefer not to touch inputs at all, use Mode = CVarProfile and tweak the convars for your server.

## Disclaimer
Due to engine-level changes, perfect restoration is unlikely via a plugin alone. This plugin provides two strategies so you can dial in the feel you want.

## License
MIT
