# R3 stage audit: Audio stages S07 to S12

Repo ID R3, /home/user/AudioRouter. Audited at commit 1bdb71dfb0e7319db0aa2f6cf41aedc853369204 (`git log`: one commit, subject "Add files via upload", 2025-11-13). Working tree clean (`git status --porcelain` empty). Branches `main` and the session branch point at the same commit; no tags, no stashes (`git branch -a`, `git tag`, `git stash list`).

## Conventions used in this file

- `path:line` is a tracked file at the commit above.
- `ZIP!pbxproj:line` is the member `AudioRouter.xcodeproj/project.pbxproj` of the tracked file `AudioRouter.xcodeproj`, which is a Zip archive stored as one regular file. `ZIP!xcscheme:line` is the member `AudioRouter.xcodeproj/xcshareddata/xcschemes/AudioRouter.xcscheme`. Line numbers come from `unzip -p AudioRouter.xcodeproj <member> | cat -n` (standard output only, nothing extracted).
- `ZIP!xcuserstate` is the member `AudioRouter.xcodeproj/project.xcworkspace/xcuserdata/mark.xcuserdatad/UserInterfaceState.xcuserstate`, a binary property list. It was read with `unzip -p ... | strings`; it has no line numbers.
- SOURCE ABSENT means: the file is named in documentation or in the archived project file, but it is not in the repo. `git ls-files` lists 13 paths, all at the repo root: eight `.md` files, three `.sh` files, `Package.swift` and the archive `AudioRouter.xcodeproj`. No path under `AudioRouter/` or `ChromecastReceiver/` is tracked, the one commit holds the same 13 paths (`git show --stat HEAD`), and the archive holds project metadata only (`unzip -l`).
- Because all application source is absent, every record below rests on documentation text, the archived project metadata, the three shell scripts and `Package.swift`. Presence is given as CURRENT when the text is in the default branch, with a note where the code it describes is SOURCE ABSENT. Listings that exist only inside a guide, and are not part of any build target, are given as DISABLED.
- Searches named in the records were run with `grep -n -i -E` over all 13 tracked files and, where stated, over the archive members.

---

## S07 Processing

### S07.1 Every transformation between capture and output

### R3-S07-01  Audio engine processing chain described for AudioEngineManager
- Question: S07.1
- What was attempted: The documents describe one manager that "Manages AVAudioEngine for audio routing" and "Performs real-time audio processing" (README.md:178-183). The processing list reads "Real-time audio capture via AVAudioEngine", "Tap-based buffer processing", "32-band frequency analysis", "Gap detection algorithm", "Volume normalization" (PROJECT_SUMMARY.md:164-169). A diagram places AVAudioEngine between the managers and the outputs (PROJECT_SUMMARY.md:101-117). A helper file is described as "Audio processing helpers" (PROJECT_SUMMARY.md:35, 135). No document names a decode, resample or mix step for audio arriving from a Bluetooth source: `decode`, `resampl`, `mix`, `SBC`, `convert`, `sample rate` return hits only in the Chromecast listing at CHROMECAST_SETUP_GUIDE.md:311-341.
- Presence: CURRENT as documentation text (README.md:178-183; PROJECT_SUMMARY.md:164-169). SOURCE ABSENT for AudioEngineManager.swift and AudioUtilities.swift, which are named at ZIP!pbxproj:32, 39.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No test output, log or statement of result covers the processing chain as a whole.
- Depends on: AVAudioEngine on macOS 14.0 or later (README.md:42; PROJECT_SUMMARY.md:145; ZIP!pbxproj:286). "The macOS implementation uses AVAudioEngine directly." (SETUP.md:79).
- Open questions: How does audio from a Bluetooth source reach AVAudioEngine? Which listed steps alter the audio sent to an output and which only analyse it? What does "Volume normalization" (PROJECT_SUMMARY.md:169) do to the signal? Is any sample-rate or format conversion performed?
- Confidence: MEDIUM. STATED description only; the source it describes is absent.

### R3-S07-02  Volume control and mute toggle applied to the audio
- Question: S07.1
- What was attempted: A volume slider that "Adjusts output volume (0-100%)" and a mute toggle that "Mutes audio output while maintaining video stream to Chromecast" (README.md:171-172; also README.md:29-32; SETUP.md:110-111; CHANGELOG.md:39-42). The feature table gives the mute implementation as "Buffer manipulation" and the volume implementation as "Real-time processing", each with status "Complete" (PROJECT_SUMMARY.md:86-87). The slider "doesn't affect mute behavior" (SETUP.md:111). "Volume preferences" is listed as a setting (PROJECT_SUMMARY.md:288).
- Presence: CURRENT as documentation text (citations above). SOURCE ABSENT for the manager and view files that would hold it (ZIP!pbxproj:30, 32).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The ticked "Testing Checklist" has no item for volume or mute (PROJECT_SUMMARY.md:345-354).
- Why it turned out that way: UNKNOWN.
- Depends on: the AVAudioEngine tap design named in PROJECT_SUMMARY.md:165-166.
- Open questions: At which point in the chain is the gain applied? Does the mute act on the buffers sent to AirPlay, to Chromecast, or to both? Is the volume linked to the Bluetooth source's volume?
- Confidence: MEDIUM. STATED description only.

### R3-S07-03  32-band frequency analysis feeding a visual equalizer
- Question: S07.1
- What was attempted: "32-Band Equalizer", implementation "Real-time FFT analysis", status "Complete" (PROJECT_SUMMARY.md:83). The audio manager "Generates equalizer data" (README.md:183). The result is described as a display: "32-band real-time frequency visualization" (README.md:24-27), refreshed every 50 ms (PROJECT_SUMMARY.md:214). The same levels are sent to the Chromecast receiver page as `VISUALIZER_DATA` messages in the guide listing (CHROMECAST_SETUP_GUIDE.md:198-205). Equalisation applied to the audio itself appears only as an unticked planned item, "Custom EQ controls" (README.md:330; CHANGELOG.md:117; PROJECT_SUMMARY.md:325).
- Presence: CURRENT as documentation text. SOURCE ABSENT for EqualizerView.swift and AudioEngineManager.swift (ZIP!pbxproj:32, 37).
- Outcome: WORKED
- Outcome evidence: STATED. "[x] Equalizer displays correctly" (PROJECT_SUMMARY.md:348).
- Why it turned out that way: UNKNOWN. No cause or test detail is given.
- Depends on: an AVAudioEngine tap supplying buffers (PROJECT_SUMMARY.md:166); macOS 14 named in the same checklist (PROJECT_SUMMARY.md:345).
- Open questions: With which input source was the display checked? Was it ever checked with audio from a Bluetooth source? Who ticked the checklist, and when?
- Confidence: MEDIUM. STATED evidence alone; the code is absent.

### R3-S07-04  AAC encode step for Chromecast, present as a guide listing
- Question: S07.1
- What was attempted: A listing headed "Implement Audio Streaming" replaces `sendAudioData(_ buffer: AVAudioPCMBuffer)` so that it calls `encodeToAAC(buffer)` with the comment "Convert PCM buffer to AAC" (CHROMECAST_SETUP_GUIDE.md:306-316). The body of `encodeToAAC` is two comment lines and `return Data()` (CHROMECAST_SETUP_GUIDE.md:338-342). The value `aacData` assigned at :315 is not referred to again in the listing (:316-336).
- Presence: DISABLED. The text is a listing inside a guide whose header reads "Current Status: **Mock Implementation** (simulated connections only)" (CHROMECAST_SETUP_GUIDE.md:7). It is not part of any build target, and ChromecastManager.swift is SOURCE ABSENT (ZIP!pbxproj:36).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. The listing's own comment reads "This is complex - see Apple's documentation" (CHROMECAST_SETUP_GUIDE.md:340).
- Depends on: Google Cast SDK "4.8.0+" (CHROMECAST_SETUP_GUIDE.md:57), which is not a declared dependency (Package.swift:17-21, with the SDK line commented out at :20; ZIP!pbxproj:54-55 empty Frameworks phase); AVAudioConverter named at CHROMECAST_SETUP_GUIDE.md:339.
- Open questions: Was this listing ever placed in ChromecastManager.swift? Was any AAC encoder written?
- Confidence: MEDIUM. INFERRED from a clear listing that the encode step is a stub; no result evidence exists.

### S07.2 Latency

### R3-S07-05  Latency figure given as an estimate
- Question: S07.2
- What was attempted: A performance section headed "Benchmarks (Estimated)" lists "**Latency**: <50ms (audio routing)" (PROJECT_SUMMARY.md:294, 298) and "**Equalizer Refresh**: 50ms (20 FPS)" (PROJECT_SUMMARY.md:299). The changelog holds an unticked item "Optimize audio processing for lower latency" (CHANGELOG.md:132). No document says where latency arises, and no compensation is described (searched `latenc`, `delay`, `compensat`, `buffer`: the two lines above are the only latency hits; `delay` and `compensat` return nothing).
- Presence: CURRENT as documentation text (PROJECT_SUMMARY.md:294-300; CHANGELOG.md:132).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The heading labels the figure as estimated; no measurement, method or log is in the repo.
- Why it turned out that way: UNKNOWN.
- Depends on: nothing stated. The figure is not tied to an input source, an output or a machine.
- Open questions: Was latency ever measured? Does the figure refer to the path from a Bluetooth source to AirPlay, or to another path? What is the latency of the AirPlay leg?
- Confidence: MEDIUM. STATED figure, labelled by its author as an estimate.

### S07.3 Clock drift or synchronisation

### R3-S07-06  NOT FOUND
- Question: S07.3
- Searched: `drift`, `resampl`, `synchron`, `sample rate`, `clock`, `48 ?k`, `44\.1`, `bit depth`, `jitter`, `underrun` over all 13 tracked files; `strings` output of ZIP!xcuserstate for `sync`; archive listing for any source file.
- Nearest related material: "Multi-room synchronization" as an unticked planned item (README.md:328; CHANGELOG.md:115; PROJECT_SUMMARY.md:330). It concerns several outputs, not source-to-output clocks.

---

## S08 Output

### S08.1 Output destinations and how one is selected

### R3-S08-01  AirPlay as an output destination, selected through the system menu
- Question: S08.1
- What was attempted: The documents name two output destinations, AirPlay and Chromecast (README.md:13-16; PROJECT_SUMMARY.md:114-116). For AirPlay the user is told to click the "AirPlay" button in "Output Destinations" and "Select your AirPlay device from the picker" (README.md:145-150). Another document states "AirPlay Picker: Uses system menu (no custom picker UI on macOS)" (PROJECT_SUMMARY.md:317; also CHANGELOG.md:103). The feature table reads "AirPlay Output", status "Complete", implementation "AVFoundation + MediaPlayer" (PROJECT_SUMMARY.md:79). File output and recording appear only as unticked planned items (README.md:329; CHANGELOG.md:116, 140). No document describes playback on the Mac's own sound device as a selectable output.
- Presence: CURRENT as documentation text. SOURCE ABSENT for AirPlayManager.swift and DevicePickerViews.swift (ZIP!pbxproj:35, 42).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The ticked checklist has no AirPlay item (PROJECT_SUMMARY.md:345-354); "Test AirPlay functionality" is unticked (BUILD_AND_DEPLOY.md:478).
- Why it turned out that way: UNKNOWN.
- Depends on: an AirPlay device on the same network (README.md:252); local network permission (README.md:253); macOS system AirPlay routing (PROJECT_SUMMARY.md:174).
- Open questions: Did audio from any input ever reach an AirPlay device? Does the app send its own audio to AirPlay, or does it rely on the system output route? What does the "picker" at README.md:148 refer to, given PROJECT_SUMMARY.md:317?
- Confidence: MEDIUM. STATED description only; two documents differ on the selection control.

### R3-S08-02  Chromecast as an output destination with a custom receiver
- Question: S08.1
- What was attempted: The user clicks "Chromecast" in "Output Destinations" and selects a device; the receiver shows title, artist, album, artwork and an equalizer (README.md:152-160). Feature table: "Chromecast Output", status "Complete", implementation "Custom receiver + Cast SDK stubs" (PROJECT_SUMMARY.md:80). The author describes the state as "placeholder code" (README.md:110, 286), "Mock Implementation (simulated connections only)" (CHROMECAST_SETUP_GUIDE.md:7) and "The current implementation streams metadata and visualizer data only." (CHROMECAST_SETUP.md:202). The guide names an existing `ChromecastDiscovery` class to be deleted (CHROMECAST_SETUP_GUIDE.md:282-285). For audio the same document points to macOS system routing: "macOS can route audio to Chromecast natively" (CHROMECAST_SETUP.md:208-210).
- Presence: CURRENT as documentation text. SOURCE ABSENT for ChromecastManager.swift (ZIP!pbxproj:36) and for `ChromecastReceiver/index.html` (named at README.md:91, 315-316).
- Outcome: FAILED (as a destination for audio)
- Outcome evidence: STATED. "Chromecast Audio: Metadata/visualizer only (no audio streaming in base implementation)" (PROJECT_SUMMARY.md:315); "The current implementation streams metadata and visualizer data only." (CHROMECAST_SETUP.md:202); "simulated connections only" (CHROMECAST_SETUP_GUIDE.md:7); unticked "Implement actual audio streaming to Chromecast" (CHANGELOG.md:134).
- Why it turned out that way: STATED. "Full implementation requires Google Cast SDK" (README.md:285; CHANGELOG.md:101). The SDK is not a declared dependency: its line is commented out (Package.swift:20) and the project's Frameworks phase is empty (ZIP!pbxproj:54-55).
- Depends on: Google Cast SDK 4.8.0+ (CHROMECAST_SETUP_GUIDE.md:57), not integrated; a registered Cast application and a receiver page hosted over HTTPS (CHROMECAST_SETUP.md:12-84); the author's time estimate "Total: 5-8 hours" for the integration (CHROMECAST_SETUP_GUIDE.md:408-414).
- Open questions: Did the app ever open a session on a real Chromecast? PROJECT_SUMMARY.md:80 marks the feature "Complete" while CHROMECAST_SETUP_GUIDE.md:7 calls it a mock: which describes the last state of the code?
- Confidence: MEDIUM. STATED in four documents and consistent with the dependency manifests; the application source is absent.

### S08.2 Protocol and format for each output

### R3-S08-03  AirPlay output through AVFoundation routing with MediaPlayer metadata
- Question: S08.2
- What was attempted: AirPlayManager is described as "AVFoundation AirPlay routing", "Now Playing metadata updates", "Route change monitoring" (README.md:197-200). The output section lists "System-native AirPlay integration", "Now Playing metadata", "Album artwork display", "Multi-room support (system-level)" (PROJECT_SUMMARY.md:173-177). MediaPlayer is named "for AirPlay metadata" (CHANGELOG.md:75). No audio format, sample rate, codec or AirPlay protocol version is stated (searched `RAOP`, `AirPlay 2`, `ALAC`, `PCM`, `sample rate`, `contentType`).
- Presence: CURRENT as documentation text. SOURCE ABSENT for AirPlayManager.swift (ZIP!pbxproj:35). The same file name appears as an open editor document in ZIP!xcuserstate (string ending `/AudioRouter/Managers/AirPlayManager.swift`).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: AVFoundation and MediaPlayer system frameworks on macOS 14 (PROJECT_SUMMARY.md:145, 148); system-level AirPlay handling.
- Open questions: Which AVFoundation interface carries the audio to AirPlay? In what format does the audio leave the app?
- Confidence: MEDIUM. STATED description only.

### R3-S08-04  Chromecast custom-namespace messages carrying metadata and visualizer levels
- Question: S08.2
- What was attempted: ChromecastManager is described as "Custom channel messaging" and "Metadata and visualizer data streaming" (README.md:202-206; PROJECT_SUMMARY.md:179-183). A sample receiver page loads the Cast receiver framework v3 script (CHROMECAST_SETUP_GUIDE.md:120) and listens on a custom namespace for JSON messages of type `METADATA_UPDATE` (fields `title`, `artist`, `artworkURL`) and `VISUALIZER_DATA` (field `levels`, 32 bars) (CHROMECAST_SETUP_GUIDE.md:177-206). Two namespace strings appear: `urn:x-cast:com.audiorouter.app` (CHROMECAST_SETUP.md:193) and `urn:x-cast:com.audiorouter` (CHROMECAST_SETUP_GUIDE.md:187, 393).
- Presence: CURRENT as documentation text. SOURCE ABSENT for the sender (ChromecastManager.swift) and for the receiver file `ChromecastReceiver/index.html`; the only receiver markup in the repo is the `receiver.html` listing inside the guide (CHROMECAST_SETUP_GUIDE.md:116-212).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The Chromecast "Testing Checklist" is wholly unticked, including "Custom namespace matches in sender/receiver" and "Metadata displays correctly on TV" (CHROMECAST_SETUP.md:250-258).
- Why it turned out that way: UNKNOWN.
- Depends on: a registered custom receiver application and its ID (CHROMECAST_SETUP.md:57-105); HTTPS hosting of the receiver page (CHROMECAST_SETUP.md:14); the Cast SDK on the sender side (README.md:108-120).
- Open questions: Which of the two namespace strings did the absent sender code use? CHROMECAST_SETUP.md:202 says metadata is streamed while CHROMECAST_SETUP_GUIDE.md:7 says connections are simulated: were any messages ever delivered to a device?
- Confidence: MEDIUM. STATED description plus a clear listing; the two guides disagree with each other.

### R3-S08-05  Chromecast audio as a live AAC media load, present as a guide listing
- Question: S08.2
- What was attempted: The listing builds a `GCKMediaInformation` with `streamType: .live`, `contentType: "audio/aac"` and `contentID: "https://your-streaming-server.com/stream"`, then calls `remoteMediaClient?.loadMedia(mediaInfo)` (CHROMECAST_SETUP_GUIDE.md:317-335). The content URL is a placeholder host. A second document names other transports: "Implement WebRTC or HLS streaming" (CHROMECAST_SETUP.md:204-206). The guide's stated target is "Real Chromecast Streaming" (CHROMECAST_SETUP_GUIDE.md:8).
- Presence: DISABLED. A listing inside a guide, not part of any build target; no streaming server exists in the repo (`git ls-files`).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The guide's "Test Streaming" step is written as an expectation, "Should hear audio on Chromecast" (CHROMECAST_SETUP_GUIDE.md:365-368), with no result.
- Why it turned out that way: UNKNOWN.
- Depends on: Google Cast SDK 4.8.0+ (CHROMECAST_SETUP_GUIDE.md:57); an HTTP(S) server serving the live stream, which the listing assumes at the placeholder URL (:323); an AAC encoder (see R3-S07-04).
- Open questions: Was a streaming server ever planned in detail? The listing calls `loadMedia` once per audio buffer (:311, :335): was this pattern ever run?
- Confidence: MEDIUM. INFERRED from a clear listing; no result evidence exists.

### S08.3 Output unavailable, slow or changing during playback

### R3-S08-06  AirPlay route change monitoring
- Question: S08.3
- What was attempted: "Route change monitoring" is listed as a duty of AirPlayManager (README.md:200). Nothing states what the app does when a route changes or disappears. The changelog lists "Add input/output device hot-swapping" as an unticked item (CHANGELOG.md:141). User-facing text for an AirPlay failure is a three-step troubleshooting list ending "Restart your AirPlay device" (README.md:250-254). No text covers a slow output (searched `slow`, `timeout`, `retry`, `backpress`, `buffer`: the only `slow` hit is build times at BUILD_AND_DEPLOY.md:456).
- Presence: CURRENT as documentation text. SOURCE ABSENT for AirPlayManager.swift (ZIP!pbxproj:35).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: macOS system route notifications (implied by "System-native AirPlay integration", PROJECT_SUMMARY.md:174).
- Open questions: What happens to playback when the AirPlay device drops off the network? Does the unticked hot-swapping item at CHANGELOG.md:141 mean a route change during playback is not handled?
- Confidence: MEDIUM. STATED as a single list item.

### R3-S08-07  Chromecast listing guards for a missing device or a closed session
- Question: S08.3
- What was attempted: In the guide listing, `connectToDevice` logs "Device not found in Cast framework" and returns when the device is not in the discovery manager (CHROMECAST_SETUP_GUIDE.md:288-292), and `sendAudioData` begins with `guard isConnected else { return }`, which discards the buffer when no session is connected (CHROMECAST_SETUP_GUIDE.md:311-312). Session events are to be received by adding the manager as a session listener (CHROMECAST_SETUP_GUIDE.md:268-269); no listener method is shown. User-facing troubleshooting: "Can't connect to Chromecast" (CHROMECAST_SETUP.md:157-170), "Connection Fails" (CHROMECAST_SETUP_GUIDE.md:380-384).
- Presence: DISABLED. Listings inside a guide for code marked as a mock (CHROMECAST_SETUP_GUIDE.md:7); ChromecastManager.swift is SOURCE ABSENT.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: Google Cast SDK session and discovery managers (CHROMECAST_SETUP_GUIDE.md:262-273).
- Open questions: Is there any reconnect after a session ends? How does the absent mock code behave when a simulated device is deselected?
- Confidence: MEDIUM. INFERRED from a clear listing.

### S08.4 Simultaneous output to more than one destination

### R3-S08-08  Simultaneous multi-output claimed for AirPlay and Chromecast
- Question: S08.4
- What was attempted: "Simultaneous multi-output support" (README.md:16) and "Simultaneous multi-output capability" (CHANGELOG.md:23) are listed as features. The mute toggle "Mutes audio output while maintaining video stream to Chromecast" (README.md:171), which describes sound on one path and the receiver display on another. For AirPlay, "Multi-room support (system-level)" (PROJECT_SUMMARY.md:177). Several Chromecast targets at once is an unticked item (README.md:332; CHANGELOG.md:136), as is "Multi-room synchronization" (README.md:328).
- Presence: CURRENT as documentation text. SOURCE ABSENT for both output managers (ZIP!pbxproj:35-36).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No checklist item covers two outputs at once (PROJECT_SUMMARY.md:345-354; BUILD_AND_DEPLOY.md:473-486; CHROMECAST_SETUP.md:250-258).
- Why it turned out that way: UNKNOWN.
- Depends on: the Chromecast path carrying metadata and visualizer data only (PROJECT_SUMMARY.md:315); system-level AirPlay grouping (PROJECT_SUMMARY.md:177).
- Open questions: Does "simultaneous" mean audio to AirPlay together with a display on Chromecast? Was audio ever sent to two audio destinations at the same time? Is there any alignment between the two paths?
- Confidence: MEDIUM. STATED as a feature line only.

---

## S09 User interface and control surfaces

### S09.1 Every way a person or program controls or observes the product

### R3-S09-01  macOS SwiftUI main window
- Question: S09.1
- What was attempted: A native macOS window built with SwiftUI: "Main application interface", "Glass morphism design", "Input/output controls", "Now playing display" (README.md:210-214; also README.md:34-38; PROJECT_SUMMARY.md:84, 90-97). The archived project names a further view file, DevicePickerViews.swift (ZIP!pbxproj:42), which no document describes. Surfaces that appear only as unticked planned items: "Menu bar mode", "Keyboard shortcuts", "AppleScript support" (CHANGELOG.md:122-124; PROJECT_SUMMARY.md:326-327). No command-line interface, HTTP interface or configuration file for the running app is described (searched `CLI`, `command line`, `API`, `config`; command-line hits are all `xcodebuild` build commands).
- Presence: CURRENT as documentation text and project metadata. SOURCE ABSENT for ContentView.swift, EqualizerView.swift and DevicePickerViews.swift (ZIP!pbxproj:30, 37, 42). ContentView.swift appears as an open editor document in ZIP!xcuserstate.
- Outcome: WORKED
- Outcome evidence: STATED. "[x] UI is responsive", "[x] No crashes on startup", "[x] Dark mode supported" (PROJECT_SUMMARY.md:351-353).
- Why it turned out that way: UNKNOWN.
- Depends on: macOS 14.0+ and SwiftUI (PROJECT_SUMMARY.md:8-10); the app being built and launched from Xcode (README.md:77-79).
- Open questions: Which Mac and macOS version were used for the ticked items? Were the ticks made against the same code that the archived project file lists?
- Confidence: MEDIUM. STATED evidence alone; the view source is absent.

### R3-S09-02  Settings panel opened with Cmd+,
- Question: S09.1
- What was attempted: A settings window: "App configuration", "Shazam settings", "Audio device selection" (README.md:221-224), opened with `Cmd+,` (README.md:164; SETUP.md:103; PROJECT_SUMMARY.md:284). Implementation given as "SwiftUI Forms" (PROJECT_SUMMARY.md:85).
- Presence: CURRENT as documentation text. SOURCE ABSENT for SettingsView.swift (ZIP!pbxproj:38).
- Outcome: WORKED
- Outcome evidence: STATED. "[x] Settings persist" (PROJECT_SUMMARY.md:350).
- Why it turned out that way: UNKNOWN.
- Depends on: macOS 14.0+ and SwiftUI (PROJECT_SUMMARY.md:8-10).
- Open questions: Which settings were checked for persistence? Where are they stored?
- Confidence: MEDIUM. STATED evidence alone.

### R3-S09-03  In-app log viewer
- Question: S09.1
- What was attempted: The archived project lists `Views/LogViewer.swift` and `Utilities/LogManager.swift` in its Sources phase (ZIP!pbxproj:24-25, 43, 46, 110, 121, 208-209). One guide refers to it as a place to read results: "Check log viewer for \"Found device: [name]\"" (CHROMECAST_SETUP_GUIDE.md:358), "Review logs in LogViewer" (CHROMECAST_SETUP_GUIDE.md:421). Neither file appears in the structure listings of README.md:298-318 or PROJECT_SUMMARY.md:17-61, nor in the project heredoc of auto_create_project.sh (:109-121; `grep -c` for `LogViewer|LogManager` in that script returns 0).
- Presence: CURRENT as project metadata and guide text. SOURCE ABSENT for both files.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: the logger call form shown in the guide listings, `logger.log(.chromecast, .success, ...)` (CHROMECAST_SETUP_GUIDE.md:275).
- Open questions: How is the log viewer opened? Does it cover Bluetooth and audio events as well as Chromecast?
- Confidence: LOW. INFERRED from file names in project metadata and two guide sentences.

### R3-S09-04  Chromecast receiver web page shown on the TV
- Question: S09.1
- What was attempted: "Custom web-based receiver app" with gradient background, metadata display and a 32-band visualizer (CHANGELOG.md:50-55; README.md:85). The expected screen is described at CHROMECAST_SETUP.md:120-135. Two forms of the page are named: `ChromecastReceiver/index.html` (README.md:91; CHROMECAST_SETUP.md:10) and a `receiver.html` listing (CHROMECAST_SETUP_GUIDE.md:113-212).
- Presence: CURRENT as documentation text. SOURCE ABSENT for `ChromecastReceiver/index.html`. The `receiver.html` markup exists only as a listing inside the guide (DISABLED in the sense used in this file).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. All nine items of the Chromecast "Testing Checklist" are unticked (CHROMECAST_SETUP.md:250-258).
- Why it turned out that way: UNKNOWN.
- Depends on: HTTPS hosting, a registered Cast application ID, a whitelisted test device (CHROMECAST_SETUP.md:14, 57-84).
- Open questions: Was either page ever hosted and loaded on a Chromecast? How does the `index.html` named in the README differ from the `receiver.html` listing?
- Confidence: MEDIUM. STATED description; the guide listing is clear but no deployed page is evidenced.

### R3-S09-05  Now Playing metadata shown on AirPlay devices
- Question: S09.1
- What was attempted: "Song metadata from Shazam will appear on compatible devices" (README.md:150); "AirPlay streaming with metadata display" (README.md:14); "Metadata Display", status "Complete", implementation "Now Playing Info" (PROJECT_SUMMARY.md:88); "Now Playing metadata updates" in AirPlayManager (README.md:199).
- Presence: CURRENT as documentation text. SOURCE ABSENT for AirPlayManager.swift and ShazamManager.swift (ZIP!pbxproj:34-35).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The ticked item "[x] Shazam recognizes songs" (PROJECT_SUMMARY.md:349) covers recognition, not display on an output device.
- Why it turned out that way: UNKNOWN.
- Depends on: MediaPlayer framework (PROJECT_SUMMARY.md:148); ShazamKit recognition supplying the metadata (README.md:191-195).
- Open questions: Was metadata ever seen on an AirPlay receiver?
- Confidence: MEDIUM. STATED description only.

### S09.2 Actions and status items each surface offers

### R3-S09-06  Main window: actions and status items
- Question: S09.2
- What was attempted: Actions: choose one of four input sources, "Microphone", "Line In / Aux", "System Audio", "Bluetooth Device" (README.md:124-131; SETUP.md:50-54); toggle the "AirPlay" and "Chromecast" outputs and pick a device (README.md:145-156); "Mute Audio (Keep Video)" toggle and volume slider 0-100% (README.md:169-172). Status items: equalizer bars showing "real-time audio levels" (SETUP.md:61), a "Now playing display" (README.md:214) with recognised title, artist, album and artwork (CHANGELOG.md:30, 36), "Real-time audio level monitoring" (CHANGELOG.md:42). No status item for Bluetooth connection state is described in any view; "Connection monitoring" is listed only as a Bluetooth capability (PROJECT_SUMMARY.md:203). No transport controls (play, pause, skip) are described (searched `pause`, `skip`, `transport`: no hits).
- Presence: CURRENT as documentation text. SOURCE ABSENT for ContentView.swift (ZIP!pbxproj:30).
- Outcome: UNVERIFIED
- Outcome evidence: NONE for the set of actions as a whole. Single items carry ticks: "[x] Microphone input works", "[x] Bluetooth pairing works", "[x] Equalizer displays correctly" (PROJECT_SUMMARY.md:346-348).
- Why it turned out that way: UNKNOWN.
- Depends on: the managers named at README.md:178-206, all SOURCE ABSENT.
- Open questions: Does the window show which Bluetooth source is connected? Can the user disconnect or remove a source from the window? Do the output buttons show a connected or failed state?
- Confidence: MEDIUM. STATED description only.

### R3-S09-07  Settings panel: items offered
- Question: S09.2
- What was attempted: "Use Gap Detection" toggle, described as recognising songs "when silence is detected between tracks"; "Check Interval" of 1 to 5 minutes used when gap detection is off (README.md:162-167). Further items: "Input device selection", "Volume preferences" (PROJECT_SUMMARY.md:284-288); "Audio device selection", "Persistent preferences" (CHANGELOG.md:57-61). The README places the first two "In the General tab" (README.md:165); no other tab is named.
- Presence: CURRENT as documentation text. SOURCE ABSENT for SettingsView.swift (ZIP!pbxproj:38).
- Outcome: WORKED
- Outcome evidence: STATED. "[x] Settings persist" (PROJECT_SUMMARY.md:350).
- Why it turned out that way: UNKNOWN.
- Depends on: ShazamKit recognition modes (PROJECT_SUMMARY.md:187-189).
- Open questions: Is there any setting for the Bluetooth name, pairing or output device? What are the default values?
- Confidence: MEDIUM. STATED evidence alone.

### R3-S09-08  Chromecast receiver page: status items shown
- Question: S09.2
- What was attempted: The page is display-only. Described items: purple gradient background, "AudioRouter" logo, "Waiting for audio..." message, animated equalizer bars, "Listening..." status, then song title, artist, album artwork and live equalizer once audio plays (CHROMECAST_SETUP.md:120-135). The guide listing shows artwork, title, artist and 32 bars whose height follows `level * 200 + 20` pixels (CHROMECAST_SETUP_GUIDE.md:166-205). No control on the page is described. The Cast SDK guide sets `physicalVolumeButtonsWillControlDeviceVolume = true` on the sender (CHROMECAST_SETUP_GUIDE.md:265).
- Presence: CURRENT as documentation text; listing DISABLED (inside a guide). SOURCE ABSENT for `ChromecastReceiver/index.html`.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. "Metadata displays correctly on TV" and "Equalizer animates in real-time" are unticked (CHROMECAST_SETUP.md:256-257).
- Why it turned out that way: UNKNOWN.
- Depends on: the custom namespace messages in R3-S08-04.
- Open questions: Was the described "Waiting for audio..." screen taken from a deployed page or written from the design?
- Confidence: MEDIUM. STATED description plus a clear listing.

### S09.3 First-time setup as implemented, step by step

### R3-S09-09  First-time setup of the app, from project to first audio
- Question: S09.3
- What was attempted: Documented steps, in order. (1) `cd ~/claude/AudioRouter` and `open AudioRouter.xcodeproj` (SETUP.md:15-17; README.md:52-59). (2) In Xcode, select the target, "Signing & Capabilities", tick "Automatically manage signing", choose a team (SETUP.md:20-27). (3) Select the Mac as destination and press `Cmd+R` (SETUP.md:29-33). (4) On first launch allow three prompts: microphone, Bluetooth, local network (SETUP.md:35-44). (5) Click an input source; for Bluetooth click "Bluetooth Device", after which "Your Mac will show as \"AudioRouter\" to nearby Bluetooth devices" (SETUP.md:65-68). (6) On the source device press its pairing button and choose "AudioRouter" (SETUP.md:69-72). (7) Click "AirPlay" and choose a speaker, or "Chromecast" after receiver setup (SETUP.md:56-58). (8) "Start playing audio - it will stream through AudioRouter!" (SETUP.md:73).
- Presence: CURRENT as documentation text (SETUP.md:11-73; README.md:48-79, 122-160).
- Outcome: PARTIAL
- Outcome evidence: STATED. Worked: "[x] Build successfully on macOS 14", "[x] Bluetooth pairing works", "[x] Permissions requested properly" (PROJECT_SUMMARY.md:345, 347, 354). Defects stated by the author: "The project.pbxproj file was hand-crafted and Xcode is being strict about it." (FIX_PROJECT.md:3); "Some Bluetooth devices may require additional pairing steps" (CHANGELOG.md:102). No statement covers step 8, audio from a Bluetooth source being heard at an output.
- Why it turned out that way: STATED for the project-file defect: the file "was hand-crafted" (FIX_PROJECT.md:3). UNKNOWN for the pairing caveat.
- Depends on: macOS 14.0+, Xcode 15.0+, an Apple Developer account (SETUP.md:7-9); the application sources, which are not in the repo; a project path under the author's home directory (SETUP.md:16). INFERRED: in a clone of this repo step 1 does not reach a usable project, because the tracked `AudioRouter.xcodeproj` is a single Zip file (`git ls-files -s` mode 100644; `unzip -l`) and no file under `AudioRouter/` exists.
- Open questions: Did the full sequence ever end with Bluetooth audio audible on an AirPlay device? Was the project-file problem present before or after the ticks at PROJECT_SUMMARY.md:345-354 were made?
- Confidence: MEDIUM. STATED evidence alone, from two documents that point in different directions.

### R3-S09-10  First-time setup of the Chromecast receiver (CHROMECAST_SETUP.md)
- Question: S09.3
- What was attempted: Documented steps. (1) Host `ChromecastReceiver/index.html` on an HTTPS host: GitHub Pages, Firebase Hosting, Netlify or the user's own server (CHROMECAST_SETUP.md:12-55). (2) Register a "Custom Receiver" application in the Google Cast developer console with the receiver URL (CHROMECAST_SETUP.md:57-73). (3) Add the Chromecast by serial number as a test device and "Wait ~15 minutes" (CHROMECAST_SETUP.md:75-80). (4) Copy the application ID into `ChromecastManager.swift` at line 99 and rebuild (CHROMECAST_SETUP.md:82-105). (5) Run the app, click "Chromecast", select the device, check the TV screen (CHROMECAST_SETUP.md:107-135). (6) Optionally submit the application for publication (CHROMECAST_SETUP.md:137-153). A shorter form of the same steps is at README.md:81-106.
- Presence: CURRENT as documentation text. SOURCE ABSENT for `ChromecastReceiver/index.html` and ChromecastManager.swift, the two files the steps act on.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The checklist at CHROMECAST_SETUP.md:250-258 is unticked.
- Why it turned out that way: UNKNOWN.
- Depends on: a Google account, a Chromecast, HTTPS hosting (CHROMECAST_SETUP.md:7-9); the sender code, described elsewhere as a mock (CHROMECAST_SETUP_GUIDE.md:7).
- Open questions: Was a Cast application ever registered for this project? The second guide gives a different procedure (R3-S10-06): which of the two was written later?
- Confidence: MEDIUM. STATED procedure only.

### S09.4 How errors and state changes are communicated to the user

### R3-S09-11  Categorised log messages shown in an in-app log viewer
- Question: S09.4
- What was attempted: Guide listings call a logger with a subsystem, a level and a message: `logger.log(.chromecast, .success, "Real Chromecast SDK initialized")`, `logger.log(.chromecast, .error, "Device not found in Cast framework")`, `logger.log(.chromecast, .info, "Initiating Cast session with \(device.name)")` (CHROMECAST_SETUP_GUIDE.md:275, 290, 297). The user is told to read results there: "Should see \"Successfully connected\" in log" (CHROMECAST_SETUP_GUIDE.md:362). The project lists LogManager.swift and LogViewer.swift (ZIP!pbxproj:43, 46). No alert, dialog or notification for errors is described in any document (searched `alert`, `notif`, `dialog`, `banner`).
- Presence: CURRENT as project metadata and guide text; the logger calls themselves are in guide listings (DISABLED). SOURCE ABSENT for LogManager.swift and LogViewer.swift.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: the three levels and one subsystem visible in the listings; other subsystems are not evidenced.
- Open questions: Are Bluetooth connect and disconnect events logged? Is the log shown to the user by default or only on request?
- Confidence: LOW. INFERRED from three listing lines and two file names.

### R3-S09-12  Errors read from the Xcode console and Console.app
- Question: S09.4
- What was attempted: For failures the documents send the user to developer tools: "Check Xcode console for error messages" (README.md:346; CHROMECAST_SETUP.md:280), "Check Console.app for error messages" for a crash on launch (SETUP.md:87-91), Chrome DevTools and the receiver's JavaScript console for the Cast page (CHROMECAST_SETUP.md:196-198, 279; CHROMECAST_SETUP_GUIDE.md:390, 394). State is otherwise conveyed by the status items in R3-S09-06 and R3-S09-08 and by the macOS permission prompts (PROJECT_SUMMARY.md:232-236).
- Presence: CURRENT as documentation text.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: the app being run from Xcode, the documented way to start it (README.md:77-79).
- Open questions: What does a user who is not running from Xcode see when Bluetooth, AirPlay or Chromecast fails?
- Confidence: MEDIUM. STATED troubleshooting text only.

---

@@PART2@@
