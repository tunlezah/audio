# R3 map: /home/user/AudioRouter

Repo ID R3. Remote tunlezah/AudioRouter (/home/user/audio/00-manifest.md:13). Mapped at commit 1bdb71dfb0e7319db0aa2f6cf41aedc853369204, working tree clean (`git status --porcelain` empty).

Citation conventions used in this file:

- `path:line` refers to a tracked file at the commit above.
- `ZIP!member:line` refers to a member of the tracked file `AudioRouter.xcodeproj`, which is a Zip archive (see section 1). `ZIP!pbxproj` = member `AudioRouter.xcodeproj/project.pbxproj`. `ZIP!xcscheme` = member `AudioRouter.xcodeproj/xcshareddata/xcschemes/AudioRouter.xcscheme`. Line numbers come from `unzip -p AudioRouter.xcodeproj <member> | cat -n` (read to standard output, nothing extracted to disk).
- ABSENT means the path is named in a tracked file but is not in `git ls-files`, not in commit 1bdb71d, and not in the archive listing.

## 1. Layout

### Top-level directories

| Directory | Contents |
|-----------|----------|
| `.git/` | Git metadata. The only directory at the top level of the working tree. |

There is no source directory, no test directory and no documentation directory in the tree. All 13 tracked paths are regular files at the repo root (`git ls-files -s`, every entry mode 100644; commit 1bdb71d, "13 files changed, 3325 insertions(+)").

### Tracked files by type

| Type | Count | Files (line counts from `wc -l`) |
|------|-------|----------------------------------|
| Markdown `.md` | 8 | BUILD_AND_DEPLOY.md (499), CHANGELOG.md (179), CHROMECAST_SETUP.md (287), CHROMECAST_SETUP_GUIDE.md (428), FIX_PROJECT.md (191), PROJECT_SUMMARY.md (392), README.md (358), SETUP.md (123) |
| Shell `.sh` | 3 | auto_create_project.sh (725), create_xcode_project.sh (79), fix_project_quick.sh (35) |
| Swift `.swift` | 1 | Package.swift (29). This is a Swift Package Manager manifest (Package.swift:4-6), not application code. |
| Zip archive named `AudioRouter.xcodeproj` | 1 | 23900 bytes. `file` reports "Zip archive data, at least v1.0 to extract, compression method=store". Git records it as binary (commit 1bdb71d, "Bin 0 -> 23900 bytes"). |
| Total | 13 | |

### Contents of the Zip archive `AudioRouter.xcodeproj` (`unzip -l`: 31 entries = 16 directories + 15 files)

| Member type | Count | Members |
|-------------|-------|---------|
| Xcode project file | 1 | `AudioRouter.xcodeproj/project.pbxproj` (21648 bytes, 516 lines) |
| Xcode scheme | 1 | `AudioRouter.xcodeproj/xcshareddata/xcschemes/AudioRouter.xcscheme` (2840 bytes, 78 lines) |
| Workspace data | 1 | `AudioRouter.xcodeproj/project.xcworkspace/contents.xcworkspacedata` (7 lines) |
| Workspace settings plist | 2 | `.../project.xcworkspace/xcshareddata/WorkspaceSettings.xcsettings` (empty dict, 5 lines); `.../project.xcworkspace/xcuserdata/mark.xcuserdatad/WorkspaceSettings.xcsettings` (16 lines) |
| Xcode UI state, binary plist | 1 | `.../project.xcworkspace/xcuserdata/mark.xcuserdatad/UserInterfaceState.xcuserstate` (20692 bytes) |
| macOS AppleDouble `._*` entries under `__MACOSX/` | 9 | 163 or 219 bytes each |
| Empty directory | 1 of the 16 | `.../project.xcworkspace/xcshareddata/swiftpm/configuration/` |

Archive member timestamps run from 2025-11-10 08:27 to 2025-11-13 08:56 (`unzip -l`).

### Paths named in tracked files that are ABSENT from the repo

| Path named | Where it is named |
|------------|-------------------|
| `AudioRouter/AudioRouterApp.swift`, `AudioRouter/ContentView.swift` | README.md:301-302; PROJECT_SUMMARY.md:20-21; ZIP!pbxproj:29-30 |
| `AudioRouter/Managers/` with AudioEngineManager.swift, BluetoothManager.swift, ShazamManager.swift, AirPlayManager.swift, ChromecastManager.swift | README.md:303-308; PROJECT_SUMMARY.md:23-28; ZIP!pbxproj:32-36, 92-103 |
| `AudioRouter/Views/` with EqualizerView.swift, SettingsView.swift | README.md:309-311; PROJECT_SUMMARY.md:30-32; ZIP!pbxproj:37-38 |
| `AudioRouter/Views/DevicePickerViews.swift`, `AudioRouter/Views/LogViewer.swift`, `AudioRouter/Utilities/LogManager.swift` | ZIP!pbxproj:42-43, 46, 109-110, 121. Not named in README.md or PROJECT_SUMMARY.md structure listings. LogViewer is mentioned at CHROMECAST_SETUP_GUIDE.md:421. |
| `AudioRouter/Utilities/` with AudioUtilities.swift, ColorUtilities.swift, Extensions.swift | PROJECT_SUMMARY.md:34-37; ZIP!pbxproj:39-41 |
| `AudioRouter/Assets.xcassets/`, `AudioRouter/Info.plist`, `AudioRouter/AudioRouter.entitlements` | README.md:312-314; PROJECT_SUMMARY.md:39-44; ZIP!pbxproj:31, 44-45, 250, 276 |
| `ChromecastReceiver/index.html` | README.md:91, 315-316; PROJECT_SUMMARY.md:46-47; CHROMECAST_SETUP.md:10 |
| `.gitignore` | PROJECT_SUMMARY.md:60; CHANGELOG.md:90 |
| `Documentation/` directory holding the .md files | PROJECT_SUMMARY.md:52-57 (the five .md files it lists are at the repo root) |
| `add_files_to_project.sh` | create_xcode_project.sh:74 |
| `ExportOptions.plist`, `ReleaseExportOptions.plist` | BUILD_AND_DEPLOY.md:98, 187 (contents shown inline at 101-115, 190-202) |
| `.github/workflows/build.yml` | BUILD_AND_DEPLOY.md:377 (contents shown inline at 379-417) |
| `Podfile` | CHROMECAST_SETUP_GUIDE.md:62 (contents shown inline at 63-72) |

Search used for the ABSENT column: `git ls-files`, `git show --stat HEAD`, `ls -la` of the repo root, `unzip -l AudioRouter.xcodeproj`.

PROJECT_SUMMARY.md:63-65 states "Total Swift Files: 12", "Lines of Code: ~3,500+". The repo holds one Swift file, Package.swift (29 lines).

## 2. Entry points

### Scripts (all three are bash, tracked with mode 100644)

| Entry | path:line | What it does and what it starts |
|-------|-----------|---------------------------------|
| auto_create_project.sh | auto_create_project.sh:1 | `cd /Users/mark/claude/AudioRouter` (:11). If a directory `AudioRouter.xcodeproj` exists, moves it to `AudioRouter.xcodeproj.backup` (:14-19). Makes a temp dir (:25), writes a stub `Package.swift` (:34-52) and a stub `Sources/Main.swift` there (:58-69), returns to the project dir (:80), writes `AudioRouter.xcodeproj/project.xcworkspace/contents.xcworkspacedata` (:86-94), writes `AudioRouter.xcodeproj/project.pbxproj` from a heredoc (:99-602), runs `plutil -lint` on it (:609), writes the scheme file (:618-697), deletes the temp dir (:702), prints follow-up instructions (:711-724). It starts no application process. |
| create_xcode_project.sh | create_xcode_project.sh:1 | `cd /Users/mark/claude/AudioRouter` (:9). Moves a directory `AudioRouter.xcodeproj` to `.backup` (:12-15). Writes `TempMain.swift` (:21-32). Runs `swift package init --type executable --name AudioRouter 2>/dev/null \|\| true` (:35). Runs `rm -f Package.swift` (:38). Writes `create_project.swift` (:41-49). Prints manual Xcode steps under the heading "Manual Project Creation Required" (:53-72). Prints "Then run: ./add_files_to_project.sh" (:74; that script is ABSENT). Deletes the two temp files (:79). It starts no application process. |
| fix_project_quick.sh | fix_project_quick.sh:1 | `cd /Users/mark/claude/AudioRouter` (:5). Moves a directory `AudioRouter.xcodeproj` to `.backup` (:8-12). Prints "The automated fix requires creating a new Xcode project." (:16) and manual steps (:21-28). It starts no application process. |

Callers of the scripts: FIX_PROJECT.md:9-12 names `./fix_project_quick.sh` as "Option 1"; FIX_PROJECT.md:150-156 names `chmod +x auto_create_project.sh` then `./auto_create_project.sh` as "Option 3". No tracked file names a caller for create_xcode_project.sh (searched `create_xcode_project` across all tracked files).

### Build manifests that define a product

| Entry | path:line | What it defines |
|-------|-----------|-----------------|
| Package.swift | Package.swift:6-29 | SwiftPM package "AudioRouter" (:7), platform macOS v14 (:9), one **library** product (:12-15), one target with `path: "AudioRouter"` (:23-27). The directory `AudioRouter/` is ABSENT. No executable product is declared. |
| Xcode target (inside archive) | ZIP!pbxproj:129-145 | Native target "AudioRouter", `productType = "com.apple.product-type.application"` (:144), product `AudioRouter.app` (:47, :143). Sources build phase lists 15 Swift files (:191-212), all ABSENT. |
| Xcode scheme (inside archive) | ZIP!xcscheme:33-53 | LaunchAction, Debug configuration, runs `AudioRouter.app` built from target A60000000001. TestAction has an empty `<Testables>` element (ZIP!xcscheme:25-32). |
| Stub package inside a heredoc | auto_create_project.sh:34-52 | Declares an **executable** product "AudioRouter" with `path: "Sources"`. Written only to a temp dir that the script deletes (auto_create_project.sh:702). |

### Main functions

| Entry | path:line | Status |
|-------|-----------|--------|
| Application entry point `AudioRouterApp.swift` | Named at README.md:301 ("# App entry point"), PROJECT_SUMMARY.md:20 ("# App entry point & delegate"), ZIP!pbxproj:29 | ABSENT. No `@main` or `main` function for the application exists in the repo. |
| `@main struct AudioRouterApp` showing `Text("AudioRouter")` | auto_create_project.sh:61-68 | Heredoc text written to `$TEMP_DIR/Sources/Main.swift`; temp dir deleted at :702. |
| `@main struct TempApp` showing `Text("Temp")` | create_xcode_project.sh:24-31 | Heredoc text written to `TempMain.swift`; file deleted at :79. |
| Cast receiver start `context.start()` | CHROMECAST_SETUP_GUIDE.md:209 | Inside a sample `receiver.html` listing embedded in the guide (CHROMECAST_SETUP_GUIDE.md:116-212). Not a tracked HTML file. |

Search used: `grep -n -E "@main|func main"` over all tracked files (hits: auto_create_project.sh:61, create_xcode_project.sh:24 only).

### Service definitions

None in the repo. Searched all tracked files and the archive listing for: `launchd`, `LaunchAgent`, `login item`, `systemd`, `.service`, `Dockerfile`, `Makefile`, `package.json`, `PBXShellScript`. Zero hits for each. A GitHub Actions workflow appears only as an inline listing in BUILD_AND_DEPLOY.md:379-417; no `.github/` directory is tracked.

### Documented ways to start the application (text only)

- `open AudioRouter.xcodeproj`, then `Cmd+R` in Xcode: README.md:59, 79; SETUP.md:17, 32; BUILD_AND_DEPLOY.md:23, 34; PROJECT_SUMMARY.md:251-252.
- `xcodebuild -project AudioRouter.xcodeproj -scheme AudioRouter -configuration Debug -derivedDataPath ./build`: BUILD_AND_DEPLOY.md:46-49.
- `xcodebuild -scheme AudioRouter -configuration Debug build`: CHROMECAST_SETUP_GUIDE.md:351.

Note on the tracked `AudioRouter.xcodeproj`: it is a single regular file holding a Zip archive (section 1). The three scripts test for it with `[ -d "AudioRouter.xcodeproj" ]` (auto_create_project.sh:14; create_xcode_project.sh:12; fix_project_quick.sh:8).

## 3. Run path

The chain from process start to audio leaving the output cannot be followed in code. The hops below are what the repo holds.

1. Documented process start: the user opens the Xcode project and presses `Cmd+R` (README.md:59, 79; SETUP.md:17, 32).
2. Scheme launch action runs `AudioRouter.app` from target A60000000001 (ZIP!xcscheme:33-53).
3. Target "AudioRouter" is an application whose Sources phase compiles 15 Swift files, first among them `AudioRouterApp.swift` (ZIP!pbxproj:129-145, 191-212). Info.plist path `AudioRouter/Info.plist` and entitlements path `AudioRouter/AudioRouter.entitlements` are set at ZIP!pbxproj:250, 276.
4. **GAP.** `AudioRouter/AudioRouterApp.swift` and every other application source file are ABSENT. No application code exists in the repo from this hop onward: no Bluetooth code, no audio capture code, no output code. Searched: `git ls-files` (13 paths, one `.swift`, which is Package.swift); `git show --stat HEAD` (same 13 paths); `git log` (one commit, so no earlier version holds the sources); `git branch -a` (all four refs at the same commit); `git stash list` (empty); `unzip -l AudioRouter.xcodeproj` (project metadata only, no `.swift` member); `grep -n -i -E "a2dp|IOBluetooth|CoreBluetooth|AVAudioEngine|@main"` over tracked files (hits only in .md prose and the two heredoc stubs listed in section 2).

Trace of the absent sources on the author's machine: the binary member `UserInterfaceState.xcuserstate` in the archive contains the strings `file:///Users/mark/claude/AudioRouter/AudioRouter/ContentView.swift`, `.../AudioRouter/Managers/ShazamManager.swift` and `.../AudioRouter/Managers/AirPlayManager.swift` (read with `unzip -p ... | strings`; binary plist, no line numbers).

For the hops after the GAP, the repo holds prose descriptions only. These are the author's statements in documentation, not code:

5. Stated, input selection: clicking "Bluetooth Device" in the Input Sources section; "Your Mac will become discoverable as \"AudioRouter\"" (README.md:137-138; also SETUP.md:67-68).
6. Stated, Bluetooth component: BluetoothManager, "CoreBluetooth integration", "Peripheral mode (act as audio receiver)", "Central mode (connect to devices)" (README.md:185-189); "Act as A2DP audio sink" (PROJECT_SUMMARY.md:200); "Bluetooth Receiver | ✅ Complete | CoreBluetooth" (PROJECT_SUMMARY.md:78).
7. Stated, capture and processing: AudioEngineManager "Manages AVAudioEngine for audio routing" (README.md:178-183); "Real-time audio capture via AVAudioEngine", "Tap-based buffer processing" (PROJECT_SUMMARY.md:165-166). No document states how audio passes from the Bluetooth component to AVAudioEngine (searched the .md files for `a2dp`, `IOBluetooth`, `PCM`, `buffer`, `tap`, `sample rate`).
8. Stated, AirPlay output: AirPlayManager "AVFoundation AirPlay routing" (README.md:197-200); "AirPlay uses system menu on macOS (not custom picker)" (CHANGELOG.md:103).
9. Stated, Chromecast output: "The current implementation streams metadata and visualizer data only." (CHROMECAST_SETUP.md:202); "Chromecast Audio: Metadata/visualizer only (no audio streaming in base implementation)" (PROJECT_SUMMARY.md:315); "Current Status: **Mock Implementation** (simulated connections only)" (CHROMECAST_SETUP_GUIDE.md:7); "The current implementation includes placeholder code for Chromecast." (README.md:110).

## 4. Documentation

| Path | Kind | One-line summary |
|------|------|------------------|
| README.md | README | Describes a macOS app routing audio from microphone, line-in, system audio and a Bluetooth receiver mode to AirPlay and Chromecast, with ShazamKit recognition and an equalizer view; holds requirements (:40-46), install steps (:48-79), usage (:122-172), architecture by manager and view (:174-224), troubleshooting (:235-268), known limitations (:270-292), project structure (:296-318), future enhancements (:326-335). |
| SETUP.md | Quick setup guide | Prerequisites (:3-9), open/sign/build steps (:13-33), permission prompts (:35-44), basic usage (:46-63), "Connecting a Bluetooth Record Player" (:65-73), common issues (:75-98). |
| PROJECT_SUMMARY.md | Design summary | Version and platform header (:7-11), structure tree (:15-61), feature table with every row marked "✅ Complete" (:71-88), architecture diagram (:101-117), per-file line estimates (:125-138), framework list (:140-150), feature descriptions (:154-224), permissions and security (:228-243), "Benchmarks (Estimated)" (:294-300), known limitations (:312-317), roadmap (:321-339), "Testing Checklist" with ten ticked items (:343-354), status line "✅ Complete & Ready for Use" (:390). |
| CHANGELOG.md | Changelog | One release entry "[1.0.0] - 2025-01-10" (:8) listing added features (:12-68), technical stack (:70-76), security (:92-97), known issues (:99-103), compatibility (:105-108); an "[Unreleased]" section of planned features and "Potential Improvements" (:112-141); "Last Updated: January 10, 2025" (:179). |
| BUILD_AND_DEPLOY.md | Build and distribution guide | Development build in Xcode and by `xcodebuild` (:16-53), archive and export (:57-115), Developer ID and App Store release (:119-202), code signing and notarisation commands (:206-251), DMG/GitHub/Homebrew distribution (:255-312), sample GitHub Actions workflow (:373-417), troubleshooting (:421-465), unticked pre-release checklist (:469-486). |
| CHROMECAST_SETUP.md | Chromecast receiver setup guide | Hosting options for `ChromecastReceiver/index.html` (:12-55), registering a Cast application (:57-84), setting the receiver app ID in ChromecastManager.swift (:86-105), testing steps (:107-135), troubleshooting including "Audio not playing" (:155-210), unticked testing checklist (:248-258). Uses namespace `urn:x-cast:com.audiorouter.app` (:193). |
| CHROMECAST_SETUP_GUIDE.md | Google Cast SDK integration guide | States "Current Status: **Mock Implementation** (simulated connections only)" (:7); steps to register (:21-43), install the Cast SDK by SwiftPM, CocoaPods or manually (:47-88), Info.plist keys (:92-105), a full sample `receiver.html` (:116-212), Swift snippets to replace parts of ChromecastManager.swift (:236-343), test steps (:347-368), time estimate "Total: 5-8 hours" (:408-414). Uses namespace `urn:x-cast:com.audiorouter` (:187, :393). |
| FIX_PROJECT.md | Notes on a project-file problem | Opens "The project.pbxproj file was hand-crafted and Xcode is being strict about it." (:3); three options: run fix_project_quick.sh (:5-12), create a fresh Xcode project by hand and re-add the files (:14-146), run auto_create_project.sh (:148-156); troubleshooting (:160-181). Written in the first person to the reader (:7, :150, :191). |

Not present as separate files: a notes file, a design document other than PROJECT_SUMMARY.md, an issue list, a LICENSE file, a TODO file (checked `git ls-files`). Issue-list material sits inside the files above: "Known Issues" CHANGELOG.md:99-103; "Known Limitations" README.md:270-292 and PROJECT_SUMMARY.md:312-317; "Future Enhancements" README.md:326-335; planned items CHANGELOG.md:112-141; roadmap PROJECT_SUMMARY.md:321-339. A licence statement is at README.md:337-339, CHANGELOG.md:173-175 and PROJECT_SUMMARY.md:374-376.

Explanatory text also sits in script output lines: auto_create_project.sh:711-724; create_xcode_project.sh:53-76; fix_project_quick.sh:15-34.

Rule 10 check: no tracked file contains text addressed to an AI agent as an instruction (read all 13 files in full). FIX_PROJECT.md:7, 150, 191 is first-person assistant-style text addressed to the human reader ("Let me know which option you'd like to try!", FIX_PROJECT.md:191).

## 5. Dependencies

### Dependency manifests in the repo

| Manifest | path:line | Dependencies declared |
|----------|-----------|-----------------------|
| Package.swift | Package.swift:1 | `swift-tools-version: 5.9`. Platform `.macOS(.v14)` (:9). `dependencies: [` holds only comments (:17-21); one is a commented-out line for `https://github.com/google/google-cast-sdk-ios.git`, `from: "4.8.0"` (:20). Target `dependencies: []` (:25). No Bluetooth or audio package is declared. |
| Xcode project (inside archive) | ZIP!pbxproj:50-58, 137-140 | Frameworks build phase `files = ( );` (empty, :54-55). Target `dependencies = ( );` (:139-140). No Swift package reference objects (searched the member for `XCRemoteSwiftPackage`, `XCSwiftPackage`, `packageReferences`, `GoogleCast`, `ShazamKit`, `CoreBluetooth`, `IOBluetooth`: zero hits). `MACOSX_DEPLOYMENT_TARGET = 14.0` (:286, :365, :429, :483). `SWIFT_VERSION = 5.0` (:297, :375). `CreatedOnToolsVersion = 15.0` (:157). |
| Project heredoc in script | auto_create_project.sh:143-151, 229-230 | Same empty Frameworks phase and empty target dependencies. `MACOSX_DEPLOYMENT_TARGET = 14.0` (:372), `SWIFT_VERSION = 5.0` (:383). |
| `swiftpm/configuration/` (inside archive) | archive listing | Empty directory. No `Package.resolved` anywhere. |

No other manifest is tracked: no Podfile, Podfile.lock, Package.resolved, Cartfile, Brewfile, requirements file or package.json (`git ls-files`).

Differences between the archived project file and the heredoc in auto_create_project.sh: the archive lists three more source files (DevicePickerViews.swift, LogViewer.swift, LogManager.swift; ZIP!pbxproj:23-25 against auto_create_project.sh:109-121) and carries a `DEVELOPMENT_TEAM` setting (ZIP!pbxproj:256, 341; an Apple developer team identifier, value not copied per Rule 11) that the heredoc does not.

### Install scripts

No script installs dependencies. The three shell scripts generate or replace Xcode project files (section 2). External tools they invoke: `mktemp` (auto_create_project.sh:25), `plutil` (auto_create_project.sh:609), `swift package init` (create_xcode_project.sh:35).

### Bluetooth and audio dependencies named in documentation (no versions pinned for any system framework)

| Dependency | Role stated | Where named | Version |
|------------|-------------|-------------|---------|
| CoreBluetooth | "Bluetooth connectivity" | PROJECT_SUMMARY.md:146; README.md:186, 354; CHANGELOG.md:73 | none (system framework) |
| AVFoundation / AVAudioEngine | "Audio engine & capture" | PROJECT_SUMMARY.md:145, 75-76, 165; README.md:179, 351; CHANGELOG.md:72 | none |
| ScreenCaptureKit | system audio capture | README.md:279; PROJECT_SUMMARY.md:77 | "macOS 13+" (README.md:278) |
| MediaPlayer | "Now Playing metadata" | PROJECT_SUMMARY.md:148; CHANGELOG.md:75 | none |
| ShazamKit | song recognition | PROJECT_SUMMARY.md:147; README.md:192, 352 | none |
| SwiftUI, AppKit, Combine | UI, macOS integration, reactive programming | PROJECT_SUMMARY.md:144, 149, 150 | none |
| Google Cast SDK (`google-cast-sdk-ios`, pod `google-cast-sdk`) | Chromecast, described as optional and not integrated | README.md:108-120, 353; CHANGELOG.md:76; CHROMECAST_SETUP_GUIDE.md:47-88; Package.swift:20 (commented out) | "4.8.0+" (CHROMECAST_SETUP_GUIDE.md:57); `'~> 4.8'` (CHROMECAST_SETUP_GUIDE.md:70); `from: "4.8.0"` (Package.swift:20, commented out) |
| Cast Application Framework receiver script `cast_receiver_framework.js` | Chromecast receiver page | CHROMECAST_SETUP.md:185; CHROMECAST_SETUP_GUIDE.md:120 | `v3` in the script URL |

IOBluetooth, BlueZ, BlueALSA, PipeWire and PulseAudio are not named anywhere in the repo (grep over all tracked files and the archived project file, zero hits).

### Platform and tool requirements named

- macOS 14.0 or later; Xcode 15.0 or later; Apple Developer account (README.md:42-44; SETUP.md:7-9).
- "Language: Swift 5.9" (PROJECT_SUMMARY.md:9); project setting `SWIFT_VERSION = 5.0` (ZIP!pbxproj:297).
- Tools named for optional distribution or hosting steps: `create-dmg` via `brew install` (BUILD_AND_DEPLOY.md:263), `firebase-tools` via `npm install -g` (CHROMECAST_SETUP.md:29), CocoaPods `pod install` (CHROMECAST_SETUP_GUIDE.md:77). None pinned.

## 6. Stage pointers

No application source code is in the repo (section 3, hop 4). Every pointer below leads to documentation prose, project metadata or the three project-generation scripts. Where a stage has no material of any kind, the entry is NONE SEEN with the search terms.

| Stage | Paths most likely to hold the answers |
|-------|----------------------------------------|
| S01 Platform and stack | README.md:40-46, 349-354; PROJECT_SUMMARY.md:7-11, 99-117, 140-150; CHANGELOG.md:70-76, 105-108; Package.swift:1, 9, 17-21; ZIP!pbxproj:157, 286, 297; SETUP.md:3-9, 77-79 |
| S02 Adapter and controller | README.md:185-189 (BluetoothManager description), 243-248 (troubleshooting); SETUP.md:39-44 (Bluetooth permission); PROJECT_SUMMARY.md:197-203. Adapter selection, adapter model and missing-adapter handling: NONE SEEN (searched `adapter`, `controller`, `chipset`, `powered`, `IOBluetooth`; BluetoothManager.swift is ABSENT). |
| S03 Discoverability, pairing and trust | README.md:131, 133-143, 243-248, 288-292; SETUP.md:52, 65-73; CHANGELOG.md:44-48, 102; PROJECT_SUMMARY.md:197-208, 347 |
| S04 Connection and A2DP profile | CHANGELOG.md:44-48; PROJECT_SUMMARY.md:78, 197-208, 316; README.md:11, 185-189, 288-292. Codec list, reconnect and multiple-source handling: NONE SEEN (searched `codec`, `SBC`, `AAC`, `reconnect`, `disconnect`, `second device`, `priority`, `out of range`; the single `codec` hit is README.md:292, and every `AAC` hit is in the Chromecast snippet at CHROMECAST_SETUP_GUIDE.md:314-339, 387). |
| S05 Audio capture | README.md:178-183; PROJECT_SUMMARY.md:75-78, 156-169; SETUP.md:77-79. Capture point from the Bluetooth stack, format at that point, stream start/stop detection for Bluetooth, buffering: NONE SEEN (searched `PCM`, `sample rate`, `44.1`, `48k`, `bit depth`, `underrun`, `overrun`, `tap`; the one `tap` hit is PROJECT_SUMMARY.md:166 and the `PCM` hits are in the Chromecast snippet at CHROMECAST_SETUP_GUIDE.md:311-338; AudioEngineManager.swift and BluetoothManager.swift are ABSENT). Gap detection is described for song recognition at README.md:166 and PROJECT_SUMMARY.md:188. |
| S06 Remote control and metadata | Volume and mute: README.md:29-32, 169-172; SETUP.md:110-111; PROJECT_SUMMARY.md:86-87, 169; CHROMECAST_SETUP_GUIDE.md:265. Metadata (from ShazamKit recognition and Now Playing): README.md:18-22, 150, 191-200; PROJECT_SUMMARY.md:88, 173-176, 185-195; CHANGELOG.md:21, 25-30, 75. Transport control between device and Bluetooth source: NONE SEEN (searched `AVRCP`, `play/pause`, `skip`, `transport`). |
| S07 Processing | PROJECT_SUMMARY.md:83, 86-87, 164-169, 292-308 ("Benchmarks (Estimated)", latency line :298); README.md:178-183; CHANGELOG.md:132; CHROMECAST_SETUP_GUIDE.md:306-343 (sample AAC encode snippet, `return Data()` at :341). Clock drift: NONE SEEN (searched `drift`, `resampl`; `synchron` appears only in planned-feature lists at README.md:328, CHANGELOG.md:115, PROJECT_SUMMARY.md:330). |
| S08 Output | README.md:13-16, 81-120, 145-160, 197-206, 250-261, 282-286; CHROMECAST_SETUP.md (whole file; "Audio not playing" at :200-210; namespace :190-194); CHROMECAST_SETUP_GUIDE.md (whole file; status :7-8; namespace :187, :393; streaming snippet :306-343); PROJECT_SUMMARY.md:79-80, 171-183, 315, 317; CHANGELOG.md:20-23, 50-55, 99-103, 134, 136, 141 |
| S09 User interface and control surfaces | README.md:34-38, 122-172, 208-224; SETUP.md:35-73, 100-111; PROJECT_SUMMARY.md:84-85, 90-97, 210-224, 282-288; CHANGELOG.md:57-68; ZIP!pbxproj:42-43 (DevicePickerViews.swift and LogViewer.swift named); CHROMECAST_SETUP_GUIDE.md:358, 362, 421 (log viewer); CHROMECAST_SETUP_GUIDE.md:116-212 (receiver page sample) |
| S10 Lifecycle and operations | Install and setup: README.md:48-79; SETUP.md:11-44; FIX_PROJECT.md (whole file); auto_create_project.sh; create_xcode_project.sh; fix_project_quick.sh; hard-coded project path at auto_create_project.sh:11, 80 and create_xcode_project.sh:9 and fix_project_quick.sh:5. Permissions: README.md:70-75, 226-233; PROJECT_SUMMARY.md:228-243; FIX_PROJECT.md:103-125; ZIP!pbxproj:250-251, 257, 279-281. Configuration: README.md:162-167; PROJECT_SUMMARY.md:282-288; CHANGELOG.md:57-61. Logging: SETUP.md:89; README.md:346; CHROMECAST_SETUP_GUIDE.md:275, 290, 297, 421; ZIP!pbxproj:43, 46. Packaging and update: BUILD_AND_DEPLOY.md (whole file); PROJECT_SUMMARY.md:247-270; README.md:320-324. Start on boot and crash recovery: NONE SEEN (searched `launchd`, `LaunchAgent`, `login item`, `boot`, `restart`, `crash`; the hits are troubleshooting and checklist text only: SETUP.md:87-91, PROJECT_SUMMARY.md:351, README.md:254, BUILD_AND_DEPLOY.md:436, CHROMECAST_SETUP_GUIDE.md:378, 384). |
| S11 Testing and measured behaviour | PROJECT_SUMMARY.md:255-258 (test command), 292-300 ("Benchmarks (Estimated)"), 343-354 ("Testing Checklist"); ZIP!xcscheme:25-32 (empty Testables); auto_create_project.sh:643-650 (same, in heredoc); FIX_PROJECT.md:3, 36; create_xcode_project.sh:69; BUILD_AND_DEPLOY.md:469-486; CHROMECAST_SETUP.md:248-258; CHROMECAST_SETUP_GUIDE.md:347-368; CHANGELOG.md:99-103; README.md:270-292; PROJECT_SUMMARY.md:312-317. Test files, test output, logs, recorded measurements: NONE SEEN (`git ls-files`; archive listing). Named source devices: only generic terms, "record players" (README.md:11, 135; SETUP.md:65), "phones/tablets" (PROJECT_SUMMARY.md:207); no make or model appears. |
| S12 Security and exposure | FIX_PROJECT.md:108-125 (App Sandbox capabilities listed, including "Incoming Connections (Server)" at :112); PROJECT_SUMMARY.md:228-243; CHANGELOG.md:92-97; README.md:226-233; ZIP!pbxproj:250 (entitlements path; the entitlements file is ABSENT), 257 and 342 (hardened runtime), 256 and 341 (developer team identifier, value not copied); CHROMECAST_SETUP_GUIDE.md:92-105 (local network and Bonjour keys); CHROMECAST_SETUP.md:57-84, 165-167; README.md:94-100 (receiver app ID placeholder `YOUR_RECEIVER_APP_ID`); BUILD_AND_DEPLOY.md:109-110, 198-199, 240-244 (placeholders in sample signing and notarisation commands, no real values). Ports opened and authentication on them: NONE SEEN (searched `port`, `listen`, `socket`, `http server`, `auth`; the only hits are the receiver page's "Listening..." status text at CHROMECAST_SETUP.md:127 and Cast message/session listeners at CHROMECAST_SETUP_GUIDE.md:187, 268). |

## 7. Git references

| Item | Value | Source |
|------|-------|--------|
| Default branch | `main`. `refs/remotes/origin/HEAD` is not set, so the default was not readable from the remote; `main` is recorded as default in the manifest because it is the only branch besides the session branch. | `git symbolic-ref refs/remotes/origin/HEAD` returned "not a symbolic ref"; /home/user/audio/00-manifest.md:24, 31 |
| Current branch | `claude/inspiring-galileo-a9dlia` (created by the session tooling at clone time, holds no work of its own) | `git status`; /home/user/audio/00-manifest.md:33 |
| Local branches | `main`; `claude/inspiring-galileo-a9dlia` | `git branch -a` |
| Remote-tracking branches | `remotes/origin/main`; `remotes/origin/claude/inspiring-galileo-a9dlia` | `git branch -a` |
| Commit each branch points at | All four at 1bdb71dfb0e7319db0aa2f6cf41aedc853369204 | `git rev-parse <branch>` for each |
| Unmerged branches | None. Every branch is at the same commit as `main`. | as above |
| Tags | None (0) | `git tag` |
| Stash count | 0 | `git stash list` |
| Commits reachable | 1 | `git log --oneline` |
| The one commit | 1bdb71d, author name "tunlezah", author and commit date 2025-11-13 19:56:14 +1100, subject "Add files via upload", empty body, 13 files added | `git log --format=... --date=iso`; `git show --stat HEAD` |
| Working tree | Clean, no untracked or ignored files | `git status --porcelain --ignored` |
| Remote | origin = https://github.com/tunlezah/AudioRouter (fetch and push) | `git remote -v`; /home/user/audio/00-manifest.md:13 |

Commands run in the repo for this map: `git status`, `git branch -a`, `git tag`, `git stash list`, `git rev-parse`, `git log`, `git show --stat`, `git ls-files -s`, and additionally the read-only commands `git ls-tree -r --name-only main`, `git rev-list --count`, `git remote -v` and `git symbolic-ref refs/remotes/origin/HEAD`. Outside git: `ls`, `wc -l`, `file`, `grep`, `unzip -l`, `unzip -Z -v`, `unzip -p` to standard output, `strings`. Nothing was written, extracted, built or executed inside the repo.
