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

## S10 Lifecycle and operations

### S10.1 Installation and setup steps, automated or manual

### R3-S10-01  Build from source in Xcode as the only install path
- Question: S10.1
- What was attempted: Installation is a manual developer build: open the project, set code signing, press `Cmd+R` (README.md:48-79; SETUP.md:11-33; BUILD_AND_DEPLOY.md:16-35), or run `xcodebuild -project AudioRouter.xcodeproj -scheme AudioRouter -configuration Debug -derivedDataPath ./build` (BUILD_AND_DEPLOY.md:39-53). No installer, package or dependency-install script exists (`git ls-files`). Every step is manual. The project in the repo is a Zip archive stored as one file named `AudioRouter.xcodeproj` (`unzip -l`: 31 entries), added in the single commit 1bdb71d, subject "Add files via upload".
- Presence: CURRENT (documentation text; archive tracked at the repo root). SOURCE ABSENT for all 15 Swift files in the Sources build phase (ZIP!pbxproj:191-212).
- Outcome: PARTIAL
- Outcome evidence: STATED and OBSERVED. STATED worked: "[x] Build successfully on macOS 14" (PROJECT_SUMMARY.md:345). STATED defect: "The project.pbxproj file was hand-crafted and Xcode is being strict about it." (FIX_PROJECT.md:3). OBSERVED: ZIP!xcuserstate holds the key `LastCompletedPersistentSchemeBasedActivityReport` followed by the strings `Build` and `Succeeded`, and a run-destination entry containing `macosx26.1`, `arm64` and model code `Mac15,13` (member dated 2025-11-11 07:46 in `unzip -l`).
- Why it turned out that way: STATED for the defect: the project file "was hand-crafted" (FIX_PROJECT.md:3). The object identifiers in the archived project follow a hand-numbered pattern, for example `A10000000001` (ZIP!pbxproj:10), the same pattern as the heredoc in auto_create_project.sh:109.
- Depends on: macOS 14.0+, Xcode 15.0+, an Apple Developer account (README.md:42-44); an Apple developer team setting stored in the project (ZIP!pbxproj:256, 341, value not copied); the sources present on the author's machine and absent here; the project path used in the documents (`~/claude/AudioRouter`, SETUP.md:16).
- Open questions: In what order did the events occur: the "Xcode is being strict" problem, the ticked build item, and the "Build Succeeded" activity report? Does the `Succeeded` string in the binary state file belong to the `Build` report next to it? Why was the project committed as a Zip file and the sources left out?
- Confidence: MEDIUM. OBSERVED artefact read through `strings` without decoding the property list, plus STATED evidence; the sources are absent.

### R3-S10-02  fix_project_quick.sh, described as a quick fix of the project file
- Question: S10.1
- What was attempted: FIX_PROJECT.md presents it as "Option 1: Quick Fix (Try This First)" with the sentence "Let me try to fix the existing project file." (FIX_PROJECT.md:5-12). The script changes to a hard-coded absolute directory (fix_project_quick.sh:5), deletes any earlier backup and renames an existing `AudioRouter.xcodeproj` directory to `AudioRouter.xcodeproj.backup` (:8-12), then prints manual Xcode steps (:15-35). It writes no project file.
- Presence: CURRENT (fix_project_quick.sh:1-35). Tracked with mode 100644, not executable (`git ls-files -s`).
- Outcome: FAILED (as an automated repair)
- Outcome evidence: STATED. The script's own output reads "The automated fix requires creating a new Xcode project." (fix_project_quick.sh:16) and "Please follow the guide in FIX_PROJECT.md" (:19).
- Why it turned out that way: UNKNOWN. No reason is given for leaving the repair manual.
- Depends on: the hard-coded directory at fix_project_quick.sh:5; `AudioRouter.xcodeproj` being a directory (`[ -d ... ]`, :8).
- Open questions: Was the script run? If it was, the project directory would have been renamed to `.backup`: how was the working project restored?
- Confidence: MEDIUM. STATED in the script text; the code agrees (it only renames and prints).

### R3-S10-03  create_xcode_project.sh, a project-creation script that ends in manual steps
- Question: S10.1
- What was attempted: Header comment: "This script will create a new Xcode project and add all our files" (create_xcode_project.sh:3-4). It renames an existing project directory to `.backup` (:12-15), writes a temporary `TempMain.swift` (:21-32), runs `swift package init --type executable --name AudioRouter 2>/dev/null || true` (:35), runs `rm -f Package.swift` (:38), writes a temporary `create_project.swift` whose only action is a print (:41-49), prints eight manual Xcode steps (:53-72), prints "Then run: ./add_files_to_project.sh" (:74) and deletes the two temporary files (:79). `add_files_to_project.sh` is not in the repo. No tracked file names a caller for this script (searched `create_xcode_project`).
- Presence: CURRENT (create_xcode_project.sh:1-79). Mode 100644.
- Outcome: FAILED (as automated project creation)
- Outcome evidence: STATED. Script output heading "Manual Project Creation Required" (create_xcode_project.sh:53); embedded comment "This will be done manually" (:47).
- Why it turned out that way: UNKNOWN.
- Depends on: the hard-coded directory at create_xcode_project.sh:9; the Swift toolchain for `swift package init` (:35).
- Open questions: Was it run? Line 38 deletes `Package.swift`, yet a `Package.swift` is tracked: was that file written after the script was last run, or was the script never run?
- Confidence: MEDIUM. STATED in the script text; the code agrees.

### R3-S10-04  auto_create_project.sh, which writes a project file from an embedded template
- Question: S10.1
- What was attempted: Presented as "Option 3: Use Script (Automated)" (FIX_PROJECT.md:148-156). With `set -e` (auto_create_project.sh:3) it changes to a hard-coded directory (:11), moves an existing project directory to `.backup` (:14-19), builds a stub Swift package in a temporary directory (:25-69) that is never used for generation and is deleted at :702, then writes `contents.xcworkspacedata` (:86-94), a complete `project.pbxproj` from a heredoc (:99-602) and a scheme file (:618-697). It checks the project file with `plutil -lint` and prints a warning on failure without stopping (:609-613). It ends by printing "Xcode Project Created Successfully!" (:706) whatever the check returned. The heredoc lists 12 Swift files (:109-121); the archived project lists the same 12 plus DevicePickerViews.swift, LogViewer.swift and LogManager.swift (ZIP!pbxproj:23-25) and a team setting (ZIP!pbxproj:256).
- Presence: CURRENT (auto_create_project.sh:1-725). Mode 100644; FIX_PROJECT.md:154 tells the user to run `chmod +x` first.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The success line at :706 is unconditional script output, not a recorded result.
- Why it turned out that way: UNKNOWN.
- Depends on: the hard-coded directory (:11, :80); `plutil` (:609); the 12 source paths under `AudioRouter/` existing on disk; Xcode accepting a hand-numbered project file (comment at :96-97: "This is a working minimal template that Xcode will accept").
- Open questions: Is the archived project the output of this script with later edits? The archive's workspace and scheme members are dated 2025-11-10 08:27 and its project file 2025-11-10 20:20 (`unzip -l`): does that order reflect a script run followed by edits? Did Xcode open the generated project without the problem named at FIX_PROJECT.md:3?
- Confidence: MEDIUM. INFERRED from clear script code; no evidence of a run.

### R3-S10-05  Manual creation of a fresh Xcode project and re-adding the files
- Question: S10.1
- What was attempted: "Option 2: Create Fresh Xcode Project (Recommended)" (FIX_PROJECT.md:14). Steps: create a macOS App project named AudioRouter with SwiftUI, with "Include Tests" unticked, saved over the existing folder (FIX_PROJECT.md:16-44); delete the two default Swift files (:46-54); add the project's own files and the Managers, Views and Utilities folders, and replace Info.plist and the entitlements file (:56-90); set bundle identifier `com.audiorouter.app`, version 1.0.0, deployment target macOS 14.0 (:92-101); set signing and App Sandbox capabilities (:103-119); build with `Cmd+B` and run with `Cmd+R` (:135-146). The document's own ranking: "Use Option 2 if Option 1 doesn't work - most reliable" (FIX_PROJECT.md:188).
- Presence: CURRENT as documentation text (FIX_PROJECT.md:14-146).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The document ends with a question to the reader, "Let me know which option you'd like to try!" (FIX_PROJECT.md:191), and records no result.
- Why it turned out that way: UNKNOWN.
- Depends on: Xcode's project template; the source files under `AudioRouter/` on the author's machine.
- Open questions: Which of the three options was used? The archived project uses hand-numbered identifiers (ZIP!pbxproj:10), which is not what a project made by Xcode's template would hold: was Option 2 ever carried out?
- Confidence: MEDIUM. STATED procedure only.

### R3-S10-06  Google Cast SDK installation and code changes (CHROMECAST_SETUP_GUIDE.md)
- Question: S10.1
- What was attempted: A manual integration guide. Register as a Cast developer and create an application (CHROMECAST_SETUP_GUIDE.md:21-43). Install the SDK by Swift Package Manager from `https://github.com/google/google-cast-sdk-ios`, by CocoaPods with `pod 'google-cast-sdk', '~> 4.8'`, or by dragging `GoogleCast.xcframework` into the project (:47-88). Add `NSLocalNetworkUsageDescription` and two `NSBonjourServices` entries to Info.plist (:92-105). Create and host `receiver.html` (:109-232). Replace four parts of ChromecastManager.swift (:236-343). Build and test (:347-368). Estimated effort "Total: 5-8 hours" (:408-414). The same SDK is described as optional in the README (README.md:108-120).
- Presence: DISABLED. The SDK dependency line is commented out (Package.swift:20); the project has no package reference and an empty Frameworks phase (ZIP!pbxproj:50-58; searched the member for `XCRemoteSwiftPackage`, `GoogleCast`: no hits); no Podfile is tracked.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The guide's header states the starting point, "Current Status: **Mock Implementation**" and "Target: **Real Chromecast Streaming**" (CHROMECAST_SETUP_GUIDE.md:7-8); nothing records that the target was reached.
- Why it turned out that way: UNKNOWN. The guide's closing note reads "This is a complex integration." (CHROMECAST_SETUP_GUIDE.md:428).
- Depends on: Google Cast SDK 4.8.0+ (:57); a paid Cast developer registration "if required" (:27); the repository URL given for the SDK, which the guide treats as a Swift package for a macOS 14 target (:49-58, :64).
- Open questions: Is the named SDK available for a macOS application target? Was any of the three install options tried? This guide and CHROMECAST_SETUP.md give different procedures and different namespaces (R3-S08-04): which one matches the absent code?
- Confidence: MEDIUM. STATED procedure; manifests agree that the SDK is not integrated.

### S10.2 Start on boot, start order, restart and recovery

### R3-S10-07  NOT FOUND
- Question: S10.2
- Searched: `launchd`, `LaunchAgent`, `login item`, `boot`, `restart`, `crash`, `recover`, `auto-?start`, `startup`, `systemd`, `.service` over all 13 tracked files; `PBXShellScript` in ZIP!pbxproj; `git ls-files` for any plist, service file or helper target.
- Nearest related material: the only start mechanism is a manual run from Xcode through the scheme's LaunchAction (ZIP!xcscheme:33-53; README.md:77-79). Crash-related text: "[x] No crashes on startup" (PROJECT_SUMMARY.md:351) and user steps under "App crashes on launch" (SETUP.md:87-91). The `restart` hits concern external devices or Xcode (README.md:254; BUILD_AND_DEPLOY.md:436; CHROMECAST_SETUP_GUIDE.md:378, 384).

### S10.3 Permissions and privileges required

### R3-S10-08  macOS privacy permissions requested at first launch
- Question: S10.3
- What was attempted: Three prompts on first launch: microphone, Bluetooth, local network (README.md:70-75; SETUP.md:35-44), with prompt texts tabled at PROJECT_SUMMARY.md:232-236. The README adds "Media Library: For ShazamKit integration" and says the permissions are "declared in `Info.plist:1`" (README.md:226-233). The project build settings carry a microphone and a Bluetooth usage string (ZIP!pbxproj:279, 281, 358, 360) together with `GENERATE_INFOPLIST_FILE = NO` and `INFOPLIST_FILE = AudioRouter/Info.plist` (ZIP!pbxproj:275-276). No local-network usage key is in the project file (searched the member for `LocalNetwork`, `Bonjour`: no hits); the Cast SDK guide tells the reader to add one (CHROMECAST_SETUP_GUIDE.md:92-105).
- Presence: CURRENT as documentation text and project settings. SOURCE ABSENT for `AudioRouter/Info.plist` (ZIP!pbxproj:45).
- Outcome: WORKED
- Outcome evidence: STATED. "[x] Permissions requested properly" (PROJECT_SUMMARY.md:354).
- Why it turned out that way: UNKNOWN.
- Depends on: macOS 14 privacy prompts; the absent Info.plist; an Apple Developer account for signing (README.md:44).
- Open questions: Which usage keys does the absent Info.plist hold? Do the `INFOPLIST_KEY_` build settings have any effect while `GENERATE_INFOPLIST_FILE` is `NO`? Was a local-network prompt ever shown?
- Confidence: MEDIUM. STATED evidence alone; the file that would confirm it is absent.

### R3-S10-09  App Sandbox capabilities and signing identity required
- Question: S10.3
- What was attempted: The project-repair guide lists the capabilities to enable under App Sandbox: "Incoming Connections (Server)", "Outgoing Connections (Client)", hardware "Audio Input" and "Bluetooth", network "Network Client" and "Network Server" (FIX_PROJECT.md:108-119). The project points at an entitlements file and uses automatic signing with hardened runtime (ZIP!pbxproj:250-251, 257). An Apple Developer account, "free or paid", is required for signing (SETUP.md:9); the paid programme is named for Developer ID distribution (BUILD_AND_DEPLOY.md:127-128). No step needs administrator rights or `sudo`; the only `sudo` in the repo is in a sample CI workflow listing (BUILD_AND_DEPLOY.md:396).
- Presence: CURRENT as documentation text and project settings. SOURCE ABSENT for `AudioRouter/AudioRouter.entitlements` (ZIP!pbxproj:44).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: the absent entitlements file; the developer team stored in the project (ZIP!pbxproj:256, value not copied).
- Open questions: Which entitlements does the absent file grant? Can a sandboxed app with these entitlements act as a Bluetooth audio receiver at all?
- Confidence: MEDIUM. STATED list plus project settings; the entitlements file is absent.

### S10.4 Configuration: where it lives, what can be set, the defaults

### R3-S10-10  In-app settings with persistent preferences
- Question: S10.4
- What was attempted: Settings reachable from the settings panel: gap detection on or off, recognition interval 1 to 5 minutes, input device selection, volume preferences (PROJECT_SUMMARY.md:282-288), described as "Persistent preferences" (CHANGELOG.md:61). Where the values are stored is not stated (searched `UserDefaults`, `AppStorage`, `preference`: no storage mechanism named). No default values are stated; the one fixed value given is the silence threshold for gap detection, "2+ seconds" (PROJECT_SUMMARY.md:188).
- Presence: CURRENT as documentation text. SOURCE ABSENT for SettingsView.swift (ZIP!pbxproj:38).
- Outcome: WORKED
- Outcome evidence: STATED. "[x] Settings persist" (PROJECT_SUMMARY.md:350).
- Why it turned out that way: UNKNOWN.
- Depends on: macOS per-app preference storage, not named in the repo.
- Open questions: Where are the preferences stored, and what are the defaults? Is the Bluetooth device name "AudioRouter" (README.md:138) configurable?
- Confidence: MEDIUM. STATED evidence alone.

### R3-S10-11  Configuration fixed in source and build settings
- Question: S10.4
- What was attempted: The Chromecast receiver application ID is set by editing a source line: "Replace `YOUR_RECEIVER_APP_ID` with your actual receiver app ID (line 99)" (README.md:94-100; CHROMECAST_SETUP.md:94-103; PROJECT_SUMMARY.md:280). A change needs a rebuild. The Cast SDK guide uses a second placeholder inside `GCKDiscoveryCriteria` (CHROMECAST_SETUP_GUIDE.md:262-264). Build-time values in the project: bundle identifier `com.audiorouter.app`, marketing version 1.0.0, deployment target 14.0, application category music (ZIP!pbxproj:278, 286-287, 291). `Package.swift` declares a library product for a target at path `AudioRouter`, a directory that is not in the repo (Package.swift:11-27).
- Presence: CURRENT as documentation text and project settings. SOURCE ABSENT for ChromecastManager.swift (ZIP!pbxproj:36).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: Xcode for any change to these values.
- Open questions: Did the absent source still hold the placeholder at the time of the upload?
- Confidence: MEDIUM. STATED in three documents; project settings read directly.

### S10.5 Logging and diagnostics available

### R3-S10-12  In-app logging component (LogManager and LogViewer)
- Question: S10.5
- What was attempted: A logging utility and a viewer are listed in the archived project (ZIP!pbxproj:43, 46, 208-209). Guide listings show calls with a subsystem and a level, `.success`, `.error`, `.info` (CHROMECAST_SETUP_GUIDE.md:275, 290, 297), and the guide's help list says "Review logs in LogViewer" (CHROMECAST_SETUP_GUIDE.md:421). The component is missing from the documented structure (README.md:298-318; PROJECT_SUMMARY.md:17-61), from the per-file line table (PROJECT_SUMMARY.md:125-138) and from the project heredoc (auto_create_project.sh:109-121).
- Presence: CURRENT as project metadata and guide text. SOURCE ABSENT for both files.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: nothing stated.
- Open questions: Does the logger write to a file, to the system log, or to memory only? Was it added after the README and summary were written?
- Confidence: LOW. INFERRED from file names and three listing lines.

### R3-S10-13  Diagnostics through external tools
- Question: S10.5
- What was attempted: The documents name external tools for diagnosis: the Xcode console (README.md:346; CHROMECAST_SETUP.md:280), Console.app (SETUP.md:89), `ping` of the Chromecast address (CHROMECAST_SETUP.md:159-163), Chrome DevTools on the receiver URL (CHROMECAST_SETUP.md:196-198, 279), "Cast Debug Logger" (CHROMECAST_SETUP_GUIDE.md:404, 423), `codesign` and `spctl` checks and `xcrun notarytool log` for signing (BUILD_AND_DEPLOY.md:208-219, 250, 452). The Debug build configuration keeps debug symbols and assertions (BUILD_AND_DEPLOY.md:318-324; ZIP!pbxproj:255, 294-296). No log file, crash report or diagnostic capture is in the repo (`git ls-files`).
- Presence: CURRENT as documentation text and project settings.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: running the app under Xcode (README.md:77-79).
- Open questions: Is there any diagnostic for the Bluetooth link, such as connected source, codec or signal?
- Confidence: MEDIUM. STATED tool list only.

### S10.6 Packaging and update approach

### R3-S10-14  Distribution paths documented: archive, Developer ID, App Store, DMG, Homebrew, CI
- Question: S10.6
- What was attempted: A build and deployment guide covers: Xcode archive and development export (BUILD_AND_DEPLOY.md:57-99); Developer ID export with notarisation and stapling (:123-151, :233-251); Mac App Store submission (:153-171); command-line archive with two export-options files shown inline (:101-115, :173-202); DMG creation with `create-dmg` (:257-278); GitHub Releases (:280-285); a Homebrew cask listing with placeholder checksum and URLs (:287-303); a GitHub Actions workflow listing (:373-417). The summary repeats four distribution options (PROJECT_SUMMARY.md:266-270). None of the named files is tracked: no `ExportOptions.plist`, `ReleaseExportOptions.plist`, `.github/` directory, DMG or cask (`git ls-files`). The repo has no tags (`git tag`).
- Presence: CURRENT as documentation text only.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. All 14 items of the pre-release checklist are unticked, including "Code sign with Developer ID", "Notarize the app", "Create DMG or ZIP" and "Tag release in git" (BUILD_AND_DEPLOY.md:473-486).
- Why it turned out that way: UNKNOWN.
- Depends on: the paid Apple Developer Program for Developer ID (BUILD_AND_DEPLOY.md:128); hardened runtime, which is set in the project (ZIP!pbxproj:257, 342); placeholders `YOUR_TEAM_ID`, `yourname`, `checksum_here` (BUILD_AND_DEPLOY.md:110, 199, 294, 296).
- Open questions: Was the app ever archived, signed for distribution or notarised? Was any build handed to another machine?
- Confidence: MEDIUM. STATED procedures with an unticked checklist.

### R3-S10-15  Versioning scheme, with no update mechanism
- Question: S10.6
- What was attempted: Version numbers are managed by hand in Xcode or with `xcrun agvtool` (BUILD_AND_DEPLOY.md:347-358), following semantic versioning (BUILD_AND_DEPLOY.md:360-369; CHANGELOG.md:5-6). A release schedule is stated: patch "as needed", minor "monthly", major "yearly" (CHANGELOG.md:153-157). One release entry exists, "[1.0.0] - 2025-01-10" (CHANGELOG.md:8); the project carries `MARKETING_VERSION = 1.0.0` and `CURRENT_PROJECT_VERSION = 1` (ZIP!pbxproj:254, 287). No in-app update or update feed is described (searched `Sparkle`, `auto-update`, `appcast`, `update`: the `update` hits concern metadata, version numbers and receiver settings).
- Presence: CURRENT as documentation text and project settings.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The repo holds one commit and no tags (`git log`, `git tag`).
- Why it turned out that way: UNKNOWN.
- Depends on: the distribution channel chosen (R3-S10-14); Homebrew or the App Store would carry updates if used (BUILD_AND_DEPLOY.md:287-312).
- Open questions: Was a 1.0.0 build ever released on the date in the changelog? The changelog date is 2025-01-10 and the only commit is dated 2025-11-13: which date reflects the work?
- Confidence: MEDIUM. STATED scheme; git history read directly.

---

## S11 Testing and measured behaviour

### S11.1 Tests present, what they cover, and any recorded results

### R3-S11-01  Automated test action with no tests
- Question: S11.1
- What was attempted: The summary gives a test command, `xcodebuild test -project AudioRouter.xcodeproj -scheme AudioRouter` (PROJECT_SUMMARY.md:255-258). The scheme's TestAction holds an empty `<Testables>` element (ZIP!xcscheme:25-32; same in the script heredoc, auto_create_project.sh:643-650). The project has one target, the application (ZIP!pbxproj:173-175). Both project-creation texts tell the user to untick "Include Tests" (FIX_PROJECT.md:36; create_xcode_project.sh:69). No test file is tracked (`git ls-files`; searched `XCTest`, `Tests`: no hits outside the lines cited).
- Presence: CURRENT (documentation text and scheme). No test target or test source exists in the repo or in the archived project.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No test output is in the repo.
- Why it turned out that way: UNKNOWN.
- Depends on: nothing stated.
- Open questions: Were unit tests ever written on the author's machine? What would the documented test command run, given the empty test list?
- Confidence: MEDIUM. INFERRED from clear scheme and project metadata.

### R3-S11-02  Manual "Testing Checklist" with ten ticked items
- Question: S11.1
- What was attempted: A checklist of ten items, all ticked: "Build successfully on macOS 14", "Microphone input works", "Bluetooth pairing works", "Equalizer displays correctly", "Shazam recognizes songs", "Settings persist", "No crashes on startup", "UI is responsive", "Dark mode supported", "Permissions requested properly" (PROJECT_SUMMARY.md:345-354). It has no date, tester, device or method. It has no item for AirPlay output, Chromecast output, line-in, system audio, volume, mute, or audio from a Bluetooth source reaching an output.
- Presence: CURRENT as documentation text (PROJECT_SUMMARY.md:343-354).
- Outcome: WORKED (for the ten behaviours listed, as stated)
- Outcome evidence: STATED. Ten `[x]` marks (PROJECT_SUMMARY.md:345-354).
- Why it turned out that way: UNKNOWN.
- Depends on: macOS 14 (PROJECT_SUMMARY.md:345). No other condition is named.
- Open questions: Were these checks carried out, and on what hardware? The same document labels its performance figures "Estimated" (PROJECT_SUMMARY.md:294) and is dated "January 10, 2025" (PROJECT_SUMMARY.md:391), ten months before the archive members and the commit: do the ticks describe the uploaded state? Did "Bluetooth pairing works" include audio playing after pairing?
- Confidence: MEDIUM. STATED evidence alone; nothing in the repo corroborates any item except the build (R3-S11-06).

### R3-S11-03  Pre-release checklist, unticked
- Question: S11.1
- What was attempted: A 14-item list headed "Before releasing", including "Test on clean macOS install", "Verify all permissions work", "Test Bluetooth connectivity", "Test AirPlay functionality", "Test Chromecast (if configured)", "Run on macOS 14 minimum" (BUILD_AND_DEPLOY.md:469-486).
- Presence: CURRENT as documentation text.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. All 14 boxes are `[ ]` (BUILD_AND_DEPLOY.md:473-486).
- Why it turned out that way: UNKNOWN.
- Depends on: nothing stated.
- Open questions: Why are Bluetooth and permissions ticked in PROJECT_SUMMARY.md:347, 354 and unticked here?
- Confidence: MEDIUM. STATED list read directly.

### R3-S11-04  Chromecast receiver testing checklist, unticked
- Question: S11.1
- What was attempted: Nine items covering receiver URL, application ID, device whitelisting, network, firewall, namespace match, metadata on TV, equalizer animation and song changes (CHROMECAST_SETUP.md:248-258), preceded by a manual test procedure with the expected TV screen (CHROMECAST_SETUP.md:107-135).
- Presence: CURRENT as documentation text.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. All nine boxes are `[ ]` (CHROMECAST_SETUP.md:250-258).
- Why it turned out that way: UNKNOWN.
- Depends on: a deployed receiver page and a registered application (CHROMECAST_SETUP.md:12-84).
- Open questions: Was any item ever checked against a real device?
- Confidence: MEDIUM. STATED list read directly.

### R3-S11-05  Cast SDK guide test steps, written as expectations
- Question: S11.1
- What was attempted: "Step 6: Build and Test" lists a build command and three manual tests: discovery ("Should see real devices on your network"), connection ("Should see \"Successfully connected\" in log"), streaming ("Should hear audio on Chromecast") (CHROMECAST_SETUP_GUIDE.md:347-368). The streaming test names the microphone as the input (:366).
- Presence: CURRENT as documentation text; it tests code that exists only as guide listings (DISABLED, see R3-S10-06).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Each step states what is expected, with no result.
- Why it turned out that way: UNKNOWN.
- Depends on: completion of the SDK integration in R3-S10-06.
- Open questions: Were these steps ever run?
- Confidence: MEDIUM. STATED procedure read directly.

### R3-S11-06  Xcode activity report recording a completed build
- Question: S11.1
- What was attempted: The archive carries one user's Xcode interface state. Its strings include `LastCompletedPersistentSchemeBasedActivityReport`, `IDEActivityReportTitle`, `IDEActivityReportCompletionSummaryStringSegments`, `Build`, `Succeeded`, a toolchain build string `17B55`, and a run destination with `macosx26.1`, `arm64`, `Mac15,13` (ZIP!xcuserstate, read with `strings`). The same member lists ContentView.swift, ShazamManager.swift and AirPlayManager.swift as open documents. The member is dated 2025-11-11 07:46 (`unzip -l`).
- Presence: CURRENT (member of the tracked archive `AudioRouter.xcodeproj`).
- Outcome: WORKED (a build of the scheme completed; nothing about run-time behaviour)
- Outcome evidence: OBSERVED. The strings `Build` and `Succeeded` in the last-completed activity report of ZIP!xcuserstate.
- Why it turned out that way: UNKNOWN.
- Depends on: the author's machine (model code and OS string above); Xcode with build string `17B55`; the sources present there. The project's deployment target is 14.0 (ZIP!pbxproj:286) while the destination string reads `macosx26.1`.
- Open questions: Does `Succeeded` belong to the `Build` report, as their adjacency in the file implies? Which revision of the sources was built? Did the built app run and pass audio?
- Confidence: MEDIUM. OBSERVED artefact, but read as raw strings from a binary property list, so the link between the two strings is INFERRED.

### S11.2 Source devices named as tested, with the result for each

### R3-S11-07  NOT FOUND
- Question: S11.2
- Searched: all eight `.md` files read in full for any make or model of phone, tablet, turntable, speaker or computer used as an audio source; `grep` for `iPhone`, `Android`, `Pixel`, `Samsung`, `Sony`, `Audio-Technica`, `turntable`, `tested`, `verified`, `works with` over all 13 tracked files; `strings` of ZIP!xcuserstate for device names. The one `grep` hit is a Cast console form field, "Android Package Names" (CHROMECAST_SETUP.md:73).
- Nearest related material: generic source types only: "record players and other Bluetooth audio devices" (README.md:11), "Bluetooth record player" (README.md:135; SETUP.md:65), "Record players, speakers, phones" (PROJECT_SUMMARY.md:162), "phones/tablets" (PROJECT_SUMMARY.md:207). The one result line, "[x] Bluetooth pairing works" (PROJECT_SUMMARY.md:347), names no device. The model code in ZIP!xcuserstate is the build machine, not an audio source.

### S11.3 Measurements recorded

### R3-S11-08  Performance figures labelled as estimates
- Question: S11.3
- What was attempted: Under "Benchmarks (Estimated)": "CPU Usage: 5-15% (depending on input source)", "Memory: ~80-120 MB", "Latency: <50ms (audio routing)", "Equalizer Refresh: 50ms (20 FPS)", "Battery Impact: Low-Medium" (PROJECT_SUMMARY.md:294-300). A list of optimisations follows with no figures (PROJECT_SUMMARY.md:302-308). No dropout count, range, uptime or load measurement is recorded anywhere (searched `dropout`, `glitch`, `uptime`, `range`, `measured`, `benchmark`: the heading above is the only hit).
- Presence: CURRENT as documentation text.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The author's heading marks the figures as estimated; no measurement log, tool or method is in the repo.
- Why it turned out that way: UNKNOWN.
- Depends on: nothing stated; no machine, input source or output is tied to the figures.
- Open questions: Was any figure taken from a running build?
- Confidence: MEDIUM. STATED figures, labelled by their author as estimates.

### S11.4 Known issues listed by the author

### R3-S11-09  Known issue: Chromecast carries no audio and uses placeholder code
- Question: S11.4
- What was attempted: Chromecast output (see R3-S08-02). Listed by the author as a limitation in five places: "Full implementation requires Google Cast SDK" and "Current implementation includes placeholder code" (README.md:282-286); "Metadata/visualizer only (no audio streaming in base implementation)" (PROJECT_SUMMARY.md:315); "Full Chromecast implementation requires Google Cast SDK" (CHANGELOG.md:101); troubleshooting entry "Audio not playing" (CHROMECAST_SETUP.md:200-210); "Mock Implementation (simulated connections only)" (CHROMECAST_SETUP_GUIDE.md:7).
- Presence: CURRENT as documentation text.
- Outcome: FAILED (audio to Chromecast)
- Outcome evidence: STATED. Citations above.
- Why it turned out that way: STATED. The Cast SDK is not integrated (README.md:285); manifests agree (Package.swift:20 commented out; ZIP!pbxproj:54-55).
- Depends on: Google Cast SDK integration, estimated by the author at "5-8 hours" (CHROMECAST_SETUP_GUIDE.md:414).
- Open questions: NONE beyond those in R3-S08-02.
- Confidence: MEDIUM. STATED consistently; application source absent.

### R3-S11-10  Known issue: Bluetooth receiver behaviour varies by device
- Question: S11.4
- What was attempted: Bluetooth audio receiver mode. Limitations listed: "A2DP profile support varies by device", "Some devices may require additional pairing steps", "Audio quality depends on Bluetooth codec support" (README.md:288-292); "Bluetooth Compatibility: A2DP profile support varies by device" (PROJECT_SUMMARY.md:316); "Some Bluetooth devices may require additional pairing steps" (CHANGELOG.md:102).
- Presence: CURRENT as documentation text. SOURCE ABSENT for BluetoothManager.swift (ZIP!pbxproj:33).
- Outcome: PARTIAL
- Outcome evidence: STATED. Worked: "[x] Bluetooth pairing works" (PROJECT_SUMMARY.md:347). Defects: the three limitation lines above.
- Why it turned out that way: UNKNOWN. No device, codec or cause is named. The component is described as "CoreBluetooth integration" with "Peripheral mode (act as audio receiver)" (README.md:185-189).
- Depends on: CoreBluetooth as the stated framework (PROJECT_SUMMARY.md:78, 146); unnamed source devices.
- Open questions: Which devices showed the variation, if any were tried? What are the "additional pairing steps"? Did audio ever flow after pairing?
- Confidence: MEDIUM. STATED evidence alone; no device is named.

### R3-S11-11  Known issue: system audio capture requirement
- Question: S11.4
- What was attempted: System audio input. Listed limits: "Requires macOS 13 or later", "Uses ScreenCaptureKit API", "May require additional permissions" (README.md:276-280); repeated at PROJECT_SUMMARY.md:314 and CHANGELOG.md:100.
- Presence: CURRENT as documentation text.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The feature is marked "Complete" (PROJECT_SUMMARY.md:77) and has no checklist item (PROJECT_SUMMARY.md:345-354).
- Why it turned out that way: UNKNOWN.
- Depends on: ScreenCaptureKit; the app's minimum of macOS 14.0 already exceeds the stated macOS 13 requirement (README.md:42).
- Open questions: Which "additional permissions" are meant? No screen-recording usage key is in the project settings (ZIP!pbxproj:277-281).
- Confidence: MEDIUM. STATED limitation only.

### R3-S11-12  Known issue: AirPlay selection uses the system menu
- Question: S11.4
- What was attempted: AirPlay device selection. "AirPlay uses system menu on macOS (not custom picker)" (CHANGELOG.md:103); "AirPlay Picker: Uses system menu (no custom picker UI on macOS)" (PROJECT_SUMMARY.md:317). The usage text still says "Select your AirPlay device from the picker" (README.md:148).
- Presence: CURRENT as documentation text.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No result is stated for AirPlay output (see R3-S08-01).
- Why it turned out that way: UNKNOWN.
- Depends on: macOS system AirPlay controls.
- Open questions: What does the "AirPlay" button in the window do, given that selection happens in the system menu?
- Confidence: MEDIUM. STATED in two documents.

### R3-S11-13  Known issue: hand-written Xcode project file rejected by Xcode
- Question: S11.4
- What was attempted: A project file written by hand. "The project.pbxproj file was hand-crafted and Xcode is being strict about it. Let's create a proper project!" (FIX_PROJECT.md:3). Three repair routes follow (R3-S10-02, R3-S10-04, R3-S10-05). The archived project file keeps hand-numbered identifiers (ZIP!pbxproj:10-25).
- Presence: CURRENT (FIX_PROJECT.md; the archived project file).
- Outcome: PARTIAL
- Outcome evidence: STATED and OBSERVED. Defect STATED at FIX_PROJECT.md:3. A completed build is OBSERVED in ZIP!xcuserstate (`Build`, `Succeeded`; see R3-S11-06). The order of the two is not evidenced: FIX_PROJECT.md carries no date (searched the file for `20[0-9]{2}`, month names, `date`, `updated`: no hits), the one commit 1bdb71d holds both the document and the archive, and the only dates are those of the archive members (project file 2025-11-10 20:20, ZIP!xcuserstate 2025-11-11 07:46; `unzip -l`).
- Why it turned out that way: STATED. The file "was hand-crafted" (FIX_PROJECT.md:3). The exact error is not recorded.
- Depends on: Xcode 15+ project format `objectVersion = 56` (ZIP!pbxproj:6); the Xcode version in use, which is not stated beyond the build string in ZIP!xcuserstate.
- Open questions: What error did Xcode report? Which repair route produced the archived project file?
- Confidence: MEDIUM. STATED defect; OBSERVED build string read from a binary file.

### R3-S11-14  Known issue: "Cannot find 'AVAudioSession' in scope"
- Question: S11.4
- What was attempted: Listed under "Common Issues": "This is expected - AVAudioSession is primarily for iOS. The macOS implementation uses AVAudioEngine directly. The code will compile and run correctly on macOS." (SETUP.md:77-79).
- Presence: CURRENT as documentation text.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The sentence is a forecast ("will compile"), not a recorded result.
- Why it turned out that way: STATED. "AVAudioSession is primarily for iOS" (SETUP.md:79).
- Depends on: the absent source containing, or once containing, a reference to AVAudioSession.
- Open questions: Does the message appear as a compiler error, and if so how does the code compile? Was the reference removed before the build recorded in ZIP!xcuserstate?
- Confidence: MEDIUM. STATED text only.

### R3-S11-15  Known limitation note about macOS 26
- Question: S11.4
- What was attempted: "This app was developed for macOS 14+ and is compatible with future versions including macOS 26." and "Some features may require updates when macOS 26 is released." (README.md:272-274); "Future compatible with macOS 26" (CHANGELOG.md:108).
- Presence: CURRENT as documentation text.
- Outcome: UNVERIFIED
- Outcome evidence: NONE for run-time behaviour. The build destination string in ZIP!xcuserstate reads `macosx26.1` (see R3-S11-06), which shows a build on that OS line and nothing about features.
- Why it turned out that way: UNKNOWN.
- Depends on: macOS version of the machine in use.
- Open questions: Which features were expected to need changes? Was the app run on the `macosx26.1` machine?
- Confidence: MEDIUM. STATED note; one OBSERVED string.

---

## S12 Security and exposure

### S12.1 Network services and ports opened, and the authentication on each

### R3-S12-01  Inbound network capability enabled, with no listener described
- Question: S12.1
- What was attempted: The sandbox capability list enables "Incoming Connections (Server)" and "Network Server" as well as the client entries (FIX_PROJECT.md:111-119). No document describes a service the app listens on, and no port number appears anywhere (searched `port\b`, `listen`, `socket`, `http server`, `:[0-9]{2,5}`, `localhost`, `0.0.0.0` over all tracked files: the only hits are a `ping 192.168.1.xxx` example at CHROMECAST_SETUP.md:162 and Cast message listeners at CHROMECAST_SETUP_GUIDE.md:187, 268). Network discovery named: Bonjour service types `_googlecast._tcp` and `_CC1AD845._googlecast._tcp` to be added to Info.plist (CHROMECAST_SETUP_GUIDE.md:100-104). The guide listing for audio assumes a streaming endpoint at a placeholder URL (CHROMECAST_SETUP_GUIDE.md:323); no server for it exists. Troubleshooting tells the user to allow the app through the macOS firewall (CHROMECAST_SETUP.md:165-167; README.md:261). No authentication is described for any network path (searched `auth`, `token`, `password`: hits are signing and notarisation placeholders only).
- Presence: CURRENT as documentation text. SOURCE ABSENT for the entitlements file that would hold the capability (ZIP!pbxproj:44, 250).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: the absent entitlements file; AirPlay and Cast traffic being outbound from the app (README.md:46).
- Open questions: Does the app open any listening socket? Why is the server capability enabled? Would the planned audio stream for Chromecast have been served from the Mac, and with what access control?
- Confidence: MEDIUM. STATED capability list; absence of ports established by search.

### R3-S12-02  Chromecast receiver page on public HTTPS hosting, gated by Cast registration
- Question: S12.1
- What was attempted: The receiver page is to be served from a public HTTPS host: GitHub Pages, Firebase Hosting, Netlify or the user's own server (CHROMECAST_SETUP.md:12-55), and "Must be accessible from internet" (CHROMECAST_SETUP_GUIDE.md:225). Access control described: the Cast application ID registered in Google's console, and, for an unpublished application, a list of test devices entered by serial number (CHROMECAST_SETUP.md:57-84); after publication "Your app will work on any Chromecast" (CHROMECAST_SETUP.md:151-153). The custom message channel has no authentication in the listing; the receiver parses any JSON arriving on the namespace and sets the artwork image source from a URL in the message (CHROMECAST_SETUP_GUIDE.md:187-196).
- Presence: CURRENT as documentation text; the receiver listing is inside a guide (DISABLED). SOURCE ABSENT for `ChromecastReceiver/index.html`.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No hosted URL or registered application is recorded; example URLs are placeholders (CHROMECAST_SETUP.md:262-266).
- Why it turned out that way: UNKNOWN.
- Depends on: Google Cast developer registration (CHROMECAST_SETUP_GUIDE.md:21-27); third-party hosting.
- Open questions: Was a receiver page ever published, and at what address? Who can send messages to the receiver session once it is running?
- Confidence: MEDIUM. STATED procedure plus a clear listing.

### S12.2 Privilege level the processes run with

### R3-S12-03  Single user-level app, sandboxed with hardened runtime
- Question: S12.2
- What was attempted: One application process, started by the user from Xcode (ZIP!xcscheme:33-53; README.md:77-79). Stated security posture: "App Sandbox: Fully sandboxed application", "Hardened Runtime: Enabled for notarization", "No Private APIs", "Minimal Permissions" (PROJECT_SUMMARY.md:240-243); "App Sandbox enabled" (CHANGELOG.md:93). Project settings: `ENABLE_HARDENED_RUNTIME = YES` in Debug and Release, automatic code signing, an entitlements path (ZIP!pbxproj:250-251, 257, 335-336, 342). No helper tool, daemon, privileged install step or `sudo` use is described for the app (searched `sudo`, `root`, `privilege`, `admin`, `daemon`, `helper`: no hit describes a privileged component; the single `sudo` is in a CI listing, BUILD_AND_DEPLOY.md:396, and the `helper` hits are audio utility descriptions, PROJECT_SUMMARY.md:34-35).
- Presence: CURRENT as documentation text and project settings. SOURCE ABSENT for `AudioRouter.entitlements`, the file that would show whether the sandbox is on (ZIP!pbxproj:44).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Hardened runtime is set in the project file; the sandbox state cannot be read without the entitlements file, and no signed build is in the repo.
- Why it turned out that way: UNKNOWN.
- Depends on: the logged-in user's account; macOS 14 sandbox and privacy controls; the capability list at FIX_PROJECT.md:108-119.
- Open questions: Was the sandbox enabled in the build recorded in ZIP!xcuserstate? Does the Bluetooth receiver role work from inside the sandbox?
- Confidence: MEDIUM. STATED posture; hardened-runtime setting read directly; sandbox not confirmable.

### S12.3 Secrets and keys: where stored and how protected

### R3-S12-04  Chromecast application ID held in a source line
- Question: S12.3
- What was attempted: The Cast receiver application ID is a string literal in `AudioRouter/Managers/ChromecastManager.swift`, line 99 by the documents' account (README.md:94-100; CHROMECAST_SETUP.md:94-103; PROJECT_SUMMARY.md:280). The repo holds placeholders and a documentation example only (README.md:99; CHROMECAST_SETUP.md:98, 102; CHROMECAST_SETUP_GUIDE.md:263). Kind of item: Google Cast application identifier. No real value was found. No protection is described; the value would be compiled into the app.
- Presence: CURRENT as documentation text. SOURCE ABSENT for ChromecastManager.swift (ZIP!pbxproj:36).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: a Cast developer registration (CHROMECAST_SETUP.md:57-84).
- Open questions: Did the absent source hold a real application ID?
- Confidence: MEDIUM. STATED location; placeholders read directly.

### R3-S12-05  Developer team identifier and per-user Xcode data inside the committed archive
- Question: S12.3
- What was attempted: The archived project file carries a `DEVELOPMENT_TEAM` build setting in the target's Debug and Release configurations (ZIP!pbxproj:256, 341). Kind of item: Apple Developer Team identifier; value not copied here. It is stored in plain text inside the tracked Zip file and is absent from the heredoc template (`grep -c DEVELOPMENT_TEAM auto_create_project.sh` returns 0). The archive also carries a per-user `xcuserdata` directory with interface state holding absolute local file paths and a machine model code (ZIP!xcuserstate), and nine `__MACOSX/._*` metadata entries (`unzip -l`). The three scripts and one guide hold an absolute path under one user's home directory (auto_create_project.sh:11; create_xcode_project.sh:9; fix_project_quick.sh:5; CHROMECAST_SETUP_GUIDE.md:76). No `.gitignore` is tracked, although two documents list one (PROJECT_SUMMARY.md:60; CHANGELOG.md:90).
- Presence: CURRENT (members of the tracked archive `AudioRouter.xcodeproj`; commit 1bdb71d).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. INFERRED: the items entered the repo because the whole project bundle, including user data, was zipped and uploaded in one step (commit 1bdb71d, subject "Add files via upload"; `__MACOSX` entries dated 2025-11-13 08:56).
- Depends on: the upload method used for the single commit.
- Open questions: Was the inclusion of the user data and team identifier intended?
- Confidence: MEDIUM. Locations read directly; the cause is INFERRED.

### R3-S12-06  Signing and notarisation credentials shown as placeholders only
- Question: S12.3
- What was attempted: The deployment guide shows where credentials are supplied: a team ID in two export-options listings (BUILD_AND_DEPLOY.md:109-110, 198-199), a signing identity on the `codesign` command line (BUILD_AND_DEPLOY.md:227), and an Apple ID, team ID and app-specific password as `notarytool` arguments (BUILD_AND_DEPLOY.md:240-244). Kinds of item: Apple account name, team identifier, app-specific password. All are placeholders; no real value is in the repo. No keychain profile, environment variable or secret store is described (searched `keychain`, `secret`, `credential`, `env`: no hits for storage). The sample CI workflow uses no secrets (BUILD_AND_DEPLOY.md:379-417). Bluetooth pairing keys are not mentioned anywhere (searched `link key`, `pairing key`, `bond`); pairing storage is not described in any document.
- Presence: CURRENT as documentation text.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: Apple's signing and notarisation tooling (BUILD_AND_DEPLOY.md:206-251).
- Open questions: Where does the app, or macOS on its behalf, keep Bluetooth pairing records for sources that connect to it?
- Confidence: MEDIUM. STATED command listings read directly.

---

## Tally

| Stage | Questions | Answered with findings | Answered NOT FOUND | Finding records | NOT FOUND records |
|-------|-----------|------------------------|--------------------|-----------------|-------------------|
| S07 | 3 | 2 (S07.1, S07.2) | 1 (S07.3) | 5 | 1 |
| S08 | 4 | 4 | 0 | 8 | 0 |
| S09 | 4 | 4 | 0 | 12 | 0 |
| S10 | 6 | 5 | 1 (S10.2) | 14 | 1 |
| S11 | 4 | 3 | 1 (S11.2) | 14 | 1 |
| S12 | 3 | 3 | 0 | 6 | 0 |
| Total | 24 | 21 | 3 | 59 | 3 |

Questions answered with findings: 21. Questions answered NOT FOUND: 3. Total: 24.
