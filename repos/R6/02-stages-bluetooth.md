# R6 stage audit — Bluetooth stages S01 to S06

Repo: /home/user/SonicFlow, read-only, at commit 3046b9e (branch `claude/inspiring-galileo-a9dlia`, same commit as `main`; working tree clean per `git status --short`). All paths are relative to the repo root. Citations of earlier revisions give the commit and the path.

Conventions used in this file:

- "Feature description" means text that says what the product does. It is not counted as evidence of a result. "Verification statement" means text where the author says a result was checked ("verified", "live-verified", "now transitions"). Only the second kind supports WORKED, PARTIAL or FAILED.
- SOUNDSYNC-SPEC.md describes a predecessor system (SOUNDSYNC-SPEC.md:1-8). It is cited only where this repo's own files refer to it, and is never used as evidence of this repo's results.
- Rule 10: the two passages addressed to an AI agent (sonicflow-design-spec.md:4; SOUNDSYNC-SPEC.md:46-48) were not acted on and bear on no question in S01 to S06.
- Rule 11: device-address strings in test fixtures (src/bluetooth/manager.rs:875-877, 895-896; src/bluetooth/constants.rs:66-93) and the fixed PIN and passkey values returned by the pairing agent (src/bluetooth/agent.rs:44-52) are cited by location only.

---

## S01 Platform and stack

### R6-S01-01  Reference platform: Apple Macmini5,3 running Ubuntu 26.04 on x86_64
- Question: S01.1
- What was attempted: the product was built for and brought up on one named machine: "Apple Macmini5,3, Ubuntu 26.04, x86_64" (DECISIONS.md:4-5); a 2011 Mac mini running Ubuntu Server 26.04 (README.md:102-103); installer header names the same box (install.sh:2-3); hardware notes for that machine (forinstall.md:130-138). The installer assumes apt/dpkg (install.sh:94-116), systemd and loginctl (install.sh:135-141, 353-356) and optionally ufw (install.sh:276-283).
- Presence: CURRENT (install.sh:2-3; README.md:102-103)
- Outcome: WORKED
- Outcome evidence: STATED. install.sh:2-3: "every step was performed and verified on the reference box". README.md:283-285: "verified end-to-end on real hardware: real phones, a house full of real Chromecasts, and one very patient 2011 Mac mini".
- Why it turned out that way: UNKNOWN beyond the individual fixes recorded under S04 and S05. No single cause is given.
- Depends on: Macmini5,3 hardware (forinstall.md:130-135); Ubuntu 26.04 server with the pre-existing software listed at forinstall.md:20-25; BlueZ 5.85, PipeWire 1.6.2, WirePlumber 0.5.13 on that box (forinstall.md:61-62, 146-147).
- Open questions: Which of the "verified" statements refer to the installed service and which to the development instance (`sonicflow-dev`, install.sh:349)? No test log or CI result is stored in the tree.
- Confidence: MEDIUM. STATED evidence only; no stored artefact shows a run.

### R6-S01-02  Other hosts: generic Linux claim, aarch64 library path, Raspberry Pi origin
- Question: S01.1
- What was attempted: the README states a wider target: "Any Linux box with PipeWire ≥ 1.x, WirePlumber ≥ 0.5 and a Bluetooth adapter works." (README.md:104-105). The doctor script also accepts an aarch64 SPA plugin directory (deploy/doctor.sh:35). DECISIONS.md:4 says the build was adapted "from Raspberry Pi to this machine". CI builds on `ubuntu-latest` (.github/workflows/build.yml:15) with build and unit-test steps only (build.yml:48-54).
- Presence: CURRENT (README.md:104-105; deploy/doctor.sh:35)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: Debian-family tooling assumed by the installer (apt, dpkg, install.sh:94-116); the x86_64 library path in forinstall.md:107 and both paths in deploy/doctor.sh:35.
- Open questions: Was the product run on any machine other than the reference box? Was an aarch64 host ever used with this repo?
- Confidence: MEDIUM. The claim is STATED; no run on another host is recorded anywhere in the repo.

### R6-S01-03  Rust backend as a single binary (edition 2021, tokio, axum)
- Question: S01.2
- What was attempted: one binary crate `sonicflow` (Cargo.toml:1-10), edition 2021 (Cargo.toml:4), async runtime tokio 1 (Cargo.toml:38), HTTP/WebSocket server axum 0.7 (Cargo.toml:37), entry point `#[tokio::main]` at src/main.rs:24-25. Toolchain: Rust stable through rustup, no pinned version (install.sh:123-128; .github/workflows/build.yml:25-27); no `rust-toolchain` file (01-map.md section 5, checked with `ls`).
- Presence: CURRENT (Cargo.toml:1-54)
- Outcome: WORKED
- Outcome evidence: STATED. Commit d8b17a3 subject "Phase 0: toolchain, scaffold, first green build" and body: "Pre-commit gate: version sync + fmt + clippy -D warnings + tests (63 passing)". README.md:208: "cargo test                   # 66 unit tests".
- Why it turned out that way: UNKNOWN
- Depends on: Rust stable at the time of the build (unpinned); Cargo.lock (4182 lines) for crate versions.
- Open questions: Which rustc version produced the builds? No version is recorded.
- Confidence: MEDIUM. STATED only; no build or test output is stored.

### R6-S01-04  Web UI in TypeScript with Preact and Vite
- Question: S01.2
- What was attempted: single-page UI under `webui/`: `preact ^10.25.0`, `@fontsource-variable/inter ^5.1.0`, `@preact/preset-vite ^2.9.0`, `typescript ~5.6.0`, `vite ^6.0.0` (webui/package.json:11-19); Node 22.x (install.sh:120-122; .github/workflows/build.yml:31-35). The built UI is served by the Rust binary (src/main.rs:126; src/web/mod.rs:41-42, 109).
- Presence: CURRENT (webui/package.json:1-20)
- Outcome: WORKED
- Outcome evidence: STATED. Commit b19435c: "Verified live in headless Chromium: 440Hz tone through the EQ sink lights the correct bars end-to-end".
- Why it turned out that way: UNKNOWN
- Depends on: Node 22.x; versions locked in webui/package-lock.json; headless Chromium as the stated test browser (commit b19435c).
- Open questions: NONE for this question.
- Confidence: MEDIUM. STATED only.

### R6-S01-05  Bash scripts for install, uninstall, diagnostics, module sweep and the commit hook
- Question: S01.2
- What was attempted: five bash scripts: install.sh:1, uninstall.sh:1, deploy/doctor.sh:1, deploy/sweep-modules.sh:1, .githooks/pre-commit:1. Statements about parts of them: commit 1e20b41 "deploy/doctor.sh diagnostics (all-green on this box)"; commit 26a79c2 "main.conf keys restored byte-identically — sandbox-tested roundtrip".
- Presence: CURRENT (files listed above)
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The two statements above cover the doctor run and the main.conf revert only; no statement records a complete installer run and its result.
- Why it turned out that way: UNKNOWN
- Depends on: bash; root for install.sh (install.sh:37); the service user's session environment (install.sh:56-64).
- Open questions: Was install.sh at its current revision run start to finish on the reference box, and with what output? BUILD-PLAN.md:100 lists "clean install path replayed on the box" as a check with no recorded result.
- Confidence: LOW. Partial STATED material only.

### R6-S01-06  Bluetooth stack: BlueZ daemon with bluer and zbus as D-Bus clients
- Question: S01.3
- What was attempted: BlueZ from apt, version unpinned: `bluetooth bluez bluez-tools bluez-obexd` (install.sh:97). Version on the development box: BlueZ 5.85 (forinstall.md:146-147). Version floors named in prose: BlueZ ≥ 5.66 for the `Class` key (DECISIONS.md:70-71; src/bluetooth/constants.rs:16) and ≥ 5.79 for cover-art properties (forinstall.md:146). Rust clients: `bluer = { version = "0.17", features = ["full"] }` (Cargo.toml:14; locked 0.17.4, Cargo.lock:441-442), `zbus` 4 (Cargo.toml:15; locked 4.4.0, Cargo.lock:3985-3986), `zvariant` 4 (Cargo.toml:16; locked 4.2.0, Cargo.lock:4148-4149). External tools called: `btmgmt` (src/bluetooth/manager.rs:223), `hcitool` (src/bluetooth/manager.rs:580), `bluetoothctl` (install.sh:233, 388; deploy/doctor.sh:20-26).
- Presence: CURRENT (Cargo.toml:13-16; install.sh:97)
- Outcome: WORKED
- Outcome evidence: STATED. Commit 4505247: "Verified live: scan found 19 devices, names resolved, alias=SonicFlow, discoverable/pairable on, scan start/stop clean".
- Why it turned out that way: UNKNOWN
- Depends on: BlueZ 5.85 on the development box; bluer 0.17.4; zbus 4.4.0; `hcitool` being shipped by the distribution (DECISIONS.md:73 "only as a fallback while Ubuntu ships it").
- Open questions: Which BlueZ versions other than 5.85 was this run against? The code comment at src/bluetooth/avrcp.rs:41-43 names 5.7x and 5.8x path differences without saying which were tested.
- Confidence: MEDIUM. STATED evidence; code agrees on the API in use but no artefact is stored.

### R6-S01-07  A2DP media layer supplied by WirePlumber's BlueZ monitor and the SPA Bluetooth plugin
- Question: S01.3
- What was attempted: the A2DP endpoint, codec negotiation and transport are left to WirePlumber and `libspa-0.2-bluetooth` (install.sh:98-99); the app is confined to control-plane work (src/bluetooth/mod.rs:3-8; src/bluetooth/manager.rs:4-5; README.md:81-83). WirePlumber 0.5.13 on the development box (forinstall.md:61-62); installer gate accepts `0.5*|0.6*|1.*` (install.sh:86-91). Detail of the configuration is in R6-S04-01.
- Presence: CURRENT (install.sh:98-99, 185-206; src/bluetooth/mod.rs:3-8)
- Outcome: WORKED
- Outcome evidence: STATED. Commit bb2c661: "Audio Sink 110b now advertised with sbc/sbc_xq/aptx/aptx_hd/ldac endpoints." Also forinstall.md:103-104.
- Why it turned out that way: STATED. The working state followed the seat-monitoring change described in R6-S04-01 (forinstall.md:97-104).
- Depends on: WirePlumber 0.5 conf format (forinstall.md:62 "0.5 SPA-JSON `.conf` config format, NOT 0.4 Lua"); `libspa-0.2-bluetooth` (deploy/doctor.sh:35-39 calls its absence "the #1 cause of connected-but-silent").
- Open questions: NONE for this question; see R6-S04-01.
- Confidence: MEDIUM. STATED evidence; the installer content agrees.

### R6-S01-08  Installer check for competing Bluetooth audio stacks (PulseAudio daemon, bluealsa)
- Question: S01.3
- What was attempted: install step 1 counts a failure when a process named `pulseaudio` or matching `bluealsa` is running (install.sh:80-85); the doctor script prints a warning for the same two (deploy/doctor.sh:56-57). Listed as a pre-flight requirement at forinstall.md:59-60.
- Presence: CURRENT (install.sh:80-85; deploy/doctor.sh:56-57)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: process names `pulseaudio` and `bluealsa` as matched by `pgrep` (install.sh:80, 83).
- Open questions: Was either process ever present on a machine where this check ran? The stated conflict (install.sh:81, 84) is not tied to an observed incident in this repo.
- Confidence: MEDIUM. Clear code, no result evidence.

### R6-S01-09  Audio stack: PipeWire, pipewire-pulse and WirePlumber, driven through command-line tools
- Question: S01.4
- What was attempted: apt packages `pipewire pipewire-pulse pipewire-audio pipewire-alsa wireplumber` and `pulseaudio-utils` (install.sh:98-99), unpinned. Versions on the development box: PipeWire 1.6.2, WirePlumber 0.5.13 (forinstall.md:61-62). The `pipewire` crate is not linked: "All PipeWire interaction is via subprocess (pactl, pw-cat, pw-link, pw-cli, pw-loopback, wpctl, pipewire -c)" (Cargo.toml:19-20); README.md:73-76 gives the stated trade ("trades a little latency for a build that survives PipeWire upgrades"). Development packages `libpipewire-0.3-dev`, `libspa-0.2-dev` and `libmp3lame-dev` were installed on the development box and then judged "unnecessary" (forinstall.md:33-35); they still appear in a [PENDING] line at forinstall.md:74-76; no revision of install.sh contains them (`git log --all -S'libpipewire-0.3-dev' -- install.sh` returned nothing).
- Presence: CURRENT (Cargo.toml:18-21; install.sh:98-99)
- Outcome: WORKED
- Outcome evidence: STATED. Commit b19435c: "Verified live in headless Chromium: 440Hz tone through the EQ sink lights the correct bars end-to-end". Commit 174ec2f subject: "Phase 3: EQ verified live".
- Why it turned out that way: UNKNOWN
- Depends on: PipeWire 1.6.2 and WirePlumber 0.5.13 on the development box; the PulseAudio-compatible CLI (`pactl`, `parec`) answering through pipewire-pulse (deploy/doctor.sh:12-13); a user session kept alive by linger (install.sh:135-141).
- Open questions: Are forinstall.md:74-76 and forinstall.md:33-35 describing the same packages at two points in time? The file does not date line 74.
- Confidence: MEDIUM. STATED evidence; code agrees.

### R6-S01-10  Rust crate dependencies with a lock file
- Question: S01.5
- What was attempted: version ranges in Cargo.toml and exact versions in Cargo.lock: `opus` 0.3 → 0.3.1 (Cargo.toml:21; Cargo.lock:1831-1832); `rust_cast` 0.21 → 0.21.0 (Cargo.toml:24; Cargo.lock:2304-2305); `mdns-sd` 0.11 → 0.11.5 (Cargo.toml:25; Cargo.lock:1629-1630); `rustfft` 6.2 → 6.4.1 (Cargo.toml:28; Cargo.lock:2330-2331); `webrtc` and `rtp` 0.12 → 0.12.0 (Cargo.toml:31-32; Cargo.lock:3430-3431, 2289-2290); `axum` 0.7 → 0.7.9 (Cargo.toml:37; Cargo.lock:301-302); `tokio` 1 → 1.53.1 (Cargo.toml:38; Cargo.lock:2965-2966); `tower-http` 0.6 → 0.6.11 (Cargo.toml:39; Cargo.lock:3128-3129); plus serde, serde_json, toml 0.8, tracing, tracing-subscriber, dirs 6, chrono, uuid, futures, libc, bytes (Cargo.toml:33, 40-51); dev-dependency `tokio-test` 0.4 (Cargo.toml:54).
- Presence: CURRENT (Cargo.toml:12-54; Cargo.lock)
- Outcome: WORKED
- Outcome evidence: STATED. Commit d8b17a3 subject: "Phase 0: toolchain, scaffold, first green build".
- Why it turned out that way: UNKNOWN
- Depends on: Cargo.lock as tracked; system libraries `libdbus-1-dev libopus-dev libclang-dev pkg-config build-essential` at build time (install.sh:103; .github/workflows/build.yml:19-23).
- Open questions: NONE.
- Confidence: MEDIUM. STATED only.

### R6-S01-11  rustls with two crypto providers in the dependency graph, no provider selected
- Question: S01.5
- What was attempted: before commit 2641e55 the binary did not install a rustls crypto provider; `webrtc` and `rust_cast` each pull a different one (src/main.rs:33-36).
- Presence: REMOVED. Commit 2641e55 added the explicit provider installation now at src/main.rs:37-42.
- Outcome: FAILED
- Outcome evidence: STATED. Commit 2641e55: "rustls had no default CryptoProvider (ring AND aws-lc in the graph) so the cast session thread PANICKED on TLS connect". Also src/main.rs:35-36.
- Why it turned out that way: STATED. With both providers present "rustls cannot auto-select a crypto provider and PANICS on first TLS use" (src/main.rs:34-35).
- Depends on: rustls 0.23.43 (Cargo.lock:2379-2380); `webrtc` 0.12.0 and `rust_cast` 0.21.0 in the same binary.
- Open questions: NONE.
- Confidence: HIGH. STATED in the commit and the code comment, and the code agrees.

### R6-S01-12  rustls `ring` provider installed explicitly at start-up
- Question: S01.5
- What was attempted: `rustls = { version = "0.23", features = ["ring"] }` (Cargo.toml:34) and `rustls::crypto::ring::default_provider().install_default()` at process start (src/main.rs:37-42).
- Presence: CURRENT (src/main.rs:33-42)
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Commit 2641e55 records the change ("pin ring at startup") and a WebRTC check, not a Cast TLS connection after the change.
- Why it turned out that way: UNKNOWN
- Depends on: rustls 0.23 with the `ring` feature.
- Open questions: Did a Cast session connect over TLS after this change? Commit 9d619ed says of Cast and AirPlay: "live sessions left for a supervised test".
- Confidence: MEDIUM. Clear code; no result evidence.

### R6-S01-13  System packages through apt without version pins, plus Node 22 and rustup
- Question: S01.5
- What was attempted: apt list at install.sh:96-104: Bluetooth and audio packages (covered above), `avahi-daemon avahi-utils ffmpeg`, `alsa-utils rfkill`, `git curl unzip`, and build packages. Missing packages are installed and recorded (install.sh:105-116). Node 22.x is checked, not installed (install.sh:120-122). rustup stable is installed for the service user when cargo is absent (install.sh:123-128). `curl` is also called at run time for album-art lookup (src/art.rs:263). The package set was present on the development box at audit time (forinstall.md:68-71).
- Presence: CURRENT (install.sh:93-132)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: Ubuntu 26.04 package names and versions at install time; the nodesource repository for Node 22 (forinstall.md:33).
- Open questions: What package versions were installed on the reference box, other than the three versions given at forinstall.md:61-62 and 146-147?
- Confidence: MEDIUM. Clear script; the result of the apt step is not recorded.

### R6-S01-14  npm dependencies with a lock file
- Question: S01.5
- What was attempted: ranges at webui/package.json:11-19; locked versions per 01-map.md section 5: preact 10.29.8 (webui/package-lock.json:1825-1826), @fontsource-variable/inter 5.3.0 (777-778), @preact/preset-vite 2.10.6 (853-854), typescript 5.6.3 (1956-1957), vite 6.4.3 (2001-2002). Installed with `npm ci` or `npm install` (install.sh:292).
- Presence: CURRENT (webui/package.json; webui/package-lock.json)
- Outcome: WORKED
- Outcome evidence: STATED. Commit b19435c: "Verified live in headless Chromium" (the UI built and ran).
- Why it turned out that way: UNKNOWN
- Depends on: Node 22.x; lockfileVersion 3 (webui/package-lock.json:4).
- Open questions: NONE.
- Confidence: MEDIUM. STATED only. The package-lock line numbers are taken from 01-map.md and were not re-opened line by line.

---

## S02 Adapter and controller

### R6-S02-01  Adapter enumeration, default choice, power-on and property setup at start-up
- Question: S02.1
- What was attempted: the manager opens a bluer session, lists adapter names and sorts them (src/bluetooth/manager.rs:144-146); picks the configured adapter when present, otherwise the first name (src/bluetooth/manager.rs:150-158); publishes the list with addresses and a `selected` flag (src/bluetooth/manager.rs:161-178; src/state/mod.rs:14-20); then sets powered (182), alias (185), Class of Device through `btmgmt` (190), discoverable timeout 0 (191), discoverable (194), pairable (197). Each property failure is logged and bring-up continues (182-199). Config key `adapter`, default none (src/state/config.rs:22-24, 39).
- Presence: CURRENT (src/bluetooth/manager.rs:125-213)
- Outcome: WORKED
- Outcome evidence: STATED. Commit 4505247: "Verified live: scan found 19 devices, names resolved, alias=SonicFlow, discoverable/pairable on, scan start/stop clean".
- Why it turned out that way: UNKNOWN
- Depends on: BlueZ D-Bus access for the service user (groups `audio bluetooth`, deploy/sonicflow.service:14); one adapter on the development box at the time (see R6-S02-04); bluer 0.17.4.
- Open questions: Was the verification made with the installed system unit or the development instance?
- Confidence: MEDIUM. STATED evidence; code agrees.

### R6-S02-02  Adapter selection among several through the API and Settings, persisted
- Question: S02.1
- What was attempted: `POST /api/bluetooth/adapter {id}` (src/web/mod.rs:63; src/web/routes.rs:179-188; API.md:35) sends `SelectAdapter`; the manager stops discovery, stores the id in `state.toml`, clears the device list and drops the adapter so the loop re-initialises (src/bluetooth/manager.rs:527-542). The stored id overrides the config file at load (src/state/config.rs:95-97, 155-156). `GET /api/bluetooth/adapters` lists adapters (src/web/mod.rs:62; src/web/routes.rs:169-172). The Settings selector is rendered only when more than one adapter is listed (webui/src/components/Settings.tsx:581-594). The id is not checked against the adapter list before it is stored (src/web/routes.rs:183-186; src/bluetooth/manager.rs:530-534); an unknown id falls back to the first adapter with a warning (src/bluetooth/manager.rs:153-156).
- Presence: CURRENT (src/bluetooth/manager.rs:527-542)
- Outcome: UNVERIFIED
- Outcome evidence: NONE. BUILD-PLAN.md:34 lists "adapter switch works" as a check; no result is recorded.
- Why it turned out that way: UNKNOWN
- Depends on: more than one `hci*` adapter being present; the pairing agent registration being kept across the switch (src/bluetooth/manager.rs:131-141 registers only when no connection is held).
- Open questions: Was a second adapter ever attached to the reference box? install.sh:132 words the dongle as something the user may "add".
- Confidence: MEDIUM. Clear code; no result evidence.

### R6-S02-03  Installer edits to /etc/bluetooth/main.conf and restart of bluetooth.service
- Question: S02.1
- What was attempted: install step 4 sets `[General] Class = 0x000414`, `Name = SonicFlow`, `DiscoverableTimeout = 0`, `Experimental = true` and `[Policy] AutoEnable = true` by key-level edits, records the previous values, keeps a backup, restarts `bluetooth` and checks it is active (install.sh:151-181). The uninstaller restores the recorded values (uninstall.sh:113-137).
- Presence: CURRENT (install.sh:151-181)
- Outcome: UNVERIFIED
- Outcome evidence: NONE. forinstall.md:84-88 marks the main.conf edit [PENDING]; forinstall.md:144-147 marks `Experimental` "[PENDING → install.sh]". Commit 26a79c2 states a "sandbox-tested roundtrip" of the revert, which is a file-edit test.
- Why it turned out that way: UNKNOWN
- Depends on: BlueZ honouring `Class` major/minor (DECISIONS.md:70-71 names BlueZ ≥ 5.66); root.
- Open questions: Was the edited main.conf ever active on the reference box, and what did `bluetoothctl show` report afterwards? install.sh:391 anticipates that a reboot may be needed before the class takes effect.
- Confidence: MEDIUM. Clear script; no result evidence.

### R6-S02-04  Onboard Apple/Broadcom Bluetooth 4.0 controller of the reference machine
- Question: S02.2
- What was attempted: the reference machine's built-in controller is documented at forinstall.md:131-132 (USB identifier given there) and kept in scope: "The 2011 Broadcom onboard adapter stays supported (best effort); an RTL8761B-class USB dongle is the recommended path and must work plug-and-play." (DECISIONS.md:68-69). README.md:105-106 repeats this. README.md:276-277 adds: "Old Broadcom BT firmware has known unpatched weaknesses (KNOB/BIAS class)".
- Presence: CURRENT (DECISIONS.md:68-69; forinstall.md:131-132)
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The live checks in commits 4505247 and 0ad9f4e do not name the adapter in use.
- Why it turned out that way: UNKNOWN
- Depends on: the Macmini5,3 onboard controller and its firmware.
- Open questions: Which adapter was powered during the live checks in commits 4505247, bb2c661 and 0ad9f4e? No other adapter is documented as attached to the box.
- Confidence: LOW. INFERRED from partial material: only the onboard controller is listed for the box, but no statement ties a result to it.

### R6-S02-05  RTL8761B-class USB dongle named as the preferred radio
- Question: S02.2
- What was attempted: DECISIONS.md:68-69 and README.md:105-107 name an RTL8761B-class dongle as the "recommended" radio. The installer checks for the firmware file `rtl_bt/rtl8761bu_fw.bin` and prints a note when absent; it does not install it (install.sh:130-132). forinstall.md:77-78 lists firmware provision as [PENDING].
- Presence: CURRENT (install.sh:130-132; DECISIONS.md:68-69)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: the Realtek firmware file in /lib/firmware (install.sh:130); the kernel driver for the dongle (not named in the repo).
- Open questions: Was a dongle of this class ever connected to the reference box? install.sh:132 reads "only matters if you add an RTL8761B dongle".
- Confidence: MEDIUM. STATED preference; no evidence of use.

### R6-S02-06  Assumptions in code about adapter naming and count
- Question: S02.2
- What was attempted: the `btmgmt` index is derived by stripping the prefix `hci` from the adapter name (src/bluetooth/manager.rs:219); the AVRCP monitor builds the device path from the selected adapter and falls back to the literal `hci0` when none is marked selected (src/bluetooth/avrcp.rs:119-126); D-Bus paths are formed as `/org/bluez/<adapter>` (src/bluetooth/manager.rs:350); one adapter is in use at a time (src/bluetooth/manager.rs:43-44, 201-202). The installer and doctor call `bluetoothctl show` with no adapter argument (install.sh:233, 388; deploy/doctor.sh:20-26), which reads the default controller.
- Presence: CURRENT (citations above)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: BlueZ naming adapters `hciN`; the default controller reported by `bluetoothctl` being the adapter the app selected.
- Open questions: With two adapters, do the installer's sink-UUID and class checks read the adapter the app uses?
- Confidence: MEDIUM. INFERRED from clear code.

### R6-S02-07  No adapter at start-up: error status and a 10-second retry loop
- Question: S02.3
- What was attempted: when no adapter name is returned, `initialize` returns "no Bluetooth adapters present" (src/bluetooth/manager.rs:147-149); `run` logs it, sets status `Error(message)` and retries after 10 s, while still accepting `SelectAdapter` (src/bluetooth/manager.rs:70-86). The status and message reach clients as `bluetooth_status_changed` (src/web/ws.rs:148-151) and in `/api/status` (src/web/routes.rs:22-30). A configured adapter that is absent is replaced by the first adapter with a warning (src/bluetooth/manager.rs:153-156). The unit declares `Requires=bluetooth.service` (deploy/sonicflow.service:8). forinstall.md:64-65 lists an installer adapter enumeration as [PENDING]; install.sh has no such step (`grep -n hci install.sh deploy/*.sh uninstall.sh` returned nothing).
- Presence: CURRENT (src/bluetooth/manager.rs:67-87, 147-149)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: bluer returning an empty adapter list or an error when no adapter exists.
- Open questions: Was the service ever started with no adapter present?
- Confidence: MEDIUM. Clear code; no result evidence.

### R6-S02-08  Adapter blocked or unpowered
- Question: S02.3
- What was attempted: `set_powered(true)` at bring-up, with a warning on failure and no retry of that call (src/bluetooth/manager.rs:182-184); `[Policy] AutoEnable = true` in main.conf (install.sh:176); a doctor line "adapter not powered" (deploy/doctor.sh:25). The `rfkill` package is installed (install.sh:101) but no script or source file runs `rfkill` (`grep -rnE '\brfkill\b' src deploy install.sh uninstall.sh` matches only package lists at install.sh:101 and uninstall.sh:47).
- Presence: CURRENT (citations above)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: BlueZ `AutoEnable`; the adapter not being soft- or hard-blocked.
- Open questions: What does the manager report when the adapter is rfkill-blocked: an initialisation error, or status Ready with a logged warning? The code path at src/bluetooth/manager.rs:182-205 continues to Ready after a failed power-on.
- Confidence: MEDIUM. Clear code; no result evidence.

### R6-S02-09  Adapter reset or removal while the manager is running
- Question: S02.3
- What was attempted: no dedicated handling. The adapter handle is set once per initialisation (src/bluetooth/manager.rs:202) and cleared only by `select_adapter` (src/bluetooth/manager.rs:540; `grep -rn 'self.adapter = None' src` gives that one line). The poll reads `adapter.device_addresses().await.unwrap_or_default()` (src/bluetooth/manager.rs:620), so a failed read yields an empty list, after which every known device is removed from app state and `device_removed` events are published (src/bluetooth/manager.rs:692-712). Adapter properties (alias, discoverable, pairable) are applied only inside `initialize` (src/bluetooth/manager.rs:181-199).
- Presence: CURRENT (src/bluetooth/manager.rs:615-726)
- Outcome: UNVERIFIED
- Outcome evidence: NONE. BUILD-PLAN.md:79 lists "survives adapter reset" as a Phase 8 check; no result is recorded.
- Why it turned out that way: UNKNOWN
- Depends on: BlueZ keeping the same adapter object path after a reset; `bluetooth.service` restarts not being propagated to the app other than through `Requires=` (deploy/sonicflow.service:8).
- Open questions: After `bluetooth.service` restarts, is the alias, discoverable state and pairing agent re-applied? Does the agent registration made on the cached zbus connection (src/bluetooth/manager.rs:131-141) survive a BlueZ restart?
- Confidence: LOW. INFERRED from code; the behaviour of bluer after a reset is not shown in the repo.

### R6-S02-10  AVRCP monitor re-opens its D-Bus connection after five consecutive failures
- Question: S02.3
- What was attempted: the AVRCP monitor counts consecutive unexpected poll errors and drops its system-bus connection at `DBUS_RECONNECT_THRESHOLD = 5` (src/bluetooth/constants.rs:22; src/bluetooth/avrcp.rs:204-220), with the stated purpose: "Long-lived D-Bus connections go stale after adapter resets / suspend; reconnect past the threshold." (src/bluetooth/avrcp.rs:211-212). A failed connection attempt sleeps 5 s (src/bluetooth/avrcp.rs:88-98).
- Presence: CURRENT (src/bluetooth/avrcp.rs:188-221)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: errors after a reset not containing the strings "UnknownObject", "Unknown object" or "UnknownInterface", which reset the counter (src/bluetooth/avrcp.rs:197-203).
- Open questions: Was an adapter reset or suspend ever exercised with this code? An earlier form of this counter misfired; see R6-S06-08.
- Confidence: MEDIUM. Clear code; no result evidence.

---

## S03 Discoverability, pairing and trust

### R6-S03-01  Always discoverable and pairable, under an alias taken from configuration
- Question: S03.1
- What was attempted: at every initialisation the manager sets the adapter alias to `config.device_name`, discoverable timeout 0, discoverable on and pairable on (src/bluetooth/manager.rs:181-199). Default name "SonicFlow" (src/state/config.rs:25-26, 40; DECISIONS.md:87-88). The name can be changed through `POST /api/bluetooth/name` (1 to 64 characters, src/web/routes.rs:155-162), which sets the alias and stores it in `state.toml` (src/bluetooth/manager.rs:507-525). main.conf carries `Name = SonicFlow` and `DiscoverableTimeout = 0` (install.sh:171-172). No code switches discoverability off (`grep -rn set_discoverable src` gives src/bluetooth/manager.rs:191-195 only). README.md:138-140 and webui/src/components/Settings.tsx:595-599 tell the user the device is always visible while running.
- Presence: CURRENT (src/bluetooth/manager.rs:181-199)
- Outcome: WORKED
- Outcome evidence: STATED. Commit 4505247: "Verified live: scan found 19 devices, names resolved, alias=SonicFlow, discoverable/pairable on, scan start/stop clean".
- Why it turned out that way: UNKNOWN. One related incident is recorded: alias failure is logged at error level because "a silent alias failure once had a turntable rejecting the adapter because it advertised the hostname" (src/bluetooth/manager.rs:186-187).
- Depends on: the app process running (the doctor line at deploy/doctor.sh:26 reads "adapter not discoverable (is sonicflow running?)"); BlueZ accepting the property writes from the service user.
- Open questions: Does discoverability persist after the app stops? What name is advertised when the alias write fails?
- Confidence: MEDIUM. STATED evidence; code agrees.

### R6-S03-02  Class of Device set through main.conf `Class = 0x000414`
- Question: S03.1
- What was attempted: the installer writes `Class = 0x000414` (install.sh:170), described as Audio/Video major with HiFi minor, the service bits being expected from the registered A2DP profile to give 0x240414 on air (src/bluetooth/constants.rs:15-19; DECISIONS.md:70-71; forinstall.md:87-88). The installer's last step reads the class from `bluetoothctl show` and prints either an ok line or a note about one reboot (install.sh:388-392). README.md:250-252 lists a wrong class as a cause of phones listing the device without routing audio.
- Presence: CURRENT (install.sh:170, 388-392)
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The only recorded class value predates this edit: "Class of Device service bits changed 0x000104 → 0x0c0104" (forinstall.md:164), and the edit itself is marked [PENDING] at forinstall.md:84.
- Why it turned out that way: UNKNOWN
- Depends on: BlueZ ≥ 5.66 (DECISIONS.md:70); a controller re-read of main.conf, which install.sh:391 says may need a reboot.
- Open questions: What class did the adapter advertise after the installer ran? Did an iPhone list the device as a speaker, as install.sh:152 and install.sh:390 expect?
- Confidence: MEDIUM. Clear script and STATED intent; the result is not recorded.

### R6-S03-03  Class of Device set at run time through `btmgmt`
- Question: S03.1
- What was attempted: after setting the alias the manager runs `btmgmt --index <n> class 4 5` with a 5-second timeout and `kill_on_drop` (src/bluetooth/manager.rs:218-246; constants at src/bluetooth/constants.rs:20-21). All non-success outcomes are logged at debug level (src/bluetooth/manager.rs:237-245).
- Presence: CURRENT (src/bluetooth/manager.rs:190, 218-246)
- Outcome: FAILED
- Outcome evidence: STATED. Commit 4505247: "btmgmt CoD fallback with hard 5s timeout (btmgmt HANGS without CAP_NET_ADMIN instead of erroring)". src/bluetooth/manager.rs:216-217: "it usually needs CAP_NET_ADMIN, so failure is expected and only logged".
- Why it turned out that way: STATED. `btmgmt` needs CAP_NET_ADMIN (src/bluetooth/manager.rs:216-220). INFERRED in addition: the unit grants no capabilities, since deploy/sonicflow.service:10-27 has no capability directive and runs as an ordinary user.
- Depends on: the process privilege (deploy/sonicflow.service:12-14); `btmgmt` being installed.
- Open questions: Did the call ever succeed in any run? No log line "Class of Device set via btmgmt" (src/bluetooth/manager.rs:236) is recorded anywhere.
- Confidence: MEDIUM. STATED evidence that the code and the unit file agree with; no log artefact.

### R6-S03-04  Inbound pairing: default agent with NoInputNoOutput capability and automatic acceptance
- Question: S03.2
- What was attempted: a `org.bluez.Agent1` object at `/org/sonicflow/agent` is registered with capability "NoInputNoOutput" and requested as default agent (src/bluetooth/agent.rs:111-126; src/bluetooth/constants.rs:12), on a system-bus connection held by the manager (src/bluetooth/manager.rs:131-141). `RequestConfirmation` and `RequestAuthorization` return success when `auto_pair` is on and `org.bluez.Error.Rejected` when off (src/bluetooth/agent.rs:56-76, 30-36). `RequestPinCode` and `RequestPasskey` return fixed values regardless of `auto_pair` (src/bluetooth/agent.rs:44-52; values not copied). Registration failure is a warning and bring-up continues (src/bluetooth/manager.rs:134-136).
- Presence: CURRENT (src/bluetooth/agent.rs:1-126)
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Commit 4505247 lists "Auto-pair agent registered (org.bluez.Error.Rejected for denials)" among the features, not in its "Verified live" line. Commit 0ad9f4e reports an iPhone streaming, without saying how it was paired.
- Why it turned out that way: UNKNOWN
- Depends on: `auto_pair` (default true, src/state/config.rs:41; install.sh:336); BlueZ routing pairing requests to the default agent; the app process running.
- Open questions: Was a phone paired from a cold start through this agent alone? BUILD-PLAN.md:34 lists "cold-start pairing without bluetoothctl" as a check with no recorded result. Which agent callbacks does BlueZ invoke for an inbound pairing with this capability?
- Confidence: MEDIUM. Clear code; no result evidence.

### R6-S03-05  Outbound pairing started from the web UI or API
- Question: S03.2
- What was attempted: `POST /api/bluetooth/connect {address}` (src/web/mod.rs:51; src/web/routes.rs:111-122; API.md:29) makes the manager mark the device Pairing, then in a spawned task set it trusted, call `pair()` if not yet paired, then `connect()` (src/bluetooth/manager.rs:436-469). Failures are published as `error` events (src/bluetooth/manager.rs:453-466). Devices to connect to are found by an outbound scan (src/bluetooth/manager.rs:286-344), described in the UI as for "turntables and cassette adapters" (webui/src/components/Settings.tsx:595-599). No timeout wraps `pair()` or `connect()` (src/bluetooth/manager.rs:453, 461).
- Presence: CURRENT (src/bluetooth/manager.rs:436-469)
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Commit 4505247 verifies the scan ("scan found 19 devices"), not a pairing.
- Why it turned out that way: UNKNOWN
- Depends on: the remote device accepting pairing; the same agent as R6-S03-04 answering any prompt.
- Open questions: Was any device paired this way? The comment at src/bluetooth/manager.rs:186-187 mentions a turntable "rejecting the adapter" on one occasion, without a date or a follow-up result.
- Confidence: MEDIUM. Clear code; no result evidence.

### R6-S03-06  Pairing records left to BlueZ; the app persists no device list
- Question: S03.3
- What was attempted: the app's own persisted state (`state.toml`) holds name, adapter, auto-pair, EQ and output preferences and no device entries (src/state/config.rs:148-173). The in-memory device map is rebuilt from BlueZ on each poll (src/bluetooth/manager.rs:620-650) and cleared on adapter switch (src/bluetooth/manager.rs:536). No file in the repo reads or writes BlueZ's storage (`grep -rn 'var/lib/bluetooth\|LinkKey\|link.key'` over the tree, excluding .git, returned nothing). The uninstaller does not remove pairings (uninstall.sh:75-171 lists every removal).
- Presence: CURRENT (src/state/config.rs:148-173)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: BlueZ's own persistence of bonded devices.
- Open questions: Do pairings survive a reboot and an uninstall/reinstall on the reference box?
- Confidence: MEDIUM. INFERRED from clear code and a negative search.

### R6-S03-07  Device listing and trust flag synchronised by a 500 ms poll
- Question: S03.3
- What was attempted: every 500 ms (src/bluetooth/constants.rs:3; src/bluetooth/manager.rs:107, 115) the manager reads alias, RSSI, connected, paired, trusted, class and UUIDs for each BlueZ device and updates the app's device map (src/bluetooth/manager.rs:620-690); record fields at src/bluetooth/device.rs:77-92. The list is served by `GET /api/devices` (src/web/routes.rs:32-35), in the WebSocket snapshot (src/state/mod.rs:212) and in Settings (webui/src/components/Settings.tsx:616-653). The trusted flag is set by the app only in the outbound path (`grep -rn set_trusted src` gives src/bluetooth/manager.rs:448-449 only); nothing sets it for a phone that pairs inbound. The list is primed right after bring-up (src/bluetooth/manager.rs:210-211).
- Presence: CURRENT (src/bluetooth/manager.rs:615-726)
- Outcome: WORKED
- Outcome evidence: STATED. Commit 4505247: "Verified live: scan found 19 devices, names resolved, alias=SonicFlow, discoverable/pairable on, scan start/stop clean". This covers listing of scanned devices; it does not mention paired devices or the trust flag.
- Why it turned out that way: UNKNOWN
- Depends on: BlueZ device properties over D-Bus; the 500 ms interval.
- Open questions: Is an inbound-paired phone left untrusted, and does that affect later connections? Were paired devices shown at boot, as the comment at src/bluetooth/manager.rs:210 intends?
- Confidence: MEDIUM. STATED evidence for one part of the behaviour.

### R6-S03-08  Device removal ("Forget")
- Question: S03.3
- What was attempted: `DELETE /api/bluetooth/device {address}` (src/web/mod.rs:56; src/web/routes.rs:137-148; API.md:31) calls `adapter.remove_device`, clears the device from the activity set and the active-device field, removes it from app state and publishes `device_removed` (src/bluetooth/manager.rs:486-505). A failed BlueZ removal is a warning and the app entry is removed all the same (src/bluetooth/manager.rs:490-503). UI button at webui/src/components/Settings.tsx:643-649. Disconnect without removal: src/bluetooth/manager.rs:471-484.
- Presence: CURRENT (src/bluetooth/manager.rs:486-505)
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Commit 3d4afd4 states the routes were "smoke-tested via curl (200/400/404 as documented", which is the HTTP status only.
- Why it turned out that way: UNKNOWN
- Depends on: BlueZ `RemoveDevice`; no timeout on the call (src/bluetooth/manager.rs:490).
- Open questions: After a failed BlueZ removal, does the next poll re-add the device (src/bluetooth/manager.rs:646-649)?
- Confidence: MEDIUM. Clear code; no result evidence.

### R6-S03-09  Additional devices: accepted with no limit or refusal logic
- Question: S03.4
- What was attempted: no code limits the number of paired or connected devices. The agent's answers do not depend on whether another device is connected (src/bluetooth/agent.rs:56-94). Discoverable and pairable stay on after a connection (R6-S03-01). The poll tracks every BlueZ device independently (src/bluetooth/manager.rs:624-690). Searches: `grep -rnEi 'max_devices|second (device|phone)|multiple (phones|sources)|one source|single source'` over .rs, .md, .ts, .tsx and .sh files, excluding SOUNDSYNC-SPEC.md, returned no relevant match. The predecessor document lists "Multi-device concurrent A2DP" as a gap (SOUNDSYNC-SPEC.md:2945); DECISIONS.md:99-100 does not include it among items addressed in this repo.
- Presence: CURRENT (src/bluetooth/agent.rs:56-94; src/bluetooth/manager.rs:191-199)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: BlueZ and WirePlumber defaults for concurrent A2DP source connections, which the repo does not configure (install.sh:187-206 has no connection-count setting).
- Open questions: What happens on the reference box when a second phone pairs or connects while the first is streaming? Is the first stream interrupted, mixed or unaffected? See R6-S04-09 for the app-side state handling.
- Confidence: MEDIUM. INFERRED from clear code and a negative search.

### R6-S03-10  `auto_pair` switch in config, state file, API and Settings
- Question: S03.5
- What was attempted: one boolean gates the agent. Sources in rising precedence: default true (src/state/config.rs:41), `auto_pair` in config files (src/state/config.rs:54, 72-74; install.sh:336), `state.toml` value written by the UI (src/state/config.rs:98-100, 157-158). `POST /api/settings {auto_pair}` (src/web/routes.rs:69-75; API.md:23) sends `SetAutoPair`, which flips a shared atomic read by the live agent and persists it (src/bluetooth/manager.rs:273-282; src/bluetooth/agent.rs:10-20). UI toggle at webui/src/components/Settings.tsx:673-682. README.md:265-267 describes the intended use ("Turn it off in Settings once your devices are paired"). The HTTP API has no authentication (API.md:14; README.md:261-262), so any LAN client can change the switch. There is no per-device allowlist or blocklist (the device record has no such field, src/bluetooth/device.rs:77-92).
- Presence: CURRENT (src/bluetooth/manager.rs:273-282; src/bluetooth/agent.rs:56-76)
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Commit 9d619ed lists it as "New: auto-pair toggle end to end", a description of scope, with no pairing attempt recorded with the switch off.
- Why it turned out that way: UNKNOWN
- Depends on: BlueZ consulting the agent for inbound pairing; the fixed PIN and passkey replies not being gated by the switch (src/bluetooth/agent.rs:44-52).
- Open questions: With the switch off, is an inbound pairing from a new phone refused in practice? Are already-paired devices affected?
- Confidence: MEDIUM. Clear code; no result evidence.

### R6-S03-11  Service-UUID allowlist in `AuthorizeService`
- Question: S03.5
- What was attempted: `AuthorizeService` always authorises five audio UUID prefixes (A2DP source and sink, AVRCP target and controller, generic audio; src/bluetooth/agent.rs:22-28, 78-86) and authorises any other service only when `auto_pair` is on (src/bluetooth/agent.rs:87-93). The doc comment calls this "hardening for spec gap G7" (src/bluetooth/agent.rs:7-9); the predecessor gap is at SOUNDSYNC-SPEC.md:2951. `bluez5.hfphsp-backend = none` in the WirePlumber fragment switches headset profiles off (install.sh:204; forinstall.md:106).
- Presence: CURRENT (src/bluetooth/agent.rs:22-28, 78-94)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: BlueZ calling `AuthorizeService` for untrusted devices; the device not being marked trusted (R6-S03-07).
- Open questions: With `auto_pair` off, a device that is already bonded still has its audio services authorised (src/bluetooth/agent.rs:84-86). Is that the intended scope of the switch? README.md:265-266 says the agent "only auto-authorizes audio profiles", while src/bluetooth/agent.rs:87-89 authorises every service when `auto_pair` is on.
- Confidence: MEDIUM. Clear code; no result evidence.

### R6-S03-12  Earlier form: `auto_pair` fixed at agent registration
- Question: S03.5
- What was attempted: from commit d8b17a3 to commit b19435c the agent held a plain boolean copied from config when it was registered (src/bluetooth/agent.rs:9, 101-104 at commit b19435c; src/bluetooth/manager.rs:120-123 at commit b19435c). There was no API or UI control.
- Presence: REMOVED. Commit 9d619ed replaced the boolean with a shared atomic and added `SetAutoPair` (diff of src/bluetooth/agent.rs, src/bluetooth/manager.rs and src/bluetooth/mod.rs in that commit).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: STATED for the change, not for a result: the flag is shared "so the Settings toggle flips the LIVE agent without re-registering it (decision D3)" (src/bluetooth/agent.rs:11-12).
- Depends on: decision D3 placing an auto-pair toggle in Settings (DECISIONS.md:28-32).
- Open questions: NONE.
- Confidence: HIGH for the removal (commit diff); the outcome is not evidenced.

---

## S04 Connection and A2DP profile

### R6-S04-01  A2DP sink role enabled through a WirePlumber fragment with seat monitoring switched off
- Question: S04.1
- What was attempted: the installer writes `/etc/wireplumber/wireplumber.conf.d/51-sonicflow.conf` (install.sh:185-212) with `monitor.bluez.seat-monitoring = disabled` and `monitor.bluez-midi.seat-monitoring = disabled` under `wireplumber.profiles.main` (install.sh:192-197) and `bluez5.roles = [ a2dp_sink a2dp_source ]`, `bluez5.hfphsp-backend = none` under `monitor.bluez.properties` (install.sh:199-205), then restarts WirePlumber (install.sh:228-231). During development the same content lived in the user's `~/.config/wireplumber/wireplumber.conf.d/` (forinstall.md:94-96, 161-163); the installer deletes that copy (install.sh:213-219). The app itself registers no media endpoint: "It MUST NOT register `org.bluez.MediaEndpoint1` objects" (src/bluetooth/mod.rs:3-5); no revision of the repo contains endpoint code (`git log --all --diff-filter=A --name-only | grep -i endpoint` returned nothing; `grep -rn 'RegisterEndpoint\|MediaTransport' src` returned nothing).
- Presence: CURRENT (install.sh:183-231; src/bluetooth/mod.rs:1-8)
- Outcome: WORKED
- Outcome evidence: STATED. Commit bb2c661: "Audio Sink 110b now advertised with sbc/sbc_xq/aptx/aptx_hd/ldac endpoints." forinstall.md:163-164: "Verified: `bluetoothctl show` now lists Audio Sink 0000110b". Both statements were made with the user-level copy of the fragment (forinstall.md:94, 161).
- Why it turned out that way: STATED. forinstall.md:101-103: "every session is SSH (no active seat), so the bluez monitor logged \"Seat state changed: closing\" and unregistered its endpoints immediately". The setting is a profile feature, "not a `wireplumber.settings` key" (forinstall.md:98-99). The same account is at README.md:243-248.
- Depends on: WirePlumber 0.5.13 and its `bluez.lua` monitor script (forinstall.md:62, 98); a headless box with SSH or tty sessions only (install.sh:189-191); `libspa-0.2-bluetooth` (install.sh:99); linger keeping the user's WirePlumber running (install.sh:135-141); the endpoint being owned by WirePlumber alone (src/bluetooth/mod.rs:6-8 names the failure seen when an app registers its own endpoint: "paired, connected, and silent").
- Open questions: Was the system-wide copy under /etc verified separately from the user-level copy? Commit 1e20b41 states the doctor script was "all-green on this box" without saying which copy was active.
- Confidence: HIGH. STATED evidence in two places, and the installer content agrees with it.

### R6-S04-02  `MemoryDenyWriteExecute=no` override on the WirePlumber user unit, on its own
- Question: S04.1
- What was attempted: a drop-in `~/.config/systemd/user/wireplumber.service.d/override.conf` with `[Service] MemoryDenyWriteExecute=no`, with the stated reason that Bluetooth codec sub-plugins "need W|X mmap" (forinstall.md:89-91; install.sh:220-227). It existed on the reference box before the project (forinstall.md:42-43).
- Presence: CURRENT. The installer ensures the drop-in (install.sh:220-227); the uninstaller leaves it (uninstall.sh:110-111).
- Outcome: FAILED (as the only measure, for sink registration)
- Outcome evidence: STATED. forinstall.md:92-93: "exists on this box already but A2DP endpoints STILL not registering as of 2026-08-29".
- Why it turned out that way: STATED. The endpoints were being unregistered by seat monitoring, a separate cause (forinstall.md:97-103); see R6-S04-01.
- Depends on: the same headless-session condition as R6-S04-01.
- Open questions: Is the drop-in needed at all once seat monitoring is off? No test without it is recorded; forinstall.md:166-168 keeps it on the stated ground of the ldac/aptx plugins.
- Confidence: MEDIUM. STATED evidence alone.

### R6-S04-03  Sink-UUID checks in the installer and the doctor script
- Question: S04.1
- What was attempted: `check_sink_uuid` greps `bluetoothctl show` for the Audio Sink UUID (install.sh:233); if absent the installer restarts WirePlumber once and checks again (install.sh:234-241); the last step repeats the check as an "acceptance gate" (install.sh:384-387; forinstall.md:127-128). The doctor script has the same check (deploy/doctor.sh:20-24) and a check that the SPA bluez5 plugin directory exists (deploy/doctor.sh:35-39).
- Presence: CURRENT (install.sh:232-241, 384-387; deploy/doctor.sh:20-24)
- Outcome: WORKED (doctor check); the installer's restart-once path has no recorded result
- Outcome evidence: STATED. Commit 1e20b41: "deploy/doctor.sh diagnostics (all-green on this box)".
- Why it turned out that way: UNKNOWN
- Depends on: `bluetoothctl show` reporting the adapter in use (R6-S02-06); the fixed sleeps of 4 and 5 seconds after a WirePlumber restart (install.sh:230, 239).
- Open questions: Did the installer's self-heal branch (install.sh:237-240) ever run, and with what result?
- Confidence: MEDIUM. STATED evidence alone, covering one of the two scripts.

### R6-S04-04  Codec list in the WirePlumber fragment; negotiation left to WirePlumber
- Question: S04.2
- What was attempted: `bluez5.codecs = [ sbc sbc_xq aac ldac aptx aptx_hd ]` and `bluez5.enable-sbc-xq = true` (install.sh:201-202). The app takes no part in negotiation (src/bluetooth/mod.rs:3-5; README.md:81-83). The MDWE drop-in is kept for the ldac and aptx plugins (install.sh:220; forinstall.md:166-168).
- Presence: CURRENT (install.sh:199-205)
- Outcome: PARTIAL. Endpoints registered for sbc, sbc_xq, aptx, aptx_hd and ldac; the `aac` entry has no effect on the reference system.
- Outcome evidence: STATED. forinstall.md:103-104: "advertises Audio Sink 0000110b and endpoints for sbc/sbc_xq/aptx/aptx_hd/ldac". forinstall.md:108: "\"aac\" in the codecs list is inert; phones fall back to SBC-XQ/LDAC/aptX".
- Why it turned out that way: STATED. "Ubuntu ships no AAC codec .so in /usr/lib/x86_64-linux-gnu/spa-0.2/bluez5/ (licensing)" (forinstall.md:107-108).
- Depends on: the codec plugin files shipped by Ubuntu 26.04's `libspa-0.2-bluetooth`; WirePlumber's selection among the offered codecs; the source device's own codec support.
- Open questions: Which codec did the iPhone named in commit 0ad9f4e actually negotiate? Was LDAC or aptX ever negotiated with any device? No per-device codec is recorded anywhere.
- Confidence: MEDIUM. STATED evidence alone.

### R6-S04-05  Codec field in the device model and stream events, never populated
- Question: S04.2
- What was attempted: an `AudioCodec` enum (Sbc, Aac, Ldac, AptX, AptXHd; src/bluetooth/device.rs:67-75), a `codec` field on the device record (src/bluetooth/device.rs:85), a `codec` field on `StreamStarted` (src/state/mod.rs:307-310) and in the wire message (src/web/ws.rs:176-179; API.md:107). The field is always empty: initialised to None (src/bluetooth/device.rs:103) and published as None (src/bluetooth/manager.rs:759-762); no code assigns it (`grep -rnE '\.codec\s*=|codec: Some|AudioCodec::' src` matches only the serialisation test at src/bluetooth/device.rs:285). The web UI has no codec field or display (`grep -rnEi 'codec|aptx|ldac|sbc' webui/src` returned nothing).
- Presence: DISABLED. Declared and serialised but unreachable with a value (citations above).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. INFERRED context: with WirePlumber owning the transport, the app has no code path that reads the negotiated configuration (no `MediaTransport1` use in src).
- Depends on: a source of codec information the app does not currently read.
- Open questions: BUILD-PLAN.md:53-54 mentions "tokens for codec chips"; was a codec display started and dropped, or never begun?
- Confidence: MEDIUM. INFERRED from clear code and negative searches.

### R6-S04-06  Connection state tracking by polling BlueZ device properties
- Question: S04.3
- What was attempted: on each 500 ms poll a device that BlueZ reports connected is moved to Connected; a device not connected but paired is moved to Paired; otherwise to Disconnected unless it is a scan result (src/bluetooth/manager.rs:658-674). Devices BlueZ no longer lists are removed (src/bluetooth/manager.rs:692-712). Changes are published as `device_state_changed` and `device_removed` (src/bluetooth/manager.rs:676-716; src/web/ws.rs:163-173). Transitions outside the table at src/bluetooth/device.rs:36-56 are applied anyway with a warning (src/bluetooth/device.rs:111-124). An out-of-range source has no dedicated handling: it is seen only as `connected` turning false and as the stream node disappearing (R6-S05-08). User-initiated connect and disconnect: src/bluetooth/manager.rs:436-484.
- Presence: CURRENT (src/bluetooth/manager.rs:615-726; src/bluetooth/device.rs:6-57)
- Outcome: UNVERIFIED
- Outcome evidence: NONE for connect, disconnect and loss of range as such. The transition into the streaming state is covered by R6-S05-08. Commit 4505247 lists "connect/disconnect state sync" as a feature, outside its "Verified live" line.
- Why it turned out that way: UNKNOWN
- Depends on: BlueZ `Connected` and `Paired` properties; the 500 ms interval; no D-Bus signal handling (the inverse path helper is marked "kept for D-Bus signal handling" and unused, src/bluetooth/constants.rs:24-26).
- Open questions: How long after a phone walks out of range does the state change, and does the stream-stopped event follow? SOUNDSYNC-SPEC.md:3102 lists "Disconnect/reconnect the phone → state updates correctly" as a predecessor checklist item; BUILD-PLAN.md:94 refers to that checklist with no recorded result.
- Confidence: MEDIUM. Clear code; no result evidence.

### R6-S04-07  Two declared device states that no code enters
- Question: S04.3
- What was attempted: the state enum includes ProfileNegotiated and PipewireSourceReady (src/bluetooth/device.rs:14-15, 27-28) with table entries (src/bluetooth/device.rs:49-53) and UI labels (webui/src/components/Settings.tsx:540-541; webui/src/api.ts:30-31; webui/src/app.tsx:123). No code sets either state: `grep -rn 'ProfileNegotiated\|PipewireSourceReady' src` shows only the enum, the table, tests, and a match arm that reads them (src/bluetooth/manager.rs:744-745).
- Presence: DISABLED. Declared and handled downstream but never produced (citations above).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. The table's doc comment notes "BlueZ skips states in practice" (src/bluetooth/device.rs:33-35).
- Depends on: nothing at run time.
- Open questions: Were these states carried over from the predecessor's eight-state model (BUILD-PLAN.md:31 "8-state device model") without a producer?
- Confidence: MEDIUM. INFERRED from clear code and a search.

### R6-S04-08  Reconnection and reboot: no reconnect code in the app; provisions around it
- Question: S04.3
- What was attempted: the app has no automatic Bluetooth reconnect, retry or backoff (search: `grep -rnEi 'reconnect|out of range|link.?loss|supervision'` over .rs, .md, .sh, .ts, .tsx and .service files, excluding SOUNDSYNC-SPEC.md; the Bluetooth-side matches are only the AVRCP D-Bus counter of R6-S02-10). The `auto_reconnect` setting applies to the last Cast device (src/main.rs:201-219; webui/src/components/Settings.tsx:506-511). Provisions that bear on reconnection and reboot: trust is set before an outbound connect "so profiles auto-connect" (src/bluetooth/manager.rs:447-450); `[Policy] AutoEnable = true` powers the adapter at boot (install.sh:176); linger keeps the user's PipeWire and WirePlumber running with no login (install.sh:135-141); the unit starts after `bluetooth.service`, is enabled at boot and restarts on failure (deploy/sonicflow.service:6-8, 26-30; install.sh:356); adapter properties and the agent are re-applied at each start (src/bluetooth/manager.rs:125-199). The predecessor document lists "No auto-reconnect and no D-Bus operation timeouts" as defect D22 (SOUNDSYNC-SPEC.md:2899-2905); DECISIONS.md:99-100 does not list D22 among the items addressed.
- Presence: CURRENT (the provisions cited above)
- Outcome: UNVERIFIED
- Outcome evidence: NONE. BUILD-PLAN.md:25-26 ("survives reboot") and BUILD-PLAN.md:100 ("reboot → everything works untouched") are checks with no recorded result.
- Why it turned out that way: UNKNOWN. One related cause is STATED for the linger provision: "Was Linger=no; the whole PipeWire user stack died when the last SSH session ended." (forinstall.md:82-83).
- Depends on: the source device initiating reconnection; BlueZ accepting it for an untrusted, inbound-paired device (R6-S03-07); the app and its agent running; linger.
- Open questions: After a reboot, does a previously paired phone reconnect and stream without user action on the box? After the phone returns into range, who reconnects?
- Confidence: MEDIUM. INFERRED from clear code and a negative search.

### R6-S04-09  Multiple sources: per-address activity set, one active-device field, first-match player choice
- Question: S04.4
- What was attempted: no priority, switching or exclusion logic. Stream activity is tracked per address in a set (src/bluetooth/manager.rs:46-47, 733-772). A single `active_device` field is overwritten by whichever address starts most recently (src/bluetooth/manager.rs:750) and is cleared when that address stops (src/bluetooth/manager.rs:786-788); an address already in the set does not raise a new start (src/bluetooth/manager.rs:734-736), so after the latest source stops the field stays empty while an earlier source is still in the set. The AVRCP monitor uses `active_device`, else the first device in the map that is Connected or AudioActive (src/bluetooth/avrcp.rs:102-114), and the first sorted `MediaPlayer1` path under that device (src/bluetooth/avrcp.rs:328-352). The UI shows one source (webui/src/app.tsx:104-134). Audio from every Bluetooth node is expected to reach the same default sink (README.md:54-55); no code in the repo links or separates the nodes (R6-S05-01).
- Presence: CURRENT (citations above)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: BlueZ and WirePlumber permitting more than one A2DP source at once (not configured by the repo); PipeWire mixing streams that target the same sink.
- Open questions: Were two sources ever connected at once? If two streams play, are they mixed? Which device do the transport buttons control in that case? The predecessor document states "the pipeline assumes one source" (SOUNDSYNC-SPEC.md:2945).
- Confidence: MEDIUM. INFERRED from clear code; the stack's behaviour is outside the repo.

---

## S05 Audio capture

### R6-S05-01  Hand-off from the Bluetooth node to the project's sinks by default-sink routing
- Question: S05.1
- What was attempted: WirePlumber creates a `bluez_input.<address>` node for a connected source; the project relies on that node being connected automatically to the default sink (README.md:54-55 "(autoconnect to default sink)"). The app sets the default sink to `effect_input.sonicflow-eq` when the EQ is on, else to the null sink `sonicflow-capture`: at start-up (src/audio/pipeline.rs:66-81), on each EQ change (src/audio/pipeline.rs:110-129), and again when a stream start is detected (src/bluetooth/manager.rs:763-771), via `pactl set-default-sink` with a `wpctl` path (src/audio/pipeline.rs:288-314). The EQ's output targets the null sink (src/dsp/equalizer.rs:134-140). No code creates a link from the Bluetooth node: the searches `grep -rnE 'autoconnect|node\.target|target\.object|bluez_input|bluez_source'` and `grep -rn 'Command::new("pw-link")' src` show the only `pw-link` call touching Bluetooth nodes is the read-only listing at src/bluetooth/manager.rs:837, and the WirePlumber fragment has no routing rule (install.sh:187-206). The doctor script warns when the default sink is neither of the two: "BT audio will NOT reach SonicFlow" (deploy/doctor.sh:43-47).
- Presence: CURRENT (src/audio/pipeline.rs:53-86, 288-314; src/bluetooth/manager.rs:763-771)
- Outcome: WORKED
- Outcome evidence: STATED, in general terms. README.md:283-285: "verified end-to-end on real hardware: real phones, a house full of real Chromecasts, and one very patient 2011 Mac mini". No statement names Bluetooth audio arriving at the capture monitor specifically; commit 174ec2f notes "Audible check pending a Bluetooth source." and no later commit closes that note explicitly.
- Why it turned out that way: UNKNOWN. A known fragility is STATED: "something may have stolen the default sink since startup; audio must land in the EQ/capture chain" (src/bluetooth/manager.rs:763-764); README.md:252-253 lists "the default sink got moved off the EQ/capture sink" as a cause of paired-but-silent.
- Depends on: WirePlumber's default policy of connecting a Bluetooth playback-role stream to the default sink; `bluez5.a2dp-source-role = playback` being "the default on PipeWire ≥ 1.x" (src/bluetooth/manager.rs:821); the app owning the default sink (DECISIONS.md:58); nothing else changing the default sink between polls.
- Open questions: Is there a recorded instance of Bluetooth audio measured at `sonicflow-capture.monitor`? What reconnects the Bluetooth node when the default sink changes during playback (for instance when the EQ process is restarted, src/audio/pipeline.rs:110-116)?
- Confidence: MEDIUM. STATED evidence alone, and it is general.

### R6-S05-02  Entry into the project's process: `parec` on the null-sink monitor, read from a pipe
- Question: S05.1
- What was attempted: the pipeline creates or adopts the null sink `sonicflow-capture` (`pactl load-module module-null-sink`, src/audio/pipeline.rs:131-170; `pw-loopback` path at 172-189) and spawns `parec --raw --format=float32 --channels=2 --rate=48000 --device=sonicflow-capture.monitor --latency-msec=50` (src/audio/capture.rs:61-84). A tokio task reads the child's stdout pipe in fixed frames and sends them on a broadcast channel (src/audio/capture.rs:117-144), which feeds the spectrum analyser and the WebRTC manager (src/main.rs:64-66, 76, 87). The child's stderr is logged (src/audio/capture.rs:106-115).
- Presence: CURRENT (src/audio/capture.rs:61-148)
- Outcome: WORKED
- Outcome evidence: STATED. Commit b19435c: "Verified live in headless Chromium: 440Hz tone through the EQ sink lights the correct bars end-to-end". The signal in that check was a test tone, not a Bluetooth source.
- Why it turned out that way: UNKNOWN
- Depends on: `parec` from `pulseaudio-utils` (install.sh:99); pipewire-pulse being reachable (readiness probe, src/audio/pipeline.rs:213-241); the null sink existing before capture starts (src/audio/pipeline.rs:62-64).
- Open questions: NONE beyond R6-S05-01.
- Confidence: MEDIUM. STATED evidence; code agrees.

### R6-S05-03  `pw-cat` capture path used when `parec` is not found
- Question: S05.1
- What was attempted: when `which parec` fails the capture spawns `pw-cat --format f32 --channels 2 --rate 48000 -r [--target <node>] -` (src/audio/capture.rs:85-104; `which_exists` at src/audio/filter_chain.rs:100).
- Presence: CURRENT, reachable only when `parec` is absent (src/audio/capture.rs:71, 85)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: a system without `pulseaudio-utils`; the installer always installs that package (install.sh:99).
- Open questions: Was this path ever run? With `--target sonicflow-capture` and `-r`, does `pw-cat` record the sink's monitor?
- Confidence: MEDIUM. Clear code; no result evidence.

### R6-S05-04  Capture source tiers: direct Bluetooth node, then default monitor
- Question: S05.1
- What was attempted: source resolution in three tiers: the null-sink monitor; if the null sink is missing, the first `bluez_input.*`/`bluez_source.*` name found by `pactl list short sources` or by scanning `pw-dump` output, with a warning that the EQ is bypassed; otherwise `@DEFAULT_MONITOR@` (src/audio/capture.rs:46-59, 64-68, 175-218). The HTTP stream path has the same tiers (src/audio/cast_stream.rs:129-146).
- Presence: CURRENT, reachable only when the null sink is absent (src/audio/capture.rs:50-58)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: null-sink creation having failed (src/audio/pipeline.rs:62-64 logs "capture will degrade"); the Bluetooth node being listed as a source, which the manager's comment says is not the case for stream-role nodes: "the phone's node is a STREAM (media.class Stream/Output/Audio), which `pactl list short sources` does NOT list" (src/bluetooth/manager.rs:822-823).
- Open questions: Can `parec --device=<bluez_input name>` record from a stream-role node at all? The source is resolved once, at capture start (src/audio/capture.rs:63); what happens when no Bluetooth node exists yet at that moment?
- Confidence: LOW. INFERRED from code whose run-time behaviour depends on PipeWire details not shown in the repo.

### R6-S05-05  Second capture point: one `parec`→`ffmpeg` pipe chain per HTTP stream client
- Question: S05.1
- What was attempted: each request to `/api/stream/audio.aac` or `.mp3` spawns its own `parec --device=sonicflow-capture.monitor --format=s16le --rate=48000 --channels=2 --latency-msec=50` whose stdout file descriptor is handed to `ffmpeg` stdin (src/audio/cast_stream.rs:49-98), bypassing the broadcast channel by design (src/audio/cast_stream.rs:12-15). Both children are killed when the client disconnects (src/audio/cast_stream.rs:41-47, 105-106).
- Presence: CURRENT (src/audio/cast_stream.rs:12-127)
- Outcome: UNVERIFIED
- Outcome evidence: NONE for audio content. Commit 3d4afd4 states only "AAC stream headers + icy-name verified".
- Why it turned out that way: UNKNOWN. The stated reason for a separate chain: "the channel adds timing jitter that manifests as audible stuttering" (src/audio/cast_stream.rs:13-14).
- Depends on: `parec` and `ffmpeg` on the host; one process pair per client.
- Open questions: Was audio from a Bluetooth source ever heard through this path? Commit 9d619ed leaves Cast "live sessions" for "a supervised test".
- Confidence: MEDIUM. Clear code; no result evidence for audio.

### R6-S05-06  Format at the in-process capture point: raw PCM, 32-bit float little-endian, stereo, 48 kHz, 20 ms frames
- Question: S05.2
- What was attempted: "Canonical internal PCM: 48 kHz, 2ch, f32 LE interleaved, 20 ms frames" (src/audio/capture.rs:10); frame size 7680 bytes = 960 frames × 2 channels × 4 bytes (src/audio/capture.rs:11); requested from `parec` as `--format=float32 --channels=2 --rate=48000 --raw` (src/audio/capture.rs:72-80) and decoded with `f32::from_le_bytes` (src/audio/capture.rs:130-135). The EQ sink is declared at 48000 Hz, 2 channels, FL FR (src/dsp/equalizer.rs:127-133). The null sink is created with no format arguments (src/audio/pipeline.rs:142-148). The audio at this point is PCM already decoded by PipeWire's Bluetooth plugin; the repo does not set or read the format of the Bluetooth node itself (no rate or format key in install.sh:199-205).
- Presence: CURRENT (src/audio/capture.rs:10-15, 71-84, 122-144)
- Outcome: WORKED
- Outcome evidence: STATED. Commit b19435c: "440Hz tone through the EQ sink lights the correct bars end-to-end". INFERRED link: a tone landing in the expected bars indicates the rate and sample layout are interpreted as declared.
- Why it turned out that way: UNKNOWN
- Depends on: pipewire-pulse converting whatever the graph carries to the requested format; the codec's native rate (for instance 44.1 kHz sources) being resampled by PipeWire, outside the repo.
- Open questions: What sample rate and format does the `bluez_input` node carry for each codec on the reference box? Where is resampling to 48 kHz done, and was its effect ever checked?
- Confidence: MEDIUM. STATED result with an INFERRED link to the format.

### R6-S05-07  Format at the HTTP-stream capture point: raw PCM, signed 16-bit little-endian, stereo, 48 kHz
- Question: S05.2
- What was attempted: `parec --format=s16le --rate=48000 --channels=2` (src/audio/cast_stream.rs:53-60), matched by `ffmpeg -f s16le -ar 48000 -ac 2 -i pipe:0` (src/audio/cast_stream.rs:72-87).
- Presence: CURRENT (src/audio/cast_stream.rs:53-89)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: `parec` defaulting to raw output when `--raw` is not given (the flag is present at src/audio/capture.rs:74 and absent at src/audio/cast_stream.rs:54-60).
- Open questions: Is the absence of `--raw` here deliberate? The two capture commands differ on this flag and no comment explains it.
- Confidence: MEDIUM. Clear code; no result evidence.

### R6-S05-08  Stream start and stop inferred by polling node names from `pactl` sources and `pw-link` ports
- Question: S05.3
- What was attempted: every 500 ms the manager runs `pactl list short sources` and `pw-link -o`, scans every whitespace token for the prefixes `bluez_input.` or `bluez_source.`, and converts the next 17 characters to an address (src/bluetooth/manager.rs:820-866). A newly seen address whose device is Connected becomes AudioActive, is recorded as `active_device`, and a `StreamStarted` event is published; the default sink is re-asserted (src/bluetooth/manager.rs:729-773). An address that disappears produces `StreamStopped` and a return to Connected (src/bluetooth/manager.rs:775-817). A running outbound scan is stopped while any address is active (src/bluetooth/manager.rs:720-725). The signal is the existence of the node, not the presence of audio on it (src/bluetooth/manager.rs:728). Wire messages: src/web/ws.rs:174-182. Unit tests for the parser: src/bluetooth/manager.rs:868-903.
- Presence: CURRENT (src/bluetooth/manager.rs:728-866)
- Outcome: WORKED
- Outcome evidence: STATED. Commit 0ad9f4e: "iPhone now transitions to audio_active."
- Why it turned out that way: STATED. On PipeWire ≥ 1.x the node is a stream and "its ports still appear in `pw-link -o`" (src/bluetooth/manager.rs:823-824; commit 0ad9f4e).
- Depends on: PipeWire 1.6.2 node naming `bluez_input.<address>.<n>` (src/bluetooth/manager.rs:824); `pw-link` and `pactl` on the path; the device being in state Connected when the node appears (src/bluetooth/manager.rs:740-746); the 500 ms interval; an iPhone as the one named test device (commit 0ad9f4e).
- Open questions: Does the node disappear when the phone pauses, or only when it disconnects? When does `StreamStopped` fire in each case? Was any device other than an iPhone observed?
- Confidence: HIGH. STATED evidence that the code agrees with.

### R6-S05-09  Earlier detection from `pactl list short sources` alone
- Question: S05.3
- What was attempted: from commit 4505247 until commit 0ad9f4e the detector read only `pactl list short sources` and took the second column of each line (src/bluetooth/manager.rs:810-823 at commit 8d2e000).
- Presence: REMOVED. Replaced in commit 0ad9f4e (diff of src/bluetooth/manager.rs in that commit).
- Outcome: FAILED
- Outcome evidence: STATED. Commit 0ad9f4e: "PipeWire >=1.x creates the phone's A2DP node as a STREAM (role=playback), invisible to 'pactl list short sources'" and "iPhone now transitions to audio_active."
- Why it turned out that way: STATED. The node's media class is a stream, which the source listing omits (src/bluetooth/manager.rs:820-825; commit 0ad9f4e).
- Depends on: PipeWire ≥ 1.x with the playback role as default (src/bluetooth/manager.rs:821).
- Open questions: NONE.
- Confidence: HIGH. STATED in the commit and the code comment; the diff agrees.

### R6-S05-10  Doctor script's count of live Bluetooth sources, from `pactl` sources alone
- Question: S05.3
- What was attempted: the doctor script counts lines of `pactl list short sources` matching `bluez_(input|source)` and prints the count with an ok marker whatever the number (deploy/doctor.sh:49-50). It uses the listing that R6-S05-09 records as not showing stream-role nodes.
- Presence: CURRENT (deploy/doctor.sh:49-50)
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Commit 1e20b41's "all-green" statement does not give the count, and the line is ok at any count.
- Why it turned out that way: UNKNOWN
- Depends on: the Bluetooth node being listed as a source.
- Open questions: What count does this line print on the reference box while a phone is streaming?
- Confidence: MEDIUM. INFERRED from clear code and the STATED behaviour of the listing.

### R6-S05-11  Capture buffering: 50 ms request, 20 ms frames, 256-frame broadcast channel
- Question: S05.4
- What was attempted: `--latency-msec=50` on `parec`, with the stated rule "parec buffer MUST exceed the PipeWire quantum (default 1024 ≈ 21.3 ms)" and "(Fixes SoundSync D2.)" (src/audio/capture.rs:13-15, 79); a buffered reader of four frames (src/audio/capture.rs:123); `read_exact` of one 20 ms frame as the only clock: "read_exact blocking until a full 20 ms frame exists IS the system's pacer" (src/audio/capture.rs:126-127); a broadcast channel of 256 frames, "5.12 s — sized for the slowest consumer" (src/audio/capture.rs:12); send errors ignored (src/audio/capture.rs:136). Real-time limits are raised for the service and the audio group (deploy/sonicflow.service:21-22; install.sh:243-256).
- Presence: CURRENT (src/audio/capture.rs:10-15, 117-144)
- Outcome: UNVERIFIED
- Outcome evidence: NONE. BUILD-PLAN.md:68 lists "play 10 min without stutter; no Lagged warnings" as a check; commit 9d619ed records for that phase only "audio element readyState 4, playing; clean session stop".
- Why it turned out that way: UNKNOWN. The 50 ms figure is tied by the author to a predecessor defect (DECISIONS.md:99 "D2 (parec 50 ms)").
- Depends on: PipeWire quantum of 2048 frames or less (src/audio/capture.rs:14); `parec` honouring the latency request.
- Open questions: Were underruns, gaps or drop counts ever measured? What does the capture deliver when the source is silent or absent: zero-valued frames or nothing?
- Confidence: MEDIUM. Clear code and STATED rationale; no measurement.

### R6-S05-12  Overrun handling by the broadcast channel's consumers
- Question: S05.4
- What was attempted: a consumer that falls behind receives a lag count. The WebRTC pump advances its RTP timestamp and sequence number by the number of dropped frames, adds to a drop counter and logs sent and dropped totals every 60 s (src/audio/webrtc_audio.rs:224-233, 270-276; rule text at 202-206). The spectrum analyser clears its buffer (src/audio/spectrum.rs:60-63). The capture side takes no action.
- Presence: CURRENT (src/audio/webrtc_audio.rs:224-233; src/audio/spectrum.rs:60-63)
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No log with the health line is stored (`*.log` is ignored, .gitignore:4).
- Why it turned out that way: UNKNOWN
- Depends on: tokio broadcast semantics; channel capacity 256 (src/audio/capture.rs:12).
- Open questions: Did the 60-second health line ever report dropped frames in a real session?
- Confidence: MEDIUM. Clear code; no result evidence.

### R6-S05-13  Capture restart command with no sender; the read loop ends on error
- Question: S05.4
- What was attempted: `PipelineCommand::RestartCapture` restarts the capture process (src/audio/pipeline.rs:99-103) and is annotated "sent by the Bluetooth control plane (Phase 2)" under `#[allow(dead_code)]` (src/audio/pipeline.rs:20-21). Nothing sends it: `grep -rn RestartCapture src` gives only the declaration and the handler. The read task returns on the first read error with a warning (src/audio/capture.rs:138-141), and capture is started once at initialisation (src/audio/pipeline.rs:83-85).
- Presence: DISABLED. The handler exists; no call site sends the command (search above).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: `parec` staying alive for the life of the service; the unit's `Restart=on-failure` covering only the main process (deploy/sonicflow.service:26).
- Open questions: If `parec` exits (for instance when pipewire-pulse restarts), what restores capture short of restarting the service? Was the Phase 2 sender planned and left out?
- Confidence: MEDIUM. INFERRED from clear code and a search.

### R6-S05-14  Advisory about dropouts when the default route is Wi-Fi
- Question: S05.4
- What was attempted: the installer and the doctor script print a notice when the default route interface name starts with `wl` (install.sh:393-396; deploy/doctor.sh:59-63). The stated cause: "2.4 GHz BT/WiFi coexistence causes audio dropouts" (forinstall.md:137; README.md:107-109). The reference box's Ethernet port was "currently unplugged" when the note was written (forinstall.md:136).
- Presence: CURRENT (install.sh:393-396; deploy/doctor.sh:59-63)
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No dropout count or observation is recorded.
- Why it turned out that way: UNKNOWN
- Depends on: the Broadcom Wi-Fi of the reference box (forinstall.md:23, 136); interface naming `wl*`.
- Open questions: Were dropouts observed on the reference box over Wi-Fi, or is the statement general knowledge? Were the live checks run over Wi-Fi?
- Confidence: MEDIUM. STATED cause; no observation.

---

## S06 Remote control and metadata

### R6-S06-01  Transport commands to the source through BlueZ `MediaPlayer1`
- Question: S06.1
- What was attempted: play, pause, next and previous are sent as method calls on the source's `org.bluez.MediaPlayer1` object (src/bluetooth/avrcp.rs:9-22, 148-164). Entry points: `POST /api/avrcp/play|pause|next|previous` (src/web/mod.rs:79-82; src/web/routes.rs:415-433; API.md:55-57), UI buttons (webui/src/components/Transport.tsx:19-43) and the Space key (webui/src/app.tsx:88-98). No shuffle, repeat or seek (DECISIONS.md:9-10; API.md:57). The HTTP call returns ok at once; when no player object exists an `error` event "No media player available" is published; a failed method call is only logged (src/bluetooth/avrcp.rs:149-163). The player is chosen as in R6-S04-09 and its path found as in R6-S06-06.
- Presence: CURRENT (src/bluetooth/avrcp.rs:148-164)
- Outcome: UNVERIFIED
- Outcome evidence: NONE. README.md:35 ("your phone obeys play/pause/next") is a feature description. Commit 0ad9f4e's live check covers metadata only. BUILD-PLAN.md:79 lists "phone control works" with no recorded result.
- Why it turned out that way: UNKNOWN
- Depends on: the source exposing an AVRCP player through BlueZ; BlueZ 5.85 object paths (R6-S06-06).
- Open questions: Was a transport command ever sent to a real phone, and did playback respond? Which sources were tried?
- Confidence: MEDIUM. Clear code; no result evidence.

### R6-S06-02  Transport controls made inert when the source has no `MediaPlayer1` object
- Question: S06.1
- What was attempted: the monitor tracks whether a player object answers and publishes `avrcp_availability_changed` (src/bluetooth/avrcp.rs:166-175, 225-245; src/web/ws.rs:197-200); the UI disables and dims the buttons when it is false (webui/src/components/Transport.tsx:18-22; webui/src/app.tsx:180, 189-190) and ignores the Space key (webui/src/app.tsx:95). Design decision: DECISIONS.md:11-14 (sources such as "Old turntables, cassette decks, and line-in have no player object").
- Presence: CURRENT (citations above)
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Commit b19435c lists "greyed without MediaPlayer1 (D1)" as a feature. BUILD-PLAN.md:79-80 lists "controls grey out for a non-AVRCP source" with no recorded result.
- Why it turned out that way: UNKNOWN
- Depends on: the monitor's poll interval (250 ms with a device connected, 2000 ms idle; src/bluetooth/constants.rs:4-5).
- Open questions: Was a player-less source (a turntable) connected while this was observed?
- Confidence: MEDIUM. Clear code; no result evidence.

### R6-S06-03  Source volume: `bluez5.enable-hw-volume = true` in the WirePlumber fragment
- Question: S06.2
- What was attempted: the fragment sets `bluez5.enable-hw-volume = true` (install.sh:203; forinstall.md:105-106). This is the only Bluetooth volume setting in the repo. The app has no code that reads, sets or mirrors the source's volume: `grep -rniE 'volume|MediaTransport' src/bluetooth` returned nothing, and no route or WebSocket message carries a source volume (src/web/mod.rs:44-108; API.md:16-140). The EQ preamp is a separate gain stage in the filter chain (src/dsp/equalizer.rs:80-84).
- Presence: CURRENT (install.sh:203)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: WirePlumber and the SPA plugin implementing volume for the sink role; the source device's support for AVRCP absolute volume.
- Open questions: Does the phone's volume control change the level reaching `sonicflow-capture` on the reference box, and where is that gain applied? Is the level seen by Cast, AirPlay and browser outputs affected by the phone's volume setting?
- Confidence: LOW. One configuration line; its effect is outside the repo and not recorded.

### R6-S06-04  Device volume for the local output: `pactl set-sink-volume` on the hardware sink
- Question: S06.2
- What was attempted: Host PC Audio volume is applied to the chosen hardware sink with `pactl set-sink-volume <sink> <pct>%` (src/audio/host_out.rs:141-161), default 0.75 (src/state/mod.rs:201), through `POST /api/host/volume` (src/web/routes.rs:563-574; API.md:83) and a slider (webui/src/components/Casting.tsx:261-273). It is not linked in code to the source's volume (R6-S06-03).
- Presence: CURRENT (src/audio/host_out.rs:141-161)
- Outcome: WORKED
- Outcome evidence: STATED. Commit 9d619ed: "Phase 7: Host PC Audio verified — pw-link FL/FR graph links, sink RUNNING, volume 40% via API, deactivate removes all links".
- Why it turned out that way: UNKNOWN
- Depends on: the reference box's Intel HDA sink (forinstall.md:133); pipewire-pulse.
- Open questions: Is the host volume level kept across restarts? `host_volume` is not in the persisted state (src/state/config.rs:151-173).
- Confidence: MEDIUM. STATED evidence; code agrees.

### R6-S06-05  Device volume for Cast, AirPlay and browser outputs
- Question: S06.2
- What was attempted: Chromecast volume through the receiver's `set_volume` (src/audio/chromecast.rs:68-72, 246-250; route at src/web/routes.rs:475-486); AirPlay volume through `pactl set-sink-volume` on the RAOP sink (src/audio/airplay.rs:268-276; route at src/web/routes.rs:516-527); browser volume on the audio element (webui/src/webrtc.ts:207-209). None is linked in code to the source's volume or to another output's.
- Presence: CURRENT (citations above)
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Commit 9d619ed leaves Cast and AirPlay "live sessions" for "a supervised test".
- Why it turned out that way: UNKNOWN
- Depends on: each output's own protocol and device.
- Open questions: Were these volume controls exercised against real devices?
- Confidence: MEDIUM. Clear code; no result evidence.

### R6-S06-06  Track metadata and playback status polled from `MediaPlayer1`, with the player path found through ObjectManager
- Question: S06.3
- What was attempted: an AVRCP monitor task polls every 250 ms when a device is connected and every 2000 ms otherwise (src/bluetooth/constants.rs:4-5; src/bluetooth/avrcp.rs:60-86). It finds the player object by listing BlueZ's managed objects under the device path and taking the first sorted path that implements `org.bluez.MediaPlayer1`, caching it until an error (src/bluetooth/avrcp.rs:116-146, 328-352). It reads `Status` (src/bluetooth/avrcp.rs:172-187; mapping at src/state/mod.rs:67-76), `Track` (Title, Artist, Album, Duration, TrackNumber; src/bluetooth/avrcp.rs:247-267) and `Position` in milliseconds, clamped to the duration (src/bluetooth/avrcp.rs:28-31, 268-272). No title means no track record (src/bluetooth/avrcp.rs:252-263). Stored in app state (src/state/mod.rs:78-89, 146-147); published as `track_changed` on a title or artist change and `playback_status_changed` on a status change (src/bluetooth/avrcp.rs:176-185, 299-306; src/web/ws.rs:193-196); included in the snapshot (src/state/mod.rs:221-222). Shown in the Now Playing card with a read-only progress bar that the client advances locally between events (webui/src/components/NowPlaying.tsx:39-102; webui/src/state.tsx:68-70, 170-183, 317-320). Not written to disk.
- Presence: CURRENT (src/bluetooth/avrcp.rs:1-369)
- Outcome: WORKED
- Outcome evidence: STATED. Commit 0ad9f4e: "Live-verified: track title/artist/album/position from a playing iPhone."
- Why it turned out that way: STATED. The player path differs by BlueZ release and is enumerated, not guessed: "BlueZ ≤5.7x used `dev_XX/player0`, 5.8x uses `dev_XX/avrcp/player0`" (src/bluetooth/avrcp.rs:41-43).
- Depends on: BlueZ 5.85 (forinstall.md:147); an iPhone as the one named source (commit 0ad9f4e); the source providing a title; position being read only together with a track poll.
- Open questions: Was the display in the web UI checked with real metadata, or only the values on the backend? Is the position unit in milliseconds on every source? BUILD-PLAN.md:78 lists validating "the Position ms/µs defect" against a real phone; the commit says "position" was live-verified without stating the unit check.
- Confidence: HIGH. STATED evidence that the code agrees with.

### R6-S06-07  Earlier player lookup at a guessed path `<device>/player0`
- Question: S06.3
- What was attempted: from commit d8b17a3 until commit 0ad9f4e the monitor built the player path by appending `/player0` to the device path (src/bluetooth/avrcp.rs:122-128 at commit 8d2e000). The same proxy served transport commands, so this also bears on S06.1.
- Presence: REMOVED. Replaced by ObjectManager discovery in commit 0ad9f4e (diff of src/bluetooth/avrcp.rs in that commit).
- Outcome: FAILED
- Outcome evidence: STATED. Commit 0ad9f4e: "BlueZ 5.8x moved the player to dev_XX/avrcp/player0 — discover the MediaPlayer1 path via ObjectManager and cache it instead of guessing."
- Why it turned out that way: STATED. The object path changed between BlueZ releases (commit 0ad9f4e; src/bluetooth/avrcp.rs:41-43).
- Depends on: BlueZ 5.85 on the development box (forinstall.md:147).
- Open questions: NONE.
- Confidence: HIGH. STATED in the commit and in the code comment; the diff agrees.

### R6-S06-08  Earlier error counting that treated a missing player as a stale D-Bus connection
- Question: S06.3
- What was attempted: until commit 8d2e000 every failed status read incremented the consecutive-failure counter, including the case of a device with no player object (src/bluetooth/avrcp.rs:177-195 at commit 1e20b41), so the monitor dropped and re-opened its D-Bus connection each time the threshold was reached.
- Presence: REMOVED. Commit 8d2e000 added the exemption for "UnknownObject", "Unknown object" and "UnknownInterface" errors, now at src/bluetooth/avrcp.rs:194-203.
- Outcome: FAILED
- Outcome evidence: STATED. Commit 8d2e000: "A missing MediaPlayer1 (idle phone, turntable) was counted as D-Bus staleness and forced a reconnect every ~1.25s."
- Why it turned out that way: STATED. A missing player is a normal condition for "turntables and idle phones" (src/bluetooth/avrcp.rs:194-196); five failures at the 250 ms interval reach the threshold in about 1.25 s (src/bluetooth/constants.rs:4, 22).
- Depends on: sources with no AVRCP player; the string content of zbus error messages for the current exemption.
- Open questions: Does the string match at src/bluetooth/avrcp.rs:197-199 cover the error text of every BlueZ version in scope?
- Confidence: HIGH. STATED in the commit; the diff and current code agree.

### R6-S06-09  Cover art fetched natively over Bluetooth (AVRCP 1.6 BIP through obexd)
- Question: S06.3
- What was attempted: on a track change the monitor spawns an art resolver (src/bluetooth/avrcp.rs:308-323). Its first step reads the experimental `ObexPort` property of the player, opens an obexd session with target `bip-avrcp`, waits up to 3 s for an `ImgHandle` in the track properties, calls `Image1.GetThumbnail` into a temporary file and waits up to 8 s for the transfer, all under a 10 s limit (src/art.rs:23, 76-85, 113-214). Prerequisites: `Experimental = true` in main.conf (install.sh:173-175), the `bluez-obexd` package (install.sh:97), BlueZ ≥ 5.79 (forinstall.md:146-147). The doctor script warns when either prerequisite is missing (deploy/doctor.sh:28-33). The result is held in memory and served at `/api/artwork` (src/art.rs:50-68; src/web/routes.rs:578-588) and cached on disk under the user's cache directory (src/art.rs:349-400).
- Presence: CURRENT (src/art.rs:113-214)
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Commit 738f12c states a live test of the online lookup only; for the native step it says "gracefully skipped otherwise".
- Why it turned out that way: UNKNOWN
- Depends on: bluetoothd's experimental D-Bus surface; obexd on the session bus of the service user (src/art.rs:124); a source that supports AVRCP 1.6 cover art (src/art.rs:119-120).
- Open questions: Did any source ever deliver a cover image through this path? Was `Experimental = true` active on the reference box (forinstall.md:144 marks it "[PENDING → install.sh]")?
- Confidence: MEDIUM. Clear code; no result evidence.

### R6-S06-10  Cover art looked up online from the track text (iTunes, Deezer, MusicBrainz)
- Question: S06.3
- What was attempted: when the native step yields nothing and `art_online` is true, the resolver queries iTunes Search, then Deezer, then MusicBrainz with the Cover Art Archive, using the `curl` binary, and downloads the first image over 500 bytes (src/art.rs:87-107, 250-337). Results and misses are cached per artist and album (src/art.rs:14-15, 353-400). The config key `art_online` defaults to true (src/state/config.rs:28-31, 42; install.sh:337-340). Track title, artist and album are sent to those services (README.md:270-273). The art reaches clients as a second `track_changed` carrying an `/api/artwork?k=<hash>` URL (src/art.rs:50-68; API.md:88).
- Presence: CURRENT (src/art.rs:87-107, 250-337)
- Outcome: WORKED
- Outcome evidence: STATED. Commit 738f12c: "Live-tested online cascade (iTunes 600px art downloaded; Deezer fielded-query quirk fixed; MB release found)." An ignored live test exists at src/art.rs:465-485; no output of it is stored.
- Why it turned out that way: UNKNOWN. One detail is STATED: Deezer's fielded query "returns nothing through URL encoding", so a plain query is used (src/art.rs:298-299).
- Depends on: outbound internet access from the box; the three third-party services and their response formats; `curl`.
- Open questions: Was the lookup observed with metadata from a real Bluetooth source, or only with the fixed test query at src/art.rs:470?
- Confidence: MEDIUM. STATED evidence alone.

### R6-S06-11  Earlier cover-art attempt: reading a file named by an `Image` track property
- Question: S06.3
- What was attempted: until commit 738f12c the monitor looked for an `Image` entry in the track properties, read that file and served it at `/api/artwork` (src/bluetooth/avrcp.rs:278, 307-319 at commit 3624d19).
- Presence: REMOVED. Deleted in commit 738f12c (diff of src/bluetooth/avrcp.rs in that commit), replaced by R6-S06-09 and R6-S06-10.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. The removed code carried the comment "Best-effort: many BlueZ builds expose nothing here; degrade gracefully." (src/bluetooth/avrcp.rs:307 at commit 3624d19), which states an expectation and no observed result.
- Depends on: a BlueZ build exposing an `Image` track property.
- Open questions: Did this ever return an image on BlueZ 5.85? Commit 3624d19 added a fallback image "whenever the playing source provides no album art", without saying whether the `Image` path had been seen to work.
- Confidence: HIGH for the removal (commit diff); the outcome is not evidenced.

---

## Tally

| Stage | Questions | Answered with findings | Answered NOT FOUND | Finding records | NOT FOUND records |
|---|---|---|---|---|---|
| S01 | 5 | 5 | 0 | 14 | 0 |
| S02 | 3 | 3 | 0 | 10 | 0 |
| S03 | 5 | 5 | 0 | 12 | 0 |
| S04 | 4 | 4 | 0 | 9 | 0 |
| S05 | 4 | 4 | 0 | 14 | 0 |
| S06 | 3 | 3 | 0 | 11 | 0 |
| Total | 24 | 24 | 0 | 70 | 0 |

Questions answered with findings: 24. Questions answered NOT FOUND: 0. Total: 24, equal to the 24 questions in the brief.

Records per question: S01.1 two; S01.2 three; S01.3 three; S01.4 one; S01.5 five; S02.1 three; S02.2 three; S02.3 four; S03.1 three; S03.2 two; S03.3 three; S03.4 one; S03.5 three; S04.1 three; S04.2 two; S04.3 three; S04.4 one; S05.1 five; S05.2 two; S05.3 three; S05.4 four; S06.1 two; S06.2 three; S06.3 six.
