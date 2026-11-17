# R3 stage audit: Bluetooth stages S01 to S06

Repo R3, /home/user/AudioRouter. Audited at commit 1bdb71dfb0e7319db0aa2f6cf41aedc853369204, the only commit (`git log --all --oneline`). All four refs point at it (`git branch -a`, `git rev-parse`), the stash is empty (`git stash list`), there are no tags (`git tag`). Working tree clean before and after the audit (`git status --porcelain` empty). Owner notes for this repo: none (/home/user/audio/00-manifest.md:37-39).

## Conventions used in this file

- `path:line` refers to a tracked file at the commit above.
- `ZIP!pbxproj:line` refers to the member `AudioRouter.xcodeproj/project.pbxproj` inside the tracked file `AudioRouter.xcodeproj`, which is a Zip archive (/home/user/audio/repos/R3/01-map.md:28). Line numbers come from `unzip -p AudioRouter.xcodeproj AudioRouter.xcodeproj/project.pbxproj | cat -n`, read to standard output with nothing extracted.
- `ZIP!xcuserstate obj N` refers to object index N in the `$objects` array of the archive member `AudioRouter.xcodeproj/project.xcworkspace/xcuserdata/mark.xcuserdatad/UserInterfaceState.xcuserstate`. The member is a binary keyed-archive property list with no line numbers. It was read with `unzip -p ... | strings` and by decoding standard input in memory with Python's plistlib (`python3 -I`). Member timestamp 2025-11-11 07:46 (`unzip -l AudioRouter.xcodeproj`).
- ABSENT means the path is named in a tracked file but is not in `git ls-files` (13 files), not in commit 1bdb71d and not in the archive listing (`unzip -l`).
- SCOPE-A, used in "Searched" lines, means all of the following: `grep -n -i -E` over the 12 tracked text files (8 `.md`, 3 `.sh`, Package.swift); `unzip -p ... | grep -n -i -E` over the archive members project.pbxproj, AudioRouter.xcscheme, contents.xcworkspacedata and the shared WorkspaceSettings.xcsettings; a full read of the user WorkspaceSettings.xcsettings (16 lines); `strings` output of UserInterfaceState.xcuserstate. All eight `.md` files, Package.swift, create_xcode_project.sh, fix_project_quick.sh and project.pbxproj were also read in full; auto_create_project.sh was read at lines 1-100 and 600-725 and searched by grep between them.

## Facts that apply to every record

- F1. No application source is in the repo. The Xcode project lists 15 Swift sources, among them `BluetoothManager.swift` and `AudioEngineManager.swift` (ZIP!pbxproj:32-33, 194-210), and every one is ABSENT. The only tracked Swift file is the package manifest (Package.swift:1-29). With one commit, identical refs and no stash, no other version of the repo holds them. Every record below therefore rests on documentation, project configuration, the three project-generation scripts and one Xcode state file.
- F2. Presence wording. "CURRENT, documentation only" means the cited text is in the default branch `main` and the source file it describes is ABSENT.
- F3. Evidence of results held in the repo.
  - One artefact records a result. The archived Xcode state holds a `LastCompletedPersistentSchemeBasedActivityReport` (ZIP!xcuserstate obj 225) whose title is "Build", whose version string is "17B55", and whose summary segments read "Build", "Succeeded" and a date of 2025-11-11 07:46:07 UTC (ZIP!xcuserstate obj 418-445). The run destination stored beside it has architecture "arm64", SDK string "macosx26.1" and model code "Mac15,13" (ZIP!xcuserstate obj 412-417). It records a build. It records nothing about Bluetooth or audio behaviour.
  - The author's written statements of results are the ten ticked items under "Testing Checklist" (PROJECT_SUMMARY.md:343-354) and the limitation and issue lists (README.md:270-292; PROJECT_SUMMARY.md:312-317; CHANGELOG.md:99-103).
  - Status labels such as "Bluetooth Receiver | ✅ Complete | CoreBluetooth" (PROJECT_SUMMARY.md:78) and "Project Status: ✅ Complete & Ready for Use" (PROJECT_SUMMARY.md:390) are recorded as the author's claims that work was finished. Under Rule 4 they are not used here as evidence that a function ran.
  - No test file, test output, log file or recorded measurement is tracked (`git ls-files`; `unzip -l`). The scheme's test action has an empty `<Testables>` element (auto_create_project.sh:643-650).
- F4. Rule 11. Two identifiers were seen and not copied: an Apple developer team identifier (ZIP!pbxproj:256, 341) and a hardware device identifier inside the run-destination entry (ZIP!xcuserstate obj 397, 400). No Bluetooth link key or Bluetooth device address appears anywhere (SCOPE-A, terms `link key|MAC address|address`: zero hits).

---

## S01 Platform and stack

### R3-S01-01  macOS 14.0 or later on Intel and Apple-silicon Macs named as the target
- Question: S01.1
- What was attempted: The documents name "macOS 14.0 or later" (README.md:42; SETUP.md:7; CHANGELOG.md:106) and "Intel and Apple Silicon Macs" (CHANGELOG.md:107). The same floor is set in configuration: `.macOS(.v14)` (Package.swift:9), `MACOSX_DEPLOYMENT_TARGET = 14.0` (ZIP!pbxproj:286, 365, 429, 483; auto_create_project.sh:372, 450, 514, 568) and `SDKROOT = macosx` (ZIP!pbxproj:293, 371). The Bluetooth role is placed on the Mac itself: "Makes your Mac appear as a Bluetooth speaker" (README.md:131). A note adds that the app "is compatible with future versions including macOS 26" (README.md:274). No other board, computer or operating system is named as a target (SCOPE-A, terms `linux|raspberry|\bpi\b|debian|ubuntu|windows|esp32|arduino`: zero hits; the `iOS` hits are the Cast SDK name, a design credit and SETUP.md:79).
- Presence: CURRENT (README.md:42; Package.swift:9; ZIP!pbxproj:286).
- Outcome: PARTIAL
- Outcome evidence: OBSERVED for one build: the report "Build" / "Succeeded" dated 2025-11-11 07:46:07 UTC with destination "arm64", SDK string "macosx26.1", model code "Mac15,13" (ZIP!xcuserstate obj 412-417, 418-445). STATED: "[x] Build successfully on macOS 14" and "[x] No crashes on startup" (PROJECT_SUMMARY.md:345, 351). Conditions and defects: (a) the one observed build is on a single arm64 machine against SDK string "macosx26.1", and no artefact shows a build or run on macOS 14 or on an Intel Mac; (b) STATED: "The project.pbxproj file was hand-crafted and Xcode is being strict about it." (FIX_PROJECT.md:3); (c) STATED: "Some features may require updates when macOS 26 is released." (README.md:274); (d) no artefact and no checklist item shows the app receiving Bluetooth audio on this platform (PROJECT_SUMMARY.md:343-354).
- Why it turned out that way: For defect (b), STATED: the project file was written by hand (FIX_PROJECT.md:3). For the rest, UNKNOWN.
- Depends on: deployment target 14.0 (ZIP!pbxproj:286); the Mac's own Bluetooth (README.md:131, 138); the one build machine recorded, model code "Mac15,13", arm64 (ZIP!xcuserstate obj 415, 417); SDK string "macosx26.1" (ZIP!xcuserstate obj 416).
- Open questions: Was the app built or run on macOS 14, or on an Intel Mac? README.md:274 speaks of macOS 26 as not yet released while the build report carries SDK string "macosx26.1": which macOS version did the build machine run? Did the build of 2025-11-11 come after the project file was regenerated by auto_create_project.sh? Was the built app launched after that build?
- Confidence: MEDIUM. OBSERVED evidence covers one build on one machine; the other points are STATED and no source is present to compare.

### R3-S01-02  Swift with SwiftUI, built with Xcode; Bash for project-generation scripts
- Question: S01.2
- What was attempted: "Language: Swift 5.9", "Framework: SwiftUI", "Build System: Xcode 15+" (PROJECT_SUMMARY.md:9-11). `swift-tools-version: 5.9` (Package.swift:1). `SWIFT_VERSION = 5.0` (ZIP!pbxproj:297, 375; auto_create_project.sh:383, 460). `LastUpgradeCheck = 1500` and `CreatedOnToolsVersion = 15.0` (ZIP!pbxproj:154, 157). AppKit for "macOS integration" and Combine for "Reactive programming" (PROJECT_SUMMARY.md:149-150). Three Bash scripts generate or replace the Xcode project (auto_create_project.sh:1; create_xcode_project.sh:1; fix_project_quick.sh:1). HTML and JavaScript appear only as a sample Cast receiver page printed inside a guide (CHROMECAST_SETUP_GUIDE.md:116-212). The 15 Swift sources the project compiles are ABSENT (ZIP!pbxproj:194-210); the only tracked Swift file is Package.swift.
- Presence: CURRENT for the manifests and documents (Package.swift:1; ZIP!pbxproj:297; PROJECT_SUMMARY.md:9-11). The Swift application sources are ABSENT (F1).
- Outcome: WORKED
- Outcome evidence: OBSERVED, for compilation only: build report "Build" / "Succeeded", 2025-11-11 07:46:07 UTC, version string "17B55" (ZIP!xcuserstate obj 418-445). STATED: "[x] Build successfully on macOS 14" (PROJECT_SUMMARY.md:345).
- Why it turned out that way: UNKNOWN. No build log is in the repo.
- Depends on: the Xcode toolchain on the author's machine, which the report identifies only by the strings "17B55" and "macosx26.1" (ZIP!xcuserstate obj 416, 424); the absent source files.
- Open questions: Which source files were compiled in the observed build? Which Xcode version produced it, given that the documents name Xcode 15 (README.md:43) and the report carries "17B55" with SDK string "macosx26.1"? SETUP.md:77 lists the compiler message "Cannot find 'AVAudioSession' in scope" as a common issue: at what point did it occur, and was it present in the observed build?
- Confidence: MEDIUM. The build result is OBSERVED, but the artefact does not list what was compiled and the sources are absent.

### R3-S01-03  CoreBluetooth named as the Bluetooth stack, no version given
- Question: S01.3
- What was attempted: The documents name one Bluetooth framework: "CoreBluetooth integration", "Peripheral mode (act as audio receiver)", "Central mode (connect to devices)" (README.md:186-188); "Bluetooth Receiver | ✅ Complete | CoreBluetooth" (PROJECT_SUMMARY.md:78); "CoreBluetooth | Bluetooth connectivity" (PROJECT_SUMMARY.md:146); "CoreBluetooth for device connectivity" (CHANGELOG.md:73; README.md:354). It is listed among the app's frameworks, which the author describes as "Uses only public Apple frameworks" (PROJECT_SUMMARY.md:140-150, 242). No version is given; the only version bound is the macOS 14.0 floor (Package.swift:9). No manifest declares it: the Frameworks build phase is empty (ZIP!pbxproj:51-57) and target dependencies are empty (ZIP!pbxproj:139-140; Package.swift:25). The project-level traces of Bluetooth use are the file reference `BluetoothManager.swift` (ZIP!pbxproj:33, 198) and the usage-description setting (ZIP!pbxproj:279, 358). IOBluetooth, BlueZ, BlueALSA, PipeWire and PulseAudio are not named anywhere (SCOPE-A, terms `IOBluetooth` and `bluez|bluealsa|pipewire|pulseaudio`: zero hits).
- Presence: CURRENT, documentation only (README.md:186; PROJECT_SUMMARY.md:146). `BluetoothManager.swift` is ABSENT (F1).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The build report (F3) shows a build, not Bluetooth behaviour. The one ticked Bluetooth item concerns pairing and is recorded in R3-S03-02.
- Why it turned out that way: UNKNOWN.
- Depends on: CoreBluetooth as shipped with macOS 14.0 or later (Package.swift:9); the macOS Bluetooth permission (SETUP.md:40); the App Sandbox Bluetooth capability (FIX_PROJECT.md:114-116).
- Open questions: Which CoreBluetooth classes and calls did `BluetoothManager.swift` use, in its stated "~200" lines (PROJECT_SUMMARY.md:128)? Does CoreBluetooth provide the role named at PROJECT_SUMMARY.md:200, "Act as A2DP audio sink"? Was audio ever received over Bluetooth by this app?
- Confidence: MEDIUM. The framework name is STATED consistently in three documents; there is no code to compare it with.

### R3-S01-04  AVFoundation with AVAudioEngine named as the audio stack, no version given
- Question: S01.4
- What was attempted: "Manages AVAudioEngine for audio routing" (README.md:179); "AVFoundation | Audio engine & capture" (PROJECT_SUMMARY.md:145); "Real-time audio capture via AVAudioEngine" (PROJECT_SUMMARY.md:165); "AVFoundation for audio processing" (CHANGELOG.md:72); "The macOS implementation uses AVAudioEngine directly." (SETUP.md:79). Named beside it: ScreenCaptureKit for system audio capture (PROJECT_SUMMARY.md:77; README.md:279) and MediaPlayer for "Now Playing metadata" (PROJECT_SUMMARY.md:148). No versions are given and no manifest declares these frameworks (ZIP!pbxproj:51-57; Package.swift:17-25).
- Presence: CURRENT, documentation only (README.md:179; PROJECT_SUMMARY.md:145). `AudioEngineManager.swift` (ZIP!pbxproj:32) is ABSENT.
- Outcome: PARTIAL
- Outcome evidence: STATED. "[x] Microphone input works" (PROJECT_SUMMARY.md:346). Conditions and defects: (a) the stated result names microphone input only; no statement or artefact shows audio from a Bluetooth source passing through the engine (PROJECT_SUMMARY.md:343-354); (b) the author lists the compiler message "Cannot find 'AVAudioSession' in scope" as a common issue and writes "This is expected" (SETUP.md:77-79).
- Why it turned out that way: For (b), STATED: "AVAudioSession is primarily for iOS." (SETUP.md:79). For (a), UNKNOWN.
- Depends on: macOS 14.0 or later (Package.swift:9); the macOS microphone permission (SETUP.md:39); the input source chosen in the app (README.md:124-131).
- Open questions: Did the AVAudioSession message stop a build, given that the same passage says "The code will compile and run correctly on macOS." (SETUP.md:79)? Was the "Bluetooth Device" input ever routed through the engine?
- Confidence: MEDIUM. STATED evidence alone; the source file is absent.

### R3-S01-05  No third-party package declared; remaining dependencies are Apple frameworks and command-line tools
- Question: S01.5
- What was attempted: The package manifest's `dependencies: [` block holds only comments (Package.swift:17-21) and the target has `dependencies: []` (Package.swift:25). The Xcode project has an empty Frameworks phase (ZIP!pbxproj:51-57), empty target dependencies (ZIP!pbxproj:139-140) and no package reference (`grep -n -i package` on the member: zero hits). Apple frameworks named beside the Bluetooth and audio ones: ShazamKit (PROJECT_SUMMARY.md:147), MediaPlayer (PROJECT_SUMMARY.md:148), ScreenCaptureKit (PROJECT_SUMMARY.md:77), SwiftUI, AppKit, Combine (PROJECT_SUMMARY.md:144, 149, 150). None carries a version. Tools the scripts invoke: `mktemp` (auto_create_project.sh:25), `plutil` (auto_create_project.sh:609), `swift package init` (create_xcode_project.sh:35). Tools named in documents only: `create-dmg` (BUILD_AND_DEPLOY.md:263), `firebase-tools` (CHROMECAST_SETUP.md:29), CocoaPods (CHROMECAST_SETUP_GUIDE.md:77). An "Apple Developer account (for code signing)" is listed as a requirement (README.md:44). No Package.resolved, Podfile or other lock file is tracked (`git ls-files`).
- Presence: CURRENT (Package.swift:17-25; ZIP!pbxproj:51-57, 139-140).
- Outcome: WORKED
- Outcome evidence: OBSERVED, for the build only: "Build" / "Succeeded", 2025-11-11 07:46:07 UTC (ZIP!xcuserstate obj 418-445). INFERRED link to this dependency set: the project.pbxproj member is dated 2025-11-10 20:20 and the state file 2025-11-11 07:46 in the same archive (`unzip -l`), so the build is read as made from a project file with no package reference.
- Why it turned out that way: UNKNOWN.
- Depends on: frameworks shipped with macOS and Xcode on the build machine (ZIP!xcuserstate obj 416, 424); a developer team set for signing (ZIP!pbxproj:251, 256; value not copied).
- Open questions: Did the built app use ShazamKit, MediaPlayer and ScreenCaptureKit at run time without further entitlements? README.md:268 lists "Verify ShazamKit entitlements are properly configured": were they?
- Confidence: MEDIUM. The build is OBSERVED; its link to this exact project file is INFERRED from timestamps.

### R3-S01-06  Google Cast SDK named as an optional dependency and not integrated
- Question: S01.5
- What was attempted: The Cast SDK appears as a commented-out package line with `from: "4.8.0"` (Package.swift:20), as "Google Cast SDK support (optional)" (CHANGELOG.md:76) and as "Google Cast SDK (optional)" (README.md:353). A guide gives three ways to add it: Swift Package Manager at "Latest (4.8.0+)" (CHROMECAST_SETUP_GUIDE.md:49-58), CocoaPods with `'~> 4.8'` (CHROMECAST_SETUP_GUIDE.md:60-80), or a manual `GoogleCast.xcframework` (CHROMECAST_SETUP_GUIDE.md:82-88). The receiver page loads `cast_receiver_framework.js` from a `v3` path (CHROMECAST_SETUP_GUIDE.md:120; CHROMECAST_SETUP.md:185). The guide opens with "Current Status: **Mock Implementation** (simulated connections only)" (CHROMECAST_SETUP_GUIDE.md:7), and the README says "The current implementation includes placeholder code for Chromecast." (README.md:110).
- Presence: DISABLED. The only declaration is commented out (Package.swift:20) and the Xcode project holds no reference to it (ZIP!pbxproj:51-57).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No integration is recorded, so there is no result to grade.
- Why it turned out that way: UNKNOWN. No document says why the SDK was left out of the build.
- Depends on: Cast SDK 4.8.0 or later (Package.swift:20; CHROMECAST_SETUP_GUIDE.md:57); "Full Chromecast implementation requires Google Cast SDK" (CHANGELOG.md:101).
- Open questions: Was the SDK ever added to the project on the author's machine? Does the SDK named, `google-cast-sdk-ios`, build for the macOS target set at Package.swift:9?
- Confidence: HIGH. The documents state that the SDK is not integrated, and both manifests agree (Package.swift:20; ZIP!pbxproj:51-57).

---

## S02 Adapter and controller

### R3-S02-01  Bluetooth access obtained through the macOS permission prompt and an App Sandbox capability
- Question: S02.1
- What was attempted: No text describes the app finding, selecting or configuring a Bluetooth adapter (SCOPE-A, terms `adapter|controller|chipset|dongle|firmware|hci|powered|rfkill`: zero hits). What the repo holds is access configuration. The build setting `INFOPLIST_KEY_NSBluetoothAlwaysUsageDescription` carries the text "AudioRouter needs Bluetooth access to connect to audio devices." (ZIP!pbxproj:279, 358; auto_create_project.sh:367, 445; PROJECT_SUMMARY.md:235). The manual project steps tick "Bluetooth" under App Sandbox "Hardware" (FIX_PROJECT.md:108-116) and expect "Privacy - Bluetooth Always Usage Description" in the target properties (FIX_PROJECT.md:121-124). The user is told the app asks on first launch: "**Bluetooth** - Click "OK" to allow" (SETUP.md:40; README.md:72-75; auto_create_project.sh:719-722). The settings point at `AudioRouter/AudioRouter.entitlements` and `AudioRouter/Info.plist` (ZIP!pbxproj:250, 276), and both files are ABSENT.
- Presence: CURRENT (ZIP!pbxproj:279; SETUP.md:40; FIX_PROJECT.md:116). The Info.plist and entitlements files are ABSENT.
- Outcome: WORKED
- Outcome evidence: STATED. "[x] Permissions requested properly" (PROJECT_SUMMARY.md:354). The item does not name Bluetooth; SETUP.md:37-41 lists Bluetooth as one of three prompts shown on first launch.
- Why it turned out that way: UNKNOWN.
- Depends on: App Sandbox (PROJECT_SUMMARY.md:240; FIX_PROJECT.md:110); `ENABLE_HARDENED_RUNTIME = YES` (ZIP!pbxproj:257); `GENERATE_INFOPLIST_FILE = NO` with `INFOPLIST_FILE = AudioRouter/Info.plist` (ZIP!pbxproj:275-276); the user's choice in macOS Privacy & Security settings (SETUP.md:43-44).
- Open questions: With `GENERATE_INFOPLIST_FILE = NO` (ZIP!pbxproj:275), which carried the Bluetooth usage text at run time: the absent Info.plist or the build setting at ZIP!pbxproj:279? Does the ticked item cover the Bluetooth prompt specifically? The pre-release list leaves "[ ] Verify all permissions work" unticked (BUILD_AND_DEPLOY.md:476): was that check ever made?
- Confidence: MEDIUM. STATED evidence alone; the two files that would confirm it are absent.

### R3-S02-02  Bluetooth power state addressed only as a user step in macOS System Settings
- Question: S02.1
- What was attempted: The only text about adapter power is the first troubleshooting step for a device that does not connect: "Ensure Bluetooth is enabled in System Settings" (README.md:245). No text describes the app reading or changing the power state (SCOPE-A, terms `adapter|controller|chipset|dongle|firmware|hci|powered|rfkill`: zero hits).
- Presence: CURRENT, documentation only (README.md:245).
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: the user enabling Bluetooth in macOS System Settings (README.md:245).
- Open questions: Did `BluetoothManager.swift` check the Bluetooth power state before starting receiver mode? What did the app show when Bluetooth was off?
- Confidence: LOW. INFERRED from one troubleshooting line: no other text mentions power, so power control is read as sitting outside the app.

### R3-S02-03  NOT FOUND
- Question: S02.2
- Searched: SCOPE-A with `adapter|controller|chipset|dongle|firmware|hci|powered|rfkill` (zero hits), `IOBluetooth` (zero hits), `Intel|Apple Silicon|macOS 1[0-9]|macOS 26|Xcode 1[0-9]|Swift 5` (platform and toolchain lines only). Full read of README.md, SETUP.md, PROJECT_SUMMARY.md and CHANGELOG.md.
- Nearest related material: CHANGELOG.md:107 ("Intel and Apple Silicon Macs") and README.md:138 ("Your Mac will become discoverable as "AudioRouter""), which place the Bluetooth role on the Mac without naming an adapter, chipset, firmware or adapter count. ZIP!xcuserstate obj 417 gives the model code of the one build machine.

### R3-S02-04  Manual troubleshooting list for a Bluetooth device that does not connect
- Question: S02.3
- What was attempted: The README section "Bluetooth Device Won't Connect" gives four user steps: Bluetooth enabled in System Settings, source device in pairing mode, input set to "Bluetooth Device", and a check of "System Settings > Privacy & Security > Bluetooth" (README.md:243-248). For a crash on launch the setup guide says "Ensure all permissions are granted in System Settings" (SETUP.md:87-90). These cover Bluetooth switched off and permission denied, as checks the user makes. No text covers a missing adapter or an adapter reset, and no text describes handling inside the app (SCOPE-A, terms `adapter|controller|chipset|dongle|firmware|hci|powered|rfkill` and `hot-?swap|route change|unavailable|lost|drop`: no Bluetooth hit).
- Presence: CURRENT, documentation only (README.md:243-248).
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: user action in macOS System Settings (README.md:245, 248).
- Open questions: What did the app do or show when Bluetooth was off, when permission was denied, or when the adapter reset during use? Did receiver mode resume after Bluetooth was switched back on?
- Confidence: MEDIUM. The steps are STATED plainly; there is no code to compare them with.

---

## S03 Discoverability, pairing and trust

### R3-S03-01  Mac described as discoverable under the name "AudioRouter" once the "Bluetooth Device" input is selected
- Question: S03.1
- What was attempted: The user clicks "Bluetooth Device" in the Input Sources section, and then "Your Mac will become discoverable as "AudioRouter"" (README.md:137-138). The setup guide says "Your Mac will show as "AudioRouter" to nearby Bluetooth devices" (SETUP.md:68). The input is described as "Makes your Mac appear as a Bluetooth speaker" (README.md:131), and the mechanism is named as "Peripheral mode (act as audio receiver)" (README.md:187). How long the Mac stays discoverable or pairable is not stated (SCOPE-A, terms `timeout|seconds|minutes|duration|how long`: hits are song-recognition intervals and Chromecast waits only). No device class is stated beyond the words "Bluetooth speaker" (SCOPE-A, terms `class of device|device class|CoD`: no Bluetooth hit).
- Presence: CURRENT, documentation only (README.md:137-138; SETUP.md:68). `BluetoothManager.swift` is ABSENT (F1).
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: the user selecting the "Bluetooth Device" input (README.md:137, 247); CoreBluetooth peripheral mode as named (README.md:186-187); the macOS Bluetooth permission (SETUP.md:40).
- Open questions: Was the name "AudioRouter" set by the app, or did source devices see the Mac's own Bluetooth name? Did any source device ever list "AudioRouter"? How long does discoverability last, and does it end when another input is selected? Does the ticked item "[x] Bluetooth pairing works" (PROJECT_SUMMARY.md:347) cover discovery under this name?
- Confidence: MEDIUM. STATED the same way in two documents; there is no code to compare it with.

### R3-S03-02  Pairing started from the source device, with no PIN, confirmation or acceptance rule described
- Question: S03.2
- What was attempted: The documented steps are all on the source device: "Enter pairing mode", "Look for "AudioRouter" in available devices", "Connect to it" (README.md:139-142); "Press the Bluetooth pairing button" and "Select it to connect" (SETUP.md:69-72). The capability is labelled "Device discovery and pairing" (CHANGELOG.md:46) and "Pairing management" (PROJECT_SUMMARY.md:202). No PIN, passkey, confirmation dialog, pairing agent or automatic acceptance rule is described (SCOPE-A, terms `PIN|passkey|confirm|agent|auto-?accept|just ?works`: no Bluetooth hit).
- Presence: CURRENT, documentation only (README.md:139-142; SETUP.md:69-72). `BluetoothManager.swift` is ABSENT (F1).
- Outcome: PARTIAL
- Outcome evidence: STATED. "[x] Bluetooth pairing works" (PROJECT_SUMMARY.md:347). Known defect, STATED: "Some Bluetooth devices may require additional pairing steps", listed under "Known Issues" (CHANGELOG.md:99-102) and under "Known Limitations" (README.md:291). No test output or log accompanies either statement (F3).
- Why it turned out that way: UNKNOWN. Neither the extra steps nor the devices that need them are named.
- Depends on: a source device with a pairing mode (README.md:140; SETUP.md:70); CoreBluetooth as the named framework (README.md:186); the macOS Bluetooth permission (SETUP.md:40).
- Open questions: Which source devices were paired, and with what result for each? What are the "additional pairing steps"? Did macOS show a pairing confirmation, and was it handled by the app or by the operating system? The pre-release list leaves "[ ] Test Bluetooth connectivity" unticked (BUILD_AND_DEPLOY.md:477): was the tick at PROJECT_SUMMARY.md:347 made from a test? The checklist document is dated "January 10, 2025" (PROJECT_SUMMARY.md:391) while the archive members are dated 2025-11-10 to 2025-11-13 (`unzip -l`): when was the checklist filled in?
- Confidence: MEDIUM. STATED evidence alone; the repo holds no source code and no test artefact to compare it with.

### R3-S03-03  NOT FOUND
- Question: S03.3
- Searched: SCOPE-A with `trust|forget|unpair|remove device|allow ?list|whitelist|block ?list` (hits CHROMECAST_SETUP.md:80, 252 only, about Chromecast test devices), `store|persist|remember|saved|known devices|device list|picker` (hits: App Store text, CHANGELOG.md:61, PROJECT_SUMMARY.md:350, AirPlay picker lines, CHROMECAST_SETUP.md:117), `link key|MAC address|address` (zero hits), `discoverab|pairable|pairing|pair\b|paired`.
- Nearest related material: PROJECT_SUMMARY.md:202 ("Pairing management", two words with no description). ZIP!pbxproj:42 names `DevicePickerViews.swift`, which is ABSENT and whose contents are unknown. CHANGELOG.md:61 ("Persistent preferences") and PROJECT_SUMMARY.md:350 ("[x] Settings persist") concern app settings, not paired devices.

### R3-S03-04  NOT FOUND
- Question: S03.4
- Searched: SCOPE-A with `second device|multiple|simultaneous|priority|multipoint` (hits concern outputs and builds: README.md:7, 16, 332; CHANGELOG.md:23, 136; BUILD_AND_DEPLOY.md:61), `switch|priorit|last device|most recent` (zero hits), `reject|refus|deny|denied|accept` (hits README.md:11, SETUP.md:52, CHROMECAST_SETUP_GUIDE.md:26).
- Nearest related material: README.md:11 ("accept connections from record players and other Bluetooth audio devices"), plural, with no rule for a second device. PROJECT_SUMMARY.md:203 ("Connection monitoring").

### R3-S03-05  NOT FOUND
- Question: S03.5
- Searched: SCOPE-A with `PIN|passkey|confirm|agent|auto-?accept|just ?works` (no Bluetooth hit; BUILD_AND_DEPLOY.md:147 concerns notarisation email), `auth|encrypt|secur` (hits are the "Privacy & Security" settings path and section headings), `trust|forget|unpair|remove device|allow ?list|whitelist|block ?list` (Chromecast hits only), `reject|refus|deny|denied|accept`.
- Nearest related material: README.md:137-138, where receiver mode begins only when the user selects "Bluetooth Device": a control over when pairing is possible, not over who pairs. SETUP.md:40 and PROJECT_SUMMARY.md:235, the macOS Bluetooth permission, which governs the app's access to Bluetooth and not which peer may pair.

---

## S04 Connection and A2DP profile

### R3-S04-01  A2DP sink role claimed through CoreBluetooth peripheral mode and switched on by input selection
- Question: S04.1
- What was attempted: The documents claim the sink role: "Act as A2DP audio sink" (PROJECT_SUMMARY.md:200); "Support for audio sink profile (A2DP)" (CHANGELOG.md:48); "Act as Bluetooth audio receiver" (CHANGELOG.md:45). The mechanism named is "Peripheral mode (act as audio receiver)" under "CoreBluetooth integration" (README.md:186-187), with the status row "Bluetooth Receiver | ✅ Complete | CoreBluetooth" (PROJECT_SUMMARY.md:78). The role is switched on when the user selects the "Bluetooth Device" input (README.md:131, 137; SETUP.md:52, 67). No text says how the role is registered: no service record, service identifier, profile registration call or operating-system setting is described (SCOPE-A, terms `sdp|uuid|advertis|rfcomm|l2cap|gatt|\bBLE\b|low energy|profile`: the only Bluetooth hits are CHANGELOG.md:48, PROJECT_SUMMARY.md:316 and README.md:290).
- Presence: CURRENT, documentation only (PROJECT_SUMMARY.md:200; CHANGELOG.md:48; README.md:187). `BluetoothManager.swift` is ABSENT (F1).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The "✅ Complete" row is a completion claim (F3). The testing checklist has no item for Bluetooth audio or for A2DP (PROJECT_SUMMARY.md:343-354).
- Why it turned out that way: UNKNOWN.
- Depends on: CoreBluetooth as the named framework (README.md:186); the stated limitation "A2DP profile support varies by device" (README.md:290; PROJECT_SUMMARY.md:316); the user selecting the "Bluetooth Device" input (README.md:137).
- Open questions: How did `BluetoothManager.swift` register or expose an A2DP sink? Does CoreBluetooth peripheral mode carry A2DP audio? Did any source device open an audio stream to the Mac? Which devices does "varies by device" refer to, and what was seen with each?
- Confidence: MEDIUM. The claim is STATED in three documents; there is no code and no stated result for it.

### R3-S04-02  Central mode described as a second connection direction, from the Mac out to devices
- Question: S04.1
- What was attempted: Beside peripheral mode, the Bluetooth component is listed with "Central mode (connect to devices)" and "Device discovery and connection management" (README.md:188-189), and with "Device discovery" (PROJECT_SUMMARY.md:201). One listed use case is "Stream from Bluetooth speakers" (PROJECT_SUMMARY.md:208). The permission text reads "AudioRouter needs Bluetooth access to connect to audio devices." (ZIP!pbxproj:279). No text says what is exchanged over a central-mode connection or how it relates to receiving audio.
- Presence: CURRENT, documentation only (README.md:188-189). `BluetoothManager.swift` is ABSENT (F1).
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: CoreBluetooth as the named framework (README.md:186).
- Open questions: What did central mode connect to, and for what purpose? Was it a second way of receiving audio from a source, or separate from the sink role in R3-S04-01? Which of the two modes ran when the "Bluetooth Device" input was selected?
- Confidence: LOW. INFERRED from two short list lines: the words "connect to devices" are read as the Mac opening the connection, and nothing else describes the mode.

### R3-S04-03  NOT FOUND
- Question: S04.2
- Searched: SCOPE-A with `codec|SBC|AAC|aptX|LDAC` (Bluetooth hit README.md:292 only; the AAC hits at CHROMECAST_SETUP_GUIDE.md:314-339, 387 are a Chromecast sample; the auto_create_project.sh hits are the substring in `buildActionMask`), `a2dp` (four lines, none naming a codec), `negotiat` within the full reads (no hit).
- Nearest related material: README.md:292 ("Audio quality depends on Bluetooth codec support"), which names no codec and no negotiation step.

### R3-S04-04  Connection management and monitoring named, with only the first connect described
- Question: S04.3
- What was attempted: The capability is labelled "Connection management" (CHANGELOG.md:47), "Connection monitoring" (PROJECT_SUMMARY.md:203) and "Device discovery and connection management" (README.md:189). The one state described is the first connect: the source device connects to "AudioRouter" and then "Audio from the device will now stream through AudioRouter" (README.md:142-143; SETUP.md:72-73). A failure to connect is covered by the manual list recorded in R3-S02-04 (README.md:243-248). No text covers disconnect, loss of range, reconnect or reconnect after a reboot (SCOPE-A, terms `reconnect|disconnect|out of range|reboot|connection (monitoring|management)`: hits are the three labels above, App Store Connect text and a Chromecast reboot step at CHROMECAST_SETUP_GUIDE.md:384).
- Presence: CURRENT, documentation only (README.md:142-143, 189; PROJECT_SUMMARY.md:203). `BluetoothManager.swift` is ABSENT (F1).
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: the pairing flow in R3-S03-02; the "Bluetooth Device" input staying selected (README.md:247).
- Open questions: What did the app do when the source disconnected or went out of range? Did a previously paired source reconnect without user action, and after a restart of the Mac or the app? What does "Connection monitoring" watch, and where is its state shown? The planned item "[ ] Add input/output device hot-swapping" (CHANGELOG.md:141): does it refer to a Bluetooth source that drops?
- Confidence: MEDIUM. The connect flow is STATED in two documents; the other states have no text at all.

### R3-S04-05  NOT FOUND
- Question: S04.4
- Searched: SCOPE-A with `second device|multiple|simultaneous|priority|multipoint`, `switch|priorit|last device|most recent` (zero hits), `reject|refus|deny|denied|accept`. Hits concern multiple outputs (README.md:16; CHANGELOG.md:23, 136) and multiple input types (README.md:7; PROJECT_SUMMARY.md:5).
- Nearest related material: README.md:124-131 and README.md:32 ("Per-source input selection"), a choice among four input types of which Bluetooth is one. PROJECT_SUMMARY.md:162 ("Bluetooth Devices (Record players, speakers, phones)"), a list of device kinds with no rule for more than one at a time.

---

## S05 Audio capture

### R3-S05-01  Capture described as an AVAudioEngine tap, with no stated path from the Bluetooth component to it
- Question: S05.1
- What was attempted: Capture is described for inputs in general: "Real-time audio capture via AVAudioEngine" and "Tap-based buffer processing" (PROJECT_SUMMARY.md:165-166); the audio component "Handles input source selection" (README.md:180). Bluetooth is one of four selectable inputs (README.md:124-131; PROJECT_SUMMARY.md:158-162). The architecture diagram draws two separate branches under the managers, AVAudioEngine leading to "Audio I/O" and CoreBluetooth leading to "BT Devices"; song recognition and both outputs hang under the AVAudioEngine branch only, and no line joins the two branches (PROJECT_SUMMARY.md:101-117). At user level the README says "Audio from the device will now stream through AudioRouter" (README.md:143). No text names the API, device, node, socket or file descriptor at which Bluetooth audio enters the app (SCOPE-A, terms `a2dp`, `IOBluetooth`, `underrun|overrun|buffer|tap|dropout|glitch`, `sample rate|44\.1|44100|48000|48k|bit depth|16-bit|PCM|stereo|channels`).
- Presence: CURRENT, documentation only (PROJECT_SUMMARY.md:101-117, 165-166). `AudioEngineManager.swift` and `BluetoothManager.swift` are ABSENT (ZIP!pbxproj:32-33).
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: an AVAudioEngine tap as the stated capture mechanism (PROJECT_SUMMARY.md:165-166); the input source chosen in the app (README.md:180).
- Open questions: Where did Bluetooth audio enter the engine: as a macOS audio input device, as data handed over by the Bluetooth component, or not at all? Is the "Bluetooth Device" input the same engine input as the microphone? Did any audio from a Bluetooth source reach the tap?
- Confidence: LOW. INFERRED from a diagram and feature lists: the diagram shows no join between the two branches and no text describes one, so the hand-over point is read as undocumented.

### R3-S05-02  NOT FOUND
- Question: S05.2
- Searched: SCOPE-A with `sample rate|44\.1|44100|48000|48k|bit depth|16-bit|PCM|stereo|channels` (hits CHROMECAST_SETUP_GUIDE.md:311, 314, 338 only), `\bHz\b|kHz|bit ?rate|bitrate|format|mono` (no audio-format figure; CHROMECAST_SETUP_GUIDE.md:388 says "Check audio format compatibility" for Chromecast).
- Nearest related material: CHROMECAST_SETUP_GUIDE.md:311, a sample output function whose parameter type is `AVAudioPCMBuffer`, which shows PCM buffers on the Chromecast side of a sample and gives no rate, depth or channel count. PROJECT_SUMMARY.md:166 ("Tap-based buffer processing").

### R3-S05-03  NOT FOUND
- Question: S05.3
- Searched: SCOPE-A with `silence|\bgap\b|stream (start|stop)|starts? playing|stops?`, `reconnect|disconnect|out of range|reboot|connection (monitoring|management)`, `gap|silence`.
- Nearest related material: gap detection for song recognition, "Auto-recognize after silence (2+ seconds)" (PROJECT_SUMMARY.md:188) and "Automatically recognizes songs when silence is detected between tracks" (README.md:166; also README.md:182). It detects silence inside audio already captured and triggers recognition; no text ties it to the start or stop of a Bluetooth stream. Also "Real-time audio level monitoring" (CHANGELOG.md:42) and "Connection monitoring" (PROJECT_SUMMARY.md:203).

### R3-S05-04  NOT FOUND
- Question: S05.4
- Searched: SCOPE-A with `underrun|overrun|buffer|tap|dropout|glitch`, `latency|delay|sleep|retry`, `hot-?swap|route change|unavailable|lost|drop`.
- Nearest related material: "Tap-based buffer processing" (PROJECT_SUMMARY.md:166) and "Efficient buffer processing" (PROJECT_SUMMARY.md:304), neither with a buffer size or a rule for underruns, overruns or gaps. "Latency: <50ms (audio routing)" under the heading "Benchmarks (Estimated)" (PROJECT_SUMMARY.md:294-298). The planned item "[ ] Optimize audio processing for lower latency" (CHANGELOG.md:132).

---

## S06 Remote control and metadata

### R3-S06-01  NOT FOUND
- Question: S06.1
- Searched: SCOPE-A with `AVRCP|play/pause|pause|skip|transport|next track|previous` (zero hits), `\bplay\b|playback|resume|\bnext\b|remote control|media key` (hits are the Xcode Play button at SETUP.md:32, "Next" buttons, test steps at CHROMECAST_SETUP.md:131 and CHROMECAST_SETUP_GUIDE.md:366, and the planned "Audio file playback" at CHANGELOG.md:125).
- Nearest related material: README.md:197-200, the AirPlay component's "Now Playing metadata updates" and "Route change monitoring", which face the output and not the Bluetooth source. For control between this device and the source: NONE.

### R3-S06-02  Volume described as an in-app output slider with a separate mute toggle
- Question: S06.2
- What was attempted: "Volume Slider: Adjusts output volume (0-100%)" (README.md:172); "Volume control (0-100%)" (CHANGELOG.md:41). Status rows give the method as "Volume Control | ✅ Complete | Real-time processing" and "Audio Mute Toggle | ✅ Complete | Buffer manipulation" (PROJECT_SUMMARY.md:86-87). "Volume normalization" is listed under input processing (PROJECT_SUMMARY.md:169) and "Volume preferences" under settings (PROJECT_SUMMARY.md:288). The slider "doesn't affect mute behavior" (SETUP.md:111), and the mute toggle "Mutes audio output while maintaining video stream to Chromecast" (README.md:171). By these lines volume is applied inside the app on the output side. No text links the slider to the volume of the Bluetooth source or describes volume messages in either direction (SCOPE-A, term `AVRCP`: zero hits; term `volume|mute`: every hit is cited here or is the Cast sample line at CHROMECAST_SETUP_GUIDE.md:265).
- Presence: CURRENT, documentation only (README.md:171-172; PROJECT_SUMMARY.md:86-87). `AudioEngineManager.swift` is ABSENT (ZIP!pbxproj:32).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The testing checklist has no volume or mute item (PROJECT_SUMMARY.md:343-354); the "✅ Complete" rows are completion claims (F3).
- Why it turned out that way: UNKNOWN.
- Depends on: in-app buffer processing as the stated method (PROJECT_SUMMARY.md:86-87).
- Open questions: Did a volume change on the source device alter what the app received or played? Did the slider act before or after the equaliser data and song recognition were fed? Was the source's volume ever read by the app?
- Confidence: MEDIUM. STATED consistently in four documents; there is no code and no stated result.

### R3-S06-03  Track metadata obtained by ShazamKit recognition of the audio, then stored and shown in the app and on outputs
- Question: S06.3
- What was attempted: Track information comes from recognition of the audio: "Automatic song recognition" (README.md:19), with the modes "Gap Detection: Auto-recognize after silence (2+ seconds)" and "Time-Based: Check every 1-5 minutes" (PROJECT_SUMMARY.md:188-189). Stored: "Match history (50 tracks)" and "Artwork download" (PROJECT_SUMMARY.md:192-193; CHANGELOG.md:29-30). Shown: a "Now playing display" in the main window (README.md:214), "Now Playing metadata updates" on the AirPlay side through MediaPlayer (README.md:199; PROJECT_SUMMARY.md:148), and "Metadata and visualizer data streaming" to the Chromecast receiver (README.md:206). The README sums it up as "Song metadata from Shazam will appear on compatible devices" (README.md:150). No text describes track metadata or playback status read from the Bluetooth source (SCOPE-A, terms `AVRCP` and `playback status`: zero hits; the `metadata|now playing|track` hits all concern recognition, AirPlay, Chromecast or App Store submission text at BUILD_AND_DEPLOY.md:160, 311).
- Presence: CURRENT, documentation only (README.md:150, 191-200; PROJECT_SUMMARY.md:185-195). `ShazamManager.swift`, `AirPlayManager.swift` and `ChromecastManager.swift` are ABSENT (ZIP!pbxproj:34-36).
- Outcome: PARTIAL
- Outcome evidence: STATED. "[x] Shazam recognizes songs" (PROJECT_SUMMARY.md:349). Conditions and defects: (a) the input source in use when the item was ticked is not named; (b) the Chromecast side is stated as "Current Status: **Mock Implementation** (simulated connections only)" (CHROMECAST_SETUP_GUIDE.md:7), and its checklist item "[ ] Metadata displays correctly on TV" is unticked (CHROMECAST_SETUP.md:256); (c) no statement gives a result for metadata on an AirPlay device.
- Why it turned out that way: For (b), STATED: "Full Chromecast implementation requires Google Cast SDK" (CHANGELOG.md:101). For the rest, UNKNOWN.
- Depends on: ShazamKit (PROJECT_SUMMARY.md:147); MediaPlayer (PROJECT_SUMMARY.md:148); the recognition settings (README.md:166-167); audio being present, "Ensure there's actual audio playing" (README.md:265).
- Open questions: Was recognition ever run on audio from a Bluetooth source? Was the source's own track information or play state read at all? Did recognised metadata reach a real AirPlay device?
- Confidence: MEDIUM. STATED evidence alone; the three source files are absent.

---

## Tally

| Stage | Questions | Answered with findings | Answered NOT FOUND |
|-------|-----------|------------------------|--------------------|
| S01 | 5 | 5 (S01.1, S01.2, S01.3, S01.4, S01.5) | 0 |
| S02 | 3 | 2 (S02.1, S02.3) | 1 (S02.2) |
| S03 | 5 | 2 (S03.1, S03.2) | 3 (S03.3, S03.4, S03.5) |
| S04 | 4 | 2 (S04.1, S04.3) | 2 (S04.2, S04.4) |
| S05 | 4 | 1 (S05.1) | 3 (S05.2, S05.3, S05.4) |
| S06 | 3 | 2 (S06.2, S06.3) | 1 (S06.1) |
| Total | 24 | 14 | 10 |

Record count: 17 finding records (S01.5, S02.1 and S04.1 have two each) and 10 NOT FOUND records, 27 records in all.

- Questions answered with findings: 14
- Questions answered NOT FOUND: 10
- Total: 24 (equals the 24 questions in the brief)
