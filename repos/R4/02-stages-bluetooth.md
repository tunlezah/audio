# R4 stages S01 to S06 (Bluetooth), Aurilis

- Repo path: /home/user/Aurilis
- HEAD when audited: d740bc2b39836a0af00d8ad35144d31511bc9060 (branch `claude/inspiring-galileo-a9dlia`, same commit as `main`). `git status --porcelain` printed nothing.
- Audited 2026-11-17 with read-only commands only. Nothing was built, installed or run.
- All paths are relative to the repo root. "Search" means `git grep` over the tracked tree, case-insensitive unless stated, with `go.sum` left out.
- History facts used throughout: all 34 files arrived in one commit, c98d8d3 ("Phase 1: A2DP Sink Bluetooth Audio System v1.0.0"), merged by d740bc2. No other branch content, no tag and no stash exist (`git branch -a`, `git tag`, `git stash list`). Every Presence value of CURRENT below means "in `main` at d740bc2".
- Result evidence available in the repo: none for the Bluetooth path. There is no log file, test output file, CI result or measurement file in the tree (01-map.md section 1). No test file refers to the Bluetooth code (search `bluetooth|bluez|a2dp|transport|pair|adapter` in `*_test.go`: no hits; test/integration_test.go:6-9 imports config, dsp, pipeline and version only). The message of commit c98d8d3 states "43 passing tests (unit + integration)"; `^func Test` occurs 54 times across the seven test files. No source device is named anywhere (search `iphone|android|pixel|samsung|macbook|windows`: no hits).

---

## S01 Platform and stack

### R4-S01-01  Stated target: any Linux machine, Raspberry Pi named
- Question: S01.1
- What was attempted: the README names the target as "any Linux machine (including Raspberry Pi)" (README.md:5) and lists the hardware as "Linux system (x86_64 or ARM/ARM64)" (README.md:78). Cross-compile comments name "Raspberry Pi 4/5" for arm64 and "Raspberry Pi 3" for ARMv7 (README.md:108, 112). No OS release or kernel version is named in the README (search `kernel`: no hits).
- Presence: CURRENT (README.md:5, 78, 108, 112)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No record of a run on any named machine exists in the repo.
- Depends on: Linux with systemd, BlueZ and D-Bus (README.md:82-87; systemd/aurilis.service:4-6).
- Open questions: On which machine, if any, was the program run? Which OS release was in use?
- Confidence: MEDIUM. STATED targets only; no run evidence.

### R4-S01-02  Installer platform gate: Debian-family distributions and three CPU families
- Question: S01.1
- What was attempted: `detect_os` reads `/etc/os-release`, accepts the IDs ubuntu, debian, raspbian, linuxmint and pop, and exits for any other (scripts/install.sh:33-60). `OS_VERSION` is captured (scripts/install.sh:37, 41) and is used for no check; its only other use builds the display name (scripts/install.sh:42). `detect_arch` accepts x86_64/amd64, aarch64/arm64 and armv7l/armv6l/arm, and exits otherwise (scripts/install.sh:62-82). `apt-get` is the only package manager (scripts/install.sh:52).
- Presence: CURRENT (scripts/install.sh:33-82)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No installer run is recorded.
- Depends on: `/etc/os-release` or `/etc/debian_version` being present (scripts/install.sh:34, 39); root privileges (scripts/install.sh:26-31).
- Open questions: Was the installer ever run to completion on any of the five listed distributions?
- Confidence: MEDIUM. INFERRED from clear script code.

### R4-S01-03  Go daemon with cgo, bash installer, framework-free web page
- Question: S01.2
- What was attempted: one Go program (cmd/aurilis/main.go:26). `go.mod` declares `go 1.24.7` (go.mod:3); the README states "Go 1.21+ (build only)" (README.md:86). cgo is used for ALSA (internal/audio/alsa.go:3-6) and builds set `CGO_ENABLED=1` (README.md:99; scripts/install.sh:278). HTTP uses the Go standard library (internal/server/server.go:9, 74-86). WebSocket uses gorilla/websocket (internal/websocket/hub.go:10). D-Bus uses godbus (internal/bluetooth/bluez.go:10). The web page is static HTML, CSS and JavaScript with no external script (web/static/index.html:9, 140), embedded with `go:embed` (web/embed.go:5). The installer is bash (scripts/install.sh:1-2).
- Presence: CURRENT (citations above)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No build log or binary is in the repo (binary names are ignored at .gitignore:1-2).
- Depends on: a Go toolchain satisfying go.mod:3; a C compiler and ALSA headers for cgo (README.md:87; scripts/install.sh:125, 144).
- Open questions: Which Go version was used by the author? Did the packages that hold the Bluetooth code ever compile, given that no test imports them (test/integration_test.go:6-9)?
- Confidence: MEDIUM. INFERRED from clear manifests and imports; the README and go.mod give different Go versions.

### R4-S01-04  BlueZ driven directly over the system D-Bus
- Question: S01.3
- What was attempted: the daemon talks to `org.bluez` on the system bus (internal/bluetooth/bluez.go:14, 94). The README states "Native BlueZ D-Bus integration — Direct D-Bus API calls, no shell wrappers" (README.md:10) and names the version only as "BlueZ 5.x (Bluetooth stack)" (README.md:83). The apt package `bluez` is installed without a version (scripts/install.sh:119; README.md:150). The client library is `github.com/godbus/dbus/v5 v5.2.2` (go.mod:6). Interfaces used: `Adapter1`, `Device1`, `MediaTransport1` (bluez.go:15-17), `ObjectManager` and `Properties` (bluez.go:19-20), `Media1` and `MediaEndpoint1` (internal/bluetooth/a2dp_sink.go:49, 123), `AgentManager1` and `Agent1` (internal/bluetooth/pairing_agent.go:11, 32, 38). A constant for `MediaPlayer1` is declared and has no other reference (bluez.go:18; search `bluezMediaPlayer`). The installer installs `libbluetooth-dev` (scripts/install.sh:122); no source file includes `bluetooth.h` (search `bluetooth/bluetooth\.h`: install.sh:122 only).
- Presence: CURRENT (citations above)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No run against a BlueZ daemon is recorded.
- Depends on: `bluetoothd` running (systemd/aurilis.service:4-5; scripts/install.sh:163-173); the D-Bus policy file written by the installer (scripts/install.sh:399-417); godbus v5.2.2.
- Open questions: Which BlueZ minor version was targeted? Was another owner of A2DP endpoints (a sound server) present on the author's machine?
- Confidence: MEDIUM. STATED stack plus code that agrees on the interfaces; version stated only as "5.x".

### R4-S01-05  No sound server on the Bluetooth path; ALSA library used for line-in capture only
- Question: S01.4
- What was attempted: ALSA is linked through cgo (`-lasound`, internal/audio/alsa.go:4-5) and opened for capture (internal/audio/alsa.go:13). No playback open exists (01-map.md section 3, hop 27). Bluetooth audio goes from a file descriptor into an in-process ring buffer (internal/audio/bluetooth_input.go:189-222) and from there to an HTTP response (internal/stream/http_stream.go:84-104). The README lists "ALSA (libasound2)" with no version (README.md:84). `alsa-utils` is installed when `aplay` is absent (scripts/install.sh:192-196). Search `pipewire|pulseaudio|bluealsa|bluez-alsa|wireplumber|libsbc|gstreamer|ffmpeg`: no hits.
- Presence: CURRENT (citations above)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No run is recorded.
- Depends on: `libasound2-dev` at build time (scripts/install.sh:125); the design choice to stream over HTTP (README.md:13).
- Open questions: Which ALSA library version was linked? Was a sound server running beside the daemon on the author's machine?
- Confidence: MEDIUM. INFERRED from clear code and a search with no hits.

### R4-S01-06  Other external dependencies
- Question: S01.5
- What was attempted: Go modules `github.com/gorilla/websocket v1.5.3` (go.mod:7) and `golang.org/x/sys v0.27.0 // indirect` (go.mod:10), with checksums (go.sum:1-6). apt packages without versions: `dbus` (scripts/install.sh:128), `libdbus-1-dev` (131), `golang` with a snap fallback (135-138), `build-essential` (144), `pkg-config` (145). Tools probed and used when present: `hciconfig` (176-178), `ss` or `netstat` (209-216). systemd is used for the service (scripts/install.sh:364-420). No source file includes `dbus.h`; `libdbus-1-dev` is checked through `pkg-config` only (scripts/install.sh:131; search `dbus\.h`).
- Presence: CURRENT (citations above)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No install or build record exists.
- Depends on: network access to apt and the Go module proxy at install time (scripts/install.sh:114, 278-280).
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from manifests; only the Go modules are pinned.

---

## S02 Adapter and controller

### R4-S02-01  Adapter enumerated through GetManagedObjects, chosen by configured name with first-found fallback, powered on at start
- Question: S02.1
- What was attempted: `loadManagedObjects` calls `GetManagedObjects` on `org.bluez` and stores every object with `Adapter1` (internal/bluetooth/bluez.go:149-167); the first adapter seen becomes the active one (bluez.go:164-166). `main` then compares the base name of each adapter path with `cfg.BluetoothAdapter` (cmd/aurilis/main.go:71-76), default `hci0` (internal/config/config.go:38; scripts/install.sh:311). With no match it takes `adapters[0]` (main.go:77-79); that list is built by ranging over a Go map (bluez.go:337-340). `SetActiveAdapter` records the choice (main.go:80; bluez.go:369-379). `Powered` is set to true through `Properties.Set` (main.go:85; bluez.go:389-394); a failure is logged as a warning and start-up continues (main.go:86-87). No other adapter property is written at start-up (see R4-S03-01).
- Presence: CURRENT (cmd/aurilis/main.go:67-92)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No run is recorded.
- Depends on: BlueZ exposing the adapter before the process starts; the `bluetooth_adapter` config value; D-Bus permission to set `Adapter1` properties (scripts/install.sh:404-412).
- Open questions: With two adapters and no name match, which one is taken, given the map-ordered list?
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S02-02  Adapter switch at run time from the web page
- Question: S02.1
- What was attempted: clicking an adapter row sends `cmd_set_adapter` (web/static/js/app.js:200, 497-499). The hub calls `SetActiveAdapter` and broadcasts a status message (internal/websocket/hub.go:462-471). `SetActiveAdapter` changes only the manager's `activeAdapter` field (bluez.go:369-379). That field is read by `StartDiscovery` (bluez.go:405-415), `StopDiscovery` (419-429), `RemoveDevice` (464-475), endpoint `Register` (internal/bluetooth/a2dp_sink.go:99) and `Unregister` (a2dp_sink.go:135). `Register` has one call site, in `main` (main.go:140). INFERRED: after a switch the A2DP endpoint stays on the adapter that was active at start, because the handler holds no registration call (hub.go:462-471). The handler does not power the new adapter and does not write `cfg.BluetoothAdapter` (hub.go:462-471), so "Save Configuration" (hub.go:489-492) stores the earlier name.
- Presence: CURRENT (internal/websocket/hub.go:462-471)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No run is recorded.
- Depends on: a second adapter being present; a WebSocket client.
- Open questions: Was the switch ever used with two adapters? What did a source device see after a switch?
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S02-03  Adapter power toggle at run time from the web page
- Question: S02.1
- What was attempted: each adapter row has a "Power On" / "Power Off" button (web/static/js/app.js:206-208) that sends `cmd_power_adapter` (app.js:501-503). The hub calls `SetAdapterPowered` with the given path and value (internal/websocket/hub.go:473-484; bluez.go:389-394). The new state returns through `PropertiesChanged` (bluez.go:311-315, 637-647) and is broadcast as the adapter list (hub.go:260-263).
- Presence: CURRENT (internal/websocket/hub.go:473-484)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No run is recorded.
- Depends on: D-Bus permission to set `Powered`; the event reaching the hub's reader and not the other reader of the same channel (see R4-S04-05).
- Open questions: What happens to a registered endpoint and an acquired transport when the adapter is powered off from the page?
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S02-04  Default adapter name hci0; no model, chipset or firmware named
- Question: S02.2
- What was attempted: the adapter name defaults to `hci0` in the code (internal/config/config.go:38), in the installer-written config (scripts/install.sh:311) and in the README sample (README.md:195). The README hardware line reads "Bluetooth adapter (USB or built-in)" (README.md:79). No chipset, model or firmware is named (search `chipset|firmware|broadcom|realtek|csr|dongle`: no hits). Several adapters are held in a map and listed on the page (bluez.go:83, 333-342; web/static/js/app.js:187-216); one is active at a time (bluez.go:86). The installer counts adapters with `hciconfig` when that tool exists (scripts/install.sh:176-186).
- Presence: CURRENT (citations above)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No adapter is named as tested.
- Depends on: an adapter that BlueZ presents as `Adapter1`.
- Open questions: Which adapter did the author have, if any?
- Confidence: MEDIUM. INFERRED from code and a search with no hits.

### R4-S02-05  README statement about the Raspberry Pi built-in adapter
- Question: S02.2
- What was attempted: under "Audio latency/dropouts" the README lists "On Raspberry Pi, use a USB Bluetooth adapter instead of built-in" (README.md:301), next to "Reduce WiFi/Bluetooth interference" (README.md:300).
- Presence: CURRENT (README.md:297-301)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. The README gives no reason, test or measurement for the statement.
- Depends on: nothing recorded. No Raspberry Pi model, adapter or result appears with the statement.
- Open questions: Was a dropout observed on a Raspberry Pi built-in adapter, or was the line written without a test?
- Confidence: LOW. STATED advice with no supporting artefact and no code that relates to it.

### R4-S02-06  No adapter at start-up: warning, start-up continues, endpoint registration fails once
- Question: S02.3
- What was attempted: with an empty adapter list `main` logs "Warning: no Bluetooth adapters found" and continues (cmd/aurilis/main.go:69, 90-92). The pairing agent is still registered (main.go:95-105). Endpoint `Register` returns "no active bluetooth adapter" (internal/bluetooth/a2dp_sink.go:99-102), which `main` logs as a warning (main.go:140-141). No retry exists (search `time\.Sleep|attempt` in `*.go`: bluez.go:486-494 only). The web server still starts (main.go:190-198). The installer prints "No Bluetooth adapters detected. The system will start but Bluetooth features will not work until an adapter is connected." (scripts/install.sh:182).
- Presence: CURRENT (cmd/aurilis/main.go:90-92, 140-141)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No run is recorded.
- Depends on: the adapter being present before the process starts (see R4-S02-07 for a later arrival).
- Open questions: Does connecting an adapter after start lead to a working sink without a process restart? The code located holds no path for it.
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S02-07  Adapter appearing or disappearing while the daemon runs
- Question: S02.3
- What was attempted: an `InterfacesAdded` signal carrying `Adapter1` adds the adapter to the map and emits `EventAdapterChanged` (internal/bluetooth/bluez.go:245-249), which the hub turns into an adapter-list broadcast (internal/websocket/hub.go:260-263). That branch does not set the active adapter, does not power the adapter and does not register the endpoint (bluez.go:245-249). `handleInterfacesRemoved` handles `Device1` and `MediaTransport1` only (bluez.go:273-285), so a removed adapter stays in the map and stays active (INFERRED: no delete of `m.adapters` exists in the file). The match rules cover `ObjectManager` and `PropertiesChanged` signals only (bluez.go:183-186); search `NameOwnerChanged`: no hits. Search `rfkill`: no hits.
- Presence: CURRENT (internal/bluetooth/bluez.go:224-287)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No run is recorded.
- Depends on: BlueZ emitting `InterfacesAdded` for a new adapter.
- Open questions: What does the daemon do after an adapter reset or after `bluetoothd` restarts while it is running? Is a blocked (rfkill) adapter reported in any way beyond the power-on warning at main.go:86?
- Confidence: MEDIUM. INFERRED from clear code and searches with no hits.

### R4-S02-08  BlueZ or D-Bus unavailable at start: fatal exit, restart by systemd
- Question: S02.3
- What was attempted: a failed system-bus connection ends the process with `log.Fatalf` (cmd/aurilis/main.go:57-60; bluez.go:94-97). A failed `GetManagedObjects` does the same (main.go:62-64; bluez.go:124-126, 152-155). The unit sets `Restart=on-failure`, `RestartSec=5`, `StartLimitIntervalSec=60` and `StartLimitBurst=3` (systemd/aurilis.service:13-16; scripts/install.sh:377-380), and orders the service after `bluetooth.service` with `Wants=bluetooth.service` and `Requires=dbus.service` (systemd/aurilis.service:4-6).
- Presence: CURRENT (citations above)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No service log is in the repo.
- Depends on: the systemd unit being installed; a manual start (README.md:102) has no restart.
- Open questions: After three failed starts within 60 seconds, was the service ever seen to stay down?
- Confidence: MEDIUM. INFERRED from clear code and unit text.

### R4-S02-09  Documented manual checks for a missing adapter
- Question: S02.3
- What was attempted: the README section "Bluetooth adapter not found" lists `sudo systemctl status bluetooth`, `hciconfig -a` and `sudo hciconfig hci0 up` (README.md:261-272). The installer probes for `hciconfig` and does not install it (scripts/install.sh:176, 185).
- Presence: CURRENT (README.md:261-272)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No use of the commands is recorded.
- Depends on: `hciconfig` being installed on the host; the adapter being named `hci0`.
- Open questions: NONE
- Confidence: MEDIUM. STATED procedure only.

---

## S03 Discoverability, pairing and trust

### R4-S03-01  No discoverable or pairable setting in the repo; adapter alias setter present with no caller
- Question: S03.1
- What was attempted: search `discoverable|pairable` over the whole tree: no hits. No BlueZ `main.conf` is shipped or edited (search `main\.conf|/etc/bluetooth`: no hits). No `bluetoothctl`, `btmgmt` or `hcitool` call exists (search: no hits); `hciconfig` appears only for an adapter count and in README troubleshooting (scripts/install.sh:176-178; README.md:268-271). No timeout for visibility exists (search `Timeout`: HTTP server and browser timers only, internal/server/server.go:78-80, 96; web/static/js/app.js:44, 443, 475). No device class is set (search `class`: HTML/CSS classes and scripts/install.sh:138 only). For the name, `SetAdapterAlias` writes the adapter `Alias` property (internal/bluetooth/bluez.go:396-402) and has no caller (search `SetAdapterAlias` in `*.go`, `*.js`: definition only). The only adapter property written at start-up is `Powered` (cmd/aurilis/main.go:85).
- Presence: DISABLED for the alias setter, which is unreachable (bluez.go:396-402). Nothing else for this question is present.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No pairing session is recorded.
- Depends on: whatever `Discoverable`, `Pairable`, alias and class state BlueZ holds on the host. The repo does not set them.
- Open questions: How did a source device find this device in the author's use, if it did? Under what name and class did it appear, and for how long?
- Confidence: MEDIUM. INFERRED from searches with no hits and one function with no caller.

### R4-S03-02  Reverse direction: this device scans for source devices from the web page
- Question: S03.1
- What was attempted: the Bluetooth page has "Scan" and "Stop" buttons (web/static/index.html:82-83). They send `cmd_scan` and `cmd_stop_scan` (web/static/js/app.js:468-488), which call `Adapter1.StartDiscovery` and `StopDiscovery` on the active adapter (internal/websocket/hub.go:319-327; internal/bluetooth/bluez.go:404-430). Found devices arrive through `InterfacesAdded` (bluez.go:240-244) and are listed on the page (hub.go:255-258; app.js:141-185). The page re-enables the Scan button after 12 seconds (app.js:475-479); INFERRED: discovery itself is not stopped at that moment, because the timer body only resets the button text and flag (app.js:476-478).
- Presence: CURRENT (internal/websocket/hub.go:319-327)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No scan result is recorded.
- Depends on: an active adapter (bluez.go:410-412); a browser connected to the page; the source device being visible to a scan (INFERRED from the use of `StartDiscovery`).
- Open questions: Was a phone ever found by this scan? Does discovery keep running after the 12-second timer until "Stop" is pressed?
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S03-03  Documented pairing flow
- Question: S03.1
- What was attempted: the installer summary prints "Pair your phone/laptop via Bluetooth, then open the Web UI to start streaming." (scripts/install.sh:450-451). The README lists "Automatic Pairing — Headless pairing agent for seamless device connections" (README.md:17), describes the Bluetooth page as "Adapter management, device scanning, pair/trust/connect/disconnect" (README.md:238) and, under "No audio from Bluetooth device", lists "Ensure the device is paired and connected (check Web UI Bluetooth page)" (README.md:276). No document states from which side pairing starts, the name shown to the phone, or any visibility period.
- Presence: CURRENT (citations above)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No pairing session is recorded.
- Depends on: host BlueZ visibility state (R4-S03-01) or the page-driven scan (R4-S03-02).
- Open questions: Which of the two directions did the author expect a user to follow?
- Confidence: MEDIUM. STATED instructions only.

### R4-S03-04  Default pairing agent with NoInputNoOutput capability that accepts every request
- Question: S03.2
- What was attempted: `NewPairingAgent` exports an `Agent1` object at `/org/aurilis/agent` (internal/bluetooth/pairing_agent.go:13, 26), registers it with capability `NoInputNoOutput` (pairing_agent.go:32-33) and requests default-agent status (pairing_agent.go:38-39). `RequestConfirmation`, `RequestAuthorization` and `AuthorizeService` return success for any device and any UUID (pairing_agent.go:86-101). `RequestPinCode` returns a fixed legacy PIN constant (pairing_agent.go:62-65; value not copied) and `RequestPasskey` returns a fixed numeric constant (pairing_agent.go:74-77; value not copied). The display methods only log (pairing_agent.go:68-71, 80-83). Every request is logged with the device path (pairing_agent.go:63, 69, 75, 81, 87, 93, 99). The type comment reads "It accepts all pairing requests to function as a headless A2DP Sink." (pairing_agent.go:17). A registration failure is logged as a warning and start-up continues (cmd/aurilis/main.go:99-101). The message of commit c98d8d3 lists "Automatic pairing agent for headless Bluetooth connections" as content of the commit.
- Presence: CURRENT (cmd/aurilis/main.go:94-105)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. The commit message and README describe the feature; neither reports a pairing result.
- Depends on: the D-Bus policy file, written only when the systemd option is chosen (scripts/install.sh:357-360, 399-417) and absent from the README manual steps (README.md:148-167); the service user being in group `bluetooth` (scripts/install.sh:347); or a root start (README.md:102).
- Open questions: Was pairing ever completed from a phone? Is the policy at scripts/install.sh:404-412 sufficient for `bluetoothd` to reach the agent of the `aurilis` user?
- Confidence: MEDIUM. STATED design in a comment, with code that agrees; no result evidence.

### R4-S03-05  Pairing started from this device through the web page
- Question: S03.2
- What was attempted: an unpaired device in the list gets a "Pair" button (web/static/js/app.js:165-167) that sends `cmd_pair` (app.js:490). The hub calls `Device1.Pair` in a goroutine (internal/websocket/hub.go:329-339; internal/bluetooth/bluez.go:432-438). An error goes back to the requesting client and is shown as a toast (hub.go:336-338; app.js:114-116). The handler does not trust or connect the device after a successful pair (hub.go:335-339).
- Presence: CURRENT (internal/websocket/hub.go:329-339)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No pairing session is recorded.
- Depends on: the device having been found by a scan (R4-S03-02); the agent of R4-S03-04 answering any confirmation on this side.
- Open questions: Does a phone accept a pairing request that starts from the sink side? Was this tried?
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S03-06  Paired-device store left to BlueZ; in-memory mirror listed on the page
- Question: S03.3
- What was attempted: the project writes no device data. The config structure has no device list (internal/config/config.go:16-32). `data_dir` is declared (config.go:20, 37) and created by the installer (scripts/install.sh:295, 351) and is read by no code (search `DataDir`: config.go only). The manager mirrors BlueZ `Device1` objects in a map, filled at start (internal/bluetooth/bluez.go:168-171) and kept current by signals (bluez.go:240-244, 275-279, 306-310, 616-635). Mirrored fields: address, name, alias, paired, connected, trusted, RSSI, icon, UUIDs, adapter (bluez.go:34-46). The full list, with no filter for paired devices (bluez.go:345-354), goes to each client on connect (internal/websocket/hub.go:217-220) and on each device event (hub.go:255-258). The page shows name, address and the badges Paired, Connected, Trusted (web/static/js/app.js:153-161). `GetDeviceByAddress` is defined (bluez.go:526-539) and has no caller (search).
- Presence: CURRENT (citations above). `GetDeviceByAddress` is DISABLED in the sense of unreachable (bluez.go:526-539).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No run is recorded.
- Depends on: BlueZ's own storage of bonded devices on the host; the hub's reader receiving the device events (R4-S04-05).
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S03-07  Trust set by a page button or as the first step of reconnect
- Question: S03.3
- What was attempted: a paired, unconnected, untrusted device gets a "Trust" button (web/static/js/app.js:168-172, 491) that sends `cmd_trust`; the hub sets the `Trusted` property to true (internal/websocket/hub.go:341-349; internal/bluetooth/bluez.go:440-446). `ReconnectDevice` also calls `TrustDevice` first (bluez.go:481-483). The comment reads "TrustDevice marks a device as trusted so it can auto-connect." (bluez.go:440). No code sets `Trusted` to false (search `"Trusted"`: one write, bluez.go:445). The agent does not set trust during an inbound pairing (internal/bluetooth/pairing_agent.go:62-101).
- Presence: CURRENT (internal/websocket/hub.go:341-349)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No run is recorded.
- Depends on: a person pressing "Trust" or "Reconnect" on the page.
- Open questions: Does a device paired from the phone side ever become trusted without the page?
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S03-08  Removal through Adapter1.RemoveDevice on the active adapter
- Question: S03.3
- What was attempted: every listed device gets a "Remove" button (web/static/js/app.js:180, 494) that sends `cmd_remove`; the hub calls `RemoveDevice` (internal/websocket/hub.go:373-381), which invokes `Adapter1.RemoveDevice` on the active adapter (internal/bluetooth/bluez.go:463-476). The mirrored device record carries its own adapter path (bluez.go:45, 592-595); `RemoveDevice` does not read it. The page asks for no confirmation (app.js:494).
- Presence: CURRENT (internal/websocket/hub.go:373-381)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No run is recorded.
- Depends on: the device belonging to the active adapter.
- Open questions: What is returned when the device belongs to an adapter other than the active one?
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S03-09  No distinction between a first and a later device at pairing or connection
- Question: S03.4
- What was attempted: the agent methods take no account of devices already paired or connected (internal/bluetooth/pairing_agent.go:62-101). The manager keeps no count or limit (internal/bluetooth/bluez.go:80-90; search `max_devices|maxdevices`: no hits). When a further device connects and a transport appears for it, `SetTransport` closes the current transport and acquires the new one (internal/audio/bluetooth_input.go:93, 129-135; see R4-S04-09). No message tells a page user that a second device arrived, beyond the refreshed device list (internal/websocket/hub.go:255-258).
- Presence: CURRENT, as an absence of special handling (citations above)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No session with two devices is recorded.
- Depends on: BlueZ accepting a second link; the accept-all agent (R4-S03-04).
- Open questions: What happens to the first device's audio when a second device pairs without starting a stream? Was a second device ever tried?
- Confidence: LOW. INFERRED from the absence of code, with no run to compare against.

### R4-S03-10  Accept-all pairing policy, stated in a comment
- Question: S03.5
- What was attempted: the agent comment states "It accepts all pairing requests to function as a headless A2DP Sink." (internal/bluetooth/pairing_agent.go:17). No allow list, deny list, pairing window or physical confirmation exists (search `whitelist|allowlist|deny|reject|blocklist`: no hits; `allow` hits are the D-Bus policy at scripts/install.sh:405-414, a CORS header, a firewall hint and comments). `AuthorizeService` accepts for any device, trusted or not (pairing_agent.go:98-101). The controls that exist act after the fact: "Disconnect" and "Remove" on the page (internal/websocket/hub.go:363-381). Those page commands, and pair, trust and connect, are accepted from any WebSocket client: `CheckOrigin` returns true (hub.go:21), the `/ws` route has no wrapper (internal/server/server.go:58) and no authentication code exists (search `auth|password|token|login` in sources: no hits other than the agent's `RequestAuthorization`/`AuthorizeService`).
- Presence: CURRENT (internal/bluetooth/pairing_agent.go:16-17, 86-101)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: STATED for the design choice: headless operation (pairing_agent.go:16-17). UNKNOWN for any result.
- Depends on: host BlueZ visibility state, which the repo does not set (R4-S03-01).
- Open questions: Was any restriction on who can pair planned? None is mentioned in the README or the commit messages.
- Confidence: MEDIUM. STATED design with code that agrees; no result evidence.

---

## S04 Connection and A2DP profile

### R4-S04-01  A2DP sink role through a MediaEndpoint1 object registered with Media1
- Question: S04.1
- What was attempted: `Register` exports an object at `/org/aurilis/a2dp_sink` with interface `org.bluez.MediaEndpoint1` (internal/bluetooth/a2dp_sink.go:14, 49) and an introspection node (a2dp_sink.go:54-96). It calls `org.bluez.Media1.RegisterEndpoint` on the active adapter (a2dp_sink.go:99-104, 123-124) with the properties `UUID` (constant `a2dpSinkUUID`, a2dp_sink.go:13, 118), `Codec` 0x00 (a2dp_sink.go:15, 119) and `Capabilities` (a2dp_sink.go:110-115, 120). `main` calls it once (cmd/aurilis/main.go:108, 130-145); a failure is logged as a warning (main.go:140-141). `Unregister` is deferred to process exit (main.go:143; a2dp_sink.go:134-143). Endpoint methods: `SetConfiguration` passes the transport path to the Bluetooth input (a2dp_sink.go:146-152; main.go:131-136); `ClearConfiguration` and `Release` log only (a2dp_sink.go:209-221; main.go:137-139). The D-Bus policy names `MediaEndpoint1` and `Profile1` (scripts/install.sh:408-409); no code uses `Profile1` (search: install.sh:409 only).
- Presence: CURRENT (cmd/aurilis/main.go:107-145)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. The README feature line (README.md:9) and commit c98d8d3 describe the feature; no registration log or session is recorded.
- Depends on: an active adapter at start (R4-S02-06); the `Media1` interface on that adapter; the D-Bus policy or root; the adapter not being switched afterwards (R4-S02-02).
- Open questions: Did `RegisterEndpoint` ever succeed on a real system? What happens when another program has already registered an A2DP sink endpoint on the same adapter?
- Confidence: MEDIUM. INFERRED from clear code; no result evidence.

### R4-S04-02  SBC as the only codec offered
- Question: S04.2
- What was attempted: one endpoint is registered, with codec constant `sbcCodec = 0x00` (internal/bluetooth/a2dp_sink.go:15, 119). The capability bytes are 0x3F, 0xFF, 0x02, 0x35 (a2dp_sink.go:110-115). The block comment lists "Sampling: 48kHz, 44.1kHz; Channel Mode: Joint Stereo, Stereo" and "Block Length: 16; Subbands: 8; Allocation: Loudness" (a2dp_sink.go:107-108), while the per-byte comments say "(all)" for each field (a2dp_sink.go:111-112). Using the bit meanings in the same file (0x10 for 48 kHz and 0x20 for 44.1 kHz, a2dp_sink.go:166-169), byte 0x3F carries those two sampling bits and all four low bits. Bitpool range 2 to 53 (a2dp_sink.go:113-114). Search `aac|aptx|ldac|opus`: no hits.
- Presence: CURRENT (internal/bluetooth/a2dp_sink.go:106-121)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No negotiation log is recorded.
- Depends on: the source device offering SBC.
- Open questions: Which of the two comment sets describes the intended capabilities?
- Confidence: MEDIUM. INFERRED from clear code; the comments in the file disagree with each other.

### R4-S04-03  SelectConfiguration preference order
- Question: S04.2
- What was attempted: `SelectConfiguration` rejects input shorter than 4 bytes (internal/bluetooth/a2dp_sink.go:159-161). It picks 48 kHz when bit 0x10 is set, else 44.1 kHz when 0x20 is set, else copies the remaining frequency bits unchanged (a2dp_sink.go:166-172). It picks Joint Stereo (0x01), else Stereo (0x02), else copies the remaining bits (a2dp_sink.go:175-181). It picks block length 16, 8 subbands and Loudness allocation, each with a fallback that copies the capability bits unchanged (a2dp_sink.go:184-198). Minimum and maximum bitpool are copied from the input (a2dp_sink.go:201-202). The header comment reads "We select optimal SBC parameters: 48kHz, Joint Stereo, Bitpool 53." (a2dp_sink.go:155). The input length and the result are logged (a2dp_sink.go:157, 204). The chosen configuration is not stored; `SetConfiguration` forwards the transport properties (a2dp_sink.go:149) and the callback in `main` does not read them (cmd/aurilis/main.go:131-136).
- Presence: CURRENT (internal/bluetooth/a2dp_sink.go:154-206)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No negotiation log is recorded.
- Depends on: BlueZ calling `SelectConfiguration` on this endpoint; the capability bytes the peer presents.
- Open questions: Which configuration did any source device negotiate? Do the fallback branches, which can return several bits in one field, produce a configuration that a peer accepts?
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S04-04  Connect and disconnect on request from the web page
- Question: S04.3
- What was attempted: a paired, unconnected device gets a "Connect" button and a connected device gets a "Disconnect" button (web/static/js/app.js:168-176, 492-493). `cmd_connect` calls `Device1.Connect` in a goroutine (internal/websocket/hub.go:351-361; internal/bluetooth/bluez.go:448-454). `cmd_disconnect` calls `Device1.Disconnect` (hub.go:363-371; bluez.go:456-461). The `Connected` flag is updated from `PropertiesChanged` (bluez.go:306-310, 626-628). Errors return to the requesting client as a toast (hub.go:358-359, 369-370; app.js:114-116).
- Presence: CURRENT (internal/websocket/hub.go:351-371)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No session is recorded.
- Depends on: a browser connected to the page; a paired device in the list.
- Open questions: Does a phone start an A2DP stream after a connection that the sink side opened?
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S04-05  Transport lifecycle taken from BlueZ signals, with two readers on one event channel
- Question: S04.3
- What was attempted: `InterfacesAdded` with `MediaTransport1` emits `EventTransportAdded` (internal/bluetooth/bluez.go:250-254); a `PropertiesChanged` on a known transport updates `State` and emits `EventTransportChanged` (bluez.go:316-320, 649-653); `InterfacesRemoved` emits `EventTransportRemoved` (bluez.go:280-284). Two goroutines receive from the one channel returned by `Events()` (bluez.go:104, 110-112): the Bluetooth input monitor (internal/audio/bluetooth_input.go:112, 127) and the hub forwarder (internal/websocket/hub.go:248, 253). INFERRED: each event reaches one of the two and not both, because a Go channel hands each value to a single receiver. The channel holds 64 events; when it is full the event is dropped with a log line (bluez.go:324-330). No code looks at a reason for a disconnect or treats loss of range separately (search `reason`: no hits; search `out of range`: no hits; `range` occurs only as the Go loop keyword, for example bluez.go:160, 273; `Timeout` hits are listed in R4-S03-01).
- Presence: CURRENT (citations above)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No session is recorded.
- Depends on: which goroutine receives a given event; `audio_source` being `bluetooth`, since the monitor runs only then (cmd/aurilis/main.go:148-152).
- Open questions: How often does the hub forwarder take a transport event that the input monitor was waiting for, and the reverse for device events the page needs?
- Confidence: MEDIUM. INFERRED from clear code and Go channel semantics.

### R4-S04-06  Manual reconnect with three attempts and backoff
- Question: S04.3
- What was attempted: a paired, unconnected device gets a "Reconnect" button (web/static/js/app.js:177-179, 495) that sends `cmd_reconnect` (internal/websocket/hub.go:383-393). `ReconnectDevice` sets trust, then calls `Connect` up to three times, sleeping 1, 2 and 4 seconds after each failure (internal/bluetooth/bluez.go:478-495). The final error reads "reconnect failed after 3 attempts" (bluez.go:494). The WebSocket command is the only caller (search `ReconnectDevice`). The README lists "Auto-reconnect — Retry logic for unstable Bluetooth sources" (README.md:18).
- Presence: CURRENT (internal/websocket/hub.go:383-393)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No reconnect attempt is recorded.
- Depends on: a person pressing the button; the device still being known to BlueZ.
- Open questions: Which "unstable Bluetooth sources" does README.md:18 refer to? None is named.
- Confidence: MEDIUM. STATED feature; the code agrees on retry logic and shows it is started by hand only.

### R4-S04-07  auto_reconnect configuration flag with no behaviour attached
- Question: S04.3
- What was attempted: the config has `auto_reconnect`, default true (internal/config/config.go:30, 47; scripts/install.sh:331; README.md:215). Its only other reference sends the value to the page in the `config` message (internal/websocket/hub.go:241). The page script does not read it (search `auto_reconnect` in web/static/js/app.js: no hits). No connection is started without a page command: the callers of `ConnectDevice` are hub.go:358 and bluez.go:487 (search).
- Presence: DISABLED. The flag is present and nothing acts on it (internal/config/config.go:30; internal/websocket/hub.go:241).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No comment or commit text explains the unused flag.
- Depends on: nothing; the value has no effect in the code located.
- Open questions: What was the flag meant to switch?
- Confidence: MEDIUM. INFERRED from a search that found no reader of the flag.

### R4-S04-08  Pickup of existing transports at process start (restart or reboot)
- Question: S04.3
- What was attempted: at start the manager records transports that already exist (internal/bluetooth/bluez.go:172-175). When the Bluetooth input starts, it calls `SetTransport` for each recorded transport whose state is `active` or `idle` (internal/audio/bluetooth_input.go:114-121). No start-up connection to known devices exists (callers of `ConnectDevice`: hub.go:358, bluez.go:487). The service is enabled for boot (systemd/aurilis.service:30; scripts/install.sh:420) and ordered after `bluetooth.service` (systemd/aurilis.service:4).
- Presence: CURRENT (internal/audio/bluetooth_input.go:114-121)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No reboot test is recorded.
- Depends on: the source device or BlueZ re-opening the link after a reboot; the device being trusted (comment at bluez.go:440); the endpoint being registered before the source connects (main.go:140, 150).
- Open questions: After a reboot, does a previously paired phone reconnect and play without any action on the page?
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S04-09  Single active transport; the most recent transport event replaces the current one
- Question: S04.4
- What was attempted: the Bluetooth input holds one descriptor, one file and one transport path (internal/audio/bluetooth_input.go:25-27). `SetTransport` closes and releases the current transport before it acquires another (bluetooth_input.go:93, 226-238). It is called for every `EventTransportAdded` (bluetooth_input.go:129-135), for a transport that changes to `active` and differs from the current one (bluetooth_input.go:136-147), for every `SetConfiguration` (cmd/aurilis/main.go:131-136) and for each existing transport at start, in map order (bluetooth_input.go:115-121; internal/bluetooth/bluez.go:357-366). No priority, preferred device or mixing exists (search `priority`: no hits). No limit on simultaneous connections exists (R4-S03-09).
- Presence: CURRENT (internal/audio/bluetooth_input.go:88-108)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No session with two sources is recorded.
- Depends on: the order in which BlueZ reports transports; which goroutine receives each event (R4-S04-05).
- Open questions: With two connected sources, does the capture follow the one that is playing or the one that connected last?
- Confidence: MEDIUM. INFERRED from clear code.

---

## S05 Audio capture

### R4-S05-01  Capture point: file descriptor obtained with MediaTransport1.Acquire
- Question: S05.1
- What was attempted: `AcquireTransport` calls `org.bluez.MediaTransport1.Acquire` and, when that fails, `TryAcquire` (internal/bluetooth/bluez.go:497-510). The reply is stored into a Go `int` descriptor and two `uint16` MTU values (bluez.go:499, 512). `SetTransport` wraps the descriptor with `os.NewFile` (internal/audio/bluetooth_input.go:95-102) and starts `readLoop` (bluetooth_input.go:106), which reads with `f.Read` (bluetooth_input.go:189). The write MTU is discarded (bluetooth_input.go:95). On close the file is closed and `MediaTransport1.Release` is called (bluetooth_input.go:226-236; bluez.go:519-524). Routes into `SetTransport`: the endpoint `SetConfiguration` callback (internal/bluetooth/a2dp_sink.go:146-152; cmd/aurilis/main.go:131-136), `EventTransportAdded` (bluetooth_input.go:129-135), `EventTransportChanged` to `active` (bluetooth_input.go:136-147) and the start-up walk (bluetooth_input.go:115-121). The function comment reads "This is used to read A2DP audio data from the Bluetooth stack." (bluez.go:498).
- Presence: CURRENT (internal/audio/bluetooth_input.go:88-108)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No acquisition log is recorded.
- Depends on: the endpoint of R4-S04-01 being the one BlueZ configures for the stream; godbus v5.2.2 (go.mod:6).
- Open questions: Does `Store` at bluez.go:512 accept a plain `int` for the descriptor that `Acquire` returns? Was a transport ever acquired on a real system? What follows when `SetConfiguration` and `EventTransportAdded` both trigger an acquire for the same transport?
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S05-02  Second capture input, not from the Bluetooth stack: ALSA line-in
- Question: S05.1
- What was attempted: `ALSAInput` opens an ALSA capture device (internal/audio/alsa.go:13, 153-182) and its capture loop writes into the same ring buffer as the Bluetooth input (alsa.go:218-252; cmd/aurilis/main.go:126-127). It is chosen with `audio_source` set to `linein` (main.go:153-156) or from the page (internal/websocket/hub.go:450-460; main.go:160-178). The README lists it as "Native ALSA integration — CGo-based ALSA capture for line-in audio" (README.md:11).
- Presence: CURRENT (cmd/aurilis/main.go:127, 153-156)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No capture run is recorded.
- Depends on: an ALSA capture device named by `alsa_device`, default `default` (internal/config/config.go:40).
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S05-03  Bytes from the transport descriptor treated as S16_LE PCM, with a 13-byte skip when the first byte is 0x80
- Question: S05.2
- What was attempted: for each read, when more than 13 bytes arrived and the first byte is 0x80, `readLoop` skips 13 bytes (internal/audio/bluetooth_input.go:204-209). The remaining bytes are taken two at a time as little-endian signed 16-bit values and scaled by 1/32768 to `float64` (bluetooth_input.go:211-220), then written to the ring buffer (bluetooth_input.go:222). The comments give two accounts. The type comment: "It reads SBC-encoded audio from BlueZ, decodes it, and writes PCM to the ring buffer." (bluetooth_input.go:16). The loop comment: "For A2DP Sink, the data arrives as raw PCM when using BlueZ's built-in SBC decoder via the media transport." (bluetooth_input.go:163-164) and "The transport FD provides decoded PCM data in S16_LE format." (bluetooth_input.go:165). The endpoint is registered with the SBC codec (internal/bluetooth/a2dp_sink.go:119). No decode call exists in the repo (search `decode|sbc|libsbc`: comments and the endpoint code only).
- Presence: CURRENT (internal/audio/bluetooth_input.go:162-224)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No captured audio, log or listening result is recorded.
- Depends on: the content BlueZ delivers on a `MediaTransport1` descriptor for an SBC endpoint; the stated assumption at bluetooth_input.go:163-165.
- Open questions: Is the data at the descriptor SBC frames or PCM? If a 13-byte media header is present, what follows it? What happens to a PCM read whose first byte happens to equal 0x80?
- Confidence: MEDIUM. INFERRED from clear code; the two comments place the decoding in different components.

### R4-S05-04  Sample rate and channel count taken from the config, not from the negotiated transport
- Question: S05.2
- What was attempted: the Bluetooth input stores a sample rate and channel count (internal/audio/bluetooth_input.go:21-22, 35-36) and no code reads them (search `b\.sampleRate|b\.channels`: no hits). The ring buffer size, equaliser, analyser and stream header use `cfg.SampleRate`, `cfg.Channels` and `cfg.BitDepth` (cmd/aurilis/main.go:114, 118, 123, 181-182), defaults 48000, 2 and 16 (internal/config/config.go:41-43). `SelectConfiguration` can choose 44.1 kHz (internal/bluetooth/a2dp_sink.go:168-169). The transport properties passed to the callback are not read (main.go:131-136), and the transport parser reads `Device`, `UUID`, `Codec` and `State` only (internal/bluetooth/bluez.go:599-614). The commit message describes the pipeline as "48kHz stereo PCM" (commit c98d8d3).
- Presence: CURRENT (citations above)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No session is recorded.
- Depends on: the source accepting 48 kHz SBC; the `sample_rate` config value.
- Open questions: What is heard when a source negotiates 44.1 kHz while the stream header declares 48 kHz?
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S05-05  Stream start taken from transport appearance or the state "active"
- Question: S05.3
- What was attempted: an acquire is triggered by `SetConfiguration` (internal/bluetooth/a2dp_sink.go:147-150; cmd/aurilis/main.go:132-133), by `EventTransportAdded` whatever the state (internal/audio/bluetooth_input.go:129-135), by a change to `active` on a transport other than the current one (bluetooth_input.go:136-147), and at start for states `active` or `idle` (bluetooth_input.go:116). The input's `active` flag records that the input was started, not that audio is flowing (bluetooth_input.go:46-50, 82-86). `SetTransport` holds no check of that flag (bluetooth_input.go:89-108), so the `SetConfiguration` route acquires a transport even when the source is `linein`. The hub broadcasts a `status` message with the transport list on transport events (internal/websocket/hub.go:265-267, 305-314); the page's `status` handler reads `audio_source` and `active_adapter` only (web/static/js/app.js:109-113).
- Presence: CURRENT (internal/audio/bluetooth_input.go:110-160)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No session is recorded.
- Depends on: which goroutine receives the transport event (R4-S04-05).
- Open questions: Does `Acquire` succeed on a transport whose state is `idle`, or does only the `TryAcquire` fallback (bluez.go:506) return a descriptor? Is the start of playback visible anywhere to a user besides the spectrum display?
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S05-06  Stream stop taken from transport removal or a read error
- Question: S05.3
- What was attempted: `EventTransportRemoved` for the current path closes the transport (internal/audio/bluetooth_input.go:148-156). A read error ends `readLoop`; it is logged unless it is a closed-file error (bluetooth_input.go:189-198, 240-247). INFERRED: after a read error the file and transport fields stay set until a later event, because the loop returns without calling `closeTransport` (bluetooth_input.go:197). A zero-length read is skipped (bluetooth_input.go:200-202). `ClearConfiguration` only logs (cmd/aurilis/main.go:137-139). A change of state away from `active`, such as a pause, triggers nothing (bluetooth_input.go:136-147 acts on `active` only).
- Presence: CURRENT (citations above)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No session is recorded.
- Depends on: BlueZ removing the transport object when the source disconnects.
- Open questions: After a pause and resume on the phone, does reading continue on the same descriptor? After a read error, is capture resumed without a new transport object?
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S05-07  Read buffer sized from the read MTU with a 4096-byte floor; no gap handling in the read loop
- Question: S05.4
- What was attempted: the byte buffer is the read MTU or 4096 bytes, whichever is larger (internal/audio/bluetooth_input.go:167-171); the sample buffer holds half as many values (bluetooth_input.go:172). Each loop pass makes one blocking read (bluetooth_input.go:189). A temporary `net.OpError` leads to another pass (bluetooth_input.go:191-193). INFERRED: an odd trailing byte is discarded, through the integer division at bluetooth_input.go:212. The 13-byte header is skipped and not parsed, so no sequence number or timestamp is examined (bluetooth_input.go:204-209). No silence is inserted and no counter or log line records a gap (search `underrun|overrun|xrun|silence|dropout` in `*.go`: no hits in the Bluetooth path).
- Presence: CURRENT (internal/audio/bluetooth_input.go:166-224)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No session is recorded.
- Depends on: the MTU BlueZ reports; the format assumption of R4-S05-03.
- Open questions: Were dropouts ever heard or counted? README.md:297 names "Audio latency/dropouts" as a troubleshooting heading with no figure.
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S05-08  Shared ring buffer: the writer overwrites, a slow reader skips ahead, an empty read is skipped
- Question: S05.4
- What was attempted: the buffer length is `buffer_size_ms` times ten (cmd/aurilis/main.go:114); with the defaults 100, 48000 and 2 (internal/config/config.go:41-42, 48) that is 1000 ms or 96000 samples (internal/pipeline/ringbuffer.go:34-41), with a floor of 4096 (ringbuffer.go:37-39). `Write` never blocks and does not look at reader positions (ringbuffer.go:44-55). `Read` moves a reader that is more than one buffer behind to the oldest retained sample (ringbuffer.go:95-99) and returns 0 when nothing is available (ringbuffer.go:91-93); the HTTP streamer then skips that tick and sends nothing (internal/stream/http_stream.go:84-87). The type comment calls it a "lock-free single-producer, multi-consumer ring buffer" (ringbuffer.go:8); two inputs hold the same buffer (main.go:126-127) and the source switch stops both before starting one (main.go:163-164). The README lists "Increase buffer size in config (`buffer_size_ms`)" under latency and dropouts (README.md:299). Unit tests exist for the buffer, including `TestConsumerFallsBehind` and `TestWrapAround` (internal/pipeline/ringbuffer_test.go:105-124, 198). Commit c98d8d3 states "43 passing tests (unit + integration)"; it names no test and the repo holds no test output.
- Presence: CURRENT (cmd/aurilis/main.go:113-115)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. The commit statement concerns unit tests with synthetic data; no result with a Bluetooth stream is recorded.
- Depends on: `buffer_size_ms`, `sample_rate` and `channels` in the config; one writer at a time.
- Open questions: Which tests make up the 43 in the commit message, given 54 `^func Test` lines? Did the buffer ever carry Bluetooth audio?
- Confidence: MEDIUM. INFERRED from clear code; STATED test claim does not cover the Bluetooth path.

---

## S06 Remote control and metadata

### R4-S06-01  NOT FOUND
- Question: S06.1
- Searched: `avrcp|MediaPlayer|MediaControl|bluezMediaPlayer|Play\(|Pause\(|Next\(|Previous\(` over the whole tree; the full command list at internal/websocket/hub.go:41-56 and its handler at hub.go:317-494; the page's public functions at web/static/js/app.js:467-542; README.md:231-257; all of internal/bluetooth/.
- Nearest related material: a constant for `org.bluez.MediaPlayer1` is declared with no other reference (internal/bluetooth/bluez.go:18). The page's play button starts and stops browser playback of `/audio/stream` and sends nothing to the source device (web/static/js/app.js:351-404; web/static/index.html:124).

### R4-S06-02  Software volume in the DSP chain, set from the web page, with no link to the source device
- Question: S06.2
- What was attempted: `VolumeProcessor` multiplies each sample by a gain between 0 and 1, moving toward the target by 0.001 per sample (internal/dsp/volume.go:10-55, 83-85). One processor is created from the config value (cmd/aurilis/main.go:121) and applied inside each HTTP stream handler after the equaliser (internal/stream/http_stream.go:93, 141). It is set by `cmd_set_volume` (internal/websocket/hub.go:438-448), sent from two sliders on the page (web/static/index.html:50, 132; web/static/js/app.js:526-532). The value is kept in the config field `volume`, default 0.8 (internal/config/config.go:27, 44, 118-128), and written to disk on "Save Configuration" or shutdown (hub.go:489-492; main.go:213). No transport volume property is read or written: the transport parser and change handler cover `Device`, `UUID`, `Codec` and `State` only (internal/bluetooth/bluez.go:599-614, 649-653; search `"Volume"` in internal/bluetooth: no hits). The browser audio element's own volume is not set (search `audioElement\.volume`: no hits). Eight unit tests exist for the processor (internal/dsp/volume_test.go:8-96); commit c98d8d3 states "43 passing tests (unit + integration)" without naming them.
- Presence: CURRENT (internal/stream/http_stream.go:93, 141; internal/websocket/hub.go:438-448)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No session with a source device is recorded.
- Depends on: a WebSocket client to change the value; the HTTP stream being the output.
- Open questions: Does the volume control on the phone change the level heard, and if so where is it applied? Is the page value ever reported back to the phone? No code for either was located.
- Confidence: MEDIUM. INFERRED from clear code; the stated test claim covers the processor in isolation only.

### R4-S06-03  NOT FOUND
- Question: S06.3
- Searched: `title|artist|album|position|duration|metadata|track|MediaPlayer|avrcp` over the whole tree; the message types at internal/websocket/hub.go:27-39; the page's message handler at web/static/js/app.js:76-118; web/static/index.html:1-142; all of internal/bluetooth/.
- Nearest related material: the unused `MediaPlayer1` constant (internal/bluetooth/bluez.go:18). The device list shows the source's name or alias, which identifies the device and not the track (web/static/js/app.js:153). A `status` message carries the transport list with its `State` field (internal/websocket/hub.go:305-314; bluez.go:49-56); the page does not display it (app.js:109-113).

---

## Tally

- Questions in the brief: 24 (S01: 5, S02: 3, S03: 5, S04: 4, S05: 4, S06: 3).
- Questions answered with findings: 22 (S01.1 to S01.5, S02.1 to S02.3, S03.1 to S03.5, S04.1 to S04.4, S05.1 to S05.4, S06.2).
- Questions answered NOT FOUND: 2 (S06.1, S06.3).
- Total: 22 + 2 = 24.
- Finding records: 43 (S01: 6, S02: 9, S03: 10, S04: 9, S05: 8, S06: 1).
- NOT FOUND records: 2.
- BLOCKED lines: none.
