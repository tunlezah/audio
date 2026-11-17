# R6 map — /home/user/SonicFlow

Mapped read-only on 2026-11-17 at commit 3046b9e (branch claude/inspiring-galileo-a9dlia, same commit as main). All paths are relative to /home/user/SonicFlow. 75 tracked files (`git ls-files`); working tree clean (`git status --short` printed nothing). This file is a map: it locates material and makes no outcome judgement.

Rule 10 note: two documents in the repo carry text addressed to an AI agent. sonicflow-design-spec.md:4 ("Hand this document to an AI agent or developer to reproduce the SonicFlow interface exactly") and SOUNDSYNC-SPEC.md:46-48 ("a competent Rust + TypeScript engineer (or an AI agent) with **no access to the original repository** can rebuild a functionally equivalent system"). Recorded as material; not acted on.

Rule 11 note: Bluetooth-address-shaped strings appear as test fixtures at src/bluetooth/manager.rs:875-877, src/bluetooth/manager.rs:895-896, src/bluetooth/constants.rs:66-93 and src/audio/airplay.rs:473-492 (kind: device addresses / RAOP service-name prefixes used as unit-test input). Shorter placeholder-style address strings also appear in tests and comments at src/state/mod.rs:137, src/web/ws.rs:306 and src/audio/host_out.rs:255. Values not copied here. A search for password, secret, token, api key, link key and private-key headers across tracked text files (excluding lock files and images) returned no credential values.

## 1. Layout

Top-level directories (tracked):

| Directory | Contents | Tracked files |
|---|---|---|
| `.githooks/` | One pre-commit hook script: version sync, cargo fmt, clippy, test (.githooks/pre-commit:1-23) | 1 (no extension, bash) |
| `.github/` | One GitHub Actions workflow, `workflows/build.yml`: build, test, package, release on tag (.github/workflows/build.yml:1-95) | 1 yml |
| `deploy/` | systemd unit template and two shell helpers: `sonicflow.service`, `sweep-modules.sh`, `doctor.sh` | 1 service, 2 sh |
| `docs/` | One image, `screenshot.png`, referenced by README.md:13 | 1 png |
| `src/` | Rust backend, single binary crate. Subdirectories: `audio/` (12 files), `bluetooth/` (6), `dsp/` (3), `state/` (2), `web/` (3); plus `main.rs` and `art.rs` at src root | 28 rs |
| `webui/` | Preact + TypeScript single-page web UI built with Vite: `index.html`, `package.json`, `package-lock.json`, `tsconfig.json`, `vite.config.ts`, `public/` (4 png), `src/` (10 tsx, 4 ts, 1 css) | 10 tsx, 5 ts, 4 png, 3 json, 1 html, 1 css |

Top-level files (tracked): `.gitignore`, `API.md`, `BUILD-PLAN.md`, `CHECKLIST.md`, `Cargo.lock`, `Cargo.toml`, `DECISIONS.md`, `LICENSE`, `NoAlbumArt.png`, `NothingPlaying.png`, `README.md`, `SOUNDSYNC-SPEC.md`, `forinstall.md`, `install.sh`, `sonicflow-design-spec.md`, `sonicflow.png`, `uninstall.sh`.

Not present in the working tree: `target/`, `webui/dist/`, `webui/node_modules/` (listed in .gitignore:1-3; `ls` reports each as absent). `*.log` is ignored at .gitignore:4.

File counts by type, whole repo (75 tracked):

| Type | Count |
|---|---|
| .rs | 28 |
| .tsx | 10 |
| .md | 8 |
| .png | 8 |
| .ts | 5 |
| .sh | 4 |
| .json | 3 |
| .yml | 1 |
| .toml | 1 |
| .lock | 1 |
| .service | 1 |
| .html | 1 |
| .css | 1 |
| .gitignore | 1 |
| LICENSE (no extension) | 1 |
| pre-commit (no extension, bash) | 1 |

Largest source files by line count (`wc -l`): webui/src/style.css 1056; src/bluetooth/manager.rs 903; webui/src/components/Settings.tsx 704; src/web/routes.rs 596; src/audio/airplay.rs 515; src/art.rs 486; src/state/mod.rs 479; src/audio/chromecast.rs 476; install.sh 409; src/audio/pipeline.rs 378; src/bluetooth/avrcp.rs 369. Largest document: SOUNDSYNC-SPEC.md 3312 lines.

## 2. Entry points

Executables and main functions

- Rust binary `sonicflow`: declared at Cargo.toml:8-10 (`[[bin]] name = "sonicflow" path = "src/main.rs"`). The only `fn main` in the repo is src/main.rs:25 under `#[tokio::main]` at src/main.rs:24 (search: `grep -rn 'fn main' src webui/src`).
- Web UI entry: webui/index.html:12 loads `/src/main.tsx`; webui/src/main.tsx:26 renders `<App />`; `App` is defined at webui/src/app.tsx:49. The built UI is served by the Rust binary from the directory named by `SONICFLOW_WEBUI`, default `webui/dist` (src/main.rs:126; src/web/mod.rs:41-42, 109).

Service definitions

- deploy/sonicflow.service:1-30. A system unit template with `__USER__` and `__UID__` placeholders (deploy/sonicflow.service:12-13, 16-18). `ExecStartPre=-/opt/sonicflow/sweep-modules.sh` (line 24), `ExecStart=/opt/sonicflow/sonicflow` (line 25), `Restart=on-failure` (line 26), `WantedBy=multi-user.target` (line 30), `Requires=bluetooth.service` (line 8).
- A transient user unit named `sonicflow-dev` is referenced as something to stop at install.sh:349, uninstall.sh:83 and forinstall.md:43-44. No definition of it exists in the repo (search: `grep -rn 'sonicflow-dev'`, `grep -rn 'systemd-run'`).

Scripts (all files with a shebang; search: `grep -rIl '^#!'`)

- install.sh:1 — installer, 12 steps (install.sh:78-400). Run as root (install.sh:37). Flags `--user`, `--no-build`, `--port` (install.sh:43-50).
- uninstall.sh:1 — uninstaller. Flags `--purge`, `--disable-linger`, `--user`, `--list-packages` (uninstall.sh:38-57).
- deploy/doctor.sh:1 — diagnostics script that prints ok/FAIL/warn lines (deploy/doctor.sh:10-67).
- deploy/sweep-modules.sh:1 — unloads `module-null-sink` / `module-loopback` modules whose arguments mention "sonicflow" (deploy/sweep-modules.sh:5-14).
- .githooks/pre-commit:1 — version sync, `cargo fmt --check`, `cargo clippy`, `cargo test` (.githooks/pre-commit:7-23). Enabled by `git config core.hooksPath .githooks` per README.md:212-213; `.git/config` in this checkout has no `core.hooksPath` entry.
- npm scripts: `dev` = `vite`, `build` = `tsc --noEmit && vite build`, `preview` = `vite preview` (webui/package.json:6-10).
- CI: .github/workflows/build.yml, triggers at lines 3-8 (push to `main`, tags `v*`, pull_request, workflow_dispatch).

What starts what

1. install.sh:289 runs `cargo build --release`; install.sh:292 runs `npm ci`/`npm install` and `npm run build` in `webui/`.
2. install.sh:302-306 copies the binary, `sweep-modules.sh`, `doctor.sh` and `webui/dist` into `/opt/sonicflow`.
3. install.sh:353-356 renders deploy/sonicflow.service into `/etc/systemd/system/sonicflow.service` and enables it; install.sh:362 runs `systemctl restart sonicflow`; install.sh:364 polls `http://127.0.0.1:$PORT/api/status`.
4. systemd runs deploy/sonicflow.service:24 (sweep) then deploy/sonicflow.service:25 (binary).
5. The binary's `main` (src/main.rs:25) spawns these tokio tasks: audio pipeline (src/main.rs:74), spectrum analyzer (src/main.rs:76), Bluetooth manager (src/main.rs:79), AVRCP monitor (src/main.rs:80), Chromecast manager (src/main.rs:84), AirPlay manager (src/main.rs:85), host output manager (src/main.rs:86), WebRTC manager (src/main.rs:87-89), last-output tracker (src/main.rs:105), startup-output activation (src/main.rs:107-112), HTTP server (src/main.rs:128-132). `LineInManager::initialize` is awaited inline at src/main.rs:101-102.
6. The binary starts child processes by name (search: `grep -rn 'Command::new(' src`):
   - `pactl`: src/audio/pipeline.rs:142, 200, 223, 244, 257, 289; src/audio/capture.rs:176, 196; src/audio/cast_stream.rs:130; src/audio/host_out.rs:87, 146, 167, 204; src/audio/line_in.rs:45, 78, 94, 103, 112; src/audio/airplay.rs:140, 181, 271, 375, 388; src/bluetooth/manager.rs:828.
   - `parec`: src/audio/capture.rs:72; src/audio/cast_stream.rs:53.
   - `pw-cat`: src/audio/capture.rs:86. `pw-loopback`: src/audio/pipeline.rs:173. `pw-cli`: src/audio/pipeline.rs:229; src/audio/capture.rs:188. `pw-dump`: src/audio/capture.rs:209. `wpctl`: src/audio/pipeline.rs:299, 301.
   - `pw-link`: src/audio/host_out.rs:102, 131, 223; src/audio/airplay.rs:209, 258, 422, 433; src/bluetooth/manager.rs:837.
   - `pipewire-filter-chain` or `pipewire -c`: src/audio/filter_chain.rs:50-58.
   - `ffmpeg`: src/audio/cast_stream.rs:72.
   - `btmgmt`: src/bluetooth/manager.rs:223. `hcitool`: src/bluetooth/manager.rs:580.
   - `avahi-browse`: src/audio/chromecast.rs:310; src/audio/airplay.rs:306.
   - `curl`: src/art.rs:263. `hostname`: src/audio/cast_stream.rs:151. `systemctl`: src/audio/airplay.rs:37. `find`: src/audio/airplay.rs:289. `which`: src/audio/filter_chain.rs:100.
7. One OS thread named `cast-session` is spawned per Chromecast session at src/audio/chromecast.rs:148-162.
8. The browser opens a WebSocket to `/ws/status` at webui/src/state.tsx:280; the server handler is src/web/ws.rs:13-15, routed at src/web/mod.rs:108.

## 3. Run path

Chain from process start to audio leaving an output. Hops 1-12 are common; four output branches follow.

Common chain

1. deploy/sonicflow.service:24 — `ExecStartPre` runs the module sweep (deploy/sweep-modules.sh:5-14).
2. deploy/sonicflow.service:25 — `ExecStart=/opt/sonicflow/sonicflow`, with session environment from deploy/sonicflow.service:16-20.
3. src/main.rs:24-31 — `main` starts; logging initialised.
4. src/main.rs:44 — `Config::load()` → src/state/config.rs:82-102 (layer paths at src/state/config.rs:104-111). src/main.rs:52 — `AppStateHandle::new` → src/state/mod.rs:396-402.
5. src/main.rs:64-73 — `AudioPipeline::new`, then `pipeline.initialize(...)` → src/audio/pipeline.rs:53-86.
6. src/audio/pipeline.rs:54 → src/audio/pipeline.rs:213-241 — readiness probe (`pactl info` or `pw-cli info 0`, 30 s deadline).
7. src/audio/pipeline.rs:62 → src/audio/pipeline.rs:132-194 — null sink `sonicflow-capture` created or adopted (`pactl load-module module-null-sink` at src/audio/pipeline.rs:142-150; `pw-loopback` path at src/audio/pipeline.rs:172-189). Sink name constant at src/audio/pipeline.rs:9.
8. src/audio/pipeline.rs:66-81 — when EQ is enabled, `FilterChainManager::apply_eq` → src/audio/filter_chain.rs:35-70: config text from src/dsp/equalizer.rs:78-153 written to `$XDG_RUNTIME_DIR/sonicflow/filter-chain.conf` (src/audio/filter_chain.rs:17-23, 41-44), then a child process is spawned (src/audio/filter_chain.rs:50-58). The config declares sink node `effect_input.sonicflow-eq` (src/dsp/equalizer.rs:127-133) and playback node `effect_output.sonicflow-eq` with `node.target = "sonicflow-capture"` (src/dsp/equalizer.rs:134-140).
9. src/audio/pipeline.rs:71, 76, 80 → src/audio/pipeline.rs:288-314 — default sink set to the EQ sink or the null sink (`pactl set-default-sink`, `wpctl` path at 299-311).
10. src/main.rs:79 → src/bluetooth/manager.rs:67 `run` → src/bluetooth/manager.rs:125-213 `initialize`: pairing agent registered (src/bluetooth/manager.rs:131-141 → src/bluetooth/agent.rs:111-126), adapter chosen (src/bluetooth/manager.rs:144-159), powered (182), alias set (185), discoverable timeout 0 and discoverable (191-196), pairable (197).
11. A2DP sink endpoint and transport: not handled by this repo's code. src/bluetooth/mod.rs:4-5: "this crate does control-plane work ONLY. It MUST NOT register `org.bluez.MediaEndpoint1` objects — WirePlumber owns codec negotiation and transport acquisition." The WirePlumber fragment that sets `bluez5.roles = [ a2dp_sink a2dp_source ]` is written by install.sh:185-212 (roles at install.sh:200, codecs at install.sh:201).
12. GAP — the hop from the Bluetooth node (`bluez_input.*`) into `effect_input.sonicflow-eq` / `sonicflow-capture`. No code in the repo creates this link. What the repo does contain: the default-sink setting at hop 9; a repeat of it when a stream start is detected (src/bluetooth/manager.rs:763-771); the README diagram label "(autoconnect to default sink)" (README.md:55); a doctor check on the default sink (deploy/doctor.sh:43-47). Searched: `grep -rnE 'autoconnect|node\.target|target\.object|a2dp-source-role|bluez_input'` over .rs, .sh, .md and .service files; every `Command::new("pw-link")` call site (the only one touching `bluez_input` is the read-only listing `pw-link -o` at src/bluetooth/manager.rs:837); install.sh:187-206 (fragment content has no routing rule).

After hop 12 the audio is at the monitor of `sonicflow-capture`. Stream start/stop detection, which runs alongside, is at src/bluetooth/manager.rs:615-726 (`poll`, 500 ms interval from src/bluetooth/constants.rs:3) and src/bluetooth/manager.rs:729-817.

Branch A — Host PC Audio (local sound device)

- A1. src/main.rs:86 — `HostOutputManager::run` → src/audio/host_out.rs:34-57.
- A2. Activation arrives either from startup logic (src/main.rs:192-199, after the 3 s sleep at src/main.rs:175) or from `POST /api/host/activate` (src/web/mod.rs:100 → src/web/routes.rs:547-556).
- A3. src/audio/host_out.rs:68-127 `activate`: sink chosen (73-80), `pactl suspend-sink <sink> 0` (87-90), monitor ports of `sonicflow-capture` and playback ports of the hardware sink listed (92-93 → 222-236), `pw-link <monitor port> <playback port>` (101-114).
- A4. Audio leaves through the hardware sink named in `HostSinkInfo` (src/audio/host_out.rs:166-193). Volume: `pactl set-sink-volume` at src/audio/host_out.rs:141-161.

Branch B — browser via WebRTC ("Local Player")

- B1. src/audio/pipeline.rs:83 → src/audio/capture.rs:61-148 `start`: source resolved (src/audio/capture.rs:49-59), `parec --raw --format=float32 --channels=2 --rate=48000 --device=<name>.monitor --latency-msec=50` spawned (src/audio/capture.rs:71-84; `pw-cat` path at 85-104).
- B2. src/audio/capture.rs:122-144 — read loop: `read_exact` of 7680-byte frames (constant at src/audio/capture.rs:11), converted to `Vec<f32>` and sent on a tokio broadcast channel (src/audio/capture.rs:136; capacity 256 at src/audio/capture.rs:12).
- B3. src/main.rs:65 `audio_sender` → src/main.rs:87 `WebRtcManager::new(state, audio_sender)` → src/audio/webrtc_audio.rs:51-72; `run` at src/audio/webrtc_audio.rs:74-114.
- B4. Browser click → webui/src/webrtc.ts:65-90 `start` → `negotiate` (webui/src/webrtc.ts:102-159) sends `webrtc_offer` over the WebSocket (webui/src/webrtc.ts:154).
- B5. src/web/ws.rs:98-116 — `webrtc_offer` forwarded as `WebRtcCommand::Offer` → src/audio/webrtc_audio.rs:124-199 `handle_offer`: peer connection (128-140), Opus track 48000 Hz 2 ch (142-154), answer (176-183), pump started (185).
- B6. src/audio/webrtc_audio.rs:207-280 `spawn_audio_pump`: broadcast receive (224), Opus encode on a blocking thread (236-250 → src/audio/opus_encoder.rs:24-27), RTP packet payload type 111 (252-262), `track.write_rtp` (263).
- B7. webui/src/webrtc.ts:122-129 — `ontrack` attaches the stream to an `<audio>` element and calls play. Audio leaves through the browser.

Branch C — HTTP stream (browser fallback and Chromecast)

- C1. Route `GET /api/stream/audio.aac` and `/api/stream/audio.mp3` at src/web/mod.rs:105-106 → src/web/routes.rs:590-596 → src/audio/cast_stream.rs:21-39.
- C2. src/audio/cast_stream.rs:49-98 `spawn_stream_pipeline`, one per HTTP client: capture device resolved (50 → 129-146), `parec --format=s16le --rate=48000 --channels=2 --latency-msec=50` (53-64), stdout fd handed to `ffmpeg` stdin (66-70, 91), ffmpeg arguments (72-89) with codec arguments `aac 256k adts` (line 22) or `libmp3lame 192k mp3` (line 32).
- C3. src/audio/cast_stream.rs:100-127 — ffmpeg stdout streamed as the HTTP response body.
- C4. Browser fallback client: webui/src/webrtc.ts:191-205 sets the audio element `src` to `/api/stream/audio.aac`, then `.mp3` on error.
- C5. Chromecast client: src/main.rs:84 → src/audio/chromecast.rs:58-91 `run`; `POST /api/cast/connect` (src/web/mod.rs:86 → src/web/routes.rs:452-463) → src/audio/chromecast.rs:125-174 `connect`: stream URL `http://<ip>:<port>/api/stream/audio.aac` built at src/audio/chromecast.rs:139-140 (IP from src/audio/cast_stream.rs:150-169); session thread src/audio/chromecast.rs:195-293: connect (209-216), launch Default Media Receiver (217-223), `media.load` with content type `audio/aac`, stream type Live (227-239). The Cast device then pulls the URL (C1-C3). Audio leaves through the Cast device.

Branch D — AirPlay (RAOP)

- D1. src/main.rs:85 → src/audio/airplay.rs:35-57 `new`, src/audio/airplay.rs:59-87 `run`.
- D2. `POST /api/airplay/connect` (src/web/mod.rs:92 → src/web/routes.rs:500-509) → src/audio/airplay.rs:129-251 `connect`: `pactl load-module module-raop-discover` (140-150), RAOP sink located (153-177 → 405-417), `pactl suspend-sink <sink> 0` (181-184), monitor and playback ports listed (198-199 → 421-446), `pw-link` (207-221).
- D3. Audio leaves through the PipeWire RAOP sink. The RAOP sink itself is created by PipeWire's module, outside this repo (src/audio/airplay.rs:10-14).

Side paths on the same capture point

- Spectrum: src/main.rs:66, 76 → src/audio/spectrum.rs:40-70 → `SystemEvent::SpectrumData` → WebSocket (src/web/ws.rs:192). Not an audio output.
- Line-in input: src/main.rs:101-102 → src/audio/line_in.rs:24-32; `POST /api/line-in/activate` (src/web/mod.rs:73 → src/web/routes.rs:399-404) → `pactl load-module module-loopback source=<alsa_input.*> sink=sonicflow-capture` (src/audio/line_in.rs:45-57).

## 4. Documentation

| Path | Lines | One-line summary |
|---|---|---|
| README.md | 285 | Product description, signal-flow diagram (53-71), hardware (100-109), install/uninstall (111-154), UI tour (156-188), API pointer (190-201), development (203-234), troubleshooting (236-254), security/threat model (256-277), provenance (279-285). |
| API.md | 149 | REST and WebSocket contract, titled "SonicFlow API contract (v0.1.0 — FROZEN)" (API.md:1); REST tables (16-93), WebSocket server→client (101-140) and client→server (142-149). |
| BUILD-PLAN.md | 100 | Staged build plan, Phase 0 to Phase 11, each with a "Check:" line (BUILD-PLAN.md:9-100). |
| CHECKLIST.md | 37 | Per-change checklist: version sync in four files, and judgement checks for installer, uninstaller, doctor, unit, forinstall.md, API.md, README, workflow, release (CHECKLIST.md:7-37). |
| DECISIONS.md | 105 | Design decisions D1-D9 dated 2026-08-29, reconciling the two specs and naming the target machine (DECISIONS.md:3-6). |
| forinstall.md | 168 | Log of system-level changes on the development machine, with [DONE]/[PENDING] markers: provenance audit (18-52), pre-flight (54-65), packages (67-78), system configuration (80-117), service (119-128), hardware notes (130-138), dated change log (140-168). |
| sonicflow-design-spec.md | 539 | Visual design and UI technical specification: tokens, 10 palettes, layout, components, accessibility, acceptance checklist (headings at lines 10-533). |
| SOUNDSYNC-SPEC.md | 3312 | Engineering specification of a predecessor system named SoundSync, version 2.9.11, source given as a different repository (SOUNDSYNC-SPEC.md:1-8). Includes "22. Issues, defects and design debt" (2638-2971), "23. Diagnostics and runbook" (2972-3031), "24. Verification and acceptance" (3032-3106), "25. Recreation plan" (3107-3206), appendices (3207-3312). Describes the predecessor, not this repo's code. |
| LICENSE | 21 | MIT licence text. |

Changelog: no CHANGELOG file is tracked (search: `git ls-files | grep -iE 'changelog|issue|todo|notes'` returned nothing). Version history is carried in commit subjects (for example 3046b9e, 738f12c, 3624d19) and the dated log in forinstall.md:140-168.

Issue list: no standalone issue or known-issues file for this repo. Nearest material: README.md:236-254 (troubleshooting), README.md:42-49 ("What it is not"), README.md:256-277 (security), and SOUNDSYNC-SPEC.md:2638-2971 (predecessor's defect register, referenced in this repo by IDs such as D2, D3, D7, D11, D13, D18, D20, D21 at DECISIONS.md:99-100).

Author markers: a search for TODO, FIXME, HACK, XXX and WORKAROUND across tracked text files (excluding lock files and images) returned no matches.

In-code documentation of note: module header src/bluetooth/mod.rs:1-8; src/art.rs:1-15; src/audio/cast_stream.rs:12-19; src/audio/webrtc_audio.rs:202-206; src/audio/capture.rs:10-15; webui/src/webrtc.ts:1-9; unit header deploy/sonicflow.service:1-3; installer header install.sh:1-17; uninstaller header uninstall.sh:1-21.

## 5. Dependencies

Manifests and install scripts

| Path | Kind |
|---|---|
| Cargo.toml | Rust crate manifest, package version 0.2.1 (Cargo.toml:3), edition 2021 (Cargo.toml:4) |
| Cargo.lock | Rust lock file, 4182 lines |
| webui/package.json | npm manifest, version 0.2.1 (webui/package.json:4) |
| webui/package-lock.json | npm lock file, lockfileVersion 3 (webui/package-lock.json:4) |
| install.sh | apt package list (install.sh:96-104), rustup install (install.sh:123-128), node check (install.sh:120-122) |
| uninstall.sh | prints the same apt package list (uninstall.sh:44-48) |
| .github/workflows/build.yml | CI apt list (build.yml:19-23), Rust stable toolchain (25-27), Node 22 (31-35) |
| forinstall.md | package audit and notes (forinstall.md:27-35, 67-78) |

No `rust-toolchain` file, `.nvmrc`, `.node-version` or `.tool-versions` is present (checked with `ls`).

Bluetooth dependencies

- Rust crates: `bluer = { version = "0.17", features = ["full"] }` (Cargo.toml:14), locked 0.17.4 (Cargo.lock:441-442); `zbus = { version = "4", features = ["tokio"] }` (Cargo.toml:15), locked 4.4.0 (Cargo.lock:3985-3986); `zvariant = "4"` (Cargo.toml:16), locked 4.2.0 (Cargo.lock:4148-4149). Transitive: `dbus` 0.9.12 (Cargo.lock:748-749), `libdbus-sys` 0.2.7 (Cargo.lock:1547-1548).
- apt packages, no versions pinned: `bluetooth bluez bluez-tools bluez-obexd` (install.sh:97), `libspa-0.2-bluetooth` (install.sh:99), `rfkill` (install.sh:101), `libdbus-1-dev` (install.sh:103).
- Version statements in prose: BlueZ 5.85 on the development machine and BlueZ >= 5.79 for the cover-art properties (forinstall.md:146-147); "BlueZ ≥ 5.66" for main.conf Class (DECISIONS.md:70-71; src/bluetooth/constants.rs:16).
- External tools called at runtime: `btmgmt` (src/bluetooth/manager.rs:223), `hcitool` (src/bluetooth/manager.rs:580), `bluetoothctl` (install.sh:233, 388; deploy/doctor.sh:20-26).
- Firmware file checked, not installed: `rtl_bt/rtl8761bu_fw.bin` (install.sh:130-132).

Audio dependencies

- Rust crates: `opus = "0.3"` (Cargo.toml:21), locked 0.3.1 (Cargo.lock:1831-1832), with `audiopus_sys` 0.2.2 (Cargo.lock:261-262); `rustfft = "6.2"` (Cargo.toml:28), locked 6.4.1 (Cargo.lock:2330-2331); `webrtc = "0.12"` and `rtp = "0.12"` (Cargo.toml:31-32), locked 0.12.0 each (Cargo.lock:3430-3431, 2289-2290); `rust_cast = "0.21"` (Cargo.toml:24), locked 0.21.0 (Cargo.lock:2304-2305); `mdns-sd = "0.11"` (Cargo.toml:25), locked 0.11.5 (Cargo.lock:1629-1630); `rustls = { version = "0.23", features = ["ring"] }` (Cargo.toml:34), locked 0.23.43 (Cargo.lock:2379-2380).
- The `pipewire` crate is not linked: Cargo.toml:19-20 ("the `pipewire` crate is deliberately NOT linked. All PipeWire interaction is via subprocess").
- apt packages, no versions pinned: `pipewire pipewire-pulse pipewire-audio pipewire-alsa wireplumber` (install.sh:98), `pulseaudio-utils` (install.sh:99), `avahi-daemon avahi-utils ffmpeg` (install.sh:100), `alsa-utils` (install.sh:101), `libopus-dev libclang-dev pkg-config build-essential` (install.sh:103).
- Version gates and statements: WirePlumber version check accepting `0.5*|0.6*|1.*` (install.sh:86-91); PipeWire 1.6.2 and WirePlumber 0.5.13 on the development machine (forinstall.md:61-62); "PipeWire ≥ 1.x, WirePlumber ≥ 0.5" (README.md:104).
- Packages logged as installed during development and later dropped from the installer: `libpipewire-0.3-dev`, `libspa-0.2-dev`, `libmp3lame-dev` (forinstall.md:33-35).

Other dependencies named in the manifests

- Web/server crates: `axum` 0.7 with `ws`, `macros` (Cargo.toml:37; locked 0.7.9, Cargo.lock:301-302); `tokio` 1 full (Cargo.toml:38; locked 1.53.1, Cargo.lock:2965-2966); `tower-http` 0.6 (Cargo.toml:39; locked 0.6.11, Cargo.lock:3128-3129); `serde`, `serde_json`, `toml` (Cargo.toml:40-42); `tracing`, `tracing-subscriber`, `dirs`, `chrono`, `uuid`, `futures`, `libc`, `bytes` (Cargo.toml:33, 45-51); dev-dependency `tokio-test = "0.4"` (Cargo.toml:54).
- npm: `preact ^10.25.0` (locked 10.29.8, webui/package-lock.json:1825-1826), `@fontsource-variable/inter ^5.1.0` (locked 5.3.0, package-lock.json:777-778), `@preact/preset-vite ^2.9.0` (locked 2.10.6, package-lock.json:853-854), `typescript ~5.6.0` (locked 5.6.3, package-lock.json:1956-1957), `vite ^6.0.0` (locked 6.4.3, package-lock.json:2001-2002); ranges at webui/package.json:11-19.
- Toolchains: Node 22.x (install.sh:121; build.yml:33), Rust stable via rustup (install.sh:125; build.yml:25).
- Other tools: `git curl unzip` (install.sh:102); `curl` is also a runtime dependency for album-art lookup (src/art.rs:263; forinstall.md:151-152).

## 6. Stage pointers

S01 Platform and stack
- README.md:73-77, 100-109; DECISIONS.md:3-6; forinstall.md:18-65, 130-138
- Cargo.toml:1-54; Cargo.lock; webui/package.json:1-20; webui/package-lock.json
- install.sh:1-3, 78-132; .github/workflows/build.yml:14-35
- SOUNDSYNC-SPEC.md:215-312 (predecessor's prerequisites section)

S02 Adapter and controller
- src/bluetooth/manager.rs:67-87 (init retry loop), 125-213 (`initialize`), 218-246 (Class of Device via `btmgmt`), 527-542 (`select_adapter`)
- src/bluetooth/constants.rs:15-22; src/state/config.rs:22-24, 95-97, 155-156; src/state/mod.rs:14-20
- src/web/routes.rs:169-188; src/web/mod.rs:62-63
- install.sh:130-132, 151-181; deploy/doctor.sh:25-26
- src/bluetooth/avrcp.rs:211-220 (D-Bus reconnect threshold)
- DECISIONS.md:65-73; forinstall.md:64-65, 77-78, 131-132; README.md:104-109, 276-277

S03 Discoverability, pairing and trust
- src/bluetooth/manager.rs:181-199 (alias, discoverable, pairable), 260-284 (commands incl. `SetAutoPair`), 286-408 (discovery), 436-469 (`connect_device`: trust, pair, connect), 471-505 (disconnect, remove), 507-525 (rename)
- src/bluetooth/agent.rs:1-126 (agent, capability, UUID allowlist)
- src/bluetooth/device.rs:8-58 (states), 78-183 (device record, classification)
- src/bluetooth/constants.rs:12-21; src/state/config.rs:25-27, 37-43
- install.sh:151-181 (main.conf Class, Name, DiscoverableTimeout, Experimental, AutoEnable); install.sh:336 (`auto_pair = true` in the config template)
- src/web/routes.rs:37-78, 82-167; API.md:25-36
- README.md:138-140, 265-267; DECISIONS.md:28-35, 87-88
- webui/src/components/Settings.tsx

S04 Connection and A2DP profile
- src/bluetooth/mod.rs:1-8 (endpoint ownership statement)
- install.sh:183-241 (WirePlumber fragment, MDWE override, sink UUID check); install.sh:384-392 (acceptance gate)
- forinstall.md:89-108, 160-168; README.md:79-83, 243-248
- deploy/doctor.sh:18-24, 35-39
- src/bluetooth/manager.rs:615-726 (`poll`: connected/paired/removed), 729-817 (audio-active start/stop, `active_device`)
- src/bluetooth/device.rs:8-58, 69-75 (codec enum); src/web/ws.rs:174-179 (codec field on stream start)
- src/bluetooth/avrcp.rs:102-114 (player selection across devices)
- install.sh:176 (`AutoEnable`); install.sh:135-141 (linger)

S05 Audio capture
- src/audio/capture.rs:10-15 (format, frame, buffer constants), 49-59 (`resolve_source`), 61-148 (`start`), 175-218 (source lookup)
- src/audio/pipeline.rs:9-11, 53-86, 132-194, 213-241, 288-314
- src/audio/cast_stream.rs:12-19, 49-98, 129-146 (separate per-client capture)
- src/bluetooth/manager.rs:728-866 (stream start/stop detection and node-name parsing)
- src/dsp/equalizer.rs:122-140 (filter-chain capture/playback nodes)
- src/audio/webrtc_audio.rs:224-233 (lag handling); src/audio/spectrum.rs:60-63
- README.md:53-71, 89-91; deploy/doctor.sh:41-50

S06 Remote control and metadata
- src/bluetooth/avrcp.rs:1-369 (MediaPlayer1 proxy, polling, track, position)
- src/web/routes.rs:413-433; src/web/mod.rs:79-82; API.md:55-58
- src/art.rs:1-486 (cover art: BIP/OBEX and online lookup); src/web/routes.rs:578-588
- src/state/mod.rs:57-89 (playback status, track info)
- Volume: install.sh:203 (`bluez5.enable-hw-volume = true`); src/audio/host_out.rs:141-161; src/audio/airplay.rs:268-276; src/audio/chromecast.rs:68-72, 246-250; webui/src/webrtc.ts:207-209
- webui/src/components/Transport.tsx; webui/src/components/NowPlaying.tsx
- DECISIONS.md:8-15; README.md:92-93, 163-172

S07 Processing
- src/dsp/equalizer.rs:1-153 (band table, filter-chain config generator); src/dsp/presets.rs:11-177
- src/audio/filter_chain.rs:1-106; src/audio/pipeline.rs:110-129
- src/audio/opus_encoder.rs:1-28; src/audio/cast_stream.rs:21-39, 72-95 (ffmpeg AAC/MP3)
- src/audio/spectrum.rs:7-14, 40-107
- Latency and clocking: src/audio/capture.rs:13-15, 126-127; src/audio/cast_stream.rs:12-19; src/audio/webrtc_audio.rs:202-206, 227-232, 270-276; src/audio/line_in.rs:51; README.md:46-47, 73-76; DECISIONS.md:79; API.md:91-92
- DECISIONS.md:37-51; BUILD-PLAN.md:36-42

S08 Output
- src/main.rs:82-112 (output managers), 143-221 (last-output tracking, startup activation, cast rejoin)
- Host: src/audio/host_out.rs:1-257. Chromecast: src/audio/chromecast.rs:1-441 and src/audio/cast_stream.rs:1-169. AirPlay: src/audio/airplay.rs:1-464. Browser: src/audio/webrtc_audio.rs:1-290 and webui/src/webrtc.ts:1-239
- Selection and settings: src/web/routes.rs:37-78, 435-574; src/state/config.rs:161-172
- Unavailable/changed outputs: src/audio/chromecast.rs:75-87, 242-292; src/audio/airplay.rs:161-177, 223-241; src/audio/host_out.rs:40-48, 82-100; webui/src/webrtc.ts:79-89, 130-138, 196-202
- README.md:26-31, 173-177; DECISIONS.md:17-26, 75-80; API.md:59-93
- webui/src/components/Casting.tsx

S09 User interface and control surfaces
- src/web/mod.rs:40-113 (route table); src/web/routes.rs:1-596; src/web/ws.rs:1-271; API.md:1-149
- webui/src/app.tsx; webui/src/state.tsx; webui/src/api.ts; webui/src/components/*.tsx; webui/src/palettes.ts; webui/src/style.css
- Config files: src/state/config.rs:5-45, 104-111; install.sh:330-346
- Command line: install.sh:43-50; uninstall.sh:38-57; deploy/doctor.sh
- First-time use: README.md:111-140; install.sh:402-405
- Errors and state changes: src/state/mod.rs:284-387 (event enum); src/web/ws.rs:143-271; webui/src/components/Toasts.tsx; webui/src/state.tsx:270-272
- sonicflow-design-spec.md:329-453; DECISIONS.md:28-35

S10 Lifecycle and operations
- install.sh:1-409; uninstall.sh:1-186; forinstall.md:1-168
- deploy/sonicflow.service:1-30; deploy/sweep-modules.sh:1-15; deploy/doctor.sh:1-67
- Recovery in code: src/bluetooth/manager.rs:70-86; src/audio/pipeline.rs:51-86, 131-170; src/audio/line_in.rs:92-108; src/audio/chromecast.rs:145-162
- Privileges: install.sh:37, 56-64, 243-272; deploy/sonicflow.service:12-14, 21-22
- Configuration: src/state/config.rs:1-239; src/dsp/presets.rs:91-177; install.sh:311-346
- Logging: src/main.rs:26-31; deploy/sonicflow.service:20; src/audio/capture.rs:106-115; install.sh:368
- Packaging/update: .github/workflows/build.yml:56-95; install.sh:69-76, 398; CHECKLIST.md:7-37; .githooks/pre-commit:7-14

S11 Testing and measured behaviour
- Unit-test modules (`#[cfg(test)]`): src/art.rs:419; src/audio/airplay.rs:466; src/audio/chromecast.rs:443; src/audio/host_out.rs:243; src/audio/line_in.rs:147; src/audio/opus_encoder.rs:30; src/audio/pipeline.rs:333; src/audio/spectrum.rs:109; src/bluetooth/constants.rs:60; src/bluetooth/device.rs:185; src/bluetooth/manager.rs:868; src/dsp/equalizer.rs:156; src/dsp/presets.rs:179; src/state/config.rs:241; src/state/mod.rs:414; src/web/ws.rs:273. A count of `#[test]` and `#[tokio::test]` lines gives 70 across these files; one test carries `#[ignore]` (src/art.rs:466-469). README.md:208 gives the figure "66 unit tests".
- No test attributes in: src/main.rs, src/audio/capture.rs, src/audio/cast_stream.rs, src/audio/filter_chain.rs, src/audio/webrtc_audio.rs, src/bluetooth/agent.rs, src/bluetooth/avrcp.rs, src/web/mod.rs, src/web/routes.rs (same count command). No test files under webui/ (search: `git ls-files | grep -i test`).
- Test invocation: .githooks/pre-commit:22-23; .github/workflows/build.yml:53-54. No test output, log file or CI result is stored in the tree (search: `git ls-files | grep -iE 'log|result|report'`; .gitignore:4).
- Statements about tested devices and behaviour: README.md:279-285; forinstall.md:94-108, 160-165; BUILD-PLAN.md "Check:" lines (16, 25-26, 34, 42, 50, 62, 68, 74, 79-80, 87, 94, 100); src/main.rs:33-36; src/bluetooth/manager.rs:186-188, 291-293; src/audio/line_in.rs:8-10
- Runtime counters: src/audio/webrtc_audio.rs:270-276; deploy/doctor.sh:65-66
- Known issues by the author: README.md:42-49, 236-254, 276-277; API.md:91-92; forinstall.md:107-108; DECISIONS.md:77, 103-104; SOUNDSYNC-SPEC.md:2638-2971 (predecessor)

S12 Security and exposure
- Listener: src/web/mod.rs:115-124; bind address and port defaults src/state/config.rs:17-21, 37-38; install.sh:332-334
- No-auth statement and CORS: API.md:13; src/web/mod.rs:110; src/audio/cast_stream.rs:123
- Firewall: install.sh:274-284, 377-379; uninstall.sh:145-150
- Outbound connections: src/audio/webrtc_audio.rs:128-134 and webui/src/webrtc.ts:117-119 (STUN server); src/art.rs:253-337 (album-art lookups via `curl`); src/audio/chromecast.rs:209-213 (`connect_without_host_verification`); src/main.rs:33-42 (rustls provider)
- Pairing exposure: src/bluetooth/agent.rs:22-28, 56-94; src/bluetooth/manager.rs:191-199
- Process privilege: deploy/sonicflow.service:12-14, 21-22; install.sh:37, 243-272
- Subprocess argument handling: src/audio/cast_stream.rs:12-15; README.md:274-275
- Secrets and keys: none stored in tracked files (search described in the Rule 11 note above). forinstall.md:49-52 names a location on the development machine holding GitHub CLI credentials (kind: CLI auth credentials; value not in the repo). The app's own persisted state is at the path built in src/state/config.rs:203-206; the art cache at src/art.rs:349-351.
- README.md:256-277 (threat model)

## 7. Git references

- Default branch: `main`. `refs/remotes/origin/HEAD` is not set in this clone (`git symbolic-ref refs/remotes/origin/HEAD` → "not a symbolic ref"), so git does not name a default directly. Indicators for `main`: it is the only branch with upstream tracking in `.git/config` (`[branch "main"] remote = origin, merge = refs/heads/main`), and it is the push-trigger branch at .github/workflows/build.yml:5.
- Checked-out branch: `claude/inspiring-galileo-a9dlia` (`.git/HEAD` → `ref: refs/heads/claude/inspiring-galileo-a9dlia`).
- Local branches: `claude/inspiring-galileo-a9dlia`, `main`.
- Remote-tracking branches: `origin/claude/inspiring-galileo-a9dlia`, `origin/main`.
- All four refs point at the same commit, 3046b9e8baea2b9150963706525ca95d76f983bc (`git for-each-ref`); `git branch -a --merged main` lists all four.
- Tags: none (`git tag` printed nothing).
- Stash count: 0 (`git stash list | wc -l`).
- Remote: `origin` → https://github.com/tunlezah/SonicFlow (`.git/config`).
- Commits reachable from HEAD: 22. First: d8b17a3, 2026-08-29, "Phase 0: toolchain, scaffold, first green build". Last: 3046b9e, 2026-08-30, "v0.2.1: art_online config knob; installer template + README document it".
