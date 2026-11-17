# R2 map: AudioBurgerSonnet

Repo root: /home/user/AudioBurgerSonnet. All paths below are relative to that root unless they start with `/`.
Read at commit 1d878c2 (branch `main` and the checked-out branch point at the same commit; see section 7).
This file is a map. It records where things are. It carries no outcome grades.

---

## 1. Layout

### Top-level directories

| Directory | Contents |
|---|---|
| `audioburger/` | The Python package (application code), plus the Svelte web frontend source under `audioburger/web_ui/frontend/`. |
| `.git/` | Git metadata. |

There is one top-level directory besides `.git/`. Everything else at the top level is a file (20 files: 14 `.md`, `install.sh`, `audioburger.service`, `config.toml`, `requirements.txt`, `setup.py`, `.gitignore`; listing from `git ls-files`).

### Subdirectories of `audioburger/`

| Directory | Contents | Tracked files |
|---|---|---|
| `audioburger/` (package root) | `__init__.py` (version string, `audioburger/__init__.py:5`) and `main.py` (application orchestrator and CLI, 446 lines) | 2 |
| `audioburger/audio_plane/` | `audio_pipeline.py` (capture, resample, FFT, routing; 488 lines) and an empty `__init__.py` | 2 |
| `audioburger/bluetooth/` | `a2dp_sink.py` (BlueZ D-Bus adapter and device calls; 368 lines) and an empty `__init__.py` | 2 |
| `audioburger/chromecast/` | `chromecast_output.py` (HTTP stream server and pychromecast calls; 453 lines) and an empty `__init__.py` | 2 |
| `audioburger/config/` | `config_manager.py` (TOML loading, dataclasses, validation; 478 lines) and an empty `__init__.py` | 2 |
| `audioburger/control_plane/` | An empty `__init__.py` only (0 lines) | 1 |
| `audioburger/logging/` | `logger.py` (JSON logger and ring buffer; 213 lines) and an empty `__init__.py` | 2 |
| `audioburger/tests/` | An empty `__init__.py` only (0 lines) | 1 |
| `audioburger/web_ui/` | `api_server.py` (aiohttp REST and WebSocket server; 642 lines) and an empty `__init__.py` | 2 |
| `audioburger/web_ui/frontend/` | Svelte + Vite frontend source: `index.html`, `package.json`, `package-lock.json`, `vite.config.js`, `src/App.svelte`, `src/main.js` | 6 |

No built frontend output is tracked. `.gitignore:43` ignores `audioburger/web_ui/static/`.

### File counts by type (tracked files, `git ls-files`, 42 total)

| Type | Count | Notes |
|---|---|---|
| `.py` | 17 | 8 are zero-line `__init__.py` files; 1 is `setup.py`; 1 is `audioburger/__init__.py` (5 lines); 7 are modules with code |
| `.md` | 14 | all at the top level; 18,879 lines across all tracked files (`wc -l` over `git ls-files`), of which the 14 `.md` files hold 12,732 |
| `.json` | 2 | `package.json`, `package-lock.json` |
| `.js` | 2 | `vite.config.js`, `src/main.js` |
| `.svelte` | 1 | `src/App.svelte` |
| `.html` | 1 | `frontend/index.html` |
| `.sh` | 1 | `install.sh` (1,339 lines; the only file with mode 100755, `git ls-files -s`) |
| `.service` | 1 | `audioburger.service` |
| `.toml` | 1 | `config.toml` |
| `.txt` | 1 | `requirements.txt` |
| `.gitignore` | 1 | |

`git status --porcelain` and `git status --short --ignored` both returned nothing: no untracked, modified or ignored files in the working tree.

---

## 2. Entry points

### Executables, scripts, service definitions and main functions

| # | Kind | Location | What it is |
|---|---|---|---|
| E1 | Python main function | `audioburger/main.py:408` (`def main()`), invoked at `audioburger/main.py:445-446` (`if __name__ == '__main__': sys.exit(main())`) | CLI entry. Parses `--config` and `--log-level` (`audioburger/main.py:417-432`), builds `AudioBurgerApp` (`audioburger/main.py:435`), runs `asyncio.run(app.run())` (`audioburger/main.py:438`). |
| E2 | Console script | `setup.py:39-43` (`'audioburger=audioburger.main:main'`) | A pip install creates an `audioburger` command that calls E1. |
| E3 | systemd unit file in the repo | `audioburger.service:8` (`ExecStart=/usr/bin/python3 -m audioburger.main --config /etc/audioburger/config.toml`) | `Type=notify` (`:7`), `WorkingDirectory=/opt/audioburger` (`:9`), `User=audioburger` / `Group=audio` (`:10-11`), `WatchdogSec=60s` (`:14`), `Restart=on-failure` / `RestartSec=10s` (`:17-18`), `After=network.target bluetooth.target` (`:3`). Starts E1 through `python3 -m`. |
| E4 | systemd unit written by the installer | heredoc at `install.sh:965-993`, written to `/etc/systemd/system/audioburger.service` (`install.sh:35`) | `ExecStart=/usr/local/bin/audioburger --config /etc/audioburger/config.toml` (`install.sh:975`), `Type=simple` (`:972`), `Restart=always` / `RestartSec=5` (`:976-977`), `After=network.target sound.target` (`:968`). Starts E2. The installer does not copy the repo file E3; the only `.service` reference in `install.sh` is the destination path at `install.sh:35`. |
| E5 | Shell script | `install.sh:1` (`#!/usr/bin/env bash`), `main()` at `install.sh:1217`, called at `install.sh:1339` (`main "$@"`) | Installer and uninstaller. Flags `--auto`, `--uninstall`, `--help` (`install.sh:1221-1247`). |
| E6 | npm scripts | `audioburger/web_ui/frontend/package.json:6-10` (`dev`, `build`, `preview`) | Vite dev server, build and preview for the frontend. |
| E7 | Frontend JavaScript entry | `audioburger/web_ui/frontend/index.html:11` loads `/src/main.js`; `audioburger/web_ui/frontend/src/main.js:10-12` mounts `App.svelte` | Browser-side entry point. |
| E8 | Package setup script | `setup.py:29-53` | setuptools `setup()`; reads `requirements.txt` into `install_requires` (`setup.py:13-19`, `:38`). |

No Dockerfile, Makefile, CI workflow, udev rule, D-Bus policy file or other service definition is tracked (full tracked list from `git ls-files`, 42 files).

### What starts what

1. Process start, three documented routes:
   - systemd unit E3 runs `python3 -m audioburger.main` (`audioburger.service:8`).
   - systemd unit E4 runs the console script E2 (`install.sh:975`, `setup.py:41`).
   - manual run: `python -m audioburger.main` (`README.md:57`) or `audioburger` (`README.md:72`).
2. `main()` constructs `AudioBurgerApp`, whose `__init__` calls `init_config()` (`audioburger/main.py:53`, `audioburger/config/config_manager.py:451-463`) and sets the log level (`audioburger/main.py:56-59`).
3. `AudioBurgerApp.run()` (`audioburger/main.py:333`) installs signal handlers (`:350`), then `_initialize_components()` (`:353`, body `:165-204`) constructs four components: `A2DPSink` (`:175`), `AudioPipeline` (`:179`), `ChromecastOutput` (`:183`), `APIServer` (`:187-192`), and wires three callbacks (`:196-198`).
4. `_start_components()` (`audioburger/main.py:356`, body `:206-244`) starts them in this order: audio pipeline (`:220`), Bluetooth (`:224`), Chromecast (`:228`), API server (`:232`).
5. `AudioPipeline.start()` creates the background task `_audio_processing_loop` (`audioburger/audio_plane/audio_pipeline.py:111`).
6. `ChromecastOutput.start()` starts a second aiohttp server (`audioburger/chromecast/chromecast_output.py:99`, `:110-124`).
7. `APIServer.start()` starts the main aiohttp server (`audioburger/web_ui/api_server.py:92-107`).
8. If `sdnotify` imported (`audioburger/main.py:29-33`), `run()` sends `READY=1` (`:360`) and starts the watchdog heartbeat task (`:368`, body `:280-306`).
9. `install.sh` `main()` runs, in order: `check_root` (`install.sh:1251`), platform detection (`:1264-1267`), tool and build-tool checks (`:1285-1303`), `setup_python_environment` and `install_python_dependencies` (`:1308-1309`), `setup_nodejs_environment` and `build_frontend` (`:1312-1313`), `install_audioburger_package` (`:1316`), `deploy_configuration`, `deploy_web_assets`, `configure_ports` (`:1319-1321`), `setup_system_service` (`:1324`), `verify_installation` (`:1327`), `print_summary` (`:1330`). `build_frontend` runs `npm install` and `npm run build` (E6) (`install.sh:817`, `:823`). `setup_system_service` writes E4 and optionally runs `systemctl enable` and `systemctl start` (`install.sh:1002-1009`).

---

## 3. Run path

Chain from process start to audio leaving the output, as it reads in the code at commit 1d878c2. This is a trace of call sites only.

1. `audioburger.service:8` or `install.sh:975` or `README.md:57` → process starts in `audioburger/main.py:445-446`.
2. `audioburger/main.py:408` `main()` → `audioburger/main.py:435` `AudioBurgerApp(...)` → `audioburger/main.py:53` `init_config(config_path)` → `audioburger/config/config_manager.py:94-104` (`ConfigManager.__init__` → `_load_config`, `:264-375`). Config file search order: `audioburger/config/config_manager.py:88-92`, `:106-124`.
3. `audioburger/main.py:438` `asyncio.run(app.run())` → `audioburger/main.py:333` `run()`.
4. `audioburger/main.py:353` → `_initialize_components()` `audioburger/main.py:165-204`. Callback wiring:
   - `audioburger/main.py:196` web output callback = `APIServer.broadcast_audio`
   - `audioburger/main.py:197` Chromecast output callback = `ChromecastOutput.add_audio_data`
   - `audioburger/main.py:198` EQ callback = `APIServer.broadcast_eq_data`
5. `audioburger/main.py:356` → `_start_components()` → `audioburger/main.py:220` → `AudioPipeline.start()` `audioburger/audio_plane/audio_pipeline.py:96-117` → task created at `audioburger/audio_plane/audio_pipeline.py:111` → `_audio_processing_loop()` `audioburger/audio_plane/audio_pipeline.py:157-193`.
6. `audioburger/main.py:224` → `A2DPSink.start()` `audioburger/bluetooth/a2dp_sink.py:70-131`:
   - returns early if `config.enabled` is false (`:76-78`) or `dbus` did not import (`:80-82`, import guard `:16-22`)
   - `DBusGMainLoop(set_as_default=True)` (`:89`), `dbus.SystemBus()` (`:92`)
   - adapter object at `/org/bluez/<config.adapter>` (`:95-99`); on `DBusException` logs and returns (`:100-103`)
   - sets `Powered` true (`:106`) and `Alias` to the literal `"AudioBurger"` (`:109`)
   - registers receivers for `InterfacesAdded` / `InterfacesRemoved` (`:112-122`); the handlers log only (`:157-171`)
7. GAP (Bluetooth side, A2DP sink role). No hop was found from `A2DPSink.start()` to any registration of an A2DP sink endpoint, profile or pairing agent, and none to a GLib main loop being run. Searched `audioburger/` for `RegisterAgent`, `Agent1`, `RegisterProfile`, `RegisterEndpoint`, `RegisterApplication`, `GLib`, `MainLoop`, `gi.repository`, `Trusted`, `RemoveDevice`, `.Pair(`, `PropertiesChanged`, `Media1`, `MediaPlayer`, `MediaControl`: the only hits are the `dbus.mainloop.glib` import and `DBusGMainLoop(set_as_default=True)` at `audioburger/bluetooth/a2dp_sink.py:18-19`, `:89`, and the unrelated `trusted` attribute at `:28`, `:31`. Searched `install.sh` (case-insensitive) for `bluez`, `pipewire`, `wireplumber`, `pulse`, `alsa`, `bluetooth`: no package or configuration hit (the only matches are the word "audio" at `install.sh:5`, `:941-942`, `:967`).
8. Discoverable and pairable mode is not reached from the start-up chain. `enable_discoverable()` is defined at `audioburger/bluetooth/a2dp_sink.py:173-191` and its only caller is the HTTP handler `_handle_bt_pair` (`audioburger/web_ui/api_server.py:509`), routed from `POST /api/v1/bluetooth/pair` (`audioburger/web_ui/api_server.py:191`). `disable_discoverable()` (`audioburger/bluetooth/a2dp_sink.py:193-203`) has no caller in `audioburger/` (grep for `disable_discoverable`).
9. `audioburger/main.py:228` → `ChromecastOutput.start()` `audioburger/chromecast/chromecast_output.py:80-108` (returns early if disabled `:86-88` or `pychromecast` did not import `:90-92`) → `_start_http_server()` `:110-124`: route `/stream.<format>` (`:113`), bound to `0.0.0.0:<chromecast.http_port>` (`:118`).
10. `audioburger/main.py:232` → `APIServer.start()` `audioburger/web_ui/api_server.py:74-120` → `_setup_routes()` `:168-220` → `TCPSite` on `config.bind_address:config.http_port` (`:102-107`).
11. `audioburger/main.py:371` `run()` waits on the shutdown event. From here audio movement depends on the processing loop of hop 5.
12. Stream activation. The loop body is skipped while `_active_source` is `AudioSource.NONE` (`audioburger/audio_plane/audio_pipeline.py:163`); the initial value is `NONE` (`:60`), and the initial destination is `NONE` (`:61`). The only writers of those two fields are:
    - `change_source()` `audioburger/audio_plane/audio_pipeline.py:417-436`, called from `PUT /api/v1/input` (`audioburger/web_ui/api_server.py:186`, handler `:442-468`)
    - `change_destination()` `audioburger/audio_plane/audio_pipeline.py:438-457`, called from `PUT /api/v1/output` (`audioburger/web_ui/api_server.py:187`, handler `:470-496`)
    - `start_stream()` `audioburger/audio_plane/audio_pipeline.py:353-386`, called from `POST /api/v1/stream/start` (`audioburger/web_ui/api_server.py:199`, handler `:553-576`; the handler substitutes `LINE_IN` and `WEB` when the fields are `NONE`, `:565-569`)
    - `stop_stream()` `audioburger/audio_plane/audio_pipeline.py:388-415`, called from `POST /api/v1/stream/stop` (`audioburger/web_ui/api_server.py:200`, handler `:578-589`) and from `AudioPipeline.stop()` (`audioburger/audio_plane/audio_pipeline.py:132`)
    No Bluetooth event calls any of these (the Bluetooth signal handlers log only, `audioburger/bluetooth/a2dp_sink.py:157-171`). The tracked frontend calls none of these endpoints: the only request in `audioburger/web_ui/frontend/src` is `fetch('/api/v1/status')` at `audioburger/web_ui/frontend/src/App.svelte:19` (searched `WebSocket`, `AudioContext`, `/ws`, `fetch(` in `frontend/src` and `frontend/index.html`).
13. Capture. `audioburger/audio_plane/audio_pipeline.py:165` → `_capture_audio()` `:195-201`:
    - `AudioSource.BLUETOOTH` → `_capture_bluetooth()` `:222-232`
    - `AudioSource.LINE_IN` → `_capture_line_in()` `:203-220`, which calls `sd.rec(...)` on the default input (`:210-216`)
14. GAP (capture point, Bluetooth stack → this code). `_capture_bluetooth()` returns `np.zeros(...)` at `audioburger/audio_plane/audio_pipeline.py:232`. Its docstring and comment read: "For now, this is a placeholder that returns silence." (`:227`) and "# TODO: Implement Bluetooth audio capture via PipeWire" (`:229`). No call that reads audio from BlueZ, PipeWire, PulseAudio, ALSA or a file descriptor for the Bluetooth source was found. Searched `audioburger/` for `MediaTransport`, `MediaEndpoint`, `bluez_input`, `bluez_source`, `pw-record`, `pw-cat`, `parec`, `bluealsa`, `subprocess`, `pipewire`, `pulse`, `alsa`, `gst`: the only hits are comments and docstrings (`audioburger/audio_plane/audio_pipeline.py:46`, `:226`, `:229-230`, `:362`, `:426`; `audioburger/bluetooth/a2dp_sink.py:8`, `:40`) and a label string at `audioburger/web_ui/api_server.py:430`.
15. Processing, same loop iteration:
    - resample when `config.sample_rate` differs from 48000 (`audioburger/audio_plane/audio_pipeline.py:169-170`, `_resample` `:278-302`, target set at `:71`)
    - FFT to 10 bands (`:173`, `_analyze_fft` `:234-276`)
    - EQ callback call at `:176-178` (`self._eq_callback(eq_bands)`, no `await`); the registered callback `broadcast_eq_data` is declared `async def` at `audioburger/web_ui/api_server.py:591`
    - loop sleep `asyncio.sleep(0.01)` (`audioburger/audio_plane/audio_pipeline.py:185`)
16. Output routing. `audioburger/audio_plane/audio_pipeline.py:183` → `_send_to_output()` `:304-309`, branch on `_active_destination`.
17. Web branch. `_send_to_web()` `audioburger/audio_plane/audio_pipeline.py:311-325`: float32 → int16 bytes (`:321-322`) → `await self._web_output_callback(audio_bytes)` (`:323`) → `APIServer.broadcast_audio()` `audioburger/web_ui/api_server.py:619-638` → `ws.send_bytes(audio_data)` at `audioburger/web_ui/api_server.py:636`, to every client in `_websocket_clients`. Clients join through `GET /ws` (`audioburger/web_ui/api_server.py:203-204`, handler `:349-424`, append at `:375`). **Audio leaves the process at `audioburger/web_ui/api_server.py:636`.**
18. GAP (web branch, browser side). No tracked code opens the `/ws` socket or plays received bytes. Searched `audioburger/web_ui/frontend/src` and `frontend/index.html` for `WebSocket`, `AudioContext`, `/ws`: zero hits.
19. Chromecast branch. `_send_to_chromecast()` `audioburger/audio_plane/audio_pipeline.py:327-341`: float32 → int16 bytes (`:337-338`) → `await self._chromecast_output_callback(audio_bytes)` (`:339`); the registered callback `add_audio_data` is declared as a plain `def` at `audioburger/chromecast/chromecast_output.py:156` → bytes appended to `_audio_buffer` (`:163-167`, 1 MB cap at `:166-167`) → `_handle_stream()` `:126-154` drains the buffer into the HTTP response (`:142-147`) with header `Content-Type: audio/<format>` (`:131`). **Audio leaves the process at `audioburger/chromecast/chromecast_output.py:147`**, to whichever client requested `/stream.<format>`.
20. GAP (Chromecast branch, telling the device to fetch the stream). `start_playback()` (`audioburger/chromecast/chromecast_output.py:361-389`) is the only place `mc.play_media(...)` is called (`:381`), and no caller of `start_playback` was found. Searched all tracked files for `start_playback` and `play_media`: the only code hits are the definition at `audioburger/chromecast/chromecast_output.py:361` and the call at `:381`; the other hits are in `.md` files (`README.md:282`, `PROJECT-COMPLETION-ROADMAP.md:116`, `:577`, `:586`, `AI-AGENT-IMPLEMENTATION-GUIDE.md:1212`, `:1225`, `architecture-and-build-plan.md:772`, `:780`, `:824`, `:902`, `research-chromecast-output.md:362`). `connect()` (`audioburger/chromecast/chromecast_output.py:283-326`) is reached from `POST /api/v1/chromecast/connect` (`audioburger/web_ui/api_server.py:196`, handler `:537-551`) and does not call `start_playback`.
21. GAP (Chromecast branch, encoding). No encoder call was found between the int16 PCM bytes of hop 19 and the response labelled `audio/<format>`. Searched `audioburger/**/*.py` for `flac`, `ffmpeg`, `encode`, `soundfile`, `subprocess`, `opus`: the hits are config validation (`audioburger/config/config_manager.py:49`, `:246-247`, `:330-332`) and a module docstring (`audioburger/chromecast/chromecast_output.py:9`).
22. No local sound-device output path exists in the code. `AudioDestination` has three members: `WEB`, `CHROMECAST`, `NONE` (`audioburger/audio_plane/audio_pipeline.py:35-39`). `sounddevice` is used for input only (`sd.rec`, `:210`).

Frontend serving, recorded here because paths differ between files:
- Vite build output directory: `../static` relative to `audioburger/web_ui/frontend/`, that is `audioburger/web_ui/static/` (`audioburger/web_ui/frontend/vite.config.js:8`).
- The API server looks for `audioburger/web_ui/frontend/static/` then `audioburger/web_ui/frontend/dist/` (`audioburger/web_ui/api_server.py:161-163`, `:210-217`).
- The installer checks `audioburger/web_ui/static/` (`install.sh:807`, `:828`) and copies it to `/var/www/audioburger` (`install.sh:32`, `:846-849`), then writes that path into `web_ui.static_path` (`install.sh:910`).
- `static_path` is not read anywhere in `audioburger/` outside `config_manager.py` (grep for `static_path`: the hits in `audioburger/web_ui/api_server.py:161-212` are a local variable of the same name).

---

## 4. Documentation

All fourteen documents are at the top level. "Added" is the commit that first added the file (`git log --diff-filter=A --name-only`).

| Path | Lines | Added | One-line summary |
|---|---|---|---|
| `README.md` | 465 | 70fc363 (2026-01-21); rewritten in deb899b (2026-02-01) | Project description, quick start, configuration, repository structure, an "Implemented vs Stubbed" status section (`README.md:187-240`), a sprint roadmap table (`:244-320`), logging and troubleshooting. Heading at `README.md:7` reads "Current Status: Phase 4, Step 1 - Foundation & Skeleton (NOT a working application)". |
| `project-context.md` | 255 | f510135 (2026-01-20) | Requirements document, self-described as the "single source of truth" (`project-context.md:9`): platforms (`:13`), goals (`:31`), non-goals (`:44`), Bluetooth scope (`:54`), audio architecture (`:78`), web UI (`:109`), logging (`:130`), security model (`:153`), project phases (`:171`), agent task definitions (`:214`). |
| `research-existing-projects.md` | 682 | e8f523d (2026-01-20) | Survey of existing open-source projects with overlapping function, with licence notes and a reuse-or-fork section (`:429`). |
| `bluetooth-a2dp-sink-research.md` | 607 | 933dede (2026-01-20) | Research report comparing three A2DP sink approaches (PipeWire + WirePlumber, PulseAudio + BlueZ, BlueALSA) (`:5-11`), codec support (`:201`), ARM vs x86 (`:229`), known bugs (`:272`), operational risks (`:344`), open questions (`:417`), references (`:546`). |
| `audio-framework-research-report.md` | 620 | 4b3987d (2026-01-20) | Research report on audio frameworks; its key finding names PipeWire + GStreamer (`:7`); sections on audio graph (`:194`), metering (`:236`), ARM vs x86 (`:285`), debugging (`:323`), performance risks (`:420`), gaps in the context document (`:541`). |
| `research-chromecast-output.md` | 618 | f8ed837 (2026-01-20) | Research report on Chromecast output: approaches (`:13`), rejected approaches (`:221`), resampling and latency (`:270`), reconnection (`:318`), known issues (`:446`). |
| `logging-observability-report.md` | 798 | 54cd761 (2026-01-20) | Logging design: architecture (`:18`), per-component responsibilities (`:98`), log levels (`:241`), correlation (`:335`), failure scenarios (`:405`), diagnostics (`:540`), error code taxonomy (`:741`). |
| `research-web-ui-visualization.md` | 636 | 254f53b (2026-01-20) | Research report on web UI frameworks and audio visualisation: frameworks (`:11`), techniques (`:95`), chosen stack (`:229`), performance (`:280`), mobile vs desktop (`:339`), pitfalls (`:421`). |
| `architecture-and-build-plan.md` | 2,471 | e181276 (2026-01-21) | Design document: components (`:32`), audio data flow diagram (`:222-320`), control vs audio plane (`:321`), Bluetooth architecture (`:391-539`), audio pipeline (`:540-720`), Chromecast (`:721-963`), web UI and REST/WebSocket API (`:964-1236`), logging (`:1237`), security (`:1364-1554`), seven named risks (`:1555-1761`), build phases (`:1762-2185`), testing strategy (`:2246-2401`). |
| `fixes.md` | 1,587 | d51fcef (2026-01-21) | Issue list titled "Fixes Required Before Release": security and reliability assessment with items C-1 to C-6 (`:31-517`), H-1 to H-8 (`:520-1087`), M-1 to M-8 (`:1090-1254`), L-1 to L-8 (`:1257-1357`), D-1 to D-8 (`:1360-1447`), and appendices on testing and checklists (`:1449-1587`). |
| `patch1.md` | 606 | b61e1ec (2026-01-26) | Change notes titled "Fixes Implemented v2.0": which `fixes.md` items were addressed (C-2 `:21`, C-3 `:87`, C-4 `:168`, C-5 `:273`, H-8 `:328`, L-1 `:429`, C-6 partial `:471`), a testing summary (`:529`), deferred issues (`:552`), dependency changes (`:570`), upgrade notes (`:587`). This is the closest thing to a changelog in the repo. |
| `INSTALLER.md` | 722 | 70b5a5a (2026-01-26) | Guide to `install.sh`: supported platforms (`:7`), prerequisites (`:29`), the eight installer phases (`:69-202`), post-installation (`:203`), re-running (`:283`), uninstallation (`:321`), troubleshooting (`:364`), advanced configuration (`:567`), platform notes (`:655`), development mode (`:686`). |
| `PROJECT-COMPLETION-ROADMAP.md` | 1,064 | 6445123 (2026-02-01); updated in 1491214 (2026-02-02) | Status analysis dated 2026-02-02 (`:3`) with "What's Actually Working" (`:34`), "What's NOT Working (Stubs)" (`:78-141`), an eight-phase implementation plan with code listings (`:142-999`), dependency summary (`:1000`), verification checklist (`:1031`). |
| `AI-AGENT-IMPLEMENTATION-GUIDE.md` | 1,601 | cec17a7 (2026-02-02) | Instruction document addressed to an AI coding agent (`:1-11`), with full code listings per module (`:46-1437`), a verification loop (`:1438`), a completion checklist (`:1540`). Recorded as material under Rule 10; nothing in it was acted on. |

No `CHANGELOG`, `NOTES`, `TODO`, `ISSUES`, `docs/` directory or licence file is tracked (`git ls-files`). `README.md:403` reads "TBD - See project governance for licensing decisions."

A lowercase `readme.md` was added in 9f9cb0b (2026-01-20) and deleted in deb899b (2026-02-01) (`git log --diff-filter=D --name-only`). `README.md:255` still refers to it.

Documents and code were written at different commits. The last two non-merge commits, eb02d59 and add5474 (both 2026-02-02), changed code and touched no `.md` file (`git show --stat eb02d59 add5474`: eb02d59 changed five files under `audioburger/`, `package-lock.json` and `requirements.txt`; add5474 changed `audioburger.service`, `audioburger/main.py` and `audioburger/web_ui/api_server.py`). Every `.md` file therefore predates the current state of those code files, and line references inside the documents point at earlier code (example: `README.md:220` cites `api_server.py:162-173`; `README.md:240` describes `requirements.txt` as it stood before eb02d59).

---

## 5. Dependencies

### Manifests and install scripts

| Path | Kind |
|---|---|
| `requirements.txt` | Python requirements (32 lines) |
| `setup.py` | setuptools script; `install_requires` is read from `requirements.txt` (`setup.py:13-19`, `:38`); `python_requires='>=3.11'` (`setup.py:36`) |
| `audioburger/web_ui/frontend/package.json` | npm manifest |
| `audioburger/web_ui/frontend/package-lock.json` | npm lockfile, `lockfileVersion` 3 (`package-lock.json:4`), 1,308 lines |
| `install.sh` | Installer: system packages through apt, dnf, yum, pacman or zypper (`install.sh:349-380`), then `pip install -r requirements.txt` (`install.sh:740`), `npm install` (`install.sh:817`), `pip install .` (`install.sh:753`) |

No `pyproject.toml`, `Pipfile`, `poetry.lock`, `environment.yml`, `Dockerfile` or apt package list file is tracked.

### Bluetooth dependencies named

| Dependency | Where | Version |
|---|---|---|
| `dbus-python` | `requirements.txt:16` ("D-Bus communication for BlueZ") | `>=1.3.2` (lower bound, not pinned) |
| BlueZ (system daemon, reached over D-Bus name `org.bluez`) | used in code at `audioburger/bluetooth/a2dp_sink.py:97-99`; not named in any manifest or in `install.sh` | none in a manifest. Documents state "BlueZ 5.x" (`bluetooth-a2dp-sink-research.md:11`) and "BlueZ 5.50+ with PipeWire Bluetooth backend" (`architecture-and-build-plan.md:199`) |
| `bluetooth.target` (systemd) | `audioburger.service:3-4` | n/a |

`install.sh` installs no Bluetooth package (case-insensitive search for `bluez`, `bluetooth`: no hit).

### Audio dependencies named

| Dependency | Where | Version |
|---|---|---|
| `numpy` | `requirements.txt:11` | `>=1.24.0` |
| `scipy` | `requirements.txt:12` | `>=1.10.0` |
| `sounddevice` | `requirements.txt:13` ("Audio capture and playback") | `>=0.4.6` |
| `pychromecast` | `requirements.txt:19` | `>=14.0.0` |
| PipeWire | named in comments and docstrings only (`audioburger/audio_plane/audio_pipeline.py:46`, `:226-230`); not in any manifest or in `install.sh` | none in a manifest. Document statement: "PipeWire 0.3.50+" (`architecture-and-build-plan.md:420`) |
| `sound.target` (systemd) | `install.sh:968` | n/a |

`install.sh` installs no audio system package (case-insensitive search for `pipewire`, `wireplumber`, `pulse`, `alsa`, `portaudio`, `ffmpeg`, `flac`, `avahi`: no hit).

### Other dependencies named

| Dependency | Where | Version |
|---|---|---|
| `aiohttp` | `requirements.txt:5` | `>=3.9.0` |
| `tomli` | `requirements.txt:8` (fallback import at `audioburger/config/config_manager.py:13-16`) | `>=2.0.1` |
| `sdnotify` | `requirements.txt:22` (optional import at `audioburger/main.py:29-33`) | `>=0.3.2` |
| `pytest`, `pytest-asyncio`, `pytest-aiohttp` | `requirements.txt:25-27` | `>=7.2.0`, `>=0.21.0`, `>=1.0.0` |
| Python | `requirements.txt:2`, `setup.py:36`, `install.sh:26` | 3.11 minimum |
| Node.js | `install.sh:27`; NodeSource `setup_20.x` script fetched at `install.sh:421` | 18 minimum; 20 from NodeSource |
| `svelte` | `package.json:13` | `^4.2.0`; lockfile resolves 4.2.20 (`package-lock.json:1193-1194`) |
| `vite` | `package.json:14` | `^5.0.0`; lockfile resolves 5.4.21 (`package-lock.json:1232-1233`) |
| `@sveltejs/vite-plugin-svelte` | `package.json:12` | `^3.0.0`; lockfile resolves 3.1.2 (`package-lock.json:819-820`) |
| `@picocss/pico` | `package.json:17` | `^2.0.0`; lockfile resolves 2.1.1 (`package-lock.json:463-464`) |
| System packages installed by `install.sh` | `curl`, `git` (`install.sh:1285-1293`); build tools (`install.sh:442-461`); Python packages (`install.sh:382-407`); Node.js (`install.sh:409-440`) | distribution versions |

All Python requirements are lower bounds (`>=`). The only exact versions in the repo are the npm lockfile resolutions.

---

## 6. Stage pointers

Paths most likely to hold the answers. Code paths first, then documents. A code path listed here means the topic is addressed or named there; it says nothing about the result.

### S01 Platform and stack
- `project-context.md:13-30` (operating systems, hardware targets, browsers)
- `README.md:19-23`, `README.md:465` ("Built with" line)
- `requirements.txt:1-32`; `setup.py:36`, `setup.py:44-52` (classifiers)
- `audioburger/web_ui/frontend/package.json:11-18`; `audioburger/web_ui/frontend/package-lock.json`
- `install.sh:6-7`, `install.sh:26-27`, `install.sh:222-284` (architecture, distribution, package manager, init system detection)
- `INSTALLER.md:7-36`, `INSTALLER.md:655-685`
- `architecture-and-build-plan.md:32-221`, `:199`, `:393-434`, `:976-999`
- `bluetooth-a2dp-sink-research.md:5-149`, `:500`; `audio-framework-research-report.md:13-193`
- Imports at `audioburger/audio_plane/audio_pipeline.py:12-25`, `audioburger/bluetooth/a2dp_sink.py:16-22`, `audioburger/chromecast/chromecast_output.py:12-24`, `audioburger/web_ui/api_server.py:13-21`

### S02 Adapter and controller
- `audioburger/bluetooth/a2dp_sink.py:87-109` (adapter lookup by name, power on, alias), `:100-103` (adapter lookup failure branch)
- `config.toml:6`; `audioburger/config/config_manager.py:27`, `:290-292` (adapter name pattern)
- `project-context.md:54-77`
- `bluetooth-a2dp-sink-research.md:229-271`, `:362`, `:483-486`
- `architecture-and-build-plan.md:1586-1615`
- `fixes.md:1314`, `fixes.md:1488`

### S03 Discoverability, pairing and trust
- `audioburger/bluetooth/a2dp_sink.py:109` (alias), `:173-203` (discoverable, timeout, pairable), `:205-278` (paired device listing; two methods named `get_paired_devices`, `:205` and `:244`), `:25-32` (`BluetoothDevice` with a `trusted` field)
- `audioburger/web_ui/api_server.py:190-192`, `:498-527` (Bluetooth endpoints)
- `config.toml:7`; `audioburger/config/config_manager.py:28`, `:293-295`
- `architecture-and-build-plan.md:435-487`, `:1414-1511` (includes `:1445-1451`)
- `project-context.md:54-77`, `:153-170`
- `bluetooth-a2dp-sink-research.md:344-416`, `:417-545` (includes `:526-531`)
- Search note for the auditor: `RegisterAgent`, `Agent1`, `.Pair(`, `RemoveDevice`, `Trusted` have zero hits in `audioburger/`.

### S04 Connection and A2DP profile
- `audioburger/bluetooth/a2dp_sink.py:1-9` (module docstring), `:112-122` and `:157-171` (device signals), `:280-364` (connect and disconnect)
- `config.toml:8-9`; `audioburger/config/config_manager.py:29-30`, `:296-301` (reconnect settings; no reader of `reconnect_attempts` or `reconnect_backoff_base` in `audioburger/` outside `config_manager.py`, per grep)
- `architecture-and-build-plan.md:418-434`, `:471-539` (includes `:473`, `:534`)
- `bluetooth-a2dp-sink-research.md:201-228`, `:272-343`
- `README.md:266-274`; `PROJECT-COMPLETION-ROADMAP.md:95-110`, `:365-479`
- `logging-observability-report.md:98-240`

### S05 Audio capture
- `audioburger/audio_plane/audio_pipeline.py:157-193` (loop), `:195-232` (capture functions; Bluetooth capture at `:222-232`), `:66` and `:232` (buffer shape)
- `config.toml:11-16`; `audioburger/config/config_manager.py:33-40`, `:224-237`, `:304-325`
- `architecture-and-build-plan.md:222-320`, `:540-720`
- `audio-framework-research-report.md:194-235`
- `PROJECT-COMPLETION-ROADMAP.md:80-94`, `:247-364`
- `README.md:231-232`, `:257-264`

### S06 Remote control and metadata
- Code: NONE SEEN. `AVRCP` has zero hits in any tracked file; `MediaPlayer`, `MediaControl`, `volume` have zero hits in `audioburger/`.
- Documents with nearby material: `project-context.md:165`; `architecture-and-build-plan.md:1360`, `:1384`, `:1392`, `:1457`; `logging-observability-report.md:524-534`; `bluetooth-a2dp-sink-research.md:582`

### S07 Processing
- `audioburger/audio_plane/audio_pipeline.py:168-183` (per-iteration steps), `:234-276` (FFT), `:278-302` (resample), `:311-341` (float32 → int16), `:185` (loop interval), `:82-85` (band ranges)
- `audioburger/chromecast/chromecast_output.py:142-149`, `:163-167` (stream buffer and polling)
- `architecture-and-build-plan.md:540-720` (includes `:703`), `:862-877`, `:1616-1645`, `:1674-1701`
- `research-chromecast-output.md:270-317` (includes `:296`)
- `audio-framework-research-report.md:105`, `:210`, `:420-482`; `research-web-ui-visualization.md:550`
- Search note: `drift` and `clock` have zero hits in `audioburger/`.

### S08 Output
- `audioburger/audio_plane/audio_pipeline.py:35-39` (destinations), `:304-341` (routing), `:438-457` (destination change)
- `audioburger/web_ui/api_server.py:434-440` (output list), `:470-496` (output selection), `:619-638` (binary WebSocket broadcast)
- `audioburger/chromecast/chromecast_output.py:110-167` (HTTP stream), `:207-281` (discovery; two methods named `discover_devices`, `:207` and `:249`), `:283-433` (connect, playback, stream URL)
- `config.toml:18-23`; `audioburger/config/config_manager.py:43-50`
- `architecture-and-build-plan.md:721-963`, `:1116-1155`
- `research-chromecast-output.md` (whole file); `README.md:276-292`; `PROJECT-COMPLETION-ROADMAP.md:111-125`, `:480-607`

### S09 User interface and control surfaces
- `audioburger/web_ui/api_server.py:168-220` (route table), `:222-589` (handlers), `:349-424` (WebSocket), `:159-166` (index page)
- `audioburger/web_ui/frontend/src/App.svelte:1-93`; `audioburger/web_ui/frontend/src/main.js`; `audioburger/web_ui/frontend/index.html`; `audioburger/web_ui/frontend/vite.config.js:19-31` (dev proxy)
- `audioburger/main.py:417-432` (CLI flags), `:106-109` and `:137-163` (SIGHUP reload)
- `config.toml` (whole file)
- `audioburger/logging/logger.py:76-84` with `audioburger/web_ui/api_server.py:265-276` (log buffer over HTTP)
- `README.md:17-106`; `INSTALLER.md:241-282`
- `architecture-and-build-plan.md:964-1236` (includes `:1156-1236`); `research-web-ui-visualization.md` (whole file)
- `PROJECT-COMPLETION-ROADMAP.md:126-135`, `:144-246`, `:608-925`

### S10 Lifecycle and operations
- `install.sh` (whole file; flow at `:1217-1333`, service at `:920-1020`, user creation at `:540-570`, configuration at `:860-914`, verification at `:1026-1099`, uninstall at `:1105-1154`)
- `INSTALLER.md` (whole file)
- `audioburger.service:1-32`
- `setup.py:29-53`; `requirements.txt`
- `audioburger/config/config_manager.py:23-78` (defaults), `:88-124` (search paths), `:202-262` (validation), `:377-409` (reload); `config.toml`
- `audioburger/main.py:28-33`, `:84-86`, `:280-331`, `:358-383` (systemd notify and watchdog), `:88-135` (signals), `:206-278` (start and stop order)
- `audioburger/logging/logger.py:19-26`, `:37-40`, `:96-139`
- `patch1.md:328-426`, `:570-595`; `README.md:110-138`, `:365-397`
- `logging-observability-report.md:18-97`, `:540-740`
- `.gitignore`

### S11 Testing and measured behaviour
- `audioburger/tests/__init__.py` (0 lines; the only file under `audioburger/tests/`)
- `requirements.txt:24-27` (test packages)
- `README.md:23` ("tested on Ubuntu 22.04+, Raspberry Pi OS"), `README.md:237-240`
- `patch1.md:529-551` (testing summary), `:552-569` (deferred issues)
- `fixes.md:29-1447` (issue list), `:1449-1511` (testing appendix), `:1513-1587` (checklists)
- `PROJECT-COMPLETION-ROADMAP.md:34-141`, `:926-999`, `:1031-1048`
- `architecture-and-build-plan.md:1555-1761` (risks), `:2246-2401` (testing strategy)
- `AI-AGENT-IMPLEMENTATION-GUIDE.md:1438-1588` (verification loop and checklist)
- `bluetooth-a2dp-sink-research.md:272-343`; `research-chromecast-output.md:446-548`
- No test output, log file, CI result or measurement file is tracked (`git ls-files`; `.gitignore:46` ignores `*.log`).

### S12 Security and exposure
- `audioburger/web_ui/api_server.py:32-36` (limits), `:92`, `:102-107` (bind), `:286-326` (input validation), `:360-400` (connection and rate limits)
- `audioburger/chromecast/chromecast_output.py:118` (bind address literal), `:412-420` (local IP lookup)
- `config.toml:21`, `:27-28`; `audioburger/config/config_manager.py:57-58`, `:243-254`
- `audioburger.service:10-11`, `:20-24`
- `install.sh:193-197` (root check), `:935-954` (service user), `:971-989` (unit hardening lines)
- `fixes.md:31-84` (C-1), `:936-1017` (H-7), `:1169-1202` (M-6), `:1513-1532`
- `patch1.md:19-325`
- `architecture-and-build-plan.md:1364-1554`; `project-context.md:153-170`
- Secrets: NONE SEEN. Search of all tracked files except the lockfile for `auth`, `token`, `password`, `api_key`, `secret` hit documents only, plus the word "author" at `setup.py:35`. No key file, link-key store or device address list is tracked.

---

## 7. Git references

| Item | Value | Source |
|---|---|---|
| Default branch | `main`, with this qualification: `refs/remotes/origin/HEAD` is not set in the clone (`git symbolic-ref refs/remotes/origin/HEAD` → "fatal: ref refs/remotes/origin/HEAD is not a symbolic ref"), so the remote default could not be read. `main` is the only branch with a tracking block in `.git/config` (`[branch "main"]`), and `/home/user/audio/00-manifest.md:23` and `:31` record `main` as the default. | git, manifest |
| Checked-out branch | `claude/inspiring-galileo-a9dlia` (`.git/HEAD`; `git rev-parse --abbrev-ref HEAD`) | git |
| Local branches | `main` → 1d878c2; `claude/inspiring-galileo-a9dlia` → 1d878c2 | `git branch -a -vv` |
| Remote branches | `origin/main` → 1d878c2; `origin/claude/inspiring-galileo-a9dlia` → 1d878c2 | `git branch -a -vv` |
| Branches not merged into `main` | none (`git branch -a --no-merged main` returned nothing) | git |
| Tags | none (`git tag` returned nothing) | git |
| Stash count | 0 (`git stash list` returned nothing) | git |
| HEAD commit | 1d878c2e1b15c4cdb2baf342c30fbb247d491737, 2026-02-02 14:09:12 +1100, "Merge pull request #15 from tunlezah/claude/implement-ai-agent-guide-9YcaS" | `git log -1` |
| Commit count | 35 reachable from all refs; 35 on `main`; 20 non-merge and 15 merge commits | `git rev-list --count --all`, `git rev-list --count main`, `git rev-list --count --no-merges main` |
| Date range | 2026-01-20 (9f9cb0b "Create readme.md") to 2026-02-02 (1d878c2) | `git log --date=short` |
| Remote | `origin` = the GitHub repository `tunlezah/AudioBurgerSonnet` | `git remote -v` |
| Working tree | clean (`git status --porcelain` empty) | git |

All four refs point at the same commit. The fifteen merge commits (pull requests #1 to #15) name fourteen distinct source branches in their subjects, `claude/project-analysis-roadmap-9Ycyz` appearing twice (`git log --merges --format=%s`; for example `claude/implement-ai-agent-guide-9YcaS` in 1d878c2 and `claude/security-fixes-v2.0-zv5Li` in b1c1a12); none of those branches exists as a ref in this clone.

Non-merge commits that changed any non-`.md` file, for the History auditor (`git log --no-merges --name-only`): 70fc363 (2026-01-21, skeleton), b61e1ec (2026-01-26, "v2.0 security fixes and systemd watchdog integration"), 70b5a5a (2026-01-26, installer), eb02d59 (2026-02-02, "Implement Phases 1-4: Core audio routing functionality"), add5474 (2026-02-02, "Implement Phases 5, 7, and 9: Web Output, REST API, and systemd service").
