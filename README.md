# Adaptive Networks

A Cities: Skylines mod that provides a foundation for networks with extra flexibility and variability. Adaptive Networks extends the game's road and network system, enabling asset creators to build highly customisable and dynamic road, rail, and path networks beyond what the vanilla game supports.

> **Originally created by [Kian Zarrin](https://github.com/kianzarrin).** This fork is maintained by [Eoin Hasty](https://github.com/eoinhasty) with bug fixes and refactoring improvements.

## Features

### Core Capabilities

- **Custom Flags System** — Extends the vanilla flag system for segments, nodes, lanes, and props, giving asset creators fine-grained control over when and how network elements are displayed.
- **Road Editor Integration** — Tools within the asset editor to configure networks with AR-specific properties, including drag-and-drop reordering of props, lanes, and other elements.
- **Templates** — Reusable segment, node, track, prop, and transition prop templates for faster asset creation.
- **Lane Transitions** — Advanced lane transition data for rendering context-aware lane markings and props at merges, splits, and intersections.
- **Custom Expressions** — A scripting system for predicate-based evaluation of road configuration rules.

### Rendering & Visuals

- **Tracks & Tilt** — Support for track meshes with vehicle super-elevation (banking) on curves, including bike tracks and rail tracks.
- **Sharp Corners** — Enhanced corner calculation for more realistic node geometry.
- **Asymmetric Pavements** — Proper handling of roads with different pavement widths on each side.
- **Anti-Flickering** — Rendering optimisations that reduce z-fighting on DC (draw call) nodes.
- **Thin Wires** — Global thin wire rendering with an adjustable in-game slider, particularly useful for railway and tram networks.
- **Tiling** — Control over texture tiling on network meshes.
- **QuayRoads** — Custom terrain modification profiles for embankments, quays, and slopes.
- **Render Shift** — Positional rendering adjustments for pillars and other shifted elements.

### Mod Compatibility

- **TMPE** — Integration with Traffic Manager: President Edition for lane arrow and traffic rule compatibility.
- **Network Skins** — Support for NetworkSkins custom textures and colours.
- **LSMR / LSM** — Compatibility with Loading Screen Mod (Revisited) for texture caching.
- **IMT** — Intersection Marking Tool support (markups render under tracks).

### In-Game Tools

- **AN Tool** (`Ctrl+Alt+A`) — Select and edit network properties in-game, with support for Page Up/Down to switch between underground and overground networks.
- **VBS Tool** (`Ctrl+Alt+V`) — Vehicle Banking System tool for adjusting super-elevation on road curves.
- **Vanilla Mode Toggle** — Switch between Adaptive Roads mode and vanilla mode from the settings panel.

## Changes in This Fork

This fork introduces the following changes on top of the original [Adaptive Roads](https://github.com/kianzarrin/AdaptiveRoads) codebase:

### Submodule Update

- Updated the [KianCommons](https://github.com/eoinhasty/KianCommons) submodule to point to a personal fork, with updated utility methods and API changes (e.g. `IsLdLocA` signature update removing the `out int` parameter).

### Bug Fixes

- **Memory leak fix** — Removed calls to `ReflectionHelpers.SetAllDeclaredFieldsToNull()` in `OnDestroy` handlers for `UserFlagsPanel` and `UserValueDropDownPanel`, which were causing memory leaks and incorrect cleanup behaviour.
- **Event handler fix** — Corrected `UserValueDropDownPanel.OnDestroy` to properly *unsubscribe* (`-=`) from the `OnCustomFlagRenamed` event instead of subscribing (`+=`), preventing a leaked event handler.
- **`LaneHelpers` fix** — Replaced `lane.StartNode` with `lane.HeadsToStartNode` in `IsSplitsUnique`, `IsMergesUnique`, and `GetArrowsExt` to correctly determine forward transition direction.
- **Exception rethrow fix** — Changed `throw e;` to `throw;` in transpiler catch blocks to preserve the original stack trace.

### Refactoring

- **Transpiler patch separation** — Extracted the `RenderInstance` transpiler patch from `Segment/RenderInstance.cs` into a dedicated `Segment/RenderSegments.cs` file targeting the `NetSegment.RenderSegments` private method, providing cleaner separation of concerns between segment rendering and overlay rendering.
- **Updated transpiler patches** — Aligned `CheckPropFlagsCommons`, `SeedIndexCommons`, `CheckSegmentFlagsCommons`, `NodeOverlay`, `ParkingAnglePatch`, and `SegmentOverlay` with the updated KianCommons APIs.
- **`DynamicFlags<NetInfo>` refactoring** — Refactored `NetInfo` flag handling to use `DynamicFlags<NetInfo>` for improved race-day compatibility (contributed by Brandon Devlin).

## Changelog

A summary of features and fixes across major versions (from the original project):

| Version | Highlights |
|---------|------------|
| **3.16.17** | Track templates |
| **3.16.16** | Transition prop templates, destroyed selector UI cleanup |
| **3.16.14** | Track connect groups, global thin wire slider, track lane tag fixes |
| **3.16.8** | Unbroken median flags for nodes/transitions, void fix on load |
| **3.16.4** | Prop junction distance, forbid-any-tags, lane tag fixes |
| **3.15.2** | Near-curb and wind-wire lane transition flags for tracks |
| **3.14** | Prop seed, jump to selected road, add/remove elevations, change AI |
| **3.13** | Lane tags, fences, track props, asymmetric pavement fixes |
| **3.12** | Node tags (replacing custom connect groups), VBS tool fixes |
| **3.10** | Segment templates, track embankment saving |
| **3.9** | Anti-flickering for DC nodes, column direction improvement, LSMR compat |
| **3.8** | Custom selectors, DC node/track asym flags, surface/asphalt track models |
| **3.7** | Bike tracks, IMT support under tracks |
| **3.6** | Sharp corners, pillar shifting |

## Building

### Prerequisites

- Visual Studio 2017 or later
- .NET Framework 3.5 targeting pack
- Cities: Skylines game binaries (referenced via a private `CSBinaries` repository)

### Build

```bash
msbuild AdaptiveRoads.sln /p:Configuration=Debug
```

Build configurations: `Debug`, `Release`, `FAST_TEST_PATCHES`, `Workshop`.

### CI/CD

Releases are created automatically via GitHub Actions when a tag is pushed. The workflow builds the solution, packages the output into a ZIP, and uploads it as a GitHub release asset.

## Project Structure

```
AdaptiveRoads/               Main mod source
├── CustomScript/            Expression scripting engine
├── Data/                    Data models and network extensions
│   ├── Flags/               Custom flag definitions
│   ├── NetworkExtensions/   Extended segment, node, lane, and track data
│   └── QuayRoads/           Terrain modification profiles
├── DTO/                     Templates and serialisation
├── LifeCycle/               Mod entry point and lifecycle management
├── Manager/                 Network extension and track managers
├── NSInterface/             NetworkSkins integration
├── Patches/                 Harmony transpiler and prefix/postfix patches
│   ├── Lane/                Lane rendering and prop flag patches
│   ├── Node/                Node corner, anti-flickering, and overlay patches
│   ├── Segment/             Segment rendering and flag patches
│   ├── Track/               Track rendering with super-elevation
│   ├── RoadEditor/          Asset editor UI patches
│   ├── TMPE/                Traffic Manager compatibility
│   ├── Parking/             Parking angle patches
│   ├── QuayRoads/           Terrain modification patches
│   ├── RenderShift/         Pillar and mesh positioning patches
│   └── VehicleSuperElevation/ Vehicle banking patches
├── UI/                      Settings panel, road editor UI, and tools
└── Util/                    Helper utilities
PrefabMetaDataAPI/           Public API for prefab metadata access
prefab-metadata/             Metadata system for road assets
```

## License

This project is licensed under the [MIT License](LICENSE) — Copyright © 2020 Kian Zarrin.
