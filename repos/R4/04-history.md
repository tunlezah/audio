# R4 history (Aurilis)

- Repo path: /home/user/Aurilis
- HEAD at time of audit: d740bc2b39836a0af00d8ad35144d31511bc9060 (`git rev-parse HEAD`), same as 00-manifest.md:25 and 01-map.md:4
- Working tree at start and end of audit: `git status --porcelain` empty
- Audited 2026-11-17. Nothing was built, run, checked out or fetched.
- All paths are relative to the repo root unless they start with `/home/user/audio`.

## Method

Commands run inside the repo:

- `git log --all --date=short --pretty='%h %ad %s'`
- `git log --all --pretty` with full hashes, author names, author and committer dates in ISO form, parents
- `git log --all -i --grep='revert'`, `git log --all -i --grep='fix'`, and one further `-i -E --grep` with the terms `bug|hotfix|patch|correct|repair|broken|workaround|regress|todo|fixme|hack|wip|temp|rewrite|replace|switch|migrat|remove|drop|refactor`
- `git log --all --diff-filter=D --name-only`
- `git log --all --name-only --pretty=format: | sort | uniq -c | sort -rn | head -30`
- `git log --all --follow -- README.md`
- `git branch -a -v`, `git branch -a --no-merged`, `git branch -a --no-merged main`, `git branch -a --merged main`, `git branch -a --contains c98d8d3`
- `git stash list`, `git tag`, `git for-each-ref`, `git reflog --all`, `git notes list`
- `git show -s --format=%B` for each of the three commits; `git show --stat` for each; `git show 7482a74:README.md`
- `git diff --name-status 7482a74 c98d8d3`, `git diff --stat c98d8d3 d740bc2`
- `git rev-list --all --count`, `git rev-parse --is-shallow-repository`, `git ls-files`, `git status --porcelain --ignored`
- `git config --get-all remote.origin.fetch`
- `git ls-remote --heads --tags origin` (a network read of the remote's ref list; nothing was fetched and no ref in the clone changed)
- `git cat-file -t 006d973151bd00505c9ea0747c429291a1546230`
- Text searches (ripgrep / grep) over the tracked files, with the terms listed in each record.

Disclosure. One command outside the Rule 1 list was run in error: `git fsck --lost-found --no-reflogs`. That command is able to write dangling objects into `.git/lost-found`. It printed nothing. Afterwards `.git/lost-found` does not exist, `git status --porcelain` is empty, and HEAD is d740bc2. The `.git` directory listing after the run holds: FETCH_HEAD, HEAD, ORIG_HEAD, branches, config, description, hooks, index, info, logs, objects, refs.

Files read in full: every non-test `.go` file, `web/static/js/app.js`, `scripts/install.sh`, `systemd/aurilis.service`, `README.md`, `go.mod`, `.gitignore`. Test files, `web/static/index.html` and `web/static/css/style.css` were searched, not read in full.

---

## H1 Timeline (R4-H1-01)

- First commit: 7482a74, author date 2026-02-22 19:58:20 +1100 (08:58:20 UTC), author tunlezah, subject "Create README.md". 1 file, 1 insertion. The file content is one empty line (`git show 7482a74:README.md`).
- Second commit: c98d8d3, author date 2026-02-22 11:21:44 +0000, author Claude, subject "Phase 1: A2DP Sink Bluetooth Audio System v1.0.0". 34 files changed, 6999 insertions, 0 deletions (`git show --stat c98d8d3`). 33 files added and README.md modified (`git diff --name-status 7482a74 c98d8d3`).
- Last commit: d740bc2, author date 2026-02-23 18:11:15 +1100 (07:11:15 UTC), author tunlezah, subject "Merge pull request #1 from tunlezah/claude/a2dp-sink-bluetooth-Oj57l". Parents 7482a74 and c98d8d3. `git diff --stat c98d8d3 d740bc2` is empty: the merge carries no content of its own.
- Author date and committer date are identical on all three commits.
- Commit count: 3 reachable from all refs (`git rev-list --all --count`): 2 non-merge commits and 1 merge. By author: tunlezah 2, Claude 1.
- The clone is not shallow (`git rev-parse --is-shallow-repository` prints `false`; 00-manifest.md:35).
- Period of activity: one. All three commits fall within 22 h 13 min (2026-02-22 08:58 UTC to 2026-02-23 07:11 UTC). Gap between first and second commit: 2 h 23 min. Gap between second and third: 19 h 50 min.
- Period of inactivity: no commit dated after 2026-02-23 is in the clone. From the last commit to the audit date (00-manifest.md:3) is 267 days.
- All program code, tests, the installer, the unit file and the README text arrived in the single commit c98d8d3. The clone holds no intermediate development history for them.
- One remote branch tip is not in the clone (R4-H4-01). Its date is not known, so the timeline above covers the clone only.

---

## H2 Approach changes

### R4-H2-01  NOT FOUND
- Question: H2
- Searched: `git log --all` (three commits); `git log --all --diff-filter=D --name-only` (no file was ever deleted); `git diff --name-status 7482a74 c98d8d3` (33 `A`, 1 `M`); `git log --all --follow -- README.md` (7482a74, c98d8d3); `git show 7482a74:README.md`; commit-message search for `rewrite|replace|switch|migrat|remove|drop|refactor` (no match); case-insensitive tree search for `replac`, `migrat`, `rewrit`, `rewrote`, `switched`, `used to`, `formerly`, `legacy`, `deprecated`, `no longer`, `previously`, `obsolete`, `old version|approach|implementation` (hits: go.mod:10 version string, web/static/js/app.js:462 a string `.replace` call, internal/bluetooth/bluez.go:478 and 519 the phrase "previously paired" / "previously acquired", internal/bluetooth/bluez.go:498 "This is used to read A2DP audio data", internal/bluetooth/pairing_agent.go:61 "legacy pairing"; none describes one approach replacing another).
- Nearest related material: (a) README.md was modified in c98d8d3 from one empty line to 361 lines; no reason is given in the commit message. (b) Commit c98d8d3 message: "Native BlueZ D-Bus integration for Bluetooth A2DP Sink (no shell wrappers)", repeated at README.md:10. This names an approach that is not used; the clone holds no commit in which shell wrappers were present. (c) internal/audio/bluetooth_input.go:16 reads "It reads SBC-encoded audio from BlueZ, decodes it, and writes PCM to the ring buffer." and internal/audio/bluetooth_input.go:165 reads "The transport FD provides decoded PCM data in S16_LE format." Both comments entered in the same commit, c98d8d3.

---

## H3 Reverts and repeated fixes

### R4-H3-01  NOT FOUND
- Question: H3
- Searched: `git log --all -i --grep='revert'` (no output); `git log --all -i --grep='fix'` (no output); `git log --all -i -E --grep='bug|hotfix|patch|correct|repair|broken|workaround|regress|...'` (no output); the full message body of all three commits; the per-file commit count from `git log --all --name-only --pretty=format: | sort | uniq -c | sort -rn` (README.md 2, every other file 1).
- Nearest related material: README.md is the only file touched by more than one commit (7482a74 and c98d8d3). Neither commit is a fix-type commit by its subject. No area is touched by three commits.

---

## H4 Unmerged work

Refs checked:

- Local branches `main` and `claude/inspiring-galileo-a9dlia`, and remote-tracking refs `origin/main` and `origin/claude/inspiring-galileo-a9dlia`, all point at d740bc2 (`git for-each-ref`). `git branch -a --no-merged` and `git branch -a --no-merged main` print nothing.
- Stash: `git stash list` prints nothing. Tags: `git tag` prints nothing, and `git ls-remote --heads --tags origin` lists no tag.
- Branch `claude/a2dp-sink-bluetooth-Oj57l`, named in the subject of d740bc2: no ref of that name is in the clone. `git ls-remote` shows it on the remote at c98d8d3, which is the second parent of d740bc2 and is contained in `main` (`git branch -a --contains c98d8d3`). It is merged.
- `git reflog --all` shows one fetch into the clone: "fetch --no-progress --depth 50 origin main: storing head". The configured refspec is `+refs/heads/*:refs/remotes/origin/*`.
- `git ls-remote --heads --tags origin`, run 2026-11-17, lists three heads: `claude/a2dp-sink-bluetooth-Oj57l` at c98d8d3, `claude/go-cross-platform-action-VZiQD` at 006d973, `main` at d740bc2. The session branch `claude/inspiring-galileo-a9dlia` is not in that list.

### R4-H4-01  Remote branch claude/go-cross-platform-action-VZiQD, tip not present in the clone
- Question: H4, relates to NONE
- What was attempted: UNKNOWN beyond the branch name. `git ls-remote --heads --tags origin` lists `refs/heads/claude/go-cross-platform-action-VZiQD` at 006d973151bd00505c9ea0747c429291a1546230. That commit object is not in the clone (`git cat-file -t 006d973151bd00505c9ea0747c429291a1546230` fails with "could not get object info"). No file, message or diff of the branch was read.
- Presence: BRANCH-ONLY. Branch `claude/go-cross-platform-action-VZiQD` on the remote. Its tip 006d973 is none of the three commits reachable from `main` (d740bc2, c98d8d3, 7482a74), so the tip is not merged into `main` as `main` stands in the clone and on the remote (`main` at d740bc2 in both).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. Nothing of the branch was readable.
- Depends on: UNKNOWN. The clone was made by a fetch of `origin main` only (`git reflog --all`), which is why the branch is absent from the clone.
- Open questions: What does the branch change? How many commits does it hold beyond `main`? Which commit is it based on? When was it last touched? Is there a pull request for it?
- Confidence: LOW. Only the ref name and tip hash were observed; the content was not read.

---

## H5 Dead and disabled code

Search coverage. No commented-out code block was found: a search of `*.go` and `*.js` for comment lines shaped like statements (`// if`, `// return`, `// go`, `// x := ...`, `// f(...)`) returned two prose hits (internal/stream/http_stream.go:18, internal/dsp/biquad.go:18); a search of scripts/install.sh for commented commands and of systemd/aurilis.service for `#` or `;` lines returned nothing; web/static/index.html holds five section-label comments only (lines 30, 70, 91, 110, 121). No build tag, `t.Skip`, `if false` or environment switch was found in `*.go`. The records below are declarations and settings with no reader or caller. "No caller found" means a fixed-string search for the name across `*.go`, `*.js`, `*.html`, `*.sh`, `*.md` and `*.service` returned only the lines cited.

### R4-H5-01  AudioOutput interface declared, with no implementing type and no reference
- Question: H5, relates to S08.1
- What was attempted: an output plug-in interface with `Start`, `Stop` and `Name` (internal/pipeline/interfaces.go:16-25). Its comment reads "All audio outputs (HTTP stream, local playback) must implement this interface." (internal/pipeline/interfaces.go:17). README.md:69 lists it under "Plugin Interfaces".
- Presence: DISABLED. The name `AudioOutput` occurs only at internal/pipeline/interfaces.go:16, 18 and README.md:69. The one output type, `HTTPStreamer`, has the methods `ServeHTTP` and `ServeRawPCM` (internal/stream/http_stream.go:46, 110) and none of `Start`, `Stop`, `Name`.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: the design statement at README.md:66 ("All components follow strict interfaces defined in `internal/pipeline/interfaces.go`"); the output path being an HTTP handler (internal/server/server.go:56-57)
- Open questions: Was a "local playback" output written at any point? Is the interface referenced on the branch in R4-H4-01?
- Confidence: MEDIUM. INFERRED from clear code: the interface name has no use site and the only output type lacks its methods.

### R4-H5-02  AudioInput, DSPProcessor and DeviceDiscovery interfaces and two structs declared and never referenced
- Question: H5, relates to NONE
- What was attempted: plug-in interfaces for inputs, DSP stages and device discovery, plus the structs `DiscoveredDevice` and `AudioFormat` (internal/pipeline/interfaces.go:3-14, 27-37, 39-48, 50-60, 62-67). README.md:73: "New features plug into this system without modifying core code."
- Presence: DISABLED. A search of `*.go` for the four interface names and for `pipeline.` followed by any of the six type names returns only the declaration lines. `BluetoothInput` and `ALSAInput` carry the four `AudioInput` methods (internal/audio/bluetooth_input.go:42, 60, 77, 82; internal/audio/alsa.go:153, 185, 206, 211) and `Equalizer` and `VolumeProcessor` carry `Process`, `Reset`, `Name` (internal/dsp/equalizer.go:42, 56, 66; internal/dsp/volume.go:28, 58, 65), but main.go holds them as concrete types (cmd/aurilis/main.go:118-127). `Name()` is called only in tests (internal/dsp/equalizer_test.go:143, internal/dsp/volume_test.go:91); `IsActive()` has no caller. No type has `StartScan` or `StopScan`; the Bluetooth manager's methods are `StartDiscovery`, `StopDiscovery` and `Devices() []DeviceInfo` (internal/bluetooth/bluez.go:345, 405, 419).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: the concrete wiring in cmd/aurilis/main.go:113-198
- Open questions: Was a registry or loader for these interfaces planned? Does any code outside the clone use them?
- Confidence: MEDIUM. INFERRED from clear code: no variable, parameter, field or assertion of these types exists.

### R4-H5-03  MediaPlayer1 interface-name constant declared and unused
- Question: H5, relates to S06.1
- What was attempted: a constant `bluezMediaPlayer = "org.bluez.MediaPlayer1"` sits beside the adapter, device and transport interface names (internal/bluetooth/bluez.go:13-21, line 18).
- Presence: DISABLED. `bluezMediaPlayer` occurs only at internal/bluetooth/bluez.go:18. A case-insensitive search for `avrcp`, `MediaPlayer1`, `MediaControl` in `*.go`, `*.js`, `*.sh` returns that line only.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: NONE recorded in the repo
- Open questions: What use of the media player interface was intended (transport control, metadata, status)?
- Confidence: MEDIUM. INFERRED from clear code: a declared constant with no use site.

### R4-H5-04  SetAdapterAlias method with no caller
- Question: H5, relates to S03.1
- What was attempted: a method that sets the adapter's `Alias` property over D-Bus, commented "sets a friendly name for the adapter" (internal/bluetooth/bluez.go:396-402).
- Presence: DISABLED. `SetAdapterAlias` occurs only at internal/bluetooth/bluez.go:396-397. No WebSocket command maps to it (command list at internal/websocket/hub.go:41-56) and no config key holds a name (internal/config/config.go:16-32).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: NONE recorded in the repo
- Open questions: Under what name does the adapter appear to a source device when this method is never called?
- Confidence: MEDIUM. INFERRED from clear code: no call site.

### R4-H5-05  GetDeviceByAddress method with no caller
- Question: H5, relates to S03.3
- What was attempted: a lookup of a known device by its Bluetooth address, case-insensitive (internal/bluetooth/bluez.go:526-539).
- Presence: DISABLED. `GetDeviceByAddress` occurs only at internal/bluetooth/bluez.go:526-527. All WebSocket device commands take a D-Bus object path (internal/websocket/hub.go:329-393).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: NONE recorded in the repo
- Open questions: What caller was it written for?
- Confidence: MEDIUM. INFERRED from clear code: no call site.

### R4-H5-06  auto_reconnect setting stored and shown, with no code that acts on it
- Question: H5, relates to S04.3
- What was attempted: a boolean setting `auto_reconnect`, default `true` (internal/config/config.go:30, 47), written by the installer (scripts/install.sh:331) and shown in the sample config (README.md:215). README.md:18 lists "**Auto-reconnect** — Retry logic for unstable Bluetooth sources".
- Presence: DISABLED. The only read of `AutoReconnect` is internal/websocket/hub.go:241, which copies it into the `config` message sent to the browser. The browser's `config` handler reads `audio_source` and `active_adapter` only (web/static/js/app.js:104-108). The one retry routine, `ReconnectDevice` (internal/bluetooth/bluez.go:478-495), runs only on the `cmd_reconnect` command (internal/websocket/hub.go:383-393), sent by a UI button (web/static/js/app.js:177-179, 495); that path does not test the setting.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: a person pressing "Reconnect" in the web UI for any reconnect attempt to start
- Open questions: Was an automatic reconnect on disconnect or at start-up written at any point? Does BlueZ's own reconnect behaviour for trusted devices cover the case the README names?
- Confidence: MEDIUM. INFERRED from clear code: every occurrence of the field was listed and none branches on it.

### R4-H5-07  data_dir setting never read by the program
- Question: H5, relates to S10.4
- What was attempted: a setting `data_dir`, default `/var/lib/aurilis` (internal/config/config.go:20, 37; README.md:194). The installer prompts for it (scripts/install.sh:249-251), creates the directory (scripts/install.sh:295), makes it the service user's home (scripts/install.sh:342), gives it to that user (scripts/install.sh:351) and lists it in `ReadWritePaths` (scripts/install.sh:389; systemd/aurilis.service:24).
- Presence: DISABLED. `DataDir` occurs in `*.go` only at internal/config/config.go:20 and 37. No Go code opens, writes or reads a path built from it.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: the config file path being passed separately by flag (cmd/aurilis/main.go:33)
- Open questions: What data was the directory meant to hold?
- Confidence: MEDIUM. INFERRED from clear code: the field has no reader.

### R4-H5-08  alsa_devices message type never sent; ALSA device list endpoint has no caller in the web UI
- Question: H5, relates to S09.2
- What was attempted: a WebSocket message type `alsa_devices` (internal/websocket/hub.go:37) and an HTTP endpoint `/api/alsa/devices` that returns the capture device list (internal/server/server.go:68-70; internal/audio/alsa.go:254-299).
- Presence: DISABLED for the message type: `MsgTypeALSADevices` and the string `alsa_devices` occur only at internal/websocket/hub.go:37. CURRENT for the endpoint: the route is registered, but `/api/` occurs in no file other than internal/server/server.go:60, 68, and web/static/index.html has no `alsa` or `select` element (search returned only line 34, a `source-selector` div). The `alsa_device` value sent at internal/websocket/hub.go:239 is not read at web/static/js/app.js:104-108.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: `alsa_device` being set by editing the config file (internal/config/config.go:23, 40); README.md:220 states "All settings are editable from the Web UI at runtime."
- Open questions: Was a line-in device chooser planned for the Settings panel (web/static/index.html:110-119)?
- Confidence: MEDIUM. INFERRED from clear code: no sender for the message type and no client for the endpoint in the tree.

### R4-H5-09  Shelf filter calculators and filter-type constants used by tests only or not at all
- Question: H5, relates to S07.1
- What was attempted: `CalculateLowShelf` and `CalculateHighShelf` coefficient functions (internal/dsp/biquad.go:74-75, 99-100) and a `BiquadType` enumeration with six values: peaking, low shelf, high shelf, low pass, high pass, band pass (internal/dsp/biquad.go:5-15).
- Presence: DISABLED. The two shelf functions are called only from internal/dsp/biquad_test.go:122 and 131. The equalizer builds every band with `CalculatePeakingEQ` (internal/dsp/equalizer.go:33, 89, 103). `BiquadType` and its six constants occur only at internal/dsp/biquad.go:5-14. No coefficient function exists for low pass, high pass or band pass.
- Outcome: UNVERIFIED for use in the product. The two shelf functions each have a unit test (internal/dsp/biquad_test.go:121, 130); no test output is in the repo.
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: the ten peaking bands in internal/config/config.go:52-65
- Open questions: Were shelf filters intended for the lowest and highest bands?
- Confidence: MEDIUM. INFERRED from clear code: call sites listed in full.

### R4-H5-10  Accessor methods and one constant with test-only callers or none
- Question: H5, relates to NONE
- What was attempted: helper methods on the config, equalizer, analyzer and ring buffer, and a description constant.
- Presence: DISABLED in the running program. Each has no non-test caller: `Config.GetVolume` (internal/config/config.go:130; test use internal/config/config_test.go:82), `Config.SetEQBand` (config.go:136; config_test.go:100), `Config.GetEQBands` (config.go:151; config_test.go:101), `Equalizer.SetBandGain` (internal/dsp/equalizer.go:73; internal/dsp/equalizer_test.go:64, test/integration_test.go:167), `Analyzer.GetSpectrum` (internal/dsp/analyzer.go:90; internal/dsp/analyzer_test.go:14, test/integration_test.go:109), `RingBuffer.Available` (internal/pipeline/ringbuffer.go:116; internal/pipeline/ringbuffer_test.go:64), `RingBuffer.Reset` (ringbuffer.go:136; ringbuffer_test.go:131), `version.Description` (internal/version/version.go:7; no use anywhere).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: the hub writing config fields directly under `cfg.Lock()` (internal/websocket/hub.go:404-410, 427-429) and calling `SetAllBands` (hub.go:403)
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from clear code: call sites listed from a search of all `*.go` files.

### R4-H5-11  Struct fields stored and never read
- Question: H5, relates to S05.2
- What was attempted: fields that record the audio format or handles but are not consulted afterwards.
- Presence: DISABLED. `BluetoothInput.sampleRate` and `.channels` are set at internal/audio/bluetooth_input.go:35-36 and read nowhere (declared lines 21-22); `BluetoothInput.fd` is set at lines 100 and 237 and read nowhere (declared line 25). `TransportInfo.FD` is declared at internal/bluetooth/bluez.go:55 and never assigned or read. The write MTU returned by `AcquireTransport` is discarded (internal/audio/bluetooth_input.go:95). `Equalizer.channels` is set at internal/dsp/equalizer.go:26 and not read (declared line 17). `Analyzer.sampleRate` is set at internal/dsp/analyzer.go:33 and not read (declared line 16). `HTTPStreamer.mu` is declared at internal/stream/http_stream.go:20 and never locked. `introXML` is marshalled at internal/bluetooth/a2dp_sink.go:89 and used only for its length in a log line (a2dp_sink.go:95).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: the Bluetooth read loop treating bytes as 16-bit samples whatever the configured rate and channel count (internal/audio/bluetooth_input.go:211-222)
- Open questions: Was the configured sample rate meant to be compared with the rate negotiated at internal/bluetooth/a2dp_sink.go:165-172?
- Confidence: MEDIUM. INFERRED from clear code: each field name was searched across `*.go`.

### R4-H5-12  Browser variable audioCtx and state flag scanning never used
- Question: H5, relates to S08.2
- What was attempted: `var audioCtx = null;` (web/static/js/app.js:9). The comment at web/static/js/app.js:365 reads "Use AudioContext for Safari/iOS compatibility"; the line after it creates an `Audio` element (app.js:366).
- Presence: DISABLED. `audioCtx` occurs only at web/static/js/app.js:9 and `AudioContext` only in the comment at line 365. `state.scanning` is written at app.js:23, 469, 478, 484 and read nowhere.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: playback through an HTML `Audio` element pointed at `/audio/stream` (web/static/js/app.js:366-393)
- Open questions: Was a Web Audio playback path written and removed before the commit? Was playback tried on Safari or iOS?
- Confidence: MEDIUM. INFERRED from clear code: the variable has one occurrence.

### R4-H5-13  Installer packages and D-Bus policy lines with no counterpart in the program
- Question: H5, relates to S10.1
- What was attempted: the installer installs `libbluetooth-dev` (scripts/install.sh:122) and `libdbus-1-dev` (scripts/install.sh:131), and writes a D-Bus policy that allows the service user to own the name `org.aurilis` (scripts/install.sh:405), lets any user send to `org.aurilis` (scripts/install.sh:414) and allows the interface `org.bluez.Profile1` (scripts/install.sh:409).
- Presence: DISABLED for these items. The only cgo directives in the tree are `-lasound`, `alsa/asoundlib.h` and `stdlib.h` (internal/audio/alsa.go:4-6); a search of `*.go` for `#include`, `#cgo`, `bluetooth/bluetooth.h` and `pkg-config` returns nothing else. D-Bus access is through the Go module at go.mod:6. A search of `*.go` for `RequestName`, `org.aurilis` and `Profile1` returns nothing; the program exports objects at the paths `/org/aurilis/a2dp_sink` and `/org/aurilis/agent` (internal/bluetooth/a2dp_sink.go:14; internal/bluetooth/pairing_agent.go:13) without requesting a bus name. The variable `OS_VERSION` is set at scripts/install.sh:37 and 41 and used only to build `OS_NAME` at line 42.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: README.md:150 lists a shorter manual package line (`bluez libasound2-dev libdbus-1-dev`)
- Open questions: Was a `Profile1`-based or C-library-based Bluetooth approach written before c98d8d3? Does the policy file as written permit the calls the program makes (Agent1 and MediaEndpoint1 method calls arrive from BlueZ, not from the service user)?
- Confidence: MEDIUM. INFERRED from clear code: searches listed above.

### R4-H5-14  Installer build-from-source switch fixed to true
- Question: H5, relates to S10.1
- What was attempted: `install_dependencies` installs Go, `build-essential` and `pkg-config` only when `BUILD_FROM_SOURCE` is `true`, under the comment "Build tools (if building from source)" (scripts/install.sh:133-146).
- Presence: DISABLED for the other branch. `main` sets `BUILD_FROM_SOURCE=true` immediately before the call (scripts/install.sh:471-472) and `build_binary` always runs (scripts/install.sh:481). `BUILD_FROM_SOURCE` occurs only at lines 134 and 471. No download or copy of a prebuilt binary exists in the script.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: a Go toolchain and C compiler on the target machine at install time (scripts/install.sh:278-280)
- Open questions: Was installation from a prebuilt binary planned?
- Confidence: MEDIUM. INFERRED from clear code: the variable is assigned once, to `true`.

---

## H6 Author's markers

### R4-H6-01  NOT FOUND
- Question: H6
- Searched: case-insensitive search for `TODO`, `FIXME`, `HACK`, `XXX`, `BUG`, `WORKAROUND` across all 34 tracked files: one hit, the letters `xxx` inside a checksum string at go.sum:2, not a comment. The same terms in commit messages (`git log --all -i -E --grep`): no match. `git ls-files` filtered case-insensitively for `note`, `change`, `todo`, `issue`, `known`, `history`, `roadmap`, `plan`, `doc`: no file. 01-map.md:40 and 01-map.md:119 record the same absence of CHANGELOG, NOTES and TODO files. `git notes list`: empty. Further case-insensitive tree search for `for now`, `temporar`, `placeholder`, `stub`, `not implemented`, `simplif`, `phase N`, `future`, `planned`, `roadmap`, `later`: hits are `Temporary()` in code at internal/audio/bluetooth_input.go:191 and "any future DSP effects" at internal/pipeline/interfaces.go:28.
- Nearest related material: README.md:259-314, a "Troubleshooting" section with six headings (README.md:261, 274, 281, 291, 297, 303). It is written as user guidance and not as a list of known defects. Commit c98d8d3 message, a ten-item feature list. Three source comments that name a fallback or a compatibility purpose are recorded under H7 (R4-H7-02, R4-H7-06, R4-H7-07). Because no marker and no notes, changelog or known-issues file exists, the H6 table has no rows.

---

## H7 Workarounds

Search coverage. Case-insensitive search of all tracked files except go.sum for `sleep`, `retry`, `retries`, `attempt`, `backoff`, `timeout`, `time.After`, `NewTicker`, `NewTimer`, `Deadline`, `RestartSec`, `delay`, `wait`, `debounce`, `setTimeout`, `setInterval`, `fallback`, `fall back`, `work around`. Version pins with comments: none found; go.mod:6-7 pin two modules with no comment, go.mod:10 carries only `// indirect`, and the installer's apt package names carry no versions (scripts/install.sh:119-145). go.mod:3 declares `go 1.24.7` while README.md:86 states "Go 1.21+ (build only)". Special cases for a named device in code: none found (search for `iphone`, `android`, `samsung`, `pixel`, `macos`, `windows`, `raspberry`, `safari`, `ios`, `chrome`, `firefox`, `realtek`, `broadcom`, `intel`, `csr`); hits are README.md:5, 108, 112, 301 and the comment at web/static/js/app.js:365 (R4-H5-12). Timers seen and given no record of their own because no problem is attached to them in the repo: toast removal after 5000 ms (web/static/js/app.js:443-445), visualization broadcast every 50 ms (internal/websocket/hub.go:274), HTTP shutdown limit of 5 s (internal/server/server.go:96), volume ramp step (internal/dsp/volume.go:9, 23).

### R4-H7-01  Reconnect routine: trust, then three connect attempts with 1 s, 2 s and 4 s sleeps
- Question: H7, relates to S04.3
- What was attempted: `ReconnectDevice` sets the device trusted, then calls `Connect` up to three times, sleeping `1<<attempt` seconds after each failure, including the third (internal/bluetooth/bluez.go:478-495). Its comment: "It will trust the device and retry connection with backoff." (bluez.go:479). It runs in a goroutine on the `cmd_reconnect` WebSocket command (internal/websocket/hub.go:383-393), sent by the "Reconnect" button shown for paired, unconnected devices (web/static/js/app.js:177-179, 495).
- Presence: CURRENT (internal/bluetooth/bluez.go:480; internal/websocket/hub.go:390)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: the problem named at README.md:18, "Retry logic for unstable Bluetooth sources"; a person pressing the button (R4-H5-06); no test file exists in internal/bluetooth (01-map.md:42)
- Open questions: Which source devices, if any, showed the instability? Was the routine ever run against a real device?
- Confidence: MEDIUM. INFERRED from clear code; the problem it addresses is STATED at README.md:18 only in general terms.

### R4-H7-02  Transport acquisition falls back from Acquire to TryAcquire
- Question: H7, relates to S05.1
- What was attempted: `AcquireTransport` calls `MediaTransport1.Acquire`; on any error it calls `TryAcquire`, under the comment "Try TryAcquire as fallback" (internal/bluetooth/bluez.go:503-510).
- Presence: CURRENT (internal/bluetooth/bluez.go:506)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. The comment names no failure that the fallback answers.
- Depends on: BlueZ `MediaTransport1` (interface name at internal/bluetooth/bluez.go:17); called from internal/audio/bluetooth_input.go:95
- Open questions: Under what condition did `Acquire` fail, if it ever did? Is the first error logged anywhere (it is overwritten at bluez.go:506)?
- Confidence: MEDIUM. INFERRED from clear code.

### R4-H7-03  Read loop skips 13 bytes when a chunk begins with 0x80
- Question: H7, relates to S05.2
- What was attempted: after each read from the transport descriptor, if more than 13 bytes arrived and the first byte is 0x80, the first 13 bytes are skipped; the remainder is read as 16-bit little-endian samples (internal/audio/bluetooth_input.go:204-220). Comment: "A2DP transport may include a 13-byte media packet header (RTP). Skip the RTP header if present." (bluetooth_input.go:204-205).
- Presence: CURRENT (internal/audio/bluetooth_input.go:207-209)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: the statement at internal/audio/bluetooth_input.go:165, "The transport FD provides decoded PCM data in S16_LE format."; the statement at bluetooth_input.go:16 that the data is SBC-encoded and decoded by this type; no decoder is present in the tree (01-map.md:93)
- Open questions: What does the descriptor deliver on a real BlueZ system, encoded frames or PCM? What happens to a chunk whose first byte is 0x80 for another reason? Was the 13-byte figure taken from an observed capture?
- Confidence: LOW. INFERRED from code whose own comments disagree about the data format.

### R4-H7-04  Read loop retries on a temporary network error and detects closure by error text
- Question: H7, relates to S05.4
- What was attempted: on a read error that is a `*net.OpError` reporting `Temporary()`, the loop continues; otherwise it logs and exits unless the error matches one of two fixed strings or `os.ErrClosed` (internal/audio/bluetooth_input.go:189-198, 240-247). A zero-byte read also continues (bluetooth_input.go:200-202).
- Presence: CURRENT (internal/audio/bluetooth_input.go:191-193)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: the descriptor being wrapped with `os.NewFile` (internal/audio/bluetooth_input.go:102); the exact error strings "read/write on closed pipe" and "use of closed file" (bluetooth_input.go:244-245)
- Open questions: After the loop exits on an error, what restarts capture (the next transport event at bluetooth_input.go:127-147)?
- Confidence: MEDIUM. INFERRED from clear code.

### R4-H7-05  ALSA capture re-prepares or recovers the device and reads once more after a failed read
- Question: H7, relates to S05.4
- What was attempted: the C helper `readFrames` calls `snd_pcm_readi`; on `-EPIPE` it calls `snd_pcm_prepare` and reads again; on any other negative result it calls `snd_pcm_recover` and reads again (internal/audio/alsa.go:49-60). The Go loop skips the iteration when the result is zero or negative (alsa.go:238-241).
- Presence: CURRENT (internal/audio/alsa.go:52-58)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No comment names the condition handled.
- Depends on: the line-in source being selected (`audio_source` = `linein`, cmd/aurilis/main.go:153-156); ALSA `libasound` (internal/audio/alsa.go:4-5)
- Open questions: Does a persistent failure leave the capture loop spinning (alsa.go:223-241 has no pause)?
- Confidence: MEDIUM. INFERRED from clear code.

### R4-H7-06  C struct wrapper around the ALSA hint list, for a stated cgo pointer problem
- Question: H7, relates to NONE
- What was attempted: the device hint array is held in a C struct `hint_list_t` with accessor functions, so that Go code never handles the `void**` directly (internal/audio/alsa.go:70-108; used at alsa.go:255-299).
- Presence: CURRENT (internal/audio/alsa.go:72-75)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: the problem named at internal/audio/alsa.go:71, "We use a struct to wrap the void** to avoid CGo void pointer issues."; cgo and a C compiler at build time (README.md:87)
- Open questions: What was the specific cgo failure? Did the file compile on each architecture named at scripts/install.sh:62-82?
- Confidence: MEDIUM. STATED purpose in a comment; no build artefact in the repo.

### R4-H7-07  Endless WAV stream with a fixed maximum length in the header
- Question: H7, relates to S08.2
- What was attempted: `/audio/stream` sends a 44-byte WAV header whose data size is 0x7FFFFFFF, then PCM without end (internal/stream/http_stream.go:58-70, 153-176). Comments: "Using a very large data size to indicate continuous streaming" (http_stream.go:59) and "encodes to WAV format for maximum browser compatibility (avoids autoplay blocking issues)" (http_stream.go:17-18). The handler also sets `Transfer-Encoding: chunked` by hand (http_stream.go:64).
- Presence: CURRENT (internal/stream/http_stream.go:158)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: a browser `Audio` element as the player (web/static/js/app.js:366); HTTP server read and write timeouts set to 0 (internal/server/server.go:78-79); `bit_depth` 16 or 24 (internal/stream/http_stream.go:179-188)
- Open questions: Which browsers was the stream played in? What happens when the declared length (about 2 GiB) is reached? How does WAV relate to the autoplay behaviour named in the comment, given the catch at web/static/js/app.js:389-393?
- Confidence: MEDIUM. STATED purpose in comments; no result recorded.

### R4-H7-08  Stream handlers poll the ring buffer on a 50 ms timer
- Question: H7, relates to S07.2
- What was attempted: both stream handlers wake every 50 ms and read up to one tenth of a second of samples, commented "100ms chunks" (internal/stream/http_stream.go:73-75, 83-87, 124-126, 133-137). A tick with nothing to read is skipped.
- Presence: CURRENT (internal/stream/http_stream.go:75, 126)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No comment gives a reason for the interval.
- Depends on: ring buffer sized at `buffer_size_ms` times 10 (cmd/aurilis/main.go:114; default 100 at internal/config/config.go:48); the skip-ahead rule for a slow reader (internal/pipeline/ringbuffer.go:95-99)
- Open questions: Was any latency or dropout figure measured with this interval? README.md:297-299 names "Audio latency/dropouts" and the buffer size setting without a figure.
- Confidence: MEDIUM. INFERRED from clear code.

### R4-H7-09  WebSocket keep-alive: 30 s ping, 60 s read deadline, 10 s write deadline
- Question: H7, relates to S09.4
- What was attempted: each client connection gets a 60 s read deadline renewed on every pong (internal/websocket/hub.go:510-514), a ping every 30 s (hub.go:532, 564-568) and a 10 s deadline on each write (hub.go:541, 565). A client whose send queue is full is dropped (hub.go:180-187).
- Presence: CURRENT (internal/websocket/hub.go:510, 532)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: gorilla/websocket v1.5.3 (go.mod:7)
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from clear code.

### R4-H7-10  Browser reconnects its WebSocket 3 s after every close
- Question: H7, relates to S09.4
- What was attempted: the `onclose` handler marks the UI disconnected and schedules `connectWebSocket` after 3000 ms, without a limit on the number of tries (web/static/js/app.js:41-45).
- Presence: CURRENT (web/static/js/app.js:44)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: the page staying open in the browser
- Open questions: Does an open audio element keep playing or stop while the control socket is down (app.js:359-394 is independent of the socket)?
- Confidence: MEDIUM. INFERRED from clear code.

### R4-H7-11  Scan button re-enabled by a fixed 12 s timer
- Question: H7, relates to S09.2
- What was attempted: pressing "Scan" sends `cmd_scan`, labels the button "Scanning..." and disables it; a timer restores it after 12000 ms (web/static/js/app.js:468-481). The server side starts discovery and has no matching stop timer (internal/websocket/hub.go:319-322; internal/bluetooth/bluez.go:405-416); discovery stops on `cmd_stop_scan` (hub.go:324-327).
- Presence: CURRENT (web/static/js/app.js:475-479)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: the adapter's `Discovering` property being carried in the `adapters` message (internal/bluetooth/bluez.go:29, 641-643) and not being read by the button logic
- Open questions: Does discovery continue after the button resets, until "Stop" is pressed?
- Confidence: MEDIUM. INFERRED from clear code.

### R4-H7-12  systemd restart policy: on failure, after 5 s, at most 3 starts in 60 s
- Question: H7, relates to S10.2
- What was attempted: `Restart=on-failure`, `RestartSec=5`, `StartLimitIntervalSec=60`, `StartLimitBurst=3` in the tracked unit (systemd/aurilis.service:13-16) and in the unit the installer writes (scripts/install.sh:377-380).
- Presence: CURRENT (systemd/aurilis.service:13-14)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: start-up exiting through `log.Fatalf` when the Bluetooth manager or HTTP server cannot start (cmd/aurilis/main.go:59, 63, 192, 196; internal/server/server.go:87); README.md:19 "Production deployment with automatic restart"
- Open questions: Does the unit stay stopped after three failed starts when BlueZ comes up late at boot?
- Confidence: MEDIUM. INFERRED from clear configuration.

### R4-H7-13  Installer falls back to a snap package when Go is absent from apt
- Question: H7, relates to S10.1
- What was attempted: if installing the `golang` apt package fails, the installer logs "Go not found in packages, checking snap..." and runs `snap install go --classic` when `snap` exists; otherwise it prints a download address and marks the run failed (scripts/install.sh:135-143).
- Presence: CURRENT (scripts/install.sh:136-138)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: go.mod:3 declaring `go 1.24.7`; README.md:86 stating "Go 1.21+ (build only)"; the distro list at scripts/install.sh:50-59
- Open questions: Which Go version does each listed distro's `golang` package supply, and does it meet go.mod:3?
- Confidence: MEDIUM. INFERRED from clear code.

### R4-H7-14  Documentation note specific to the Raspberry Pi built-in adapter
- Question: H7, relates to S02.2
- What was attempted: under "Audio latency/dropouts" the README lists three measures; the third reads "On Raspberry Pi, use a USB Bluetooth adapter instead of built-in" (README.md:297-301). No code treats a Raspberry Pi or any adapter model differently (search terms in the coverage note above).
- Presence: CURRENT (README.md:301)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. The README gives no observation behind the note.
- Depends on: Raspberry Pi models named at README.md:108 and 112; adapter choice by config name or first found (cmd/aurilis/main.go:68-80)
- Open questions: Was the product run on a Raspberry Pi? Was a dropout observed with a built-in adapter?
- Confidence: LOW. STATED as advice only; no test record, log or measurement is in the repo.

---

## H8 Final state (R4-H8-01)

Subjects of the last ten commits. Only three commits exist (`git log --all`), newest first:

1. d740bc2, 2026-02-23: "Merge pull request #1 from tunlezah/claude/a2dp-sink-bluetooth-Oj57l"
2. c98d8d3, 2026-02-22: "Phase 1: A2DP Sink Bluetooth Audio System v1.0.0"
3. 7482a74, 2026-02-22: "Create README.md"

Reasons given in commit messages: 7482a74 UNKNOWN (subject only). c98d8d3 gives a description, "Complete implementation of the Aurilis A2DP Sink system with:" followed by ten items, and no reason for any choice. d740bc2 repeats the subject of c98d8d3 as its body and gives no reason.

Uncommitted changes listed in the manifest: none. 00-manifest.md:25 records `git status --porcelain` as "empty" for R4; 01-map.md:5 records the same; the command was empty again at the start and end of this audit. `git status --porcelain --ignored` is also empty. Stash count 0 (00-manifest.md:25).

Whether the repo was left mid-change:

- No direct sign of an interrupted change is in the clone: the tree is clean, there is no stash, the last commit is a completed merge, no commit subject carries a work-in-progress term (commit-message search under Method), and no marker comment exists (R4-H6-01). Grade: INFERRED from the absence of those artefacts.
- The last content commit is labelled "Phase 1" in its subject (c98d8d3). The word "phase" appears nowhere in the tracked files (case-insensitive search for `phase` across the tree, excluding `.git`: no hit); no later phase is named in the clone.
- The same commit calls itself a "Complete implementation" and README.md:5 calls the product "production-grade". Items declared, configured or documented in that commit with no acting code are listed at R4-H5-01 to R4-H5-14.
- A branch `claude/go-cross-platform-action-VZiQD` exists on the remote at a commit that is not in the clone (R4-H4-01). Whether it holds later work is not known.
- No commit follows 2026-02-23 in the clone (R4-H1-01).

---

BLOCKED: the content of remote branch `claude/go-cross-platform-action-VZiQD` (tip 006d973151bd00505c9ea0747c429291a1546230) could not be read. The commit object is not in the clone and Rule 1 does not permit a fetch. H4 and H8 are answered for the clone plus the remote ref list only.

## Tally

- Questions answered with findings: 5 (H1, H4, H5, H7, H8)
- Questions answered NOT FOUND: 3 (H2, H3, H6)
- Total: 8

Record count: 29 finding records (R4-H4-01; R4-H5-01 to R4-H5-14; R4-H7-01 to R4-H7-14), 2 factual lists (R4-H1-01, R4-H8-01), 3 NOT FOUND records (R4-H2-01, R4-H3-01, R4-H6-01).
