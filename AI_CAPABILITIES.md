# LinkFramework AI Capability Reference

> Canonical capability context for AI coding clients and automated tooling.
> Treat identifiers, paths, dependency rules, and ownership boundaries in this
> document as part of the package contract. When this document and source code
> disagree, source code and `package.json` are authoritative.

## Machine-readable identity

```yaml
name: LinkFramework
package_id: com.linkmanny.linkframework
package_type: Unity UPM library
unity_minimum: 2022.3
language_surface:
  - C#
  - Lua via XLua
repository: https://github.com/linkmanny/LinkFramework
package_root: Packages/com.linkmanny.linkframework
runtime_assembly: LinkFramework.Runtime
editor_assembly: LinkFramework.Editor
required_host_dependency:
  - XLua
capabilities_document:
  package_path: Documentation~/AI_CAPABILITIES.md
  latest_url: https://raw.githubusercontent.com/linkmanny/LinkFramework-AI-Docs/main/AI_CAPABILITIES.md
```

## What LinkFramework is

LinkFramework is a reusable Unity application framework delivered as a UPM Git
package. It provides a Lua-oriented application bootstrap plus reusable runtime
and editor systems for UI, Addressables, networking, input, audio, pooling,
tweening, logging, and build workflows.

It is a framework package, not a complete game. Game rules, business protocols,
project Lua, scenes, writable build configuration, and content assets belong to
the host project.

## Capability map

| Area | Status | Main entry points | What the framework provides |
| --- | --- | --- | --- |
| Application lifecycle | Core | `LKGame`, `LKApplication`, `LKBehaviour`, `LKMonoSingleton<T>`, `LKSingleton<T>` | Startup/restart flow, Unity lifecycle bridge, reusable behaviour and singleton bases. |
| Lua runtime | Core, XLua required | `LKLua`, `LuaBind`, `LKLuaBehaviour`, `XLuaHelper` | XLua environment lifecycle, C#/Lua binding, Lua-backed behaviours, and framework Lua helpers. |
| Resources and scenes | Core | `AssetManager`, `AssetSceneManager`, `AssetReference`, `AssetObject`, `LKResLoad` | Addressables-backed loading, scene loading, reference tracking, preload support, and reusable asset instances. |
| UI and windows | Core | `LKUIMgr`, `UIWindowGroup`, `UIWindow`, `UIItem`, `UIEvent`, `UIDragEvent` | Window groups/stacks, Lua-aware windows and items, event adapters, extended UGUI/TMP controls, guide and layout helpers. |
| Messaging and events | Core | `MessageCenter`, `LKEventMgr` | Framework message dispatch and application-level event coordination. |
| Networking | Core | `LKNet`, `LKChannel`, `LKSocket`, `LKHttp`, `LKWebRequestMgr` | TCP channels, packet buffering/queues, HTTP helpers, keep-alive support, and Unity web requests. |
| Pooling | Core | `ObjectPool<T>`, `ClearableObjectPool<T>`, `DisposableObjectPool<T>`, `ListPool<T>`, `DictionaryPool<TKey,TValue>` | Generic allocation-reduction pools plus framework asset/object pooling. |
| Input and interaction | Core | `InputHandler`, `InputEffectMgr`, `KeyCodeInputMgr`, `InputClickDelegate`, `InputDragDelegate` | Click, drag, scroll, swipe, multi-touch, keyboard groups, interaction filtering, and feedback effects. |
| Audio | Core | `LKSound`, `SoundPlayer` | Audio clip loading, playback lifecycle, and sound categories. |
| Tweening | Core plus optional DOTween adapter | `UITweener` and `Tween*` components | Inspector-friendly UI/material/shader/transform tween components; DOTween-specific behaviour is isolated behind its integration. |
| Camera and rendering helpers | Core | `VirtualCameraMgr`, `VolumeMgr`, `BlurBase`, `CameraUtility` | Cinemachine camera coordination, URP volume helpers, UI blur, aspect/layout, shader, and capture utilities. |
| Diagnostics | Core | `LKLog`, `LKSnapshot`, `FontTuningRuntimeTool` | Logging, snapshot hooks, runtime font tuning/export, FPS and debugging helpers. |
| Editor production tools | Editor only | `AutoBuildTool`, `AssetBuildTool`, `BuildUtility`, `LuaBuilder`, `ProtoBuilder`, `SpriteAtlasTool` | Player/Addressables build flows, Lua and protobuf preparation, sprite-atlas generation, preload tools, and asset policy checks. |
| Editor binding/UI tools | Editor only | `LuaBindEditor`, `PrefabViewTool`, `LKUIEditorTools`, `GenLinkFrameworkConfig` | Lua binding inspection, prefab-view generation, UI creation helpers, and XLua configuration generation. |

## Optional integrations

Optional integrations compile only when the host plugin assemblies are present
and the corresponding option is enabled in **Edit > Project Settings >
LinkFramework**.

| Integration | Compile symbol | Integration assembly |
| --- | --- | --- |
| DOTween | `LINKFRAMEWORK_DOTWEEN` | `LinkFramework.Integrations.DOTween` |
| Spine | `LINKFRAMEWORK_SPINE` | `LinkFramework.Integrations.Spine` |
| Shapes | `LINKFRAMEWORK_SHAPES` | `LinkFramework.Integrations.Shapes` |
| UnityWebSocket | `LINKFRAMEWORK_UNITY_WEBSOCKET` | `LinkFramework.Integrations.UnityWebSocket` |
| Ingame Debug Console | `LINKFRAMEWORK_INGAME_DEBUG_CONSOLE` | `LinkFramework.Integrations.IngameDebugConsole` |
| A* Pathfinding Project | `LINKFRAMEWORK_ASTAR_PATHFINDING` | `LinkFramework.Integrations.AstarPathfinding` |

The integration assemblies are adapters only. The host project remains
responsible for installing and licensing the third-party plugins. The WeChat
input bridge is separately opt-in through `LINKFRAMEWORK_WECHAT` and requires a
host-provided WeChat SDK.

## Dependency contract

- XLua is mandatory and is intentionally host-provided. The runtime assembly
  must be named `XLua`; editor usage should also provide `XLua.Editor`.
- Unity package dependencies such as Addressables, Cinemachine, Collections,
  UGUI/TMP, URP, Input System, and Newtonsoft JSON are declared in
  `package.json`.
- Optional third-party plugins must not be copied into the framework package.
- Configuration for optional integrations is stored once in
  `ProjectSettings/LinkFrameworkSettings.asset` and should be versioned by the
  host project.

## Host-project ownership contract

Do not place game content or writable project configuration under the package.
Use these ownership rules:

```text
Packages/com.linkmanny.linkframework/  framework-owned reusable code/assets
Assets/Lua/LinkFramework/              framework Lua shipped for the host
Assets/Lua/Game/                       host-owned game Lua
Assets/LKAssets/                       host-owned game content
Assets/BuildConfig/                    host-owned build configuration
Assets/AddressableAssetsData/          host-owned Addressables configuration
Assets/XLua/Gen/                       host-generated XLua output
```

The complete host directory contract is in `Documentation~/ASSETS_LAYOUT.md`.
Never edit the read-only copy under `Library/PackageCache`; embed the package or
modify this repository instead.

## Capability boundaries

AI clients must not assume that installing the package alone creates a runnable
game. In particular:

- The default Lua startup requires compatible framework/project Lua files in
  the host project.
- The default UI startup expects host-owned UI root assets and material paths
  documented in `ASSETS_LAYOUT.md`.
- Build, preload, atlas, and Addressables workflows require their corresponding
  host configuration/content directories.
- Legacy GM commands and a legacy protocol generator are samples because they
  depend on host-specific domain and protocol types.
- Optional integration types may not exist in the compilation unless both the
  plugin and its LinkFramework setting are enabled.

## Source routing for AI agents

When changing a capability, inspect these locations first:

| Task | Source location |
| --- | --- |
| Runtime lifecycle, services, Lua, UI, networking, resources | `Runtime/` |
| Unity Editor windows, inspectors, generators, builds | `Editor/` |
| Optional plugin adapters | `Runtime/Integrations/`, `Runtime/Spine/`, `Editor/Integrations/`, `Editor/Spine/` |
| Host asset/path requirements | `Documentation~/ASSETS_LAYOUT.md` and package `README.md` |
| Dependency/version truth | `package.json` |
| Release compatibility history | `CHANGELOG.md` |

Preserve the runtime/editor assembly boundary. Runtime code must not reference
`UnityEditor`; plugin APIs must stay in opt-in integration assemblies; reusable
framework code must not acquire host game types.

## Installation

Install XLua first, then add the package from Git:

```text
https://github.com/linkmanny/LinkFramework.git?path=/Packages/com.linkmanny.linkframework
```

Use a released `vX.Y.Z` tag that contains this capability reference in
production rather than following `main`.

## Fetching this document from a client

The installed package contains the document at:

```text
Documentation~/AI_CAPABILITIES.md
```

Clients that need the latest published document over HTTPS without GitHub
credentials can use
`LinkFrameworkDocumentation.CapabilitiesUrl`, whose value is:

```text
https://raw.githubusercontent.com/linkmanny/LinkFramework-AI-Docs/main/AI_CAPABILITIES.md
```

The public companion repository contains only this document. Maintainers update
it from the canonical file with `tools/Publish-AICapabilities.ps1`. For
documentation aligned to an installed package version, read the package-local
file instead. Consumers should cache the HTTP response and handle offline/network
failures; framework startup must not depend on this remote document.
