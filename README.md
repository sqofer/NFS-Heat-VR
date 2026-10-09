# NFS Heat VR — OpenXR

**0.4.0 Beta 1 · Engine build 139 · Windows x64 / PC VR**

A free native stereo VR mod for Need for Speed Heat. Both eyes are rendered by the game engine, head movement controls the view, and headset resolution is separate from the desktop window. Menus use a flat screen in front of you; driving uses VR.

## Start playing

1. Extract the entire release folder to a writable location. Keep its `bin`, `tools` and `assets` folders together; you do not need to copy files into the game folder.
2. Turn on your headset and start its PC VR software. Select a working OpenXR runtime in that software.
3. Open `NFSHeatVR-Launcher.exe`. Click **Open Mod** on the welcome page. The store buttons are optional and do not unlock anything.
4. Check the game folder. Steam installations are detected automatically when possible; **Browse** lets you select the folder containing `NeedForSpeedHeat.exe`.
5. Leave **Enable OpenXR on launch** enabled. Select your resolution and click **Save and launch**. The launcher starts Heat through Steam, waits for the renderer, then connects VR. Complete any Steam/EA app prompts if requested.
6. Load a single-player session and select the **hood camera** for the saved cockpit position. Look straight ahead and press **F6** to recenter. Adjust the camera to suit your car.

If Heat is already running from the selected folder, Save and launch connects to that process. **Stop VR** or **F8** closes VR while leaving Heat open. Closing the settings window leaves VR running. Use the launcher to reconnect after a stop. Settings are stored in `bin/heatvr.ini`.

The default runtime is **System OpenXR runtime**; the mod contains no Pimax SDK or fixed headset optics. A SteamVR runtime override is also available. Only Pimax 8KX has been tested physically so far; other OpenXR headsets still need verification. This is a PC mod, not a standalone Quest application.

## Controls

Hold camera adjustment keys while Heat is the active window. Shift slows camera movement. Adjustments are saved automatically.

| Key | Action |
| --- | --- |
| F6 | Recenter, unless reassigned in the launcher |
| F8 | Stop VR |
| P / L | Camera forward / back |
| ; / ' | Camera left / right |
| O / K | Camera up / down |
| [ / ] | Smaller / larger world scale |
| R / T | Experimental rain offset forward / back |

For another recenter button, click **Assign**, then press a keyboard key or a button on a Windows DirectInput device. Gamepads, wheels, button boxes and POV inputs exposed by DirectInput can be captured. Device identifiers are saved, so bind again when changing devices. OpenXR controller action bindings are not implemented in this beta. The default F6 binding can be restored with **Reset**.

## Headset and camera settings

- **Per-eye resolution** controls actual game rendering. The default is 3200 × 3000 per eye; each final dimension must stay within 64–4096 pixels. Width and height are independent of monitor resolution.
- **Scene scale** multiplies both rendering dimensions. **OpenXR density** changes the submitted output surfaces relative to the runtime recommendation. Increasing output density alone cannot create detail missing from the game render.
- **World size** changes stereo scale and tracked translation. It can be adjusted in the launcher or with the bracket keys.
- **HUD distance** and **Menu distance** position separate panels. Automatic flat presentation covers detected menus, garage, results and cutscenes; driving returns to stereo VR.
- Saved cockpit offsets are relative to the hood camera. They were calibrated with the test car and can need adjustment for another vehicle.
- **Hide driver's head** uses a supplied section profile for the tested face, hair and glasses. It retains arms and body. Other driver appearance choices may need an updated profile; the same matching model used by another character may also be hidden.

## Visuals and monitor

- Vehicle window hiding removes identified glass sections so cockpit visibility is clearer. It applies to matching vehicle glass draws, including other cars.
- **VR exposure adjustment** changes scene exposure for VR. The default −2.4 was tuned during the night cockpit comparison; adjust it if another scene needs a different value.
- **Lens flare size** defaults to 0.10. Headlights continue illuminating the road. The separate disable checkbox removes their flare effects entirely.
- **Model detail distance** supports 1–4×. Higher values retain more detailed models farther away and increase rendering load; they do not override every streaming limit in the engine.
- City mission/garage markers can be hidden. The minimap and HUD remain available.
- **Disable all rain effects in VR** is enabled by default. It suppresses the identified main rain particle graph, its splashes, and camera droplet effects during VR driving. This is persistent, with no test timer. Rain is restored in flat presentation and after a normal VR stop. Wet roads, reflections and rainy weather lighting remain. Report any unhandled rain effect that appears on another car or in another scene.
- The experimental rain offset is retained for research and is unnecessary while rain is disabled.
- **Mirror VR view on monitor** displays a selected eye. Set separate mirror dimensions, vertical field of view and HUD visibility for recording or streaming. Mirror dimensions define the composed output; the final display is fitted to the game's desktop backbuffer. Use a desktop window of the desired streaming size as well.

## Rendering and performance

**Stereo** renders both eyes every frame and is the recommended default. **AFR** updates one eye per game frame; each eye receives a new scene half as often and motion can double or flicker. **Shared scene preparation** is an experimental alternative and was slower in the current testing. These modes are provided for comparison, with no promised speed improvement.

**OFXR frame generation** requires a separately installed compatible OFXR layer and runtime. OFXR is not bundled. Restart VR after changing renderer, runtime or OFXR settings.

This beta does not guarantee 90 FPS or remove every game/runtime frame limit. Lower per-eye resolution or detail distance if needed. Resolution multipliers affect both dimensions, so a 1.5× setting draws 2.25× as many pixels. The current source retains timing logs for diagnosis.

## Compatibility and troubleshooting

This adapter is locked to the tested Steam executable SHA-256:

`573f91d6984b5dcfbbeacc7ef92ca2d89b46a76b328d71411747b17cd187106c`

Another executable build is rejected because the engine offsets have not been verified for it. Testing has focused on single-player. No game executable or game assets are included, and the mod does not modify the game files on disk.

If no image appears, check the selected game folder, **Enable OpenXR on launch**, the active runtime and Steam/EA app prompts. Use Stop VR before changing runtime. If an earlier build is already injected, close Heat before switching release folders.

The launcher writes `launcher-*.log` and `.errors` files in the mod folder. Native session details are in `game-openxr-research.txt`. Unsupported build errors, runtime failures or a game exit appear there. Long sessions, additional vehicles/driver models, other headsets and every menu transition remain beta test coverage gaps. The game integration renders stereo depth but does not submit an OpenXR composition depth layer.

## Build the source

Install Visual Studio 2022 with Desktop development with C++ and a Windows SDK, plus CMake 3.20 or newer on PATH. Windows PowerShell 5.1/.NET WPF is used by the launcher. OpenXR headers, import library and loader, and MinHook source are included in `third_party`.

From the source folder, run:

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File .\Build.ps1
```

This configures an x64 Release build in `build`, builds the native engine/tools, runs 24 tests, and creates the default config if missing. The launcher executable is generated in `bin` and finds the sibling `tools` directory automatically. To create a runtime archive:

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File .\Release.ps1
```

The result is in `dist`. Existing release folders are not overwritten. `Release.ps1 -SkipBuild` packages an already built tree. The source archive contains the production launcher/scripts and C++ sources/tests, without session logs, local process captures or old engine DLLs.

See `CHANGELOG.md` for this beta and `THIRD_PARTY_NOTICES.md` for dependency notices. The mod is free; the welcome page offers optional CYBRID links as developer support.
