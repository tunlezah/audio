# R2 stage audit, audio stages S07 to S12: AudioBurgerSonnet

Repo root: /home/user/AudioBurgerSonnet. Read at commit 1d878c2 (branch `main`; four refs point at it, `git branch -a -vv`). All paths are relative to the repo root.

Code files are cited by file name. Each name is unique in the repo:

| Cited as | Full path |
|---|---|
| audio_pipeline.py | audioburger/audio_plane/audio_pipeline.py |
| chromecast_output.py | audioburger/chromecast/chromecast_output.py |
| api_server.py | audioburger/web_ui/api_server.py |
| main.py | audioburger/main.py |
| config_manager.py | audioburger/config/config_manager.py |
| logger.py | audioburger/logging/logger.py |
| a2dp_sink.py | audioburger/bluetooth/a2dp_sink.py |
| App.svelte | audioburger/web_ui/frontend/src/App.svelte |
| vite.config.js | audioburger/web_ui/frontend/vite.config.js |

Three facts that many records below rely on:

1. Document dates. Every `.md` file was last changed before the last two code commits. README.md last changed in deb899b (2026-02-01); PROJECT-COMPLETION-ROADMAP.md in 1491214 (2026-02-02); fixes.md in d51fcef; patch1.md in b61e1ec; INSTALLER.md in 70b5a5a. The audio, Bluetooth and Chromecast modules were rewritten in eb02d59 and the API was extended in add5474, both later (`git log --format='%h %ad' -- <file>`).
2. General claims in two commit messages. Commit eb02d59 message: "All core audio routing components are now functional." Commit add5474 message: "All audio routing is now fully functional end-to-end". Neither message describes a run, a test or a device. In the records these are treated as general STATED claims and not as evidence of a result for a particular path. Where the code and a claim differ, both are cited.
3. Stated manual test. Commit 70fc363 message has a "TESTING:" list for the skeleton state of the code (all three audio components were stubs at that commit). It is treated as STATED evidence of results for the items it names.

---

## S07 Processing

### R2-S07-01  Resampling to 48 kHz with the scipy polyphase resampler
- Question: S07.1
- What was attempted: each captured block is passed to `scipy.signal.resample_poly` when the configured rate differs from 48000 (audio_pipeline.py:15, :168-170, :278-302). The target rate is the literal 48000 (audio_pipeline.py:71). The source rate is read from `config.sample_rate`, not from the capture source (audio_pipeline.py:72). The up and down factors are reduced by their gcd (audio_pipeline.py:293-297). On an exception the block is returned unresampled (audio_pipeline.py:300-302). Configuration accepts 44100, 48000 or 96000 (config_manager.py:224-225, :307-309) and the default is 48000 (config.toml:12; config_manager.py:36), so at the default the branch at audio_pipeline.py:169 is not taken. Each call receives one block of `buffer_size` frames and nothing is carried from one call to the following one (audio_pipeline.py:278-299).
- Presence: CURRENT. Added in commit eb02d59 (`git log -- audioburger/audio_plane/audio_pipeline.py` lists 70fc363 and eb02d59 only).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Commit eb02d59 message lists "Implement audio resampling to 48kHz using scipy" as work done; no result, log or test is tracked.
- Why it turned out that way: UNKNOWN. No result is evidenced.
- Depends on: scipy >=1.10.0 and numpy >=1.24.0 (requirements.txt:11-12); the `audio.sample_rate` setting; block size `audio.buffer_size`, default 1024 (config.toml:13).
- Open questions: Does block-by-block resampling with no carried state produce discontinuities at block edges? What rate does a Bluetooth source deliver, given that the source rate is taken from configuration? Was the 44100 or the 96000 path ever run?
- Confidence: MEDIUM. INFERRED from clear code; no result evidence.

### R2-S07-02  FFT analysis into ten bands for visualisation, passed to an EQ callback
- Question: S07.1
- What was attempted: `_analyze_fft` mixes channels to mono by mean (audio_pipeline.py:246-249), takes the last 512 samples or left-pads with zeros (audio_pipeline.py:252-257), applies a Hann window (audio_pipeline.py:70), runs `scipy.fft.fft` and keeps 256 magnitudes (audio_pipeline.py:260), averages the magnitudes over ten fixed bin ranges (audio_pipeline.py:82-85, :264-265), converts to dB with a floor of 1e-10 and clamps to the range -60 to 0 (audio_pipeline.py:267-269). On an error it returns ten values of -60.0 (audio_pipeline.py:273-276). The analysis does not alter the audio that goes on to the output (audio_pipeline.py:173, :183). The result is handed to `self._eq_callback(eq_bands)` with no `await` (audio_pipeline.py:176-178). The callback registered in main.py:198 is `APIServer.broadcast_eq_data`, declared `async def` (api_server.py:591). INFERRED: calling an `async def` function without awaiting it creates a coroutine object that is not run, so the `send_json` call at api_server.py:615 is not reached from this call site. The analysis runs once per loop iteration (audio_pipeline.py:173; the comment at :185 reads "~100Hz processing rate"). The setting `audio.eq_update_rate` (config.toml:16) has no reader outside config_manager.py (grep over `audioburger/`). No tracked frontend code receives or draws band data (grep for `WebSocket`, `canvas`, `/ws` in `audioburger/web_ui/frontend/src` and `index.html`: zero hits).
- Presence: CURRENT. Analysis added in eb02d59; callback wired in add5474 (main.py:198).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Commit add5474 message lists "Connect EQ visualization callback for real-time frequency band updates" as work done. AI-AGENT-IMPLEMENTATION-GUIDE.md:1518-1536 describes a WebSocket check ("Should receive EQ data within 5 seconds"); no output of that check is tracked.
- Why it turned out that way: UNKNOWN. No result is evidenced.
- Depends on: numpy and scipy; the 512-sample window and the bin ranges at audio_pipeline.py:82-85; the choice to register the coroutine function directly. The listing in AI-AGENT-IMPLEMENTATION-GUIDE.md:1396-1401 wraps the broadcast in `asyncio.create_task`; main.py:198 does not.
- Open questions: Did any WebSocket client ever receive an `eq_data` message from this code? Do the bin ranges match the band centres 31 Hz to 16 kHz named in architecture-and-build-plan.md:661-670, given that 48000 / 512 is 93.75 Hz per bin? The magnitudes are not scaled by window length before the 0 dB clamp (audio_pipeline.py:260-269): what range do real signals produce?
- Confidence: MEDIUM. INFERRED from clear code; no result evidence.

### R2-S07-03  Conversion from float32 to 16-bit integer PCM before output
- Question: S07.1
- What was attempted: both output branches multiply the float block by 32767, cast to int16 and call `tobytes()` (audio_pipeline.py:321-322, :337-338). There is no clipping step between the multiply and the cast. Sample layout follows the numpy array shape, frames by channels (audio_pipeline.py:66, :210-216, :232).
- Presence: CURRENT. Added in eb02d59.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. No result is evidenced.
- Depends on: capture returning float32 (audio_pipeline.py:214, :232); channel count from `audio.channels` (config.toml:14).
- Open questions: What happens at the cast to a sample outside -1.0 to 1.0, for example after resampling?
- Confidence: MEDIUM. INFERRED from clear code; no result evidence.

### R2-S07-04  Encode stage for Chromecast named in configuration and docstring, with no encoder call in the code
- Question: S07.1
- What was attempted: the setting `chromecast.format` accepts 'flac' or 'aac' (config_manager.py:49, :246-247, :330-332; config.toml:22). The module docstring lists "FLAC/AAC encoding for audio streams" (chromecast_output.py:9). The format value is used in the route name, the Content-Type header and the stream URL (chromecast_output.py:113, :131, :377, :381, :433). The bytes placed in the stream buffer are the int16 PCM bytes from the pipeline (audio_pipeline.py:337-339; chromecast_output.py:156-167) and they are written to the HTTP response unchanged (chromecast_output.py:142-147). Search of Python, Svelte and JavaScript files under `audioburger/` for flac, aac, opus, ffmpeg, encode, soundfile, subprocess, wave, lame, mp3: the only hits are config validation and that docstring. INFERRED: the response labelled `audio/flac` carries unencoded PCM, because no call between audio_pipeline.py:337 and chromecast_output.py:147 changes the bytes.
- Presence: CURRENT (the label, the setting and the absence of an encoder). The module was rewritten in eb02d59.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The Phase 4 list in commit eb02d59 message names the HTTP server, discovery, connect, playback control, buffer management and IP detection, and does not name encoding. README.md:283 and PROJECT-COMPLETION-ROADMAP.md:122 list "FLAC/AAC encoding" as missing; both were written before eb02d59.
- Why it turned out that way: UNKNOWN. No result is evidenced and no reason for the absence is given.
- Depends on: the pull model in which the Cast device fetches the URL (architecture-and-build-plan.md:759-767); the format label chosen in config.
- Open questions: Was the stream ever requested by a Cast device or any other client? What does a Cast device do with PCM bytes served as `audio/flac`?
- Confidence: MEDIUM. INFERRED from clear code and a search; no result evidence.

### R2-S07-05  Processing pipeline planned in the documents: libsamplerate, IIR equaliser, Opus and FLAC encoding, separate audio plane (document only)
- Question: S07.1
- What was attempted: architecture-and-build-plan.md:540-701 describes Stage 1 resampling with libsamplerate at `SRC_SINC_MEDIUM_QUALITY` (:552-555, :606-609), Stage 2 a 10-band IIR biquad equaliser with ±12 dB per band or a PipeWire filter-chain (:561-565, :636-648), Stage 3a a 512-point Hann FFT with 50% overlap (:654-658), and output encoding as Opus for the browser and FLAC or AAC for Chromecast (:589-593, :699-701), with an FFmpeg command for FLAC (:856-860). It also describes a control plane and an audio plane joined by IPC (:321-387). project-context.md:98 reads "A 10-band graphic equaliser is applied to the selected input". In code there is no libsamplerate, no filter, no encoder and no IPC: grep of Python files under `audioburger/` for volume, gain, iir, biquad, lfilter, sosfilt returns nothing, and `audioburger/control_plane/` holds only a 0-byte `__init__.py` (`git ls-files`, `wc -c`). AI-AGENT-IMPLEMENTATION-GUIDE.md:87-535 gives a third version of the pipeline: an `sd.InputStream` feeding an `asyncio.Queue` of 100 (:159, :291-295), a sleep of `1.0 / eq_update_rate` (:395), and "simulated data" when the queue is empty (:406). The committed code calls `sd.rec` once per iteration (audio_pipeline.py:210-216).
- Presence: CURRENT as document text only (architecture document added in e181276, guide in cec17a7). audio_pipeline.py has two commits, the stub in 70fc363 and the current file in eb02d59; neither contains these stages.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. No document says why the committed code differs from the architecture text or from the guide listing.
- Depends on: these being plans. The guide is addressed to an AI coding agent (AI-AGENT-IMPLEMENTATION-GUIDE.md:1-11); it is recorded here as material under Rule 10 and nothing in it was acted on.
- Open questions: Was any of the planned stages tried outside the repo? No trace of one is tracked.
- Confidence: MEDIUM. STATED design text; absence of implementing code INFERRED from searches and file history.

### R2-S07-06  Placeholder pipeline with no processing (first version)
- Question: S07.1
- What was attempted: the first version of the module was a stub. Its docstring reads "CURRENT STATUS: STUB - No functionality implemented yet." and `start()` only set a flag (`git show 70fc363:audioburger/audio_plane/audio_pipeline.py`, lines 11 and 78-79).
- Presence: REMOVED. Replaced in commit eb02d59 (`git show --stat eb02d59`: audio_pipeline.py, 296 lines changed).
- Outcome: FAILED
- Outcome evidence: STATED. README.md:232: "All methods are stubs. Enums and state variables are defined. No PipeWire, no resampling, no FFT, no EQ, no audio routing." The file at 70fc363 agrees.
- Why it turned out that way: STATED. It was written as a placeholder: commit 70fc363 message, "Components are properly stubbed and ready for future implementation."
- Depends on: the phased plan in which the skeleton came first (README.md:7; project-context.md:199-201).
- Open questions: NONE
- Confidence: HIGH. STATED evidence that the code at 70fc363 agrees with.

### R2-S07-07  Points in the current code where audio waits; no latency measurement and no compensation
- Question: S07.2
- What was attempted: delay arises at these points. (a) Line-in capture records `buffer_size` frames with `blocking=True` inside the async loop (audio_pipeline.py:210-216). (b) The loop sleeps a fixed 0.01 s each iteration and 0.1 s after an exception (audio_pipeline.py:185, :191). (c) The web path awaits a send to each client in turn (api_server.py:634-636). (d) On the Chromecast path bytes collect in a buffer capped at 1 MB (chromecast_output.py:163-167) and the HTTP handler polls every 50 ms when the buffer is empty (chromecast_output.py:148-149). No code measures, logs, reports or compensates for latency: grep of `audioburger/` for latency, drift, clock, underrun, overrun, xrun returns one docstring line (audio_pipeline.py:9). INFERRED by arithmetic from config.toml:12-14 and chromecast_output.py:166: 1024 frames at 48000 Hz is about 21 ms per capture call, and 1 MB of 16-bit stereo PCM at 48000 Hz is about 5.5 s.
- Presence: CURRENT. Added in eb02d59.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No latency figure for this code is tracked.
- Why it turned out that way: UNKNOWN. No result is evidenced.
- Depends on: `audio.buffer_size` and `audio.sample_rate`; a single asyncio event loop for capture, processing and both servers (main.py:165-244); buffering inside the receiving browser or Cast device, which is outside the repo.
- Open questions: What end-to-end delay does either output path show? Does the blocking capture call at audio_pipeline.py:210 delay HTTP and WebSocket handling on the same loop?
- Confidence: MEDIUM. INFERRED from clear code; no result evidence.

### R2-S07-08  Latency figures and stance stated in the documents
- Question: S07.2
- What was attempted: the documents set latency aside as a non-goal and give estimates and targets. project-context.md:46 lists "Low-latency / real-time audio guarantees" under non-goals and :97 reads "Latency is best-effort". Design estimates: resampling "~10-20 ms" (architecture-and-build-plan.md:555, :609); pass-through "10-50 ms total (resampling + buffering)" (:696); Chromecast expected latency "5-10 seconds (acceptable per project non-goals §4)" (:864) and "20-60s buffering (PyChromecast)" (:955); browser option A "~100-300 ms" (:1144). Targets: "Latency <500 ms on Raspberry Pi 4" (:1641) and "Audio latency: <500 ms (Web Output), 5-10 s (Chromecast)" (:2380). research-chromecast-output.md:296-303 has a table headed "Measured Latency Characteristics" whose rows are other implementations: generic desktop streaming 5-10 s, mkchromecast about 15 s, PyChromecast live 20-60 s, node-castv2 about 3 s. research-web-ui-visualization.md:553-554 reads "likely 100-300ms total" and "Do not attempt to compensate (project explicitly non-goal: low-latency)". The UI mock-up shows "Latency: ~8s" in a status bar (architecture-and-build-plan.md:1029). Planned measurement: "Measure latency (expect <500 ms)" (:2008) and "Custom logging for latency measurements" (:2375). No figure in any document is described as measured on this project.
- Presence: CURRENT as document text. The estimates refer to stages that are absent from the code (libsamplerate, Opus, FLAC; see R2-S07-05).
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. No measurement exists to compare with the estimates.
- Depends on: the non-goal in project-context.md:46; third-party sources named in the research report.
- Open questions: Was the planned latency measurement (architecture-and-build-plan.md:2008) ever carried out?
- Confidence: MEDIUM. STATED design text; no measurement.

### R2-S07-09  No clock-drift or synchronisation handling in code; documents state that delay is not compensated
- Question: S07.3
- What was attempted: Code. Grep of `audioburger/` for latency, drift, clock, underrun, overrun, xrun finds one docstring line (audio_pipeline.py:9) and no handling; grep for `synchron` finds two docstrings containing the word "Synchronous" (a2dp_sink.py:246; chromecast_output.py:251). There is no timestamping, rate matching or adaptive resampling. Output is paced by the loop alone: one block per iteration, after the capture call and a 10 ms sleep (audio_pipeline.py:161-185). On the Chromecast path the only bound on a mismatch between producer and consumer is the 1 MB cap that keeps the newest bytes (chromecast_output.py:166-167). With the Bluetooth source the capture function returns a block of zeros without waiting (audio_pipeline.py:232), so INFERRED: block production in that branch is tied to the 10 ms sleep and not to an audio clock. For line-in, `sd.rec` is called afresh each iteration (audio_pipeline.py:210) and `_sd_stream` is never assigned a stream (audio_pipeline.py:79, :146-149), so INFERRED: no continuous capture stream is held open between iterations. Documents. research-web-ui-visualization.md:548-554 lists "Sync drift over time" as a symptom and answers "Do not attempt to compensate (project explicitly non-goal: low-latency)". architecture-and-build-plan.md:536 has a table row for "Audio Delay/Sync Issues" with the mitigation "Acceptable per project non-goals (§4), document expected latency". The build plan for Sprint 5 lists "Audio Synchronization", "Buffering strategy (minimize latency)" and "Handle underruns (silence insertion)" (architecture-and-build-plan.md:1992-1994). audio-framework-research-report.md:105 mentions drift only as a reported problem of PipeWire's JACK layer.
- Presence: CURRENT (absence in code; stance in document text).
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN for any result. The lack of compensation matches a STATED stance (research-web-ui-visualization.md:554; architecture-and-build-plan.md:536).
- Depends on: the non-goal in project-context.md:46; loop pacing by `asyncio.sleep` (audio_pipeline.py:185).
- Open questions: Does either output stall, overrun or drift over a long run? Was the Sprint 5 "silence insertion" task attempted? No code or note shows it.
- Confidence: MEDIUM. INFERRED from clear code and STATED document text.

---

## S08 Output

### R2-S08-01  Two output destinations (web browser, Chromecast), one active at a time, selected over the REST API
- Question: S08.1
- What was attempted: `AudioDestination` has the members WEB, CHROMECAST and NONE (audio_pipeline.py:35-39); the initial value is NONE (audio_pipeline.py:61). Selection: `PUT /api/v1/output` with `output_id` "web" or "chromecast" (api_server.py:187, :470-496) calls `change_destination`, which assigns the field (audio_pipeline.py:438-457). `POST /api/v1/stream/start` substitutes WEB when the destination is NONE and LINE_IN when the source is NONE (api_server.py:553-572). `POST /api/v1/stream/stop` resets both to NONE (api_server.py:578-589; audio_pipeline.py:388-409). `GET /api/v1/outputs` returns a fixed list of two entries, both with `"available": True` (api_server.py:434-440). No configuration key selects an output (config.toml:1-35). The tracked web page has no output control (App.svelte:40-93; its only request is at :19). There is no local sound device, file or other stream destination: `sounddevice` is used for input only (audio_pipeline.py:210). project-context.md:87-92 names the same two outputs.
- Presence: CURRENT. Enum from 70fc363; routing from eb02d59; endpoints from add5474.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Commit add5474 message lists the endpoints as added; no run is recorded.
- Why it turned out that way: UNKNOWN. No result is evidenced.
- Depends on: the API server being reachable; the selection being held in memory only (no file writes under `audioburger/`: grep for `open(` and `.write(` finds the config read at config_manager.py:277 and the HTTP write at chromecast_output.py:147).
- Open questions: Was output selection ever exercised against a running instance?
- Confidence: MEDIUM. INFERRED from clear code; no result evidence.

### R2-S08-02  Chromecast device discovery and selection through pychromecast
- Question: S08.1
- What was attempted: `GET /api/v1/chromecast/devices` (api_server.py:195, :529-535) calls `discover_devices()`. The class defines that name twice, async at chromecast_output.py:207 and sync at :249. INFERRED: the sync version is the one in effect, because a second `def` of the same name in a class body replaces the first. It calls `pychromecast.discovery.discover_chromecasts(timeout=5)` and returns uuid, friendly_name, host, port and model_name (chromecast_output.py:263-277). `POST /api/v1/chromecast/connect` with `device_uuid` (api_server.py:196, :537-551) calls `connect()`, which runs discovery again, matches the uuid, calls `get_chromecast_from_service` and `cast.wait()` (chromecast_output.py:299-318). `connect()` does not call `start_playback()`. `start_playback()` holds the only `play_media` call (chromecast_output.py:361-389) and has no caller (grep for `start_playback` in `audioburger/`: the definition only). INFERRED: with no `play_media` call no Cast device is told the stream URL, so a connected device has no instruction to fetch `/stream.<format>`. The setting `chromecast.discovery_timeout` (config.toml:20) has no reader; the timeout is the default argument 5 (chromecast_output.py:249). Discovery and `cast.wait()` are synchronous calls made inside async handlers (chromecast_output.py:263, :299, :304; api_server.py:532).
- Presence: CURRENT. Added in eb02d59 (module) and add5474 (endpoints).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Commit eb02d59 message lists "Add Chromecast device discovery via pychromecast" and "Add start/stop playback control" as work done; no discovered device or playback is recorded.
- Why it turned out that way: UNKNOWN. No result is evidenced.
- Depends on: pychromecast >=14.0.0 (requirements.txt:19) and the discovery function names used; mDNS reaching the LAN; the import guard at chromecast_output.py:20-24.
- Open questions: Was a Cast device ever discovered or connected? Which pychromecast version was installed? What was meant to call `start_playback()`?
- Confidence: MEDIUM. INFERRED from clear code; no result evidence.

### R2-S08-03  Placeholder Chromecast output (first version)
- Question: S08.1
- What was attempted: in the first version `discover_devices` returned an empty list, `connect` and `start_playback` returned False, and `get_stream_url` returned a localhost URL (`git show 70fc363:audioburger/chromecast/chromecast_output.py`, lines 136-137, 157-158, 200-201, 229-230).
- Presence: REMOVED. Replaced in commit eb02d59 (`git show --stat eb02d59`: chromecast_output.py, 353 lines changed).
- Outcome: FAILED
- Outcome evidence: STATED. README.md:235: "All methods are stubs. No mDNS discovery, no HTTP streaming server, no PyChromecast, no encoding." The file at 70fc363 agrees.
- Why it turned out that way: STATED. Written as a placeholder (commit 70fc363 message: "Chromecast Output (PyChromecast stub)").
- Depends on: the phased plan (README.md:7).
- Open questions: NONE
- Confidence: HIGH. STATED evidence that the code at 70fc363 agrees with.

### R2-S08-04  Web output: raw 16-bit PCM in binary WebSocket frames
- Question: S08.2
- What was attempted: `_send_to_web` converts the block to int16 bytes and awaits the web callback (audio_pipeline.py:311-325), which is `APIServer.broadcast_audio` (main.py:196). `broadcast_audio` calls `ws.send_bytes` for each client in `_websocket_clients` (api_server.py:619-638). The endpoint is `GET /ws`, registered when `web_ui.websocket_enabled` is true (api_server.py:203-204). Format on the wire: interleaved int16 PCM with no header, no framing metadata and no codec; channel count and block length come from config (2 channels and 1024 frames at defaults, config.toml:13-14); rate 48000 after the resampling step (audio_pipeline.py:71). The same socket is meant to carry JSON text of the form `{"type": "eq_data", "bands": [...]}` (api_server.py:603-606, :615; see R2-S07-02). Browser side: no tracked code opens `/ws` or plays audio (grep for `WebSocket`, `AudioContext`, `/ws` in the frontend source: zero hits).
- Presence: CURRENT. `_send_to_web` from eb02d59; `broadcast_audio` from add5474.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Commit add5474 message lists "WebSocket streaming for browser audio playback" as a feature; no run is recorded and no browser client code is tracked.
- Why it turned out that way: UNKNOWN. No result is evidenced.
- Depends on: aiohttp >=3.9.0 (requirements.txt:5); at most 10 clients (api_server.py:34); a client able to interpret headerless PCM, which the repo does not contain.
- Open questions: Has any client received and played these frames? Rate, channel count and byte order are not signalled in-band: how was a client meant to learn them?
- Confidence: MEDIUM. INFERRED from clear code; no result evidence.

### R2-S08-05  Chromecast output: chunked HTTP response labelled audio/<format>, to be pulled by the Cast device
- Question: S08.2
- What was attempted: a second aiohttp application listens on `0.0.0.0` at `chromecast.http_port`, default 8081 (chromecast_output.py:110-124; config.toml:21). It has one route, `/stream.<format>` (chromecast_output.py:113). The response sets `Content-Type: audio/<format>` and `Transfer-Encoding: chunked` (chromecast_output.py:128-134) and the body is the shared buffer, drained in a loop (chromecast_output.py:140-149). The payload is unencoded PCM (R2-S07-04). The control protocol is pychromecast: `media_controller.play_media(stream_url, 'audio/<format>')` followed by `block_until_active()` (chromecast_output.py:380-382), with the URL built from the local IP (chromecast_output.py:376-377). The local IP is found by connecting a UDP socket toward a public address literal (chromecast_output.py:412-420). `start_playback` has no caller (R2-S08-02).
- Presence: CURRENT. Added in eb02d59.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. No result is evidenced.
- Depends on: port 8081 being reachable from the Cast device over the LAN; a default route existing for the IP lookup; `chromecast.enabled` true and pychromecast importable, without which the server is not started (chromecast_output.py:86-92).
- Open questions: Did any Cast device ever fetch the stream?
- Confidence: MEDIUM. INFERRED from clear code; no result evidence.

### R2-S08-06  Output formats planned in the documents: Opus over WebSocket for the browser, FLAC or AAC over HTTP for Chromecast (document only)
- Question: S08.2
- What was attempted: architecture-and-build-plan.md:1139-1154 sets out "Option A: WebSocket + MediaSource Extensions (MSE)" with Opus and marks it as the chosen option; :308-317 and :839-860 set out FLAC as the Chromecast format with AAC as the alternative, encoded through FFmpeg. research-chromecast-output.md:276-290 gives target formats of 48 kHz/16-bit or 96 kHz/24-bit FLAC. README.md:286-292 (Sprint 5) lists Opus encoding and Web Audio playback as work to do and README.md:283 lists FLAC/AAC encoding. The code has neither encoder (R2-S07-04) and no browser playback code (R2-S08-04).
- Presence: CURRENT as document text only. chromecast_output.py has two commits (70fc363, eb02d59) and neither contains an encoder.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. No document says why the encoders were left out.
- Depends on: FFmpeg being present, per the design text (architecture-and-build-plan.md:856-860); FFmpeg is not installed by install.sh (grep, no hit).
- Open questions: NONE
- Confidence: MEDIUM. STATED design text; absence of code INFERRED from searches.

### R2-S08-07  Web output with no client, a failing client or a slow client
- Question: S08.3
- What was attempted: with no client connected `broadcast_audio` returns at once and the block is discarded (api_server.py:626-627). If a send raises, a warning is logged and the loop moves to the following client; the failing socket is not removed there (api_server.py:634-638). Removal happens in the `finally` block of the WebSocket handler (api_server.py:413-419). Each send is awaited in sequence from inside the pipeline loop (audio_pipeline.py:183, :323; api_server.py:634-636). INFERRED: a client whose send blocks delays the following loop iteration, because the pipeline awaits the broadcast before it sleeps and captures again. An eleventh connection is closed with code 1008 (api_server.py:34, :361-367). There is no per-client queue and no drop policy.
- Presence: CURRENT. Added in add5474; limits from b61e1ec.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. patch1.md:541 lists "Load test WebSocket connections and rate limiting" under "Regression Testing Required".
- Why it turned out that way: UNKNOWN. No result is evidenced.
- Depends on: aiohttp send behaviour; the single event loop.
- Open questions: What does the pipeline do when a browser tab is suspended or its network is slow?
- Confidence: MEDIUM. INFERRED from clear code; no result evidence.

### R2-S08-08  Chromecast output when the library or device is unavailable, when nothing pulls the stream, or when the pull stops
- Question: S08.3
- What was attempted: if `chromecast.enabled` is false or pychromecast cannot be imported, `start()` returns without starting the HTTP server (chromecast_output.py:86-92), `is_running()` stays False and the status endpoint reports "stopped" (api_server.py:247-250). The Chromecast destination can still be selected (api_server.py:487-488) and `add_audio_data` appends whatever the server or device state (chromecast_output.py:156-167). With no puller the buffer grows to 1 MB and then keeps the newest 1 MB (chromecast_output.py:166-167). With a puller and no data the handler sleeps 50 ms (chromecast_output.py:148-149). When the puller disconnects the exception is caught and logged at debug level as "Stream connection closed" (chromecast_output.py:151-152). A failed connect returns False and the API answers `{"success": false}` (chromecast_output.py:320-326; api_server.py:549-550). There is no reconnection and no health monitoring, although the module docstring lists "Connection health monitoring and auto-reconnect" (chromecast_output.py:8); `chromecast.reconnect_attempts` (config.toml:23) has no reader (grep). `_send_to_chromecast` awaits the return value of `add_audio_data` (audio_pipeline.py:339), which is a plain `def` that returns None (chromecast_output.py:156; wired at main.py:197). INFERRED: awaiting None raises TypeError after the bytes have been appended, and the handler at audio_pipeline.py:340-341 then logs "Error sending to chromecast output" for every block, because it catches Exception and the loop carries on.
- Presence: CURRENT. Added in eb02d59; callback wiring in add5474.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. No result is evidenced.
- Depends on: the callback being declared sync while the caller awaits it; the 1000-entry log buffer (logger.py:39).
- Open questions: Does the log buffer fill with that error while the Chromecast destination is active? What does a Cast device do when the stream stalls?
- Confidence: MEDIUM. INFERRED from clear code; no result evidence.

### R2-S08-09  Changing the destination or the source while a stream is active
- Question: S08.3
- What was attempted: `change_destination` and `change_source` assign the field and do nothing else (audio_pipeline.py:417-457). Their docstrings describe later work: "Future implementation will" stop the current output, reconfigure, start the new output and "Maintain continuous input capture" (audio_pipeline.py:445-449). `stop_stream` carries the comment "# STUB: Future implementation will stop audio graph here" (audio_pipeline.py:407). On a switch away from Chromecast the stream buffer is not cleared and `stop_playback` is not called; its only caller is `disconnect` (chromecast_output.py:344). The following loop iteration routes to the new destination (audio_pipeline.py:304-309).
- Presence: CURRENT. Added in eb02d59 on the stub's method names from 70fc363.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. No result is evidenced.
- Depends on: the single event loop; no lock around the two fields.
- Open questions: What does a connected Cast device play after the destination is switched to web?
- Confidence: MEDIUM. INFERRED from clear code; the in-code comments are STATED.

### R2-S08-10  Reconnection and fallback behaviour planned in the documents (document only)
- Question: S08.3
- What was attempted: architecture-and-build-plan.md:878-915 describes a status check every 30 seconds, backoff of 2, 4, 8, 16 and 32 seconds, a limit of 5 retries and a message telling the user to switch to web output. research-chromecast-output.md:343-349 states "Library does not auto-reconnect after network interruption" and lists the steps an application has to take. architecture-and-build-plan.md:951-960 is a table of known Chromecast issues with mitigations. logging-observability-report.md:448-470 gives the log lines expected for "Scenario 3: Chromecast Device Becomes Unreachable". The settings `chromecast.reconnect_attempts` and `bluetooth.reconnect_attempts` exist (config.toml:8, :23) and neither has a reader outside config_manager.py.
- Presence: CURRENT as document text and as unused configuration keys. No implementing code in any commit of chromecast_output.py.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: these being plans.
- Open questions: NONE
- Confidence: MEDIUM. STATED design text; absence of code INFERRED from searches.

### R2-S08-11  One destination at a time, by design
- Question: S08.4
- What was attempted: there is one `_active_destination` field and the router is an if/elif on it (audio_pipeline.py:61, :304-309). No code path sends a block to both destinations. project-context.md:89 reads "Only **one output may be active at a time**, selectable via the Web UI", and the architecture diagram is labelled "AUDIO OUTPUT DESTINATIONS (Mutually Exclusive)" (architecture-and-build-plan.md:272-274).
- Presence: CURRENT. Enum from 70fc363; router from eb02d59.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN for any result. The exclusivity is a STATED requirement (project-context.md:87-92).
- Depends on: the requirement in project-context.md:87-92.
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from clear code; requirement STATED.

### R2-S08-12  Several receivers within one destination
- Question: S08.4
- What was attempted: on the web destination every connected WebSocket client, up to 10, is sent each block (api_server.py:34, :629-636). On the Chromecast destination there is one shared buffer and each HTTP handler takes the whole buffer and empties it (chromecast_output.py:142-144). INFERRED: two clients pulling at once would each receive different parts of the audio, because the read is destructive and is not coordinated between handlers. One Cast object is held at a time; a second `connect` overwrites the reference without disconnecting the first (chromecast_output.py:306-312).
- Presence: CURRENT. Added in eb02d59 and add5474.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. No result is evidenced.
- Depends on: the client limit constant (api_server.py:34); a single stream buffer.
- Open questions: Was more than one browser client or more than one stream puller ever connected?
- Confidence: MEDIUM. INFERRED from clear code; no result evidence.

---

## S09 User interface and control surfaces

### R2-S09-01  Inventory of control and observation surfaces
- Question: S09.1
- What was attempted: the surfaces present are these. (1) A web page: one Svelte component (App.svelte), served at `/` (api_server.py:159-166, :207). (2) A REST API of 14 routes under `/api/v1` (api_server.py:179-200). (3) A WebSocket endpoint `/ws` (api_server.py:203-204, :349-424). (4) A command line: `python -m audioburger.main` or the `audioburger` console script, with `--config` and `--log-level` (main.py:417-432; setup.py:39-43). (5) POSIX signals: SIGINT, SIGTERM and SIGABRT for shutdown, SIGHUP for reload (main.py:100-109). (6) A TOML configuration file (config.toml; R2-S10-08). (7) Logs: JSON lines on stdout (logger.py:135) and an in-memory buffer read through `GET /api/v1/logs` (api_server.py:265-276). (8) systemd: two unit definitions (R2-S10-03, R2-S10-04) and sd_notify status text (main.py:359-361, :382-383). (9) The installer's prompts and flags (install.sh:132-187, :1221-1247). (10) A Vite development server on port 5173 that proxies `/api` and `/ws` to port 8080 (vite.config.js:19-31; README.md:333-339). Not present: physical buttons, lights, a display or a mobile app. Grep of `audioburger/` for gpio, led, lcd, oled, button, display finds the page's Refresh button (App.svelte:49), one CSS rule and two comments.
- Presence: CURRENT.
- Outcome: UNVERIFIED
- Outcome evidence: NONE for the set as a whole. Outcomes for single surfaces are in R2-S09-04 to R2-S09-08.
- Why it turned out that way: UNKNOWN.
- Depends on: aiohttp; systemd for surface (8); Node.js for surface (10).
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from clear code and searches.

### R2-S09-02  Serving the built web page: build tool, server and installer name different directories
- Question: S09.1
- What was attempted: the Vite build writes to `../static`, that is `audioburger/web_ui/static` (vite.config.js:8). `.gitignore:43` ignores that directory and no build output is tracked. The server looks for `audioburger/web_ui/frontend/static/index.html`, then `audioburger/web_ui/frontend/dist/index.html`, and otherwise answers 503 with the text "Frontend not built. Please run ..." (api_server.py:159-166). The `/assets` route is registered from the same two directories (api_server.py:210-217). The installer checks `audioburger/web_ui/static` (install.sh:807, :828), copies it to `/var/www/audioburger` (install.sh:846-851) and writes that path into `web_ui.static_path` (install.sh:910). `web_ui.static_path` has no reader under `audioburger/` outside config_manager.py; the hits in api_server.py are a local variable of the same name. setup.py uses `find_packages()` with no package data, and no MANIFEST.in is tracked (setup.py:37; `git ls-files`). The listing in PROJECT-COMPLETION-ROADMAP.md:169-175 uses `Path(__file__).parent / "static"`; the committed code has `"frontend"` in the path. INFERRED: with the tracked files the build output directory is not one of the two directories the server checks, so `/` answers 503 unless files are placed by hand, because vite.config.js:8 and api_server.py:161-163 name different paths.
- Presence: CURRENT. Index and assets routes added in eb02d59.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Commit eb02d59 message lists "Add static file serving for Svelte frontend" and "Build frontend with npm (Svelte app now compiled and ready)"; no served page is recorded. For the earlier server, README.md:224 states "Accessing `http://localhost:8080` currently returns a 404."
- Why it turned out that way: UNKNOWN. No result is evidenced.
- Depends on: the three path literals; where the package is installed (system site-packages through `pip install .`, install.sh:753).
- Open questions: Was the page ever seen served by this server? Where was the build output of eb02d59 placed?
- Confidence: MEDIUM. INFERRED from clear code; no result evidence.

### R2-S09-03  Full Web UI planned in the documents (document only)
- Question: S09.1
- What was attempted: architecture-and-build-plan.md:1000-1038 lays out a single page with an EQ canvas, input and output panels, Start, Pause and Stop buttons, a status bar with latency and buffer state, and a footer for logs and diagnostics. project-context.md:109-126 lists the UI requirements. README.md:303-310 (Sprint 7) and AI-AGENT-IMPLEMENTATION-GUIDE.md:1413-1419 (Task 7) list the work. App.svelte has one commit, 70fc363 (`git log -- audioburger/web_ui/frontend/src/App.svelte`); its header reads "Main Application Component (STUB)" (App.svelte:3) and its footer says the full features "will be implemented in later build phases" (App.svelte:87-89). The messages of eb02d59 and add5474 name Phases 1 to 5, 7 and 9 and do not name a frontend phase.
- Presence: CURRENT as document text only.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. No document says why the frontend task was left out.
- Depends on: these being plans.
- Open questions: NONE
- Confidence: MEDIUM. STATED design text; absence of code INFERRED from file history.

### R2-S09-04  Web page: a status table and a refresh button
- Question: S09.2
- What was attempted: on mount and every 5 seconds the page requests `/api/v1/status` (App.svelte:15-37). It shows, per component, the name, the status string and a tick or cross for running (App.svelte:59-76), the version (App.svelte:79), a Refresh button (App.svelte:49), a loading line (App.svelte:52-53) and an alert with the fetch error text (App.svelte:54-57). It offers no control action. The extra pipeline fields in the status JSON (api_server.py:256-259) are not shown.
- Presence: CURRENT. Unchanged since 70fc363.
- Outcome: UNVERIFIED
- Outcome evidence: NONE for a displayed page. README.md:224 states for the earlier server: "The component is written and will work — but it **cannot be served**." For the current server see R2-S09-02.
- Why it turned out that way: UNKNOWN.
- Depends on: svelte ^4.2.0, vite ^5.0.0, @picocss/pico ^2.0.0 (audioburger/web_ui/frontend/package.json:11-18); the serving path in R2-S09-02 or the Vite development server.
- Open questions: Was the page ever viewed in a browser, through either route?
- Confidence: MEDIUM. INFERRED from clear code; no result evidence.

### R2-S09-05  REST API: the three original endpoints (status, logs, log level)
- Question: S09.2
- What was attempted: `GET /api/v1/status` returns "ok", the version and, per component, a status string and a running flag; for the pipeline it adds running, active_source, active_destination, sample_rate, target_sample_rate, buffer_size and sounddevice_available (api_server.py:222-263; audio_pipeline.py:469-484). `GET /api/v1/logs` returns the log buffer (api_server.py:265-276). `POST /api/v1/log-level` with `{"level": ...}` sets the global level after size, JSON, type and whitelist checks (api_server.py:278-347).
- Presence: CURRENT. Added in 70fc363; validation added in b61e1ec; live component flags added in eb02d59.
- Outcome: WORKED
- Outcome evidence: STATED. Commit 70fc363 message, TESTING list: "API endpoints respond correctly" and "Log level changes at runtime via API". README.md:211-215 lists the three under "Functional". PROJECT-COMPLETION-ROADMAP.md:62-65 lists them under "Working endpoints".
- Why it turned out that way: UNKNOWN. No cause is discussed.
- Depends on: aiohttp. The statements predate eb02d59, which changed the status handler from a fixed dictionary (README.md:221) to live flags.
- Open questions: Was the status handler run after the change in eb02d59?
- Confidence: MEDIUM. STATED evidence about earlier versions of the handlers; the current code is consistent with it.

### R2-S09-06  REST API: the eleven endpoints added for inputs, outputs, Bluetooth, Chromecast and stream control
- Question: S09.2
- What was attempted: `GET /inputs` and `GET /outputs` return fixed lists (api_server.py:426-440). `PUT /input` takes `input_id` "bluetooth" or "line_in"; `PUT /output` takes `output_id` "web" or "chromecast" (api_server.py:442-496). `GET /bluetooth/devices` returns paired devices with address, name, connected and path (api_server.py:498-504; a2dp_sink.py:244-278). `POST /bluetooth/pair` turns on discoverable and pairable mode and answers `{"success": True, "timeout": 120}` (api_server.py:506-511). `PUT /bluetooth/connect` takes `device_address` (api_server.py:513-527). `GET /chromecast/devices` and `POST /chromecast/connect` with `device_uuid` (api_server.py:529-551). `POST /stream/start` and `POST /stream/stop` (api_server.py:553-589). All are under `/api/v1` (api_server.py:184-200). Not offered: Bluetooth disconnect or unpair, Chromecast disconnect, starting playback on the Cast device, reading or writing configuration, volume, equaliser gains. The design table lists `/config` GET and PUT (architecture-and-build-plan.md:1172-1173), and the guide lists disconnect, discover and stream status routes (AI-AGENT-IMPLEMENTATION-GUIDE.md:1345-1356); none of those is registered. `ChromecastOutput.get_status` (chromecast_output.py:435-449), which holds the active device name and stream URL, has no caller.
- Presence: CURRENT. Added in add5474.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Commit add5474 message says "All endpoints properly handle errors and return JSON responses"; no request or response is recorded. AI-AGENT-IMPLEMENTATION-GUIDE.md:1495-1507 lists curl checks; no output is tracked.
- Why it turned out that way: UNKNOWN. No result is evidenced.
- Depends on: aiohttp; the component objects passed at construction (main.py:187-192).
- Open questions: Were these endpoints ever called against a running instance?
- Confidence: MEDIUM. INFERRED from clear code; no result evidence.

### R2-S09-07  WebSocket endpoint: messages offered and limits
- Question: S09.2
- What was attempted: server to client, the socket carries binary audio frames (R2-S08-04) and is meant to carry `eq_data` JSON (R2-S07-02). Client to server, text messages are counted for rate limiting and logged at debug level and are not parsed; the comment reads "Future: validate and parse as JSON command" (api_server.py:402-405). Limits: 10 clients, 64 KB per incoming message, 100 messages per second per connection, with close code 1008 on a breach (api_server.py:33-36, :360-400). The designed message types `status_update`, `error`, `subscribe` and `ping` (architecture-and-build-plan.md:1185-1233) do not appear in code (grep of Python files under `audioburger/` and of the frontend source: zero hits for `status_update` and `subscribe`; `ping` occurs only inside the words "typing" and "Stopping").
- Presence: CURRENT. Limits from b61e1ec; audio broadcast from add5474.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. patch1.md:140-164 gives test scripts with expected results only, and patch1.md:541 lists the WebSocket load test under "Regression Testing Required".
- Why it turned out that way: UNKNOWN. No result is evidenced.
- Depends on: `web_ui.websocket_enabled` (config.toml:29).
- Open questions: Was the socket ever opened by a client after b61e1ec?
- Confidence: MEDIUM. INFERRED from clear code; no result evidence.

### R2-S09-08  Command-line flags and shutdown signals
- Question: S09.2
- What was attempted: `--config PATH` and `--log-level` with six level names (main.py:417-432). SIGINT and SIGTERM set a shutdown event behind a lock so that a second signal is ignored (main.py:100-104, :115-135); SIGABRT takes the same path and logs "Killed by systemd watchdog timeout!" (main.py:130-131). Components are then stopped in reverse order (main.py:246-278, :385-390). SIGHUP reload is recorded in R2-S10-09.
- Presence: CURRENT. From 70fc363; lock and SIGABRT added in b61e1ec.
- Outcome: WORKED
- Outcome evidence: STATED. Commit 70fc363 message, TESTING list: "Graceful shutdown on SIGTERM/SIGINT works properly". README.md:205-209 lists signal handling and CLI parsing under "Functional". PROJECT-COMPLETION-ROADMAP.md:53-59 lists the lifecycle under "Fully Implemented".
- Why it turned out that way: UNKNOWN. No cause is discussed.
- Depends on: the statements were made while the three audio components were stubs; patch1.md:542 lists "Verify graceful shutdown under all signal types" under "Regression Testing Required".
- Open questions: Was shutdown run after eb02d59, with a D-Bus connection, a second HTTP server and a processing task to stop?
- Confidence: MEDIUM. STATED evidence about the skeleton state; the current code is consistent with it.

### R2-S09-09  WebSocket echo reply (first version)
- Question: S09.2
- What was attempted: in the first version the handler answered each text message with "Echo: " plus the message (`git show 70fc363:audioburger/web_ui/api_server.py`, lines 234-240).
- Presence: REMOVED in commit b61e1ec. patch1.md:126: "Removed echo functionality (security risk for XSS)".
- Outcome: WORKED
- Outcome evidence: STATED. README.md:216 lists "`GET /ws` — WebSocket endpoint (echo stub only)" under the heading "Functional" (README.md:189). That README text was committed in deb899b, after the echo had been removed.
- Why it turned out that way: STATED for the removal: patch1.md:126 gives the security reason quoted above.
- Depends on: the skeleton state of the server.
- Open questions: NONE
- Confidence: MEDIUM. STATED evidence only.

### R2-S09-10  First-time setup by the README route
- Question: S09.3
- What was attempted: the steps given are: clone (README.md:27-31); create a virtual environment and run `pip install -e .` (README.md:33-41); build the frontend, marked optional (README.md:43-51); run `python -m audioburger.main`, with `--config` or `--log-level` if wanted, or run `audioburger` (README.md:55-73); check with curl against status, logs and log-level (README.md:75-94); open `http://localhost:8080` (README.md:96-101). The README names Python 3.11+, Node.js 18+ and Linux as prerequisites (README.md:19-23). It does not cover Bluetooth pairing, the system packages for Bluetooth or audio, or choosing an input and output. The code has no first-run step and stores no first-run state.
- Presence: CURRENT (document text, from 70fc363 and deb899b).
- Outcome: PARTIAL
- Outcome evidence: STATED. Commit 70fc363 message: "Application starts and runs successfully" and "All code compiles and runs. System can start, run, and shut down cleanly." The condition is the tree at 70fc363, whose requirements.txt had two active lines, aiohttp and tomli (`git show 70fc363:requirements.txt`, lines 5 and 8). No start of the tree after eb02d59 is recorded; eb02d59 made numpy, scipy, sounddevice, dbus-python, pychromecast and sdnotify active requirements (requirements.txt:11-22).
- Why it turned out that way: UNKNOWN for the current tree.
- Depends on: the tree at 70fc363 for the stated result; for the current tree, the build needs of dbus-python and sounddevice, which no document lists.
- Open questions: Does `pip install -e .` succeed and does the application start on the current tree, and on which system?
- Confidence: MEDIUM. STATED evidence alone, about an earlier tree.

### R2-S09-11  First-time setup by the installer route
- Question: S09.3
- What was attempted: `sudo ./install.sh` runs these user-facing steps in order (INSTALLER.md:40-57; install.sh:1217-1333): a prompt to install build tools if gcc or make is missing (install.sh:1299-1303); prompts to install Python or Node.js if the version found is too old (install.sh:696, :788); a prompt before overwriting an existing configuration, default no (install.sh:873-879); port prompts only when 8080 or 8081 is in use (install.sh:496-526, :899-903); "Create systemd service", default yes (install.sh:930); dedicated user, default yes (install.sh:937); enable at boot, default yes (install.sh:1002); start now, default no (install.sh:1008); then a summary with the Web UI and API URLs (install.sh:1173-1215). `--auto` answers every prompt with its default (install.sh:136-144, :174-178). After installation the document says to open `http://localhost:8080` (INSTALLER.md:241-249).
- Presence: CURRENT. From 70b5a5a.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. See R2-S10-01.
- Why it turned out that way: UNKNOWN.
- Depends on: see R2-S10-01.
- Open questions: See R2-S10-01.
- Confidence: MEDIUM. INFERRED from clear code; no result evidence.

### R2-S09-12  First use after start: pairing a source and starting a stream, as the code implements it
- Question: S09.3
- What was attempted: nothing happens on its own after start. Source and destination begin as NONE (audio_pipeline.py:60-61) and the loop body is skipped (audio_pipeline.py:163). The steps exist only as API calls, since the page has no controls (R2-S09-04): `POST /api/v1/bluetooth/pair` makes the adapter discoverable and pairable for 120 seconds (api_server.py:506-511; a2dp_sink.py:173-191); pairing itself is left to BlueZ, as grep of `audioburger/` for `RegisterAgent`, `Agent1`, `.Pair(`, `RegisterProfile`, `RegisterEndpoint`, `MediaTransport` returns nothing; then `GET /bluetooth/devices`, `PUT /bluetooth/connect`, `PUT /input`, `PUT /output` and `POST /stream/start`; for Chromecast, `GET /chromecast/devices` and `POST /chromecast/connect` (api_server.py:184-200). No document gives this sequence for the current code. The nearest material is the design table (architecture-and-build-plan.md:1160-1175) and the endpoint list in commit add5474 message.
- Presence: CURRENT. Endpoints from add5474.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. No result is evidenced.
- Depends on: an HTTP client such as curl; BlueZ handling pairing outside this code.
- Open questions: Has this sequence ever been run against hardware?
- Confidence: MEDIUM. INFERRED from clear code and searches; no result evidence.

### R2-S09-13  Errors returned in API and WebSocket responses
- Question: S09.4
- What was attempted: the log-level handler answers `{"status": "error", "message": ...}` with status 400, 413, 415 or 500 (api_server.py:286-347). The handlers added later answer `{"success": False, "error": ...}` with 400, 500 or 503 (api_server.py:447, :452, :462, :468, :511, :522, :527, :542-551, :556, :576). The root path answers 503 with plain text when no built frontend is found (api_server.py:166). The WebSocket is closed with code 1008 and a reason on too many connections or on the rate limit (api_server.py:364, :397). INFERRED: `POST /bluetooth/pair` answers success even when the adapter was not set up, because `enable_discoverable` only logs a warning and returns in that case (a2dp_sink.py:180-182) and the handler does not look at a result (api_server.py:508-510). `GET /inputs` and `GET /outputs` report `"available": True` without a check (api_server.py:428-439).
- Presence: CURRENT. Log-level responses from b61e1ec; the others from add5474.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. patch1.md:63-83 gives curl commands with "# Expected:" lines and no recorded output.
- Why it turned out that way: UNKNOWN. No result is evidenced.
- Depends on: two response shapes in one API.
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from clear code; no result evidence.

### R2-S09-14  Logs as the channel for errors and state changes
- Question: S09.4
- What was attempted: each component logs its start, stop, state changes and errors as JSON on stdout (for example main.py:215-244; audio_pipeline.py:367-381, :430-455; chromecast_output.py:314-321). Error entries carry the exception type, message and traceback (logger.py:127-132). The last 1000 entries can be read through `GET /api/v1/logs` (logger.py:39, :76-84; api_server.py:265-276). Nothing pushes a state change to a client. The page polls status every 5 seconds and shows only its own fetch errors (App.svelte:24-26, :35, :54-57). At start a systemd status line "All components running" is sent if sdnotify is present (main.py:361).
- Presence: CURRENT. Logger from 70fc363.
- Outcome: WORKED
- Outcome evidence: STATED. Commit 70fc363 message, TESTING list: "All components initialize and log lifecycle events". README.md:191-196 and README.md:214 list the logger and the logs endpoint under "Functional".
- Why it turned out that way: UNKNOWN. No cause is discussed.
- Depends on: a reader of stdout or of the journal; logger.py unchanged since 70fc363.
- Open questions: NONE
- Confidence: MEDIUM. STATED evidence; the log calls in the rewritten components were added after the statements.

### R2-S09-15  Error and status messaging planned in the documents (document only)
- Question: S09.4
- What was attempted: WebSocket `status_update` and `error` messages with a component and an error code (architecture-and-build-plan.md:1197-1214); a status bar (architecture-and-build-plan.md:1029); an error code taxonomy (logging-observability-report.md:741-786); six failure scenarios with the log lines expected for each (logging-observability-report.md:405-539); a "User Feedback" mitigation, "Web UI displays connection status, retry attempts, and error messages" (architecture-and-build-plan.md:1604). fixes.md:1302 lists "L-5: No User-Friendly Error Messages" as an open low-priority item. No code implements these.
- Presence: CURRENT as document text only.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: these being plans.
- Open questions: NONE
- Confidence: MEDIUM. STATED design text; absence of code INFERRED from searches.

---

## S10 Lifecycle and operations

### R2-S10-01  Automated installer script install.sh
- Question: S10.1
- What was attempted: the script requires root (install.sh:193-197); detects architecture, distribution, package manager and init system (install.sh:222-280, :1263-1276); installs curl and git if missing (install.sh:1284-1296) and build tools on a prompt (install.sh:1299-1303, :442-461); checks for Python 3.11 and offers to install it (install.sh:680-728); runs `pip install -r requirements.txt` with the system interpreter (install.sh:740); checks for Node.js 18 (install.sh:774-801); runs `npm install` and `npm run build` as the invoking user (install.sh:817, :823); runs `pip install .` (install.sh:753); copies config.toml to `/etc/audioburger/config.toml` with mode 644 (install.sh:860-893); copies the built assets to `/var/www/audioburger` (install.sh:837-854); checks ports and updates the config (install.sh:895-914); writes a systemd unit (install.sh:920-1020); verifies (install.sh:1026-1099); prints a summary (install.sh:1173-1215). `--uninstall` removes these (install.sh:1105-1154). It logs to `/var/log/audioburger/install.log` (install.sh:36, :60-69). All steps are automated; the yes/no prompts are manual unless `--auto` is given. The script installs no Bluetooth or audio system package: a case-insensitive search of install.sh for bluez, bluetooth, pipewire, wireplumber, pulse, alsa, portaudio, ffmpeg, flac, avahi, dbus returns no line. No pairing agent, BlueZ configuration, D-Bus policy or udev file is tracked or installed (`git ls-files`). Config update: `update_config_value` runs inline Python that imports `tomli` and `tomli_w` (install.sh:629-663) and on failure falls back to `sed`, matching the key at the start of a line with no regard to section (install.sh:665-669). `tomli_w` is not in requirements.txt and is not installed by the script (grep: install.sh:633 and :657 only). config.toml has `http_port` in two sections (config.toml:21, :27). INSTALLER.md describes two behaviours with no matching line in the script: "Restarts service if running" (INSTALLER.md:298; the word restart occurs in install.sh only at :976-977, inside the unit text) and "installer will add `deadsnakes` PPA if needed" (INSTALLER.md:666; no such string in install.sh). INSTALLER.md:100-103 still describes the requirements as aiohttp, tomli and "Future dependencies (commented out until needed)".
- Presence: CURRENT. Added in 70b5a5a and not changed since.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No install log or run output is tracked. INSTALLER.md:18-23 has a column headed "Tested Versions" with no result and no date. Commit 70b5a5a message describes features and does not describe a run. The script predates the requirement changes of eb02d59.
- Why it turned out that way: UNKNOWN. No result is evidenced.
- Depends on: root; one of apt, dnf, yum, pacman, zypper; network access to the package manager, pip and npm registries and to the NodeSource script (install.sh:421); systemd for the service step; `tomli_w` being present for the path that does not fall back to `sed`.
- Open questions: Was the script run after eb02d59 added dbus-python, sounddevice, pychromecast, numpy and scipy to requirements.txt? With the `sed` fallback, do both `http_port` lines receive the same value, and what does `sed` do with the `/` characters in the `static_path` value (install.sh:668, :910)? In `--auto` mode the info line of `prompt_input` is written to stdout inside a command substitution (install.sh:174-177, :508): what value reaches `chosen_port` when a port is in use?
- Confidence: MEDIUM. INFERRED from clear code; no result evidence.

### R2-S10-02  Manual installation routes in the README and in the installer guide's development mode
- Question: S10.1
- What was attempted: README.md:25-49 gives clone, virtual environment, `pip install -e .` and an optional frontend build. INSTALLER.md:686-705 gives the same as "Development Mode" and adds `npm run dev`. setup.py:1-7 names `pip install -e .` and `pip install .`. All steps are manual. Stated prerequisites are Python 3.11+, Node.js 18+ and Linux (README.md:19-23). No document lists BlueZ, PipeWire, PortAudio or the D-Bus development headers as prerequisites.
- Presence: CURRENT (document text).
- Outcome: PARTIAL
- Outcome evidence: STATED. Commit 70fc363 message: "All code compiles and runs. System can start, run, and shut down cleanly." The condition is the tree at 70fc363 with two active requirements (see R2-S09-10). No installation of the current tree is recorded.
- Why it turned out that way: UNKNOWN for the current tree.
- Depends on: the tree at 70fc363 for the stated result; README.md:23 "tested on Ubuntu 22.04+, Raspberry Pi OS", which names no test and no date.
- Open questions: Does `pip install -e .` succeed on the current requirements.txt on a clean system?
- Confidence: MEDIUM. STATED evidence alone, about an earlier tree.

### R2-S10-03  systemd unit file tracked in the repo (Type=notify, watchdog)
- Question: S10.2
- What was attempted: audioburger.service:1-32. `After=network.target bluetooth.target` and `Wants=bluetooth.target` (:3-4); `Type=notify` (:7); `ExecStart=/usr/bin/python3 -m audioburger.main --config /etc/audioburger/config.toml` (:8); `WorkingDirectory=/opt/audioburger` (:9); `User=audioburger`, `Group=audio` (:10-11); `WatchdogSec=60s` (:14); `Restart=on-failure`, `RestartSec=10s` (:17-18); `WantedBy=multi-user.target` (:32). install.sh does not install this file: it writes its own unit (install.sh:965-993) and has no reference to the repo file. patch1.md:405-407 gives a manual `sudo cp audioburger.service /etc/systemd/system/`; that text dates from b61e1ec, when the tree had no such file (the file was added in add5474). No tracked step creates `/opt/audioburger` (grep for `/opt/`: this line and three example lines in INSTALLER.md:575-577). sdnotify is an optional import (main.py:29-33).
- Presence: CURRENT. Added in add5474.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. patch1.md:540 lists "Test systemd integration in actual systemd environment" under "Regression Testing Required". PROJECT-COMPLETION-ROADMAP.md:1045 has the unchecked item "Application starts via systemd service".
- Why it turned out that way: UNKNOWN. No result is evidenced.
- Depends on: the `audioburger` user and the directory `/opt/audioburger` existing; sdnotify being installed, since the unit type is notify; the package being importable by `/usr/bin/python3`.
- Open questions: Does the unit reach the started state when sdnotify is absent? Was this file ever installed on a system?
- Confidence: MEDIUM. INFERRED from clear configuration; no result evidence.

### R2-S10-04  systemd unit written by the installer (Type=simple, Restart=always)
- Question: S10.2
- What was attempted: install.sh:965-993 writes `/etc/systemd/system/audioburger.service` with `After=network.target sound.target` (:968), `Type=simple` (:972), `User` and `Group` set to `audioburger` or root (:973-974), `ExecStart=/usr/local/bin/audioburger --config /etc/audioburger/config.toml` (:975), `Restart=always` and `RestartSec=5` (:976-977) and `WantedBy=multi-user.target` (:992). Enabling at boot is a prompt with default yes (install.sh:1002-1005); starting now is a prompt with default no (install.sh:1008). It has no watchdog line and no ordering after `bluetooth.target`. It differs from the tracked unit (R2-S10-03) in type, restart policy, ordering, executable path and group.
- Presence: CURRENT. From 70b5a5a.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. No result is evidenced.
- Depends on: pip placing the console script at `/usr/local/bin/audioburger`; the script checks only that `audioburger` is on PATH (install.sh:763).
- Open questions: Which of the two unit definitions was meant to be deployed? No document says.
- Confidence: MEDIUM. INFERRED from clear code; no result evidence.

### R2-S10-05  In-process start order, stop order and behaviour on a start failure
- Question: S10.2
- What was attempted: components start in the order pipeline, Bluetooth, Chromecast, API server (main.py:206-244) and stop in reverse (main.py:246-278). An exception during start propagates; `run()` then stops the components and returns exit code 1 (main.py:242-244, :399-405). A missing Bluetooth adapter does not raise: the error is logged and `start()` returns (a2dp_sink.py:100-103). Components that are disabled or whose library is missing return early (a2dp_sink.py:76-82; chromecast_output.py:86-92). The pipeline loop catches exceptions, logs them, sleeps 0.1 s and continues (audio_pipeline.py:189-191). There is no in-process restart of a failed component; fixes.md:570 lists that as H-2 and patch1.md:558 defers it.
- Presence: CURRENT. From 70fc363.
- Outcome: WORKED
- Outcome evidence: STATED. Commit 70fc363 message, TESTING list: "Application starts and runs successfully". PROJECT-COMPLETION-ROADMAP.md:23 marks "Application Lifecycle" complete and :53-59 lists it under "Fully Implemented".
- Why it turned out that way: UNKNOWN. No cause is discussed.
- Depends on: the statements were made while the three audio components were stubs. Restart after a crash is left to systemd (R2-S10-03, R2-S10-04).
- Open questions: Was the start sequence run after eb02d59 on a machine with and without a Bluetooth adapter?
- Confidence: MEDIUM. STATED evidence about the skeleton state; main.py changed by 16 lines in eb02d59 and add5474.

### R2-S10-06  systemd watchdog heartbeat and health check
- Question: S10.2
- What was attempted: if sdnotify can be imported the application sends `READY=1` and a status line after start (main.py:359-361), then `WATCHDOG=1` every 10 seconds while `_health_check` passes (main.py:280-306), and `STOPPING=1` at shutdown (main.py:382-383). The health check tests two flags, the API server's and the pipeline's `is_running()` (main.py:308-331). The heartbeat docstring assumes "WatchdogSec=30s" (main.py:284-285); the tracked unit sets 60s (audioburger.service:14); the installer unit sets none (install.sh:965-993).
- Presence: CURRENT. Added in b61e1ec.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. patch1.md:400-423 gives test commands with expected results only, and patch1.md:540 lists the systemd test under "Regression Testing Required". PROJECT-COMPLETION-ROADMAP.md:57-58 lists the watchdog under "Fully Implemented", which names no run.
- Why it turned out that way: UNKNOWN. No result is evidenced.
- Depends on: sdnotify >=0.3.2 (requirements.txt:22); a unit of type notify with a watchdog interval.
- Open questions: Has the watchdog ever fired or been observed under systemd? Do the two flags change when the pipeline task or a server stops unexpectedly?
- Confidence: MEDIUM. INFERRED from clear code; conflicting STATED material without a result.

### R2-S10-07  Permissions and privileges used or assumed
- Question: S10.3
- What was attempted: the installer requires root (install.sh:193-197). The tracked unit runs as `User=audioburger`, `Group=audio` (audioburger.service:10-11). The installer creates a system user `audioburger` with no home and a nologin shell, adds it to the `audio` group if that group exists (install.sh:540-570, :937-954), and uses root if the prompt is declined or user creation fails (install.sh:936, :947-949). Ports below 1024 are rejected by config validation (config_manager.py:243-244, :253-254, :339-341, :353-355) and the capability line for privileged ports is commented out (install.sh:988-989). Bluetooth control uses the D-Bus system bus and sets adapter properties (a2dp_sink.py:92, :106, :109, :185-187). No D-Bus policy file, polkit rule or `bluetooth` group membership is tracked or installed. Line-in capture uses the default input device through sounddevice (audio_pipeline.py:210). `/etc/audioburger` is root-owned with mode 755 and the config file has mode 644 (install.sh:870, :890); the log and state directories are owned by the service user (install.sh:957-960). Hardening lines: `NoNewPrivileges`, `ProtectSystem=strict`, `ProtectHome` in both units and `PrivateTmp` in the installer unit (audioburger.service:21-24; install.sh:982-986).
- Presence: CURRENT.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. No result is evidenced.
- Depends on: the host's D-Bus policy for `org.bluez`; the host's sound device permissions; the `audio` group existing.
- Open questions: Can the `audioburger` user set BlueZ adapter properties over the system bus with no added policy? Can it open a sound input device as a system service with no user session?
- Confidence: MEDIUM. INFERRED from clear code and configuration; no result evidence.

### R2-S10-08  TOML configuration file: locations, settings, defaults and validation
- Question: S10.4
- What was attempted: search order is the `--config` path, `/etc/audioburger/config.toml`, `~/.config/audioburger/config.toml`, `./config.toml` (config_manager.py:88-92, :106-124; README.md:112-116). A `--config` path that does not exist raises (config_manager.py:117); with no file found the defaults apply (config_manager.py:272-274). Settings and defaults (config_manager.py:23-69; config.toml:4-35): `[bluetooth]` enabled true, adapter "hci0", discoverable_timeout 120, reconnect_attempts 5, reconnect_backoff_base 2.0; `[audio]` sample_rate 48000, buffer_size 1024, channels 2, eq_bands 10, eq_update_rate 30; `[chromecast]` enabled true, discovery_timeout 5, http_port 8081, format "flac", reconnect_attempts 5; `[web_ui]` enabled true, http_port 8080, bind_address "0.0.0.0", websocket_enabled true, static_path ("/var/www/audioburger" in the dataclass at config_manager.py:60, "./audioburger/web_ui/static" in config.toml:30); `[logging]` level "INFO", buffer_size 1000, systemd_integration true. Types and ranges are checked while each section is parsed (config_manager.py:286-375). `--log-level` overrides the file (main.py:55-59). No API endpoint reads or writes configuration. Some settings have no effect (R2-S10-10, R2-S10-11).
- Presence: CURRENT. From 70fc363; validation added in b61e1ec.
- Outcome: WORKED
- Outcome evidence: STATED. Commit 70fc363 message, TESTING list: "Configuration loaded from TOML file". README.md:198-203 lists configuration management under "Functional". PROJECT-COMPLETION-ROADMAP.md:45-51 lists it under "Fully Implemented", including "Comprehensive validation (ranges, types, patterns)".
- Why it turned out that way: UNKNOWN. No cause is discussed.
- Depends on: Python 3.11 `tomllib`, with `tomli` as the fallback import (config_manager.py:13-16); config_manager.py unchanged since b61e1ec, which precedes the roadmap text.
- Open questions: patch1.md:248-269 gives invalid-config tests with expected results only: were they run?
- Confidence: HIGH. STATED evidence that the code agrees with.

### R2-S10-09  Configuration reload on SIGHUP
- Question: S10.4
- What was attempted: SIGHUP calls `reload()`, which copies the current config, loads the file again, validates it and restores the copy on any error (config_manager.py:377-409). The handler then sets the log level and, if the web port changed, stops and starts the API server (main.py:137-163). Components are built with the section objects that exist at start (main.py:175-188), and `_load_config` creates new section objects on each load (config_manager.py:288, :311, :334, :351, :369). INFERRED: after a reload the components still hold the section objects they were given, so the reload changes the log level (main.py:153) and the API server restart at main.py:156-159 runs with the object stored at api_server.py:51, because nothing reassigns it.
- Presence: CURRENT. From 70fc363; rollback and port check added in b61e1ec.
- Outcome: UNVERIFIED
- Outcome evidence: NONE for a tested result. STATED material points both ways: README.md:202 lists "SIGHUP-triggered reload" under "Functional" and PROJECT-COMPLETION-ROADMAP.md:50 lists "SIGHUP reload with rollback on error" under "Fully Implemented"; patch1.md:539 lists "Test configuration reload with various config changes" under "Regression Testing Required" and fixes.md:1540 has the unchecked item "H-5: Config reload tested with invalid configs". The 70fc363 TESTING list does not name reload.
- Why it turned out that way: UNKNOWN. No result is evidenced.
- Depends on: components holding references to section objects.
- Open questions: After a reload with a changed web port, on which port does the API server listen?
- Confidence: LOW. Conflicting STATED material and an INFERRED reading of object references.

### R2-S10-10  Three settings stated by the author to be defined and unused
- Question: S10.4
- What was attempted: `web_ui.static_path`, `bluetooth.reconnect_attempts` and `bluetooth.reconnect_backoff_base` are defined, validated and present in config.toml (config.toml:8-9, :30; config_manager.py:29-30, :60) and no code reads them. Grep of `audioburger/` on the current tree finds no reader outside config_manager.py. The installer writes a value into `static_path` (install.sh:910).
- Presence: CURRENT. Fields from 70fc363.
- Outcome: FAILED
- Outcome evidence: STATED. README.md:253: "The `static_path` field in `WebUIConfig` is defined but never read at runtime." README.md:273: "Use `reconnect_attempts` and `reconnect_backoff_base` from config (defined but unused)." Both lines predate eb02d59; the current code agrees with them.
- Why it turned out that way: UNKNOWN. No reason is given for the fields staying unread after eb02d59.
- Depends on: the reconnection and static-path features they were meant for (R2-S08-10, R2-S09-02).
- Open questions: NONE
- Confidence: HIGH. STATED evidence that the current code agrees with.

### R2-S10-11  Seven further settings with no reader found
- Question: S10.4
- What was attempted: by grep of `audioburger/`, these settings are parsed and validated and are not read by any other module: `bluetooth.discoverable_timeout` (the pairing handler calls `enable_discoverable()` with its default of 120, api_server.py:509; a2dp_sink.py:173); `audio.eq_update_rate`; `audio.eq_bands` (logged at audio_pipeline.py:92 and not otherwise used); `chromecast.discovery_timeout`; `chromecast.reconnect_attempts`; `logging.buffer_size` (the buffer length is the literal 1000 at logger.py:39); `logging.systemd_integration`.
- Presence: CURRENT. Fields from 70fc363.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No document names these seven as unused.
- Why it turned out that way: UNKNOWN.
- Depends on: the completeness of two greps over Python files under `audioburger/`, with config_manager.py excluded, for the terms eq_update_rate, static_path, reconnect_attempts, discovery_timeout, systemd_integration, eq_bands, discoverable_timeout, reconnect_backoff_base and `.buffer_size`.
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from a search of clear code.

### R2-S10-12  Structured JSON logging to stdout with an in-memory buffer and a runtime level
- Question: S10.5
- What was attempted: each log call prints one JSON object to stdout with timestamp, level, component, session_id, message and optional data and error (logger.py:113-135) and appends it to a class-level deque of 1000 entries (logger.py:39, :137-139). There are six levels, TRACE to CRITICAL (logger.py:19-26), and one global level (logger.py:38, :53-68). The level can be set by `--log-level` (main.py:55-57), by `logging.level` in the config (main.py:59), by `POST /api/v1/log-level` (api_server.py:278-347) and by SIGHUP reload (main.py:153). Diagnostics available: `GET /api/v1/logs` and `GET /api/v1/status`; the journal, as both units send stdout to it (audioburger.service:27-29; install.sh:978-979); the installer log (install.sh:36); `jq` examples (README.md:384-397). Not present: per-component levels; a session id other than the constant "default" (logger.py:51); request ids, as no caller passes `request_id` (grep: zero hits; fixes.md:1207 states the same as M-7); a metrics endpoint (fixes.md:1102, M-2); file logging by the application (no file writes under `audioburger/`).
- Presence: CURRENT. logger.py unchanged since 70fc363.
- Outcome: WORKED
- Outcome evidence: STATED. Commit 70fc363 message, TESTING list: "Log level changes at runtime via API" and "All components initialize and log lifecycle events". README.md:191-196 lists the logging framework under "Functional". PROJECT-COMPLETION-ROADMAP.md:38-43 lists it under "Fully Implemented".
- Why it turned out that way: UNKNOWN. No cause is discussed.
- Depends on: stdout being captured by the journal or a terminal.
- Open questions: NONE
- Confidence: HIGH. STATED evidence that the unchanged code agrees with.

### R2-S10-13  Observability design in the documents that the code does not carry out (document only)
- Question: S10.5
- What was attempted: logging-observability-report.md describes per-component levels in `/etc/audioburger/logging.conf` (:61-82), a session-based correlation strategy (:335-404), operational diagnostics including a Web UI diagnostics view (:540-740), latency and buffer-health entries for the audio components (:143, :170) and an error code taxonomy (:741-786). architecture-and-build-plan.md:380 names a `BUFFER_HEALTH(underruns, latency)` status message. None of these is in the code (R2-S10-12 lists what is absent).
- Presence: CURRENT as document text only.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: these being plans.
- Open questions: NONE
- Confidence: MEDIUM. STATED design text; absence of code INFERRED from searches.

### R2-S10-14  Packaging as a setuptools package installed with pip; update by re-running the installer
- Question: S10.6
- What was attempted: setup.py:29-53 defines the package `audioburger` with the console script `audioburger=audioburger.main:main` and `python_requires='>=3.11'`. `install_requires` is every non-comment line of requirements.txt, which includes the three pytest packages (setup.py:13-19; requirements.txt:25-27). `find_packages()` is used with no package data (setup.py:37). All Python requirements are lower bounds (requirements.txt:5-27). Version strings differ: `version='0.1.0'` (setup.py:31), `__version__ = "2.0.0"` (audioburger/__init__.py:5), `"version": "0.1.0"` (audioburger/web_ui/frontend/package.json:3). No wheel, distribution package, container file or CI workflow is tracked and there is no tag (`git ls-files`; `git tag` prints nothing). Update approach as documented: `git pull origin main` and run the installer again (INSTALLER.md:289-298); removal by `--uninstall` (INSTALLER.md:321-338). patch1.md:587-592 has "Upgrade Notes" for the 2.0.0 change. fixes.md:1348 lists "L-8: No Automated Dependency Updates".
- Presence: CURRENT. setup.py from 70fc363.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The installer checks the import and the console script after `pip install .` (install.sh:757-765); no run is recorded.
- Why it turned out that way: UNKNOWN. No result is evidenced.
- Depends on: pip and setuptools; requirements.txt being present beside setup.py.
- Open questions: Which version string is the authoritative one? patch1.md:448 calls the `__init__.py` value the "Single source of truth for version string" while setup.py:31 holds a different literal.
- Confidence: MEDIUM. INFERRED from clear code; no result evidence.

---

## S11 Testing and measured behaviour

### R2-S11-01  Test package is an empty placeholder; no automated tests
- Question: S11.1
- What was attempted: `audioburger/tests/__init__.py` is the only file under `audioburger/tests/` and it is 0 bytes (`git ls-files audioburger/tests`; `wc -c`). No `test_*.py`, `conftest.py` or CI workflow is tracked. pytest, pytest-asyncio and pytest-aiohttp are active requirements (requirements.txt:25-27, made active in eb02d59). Planned test files are listed in PROJECT-COMPLETION-ROADMAP.md:926-996, AI-AGENT-IMPLEMENTATION-GUIDE.md:1423-1434 and architecture-and-build-plan.md:2246-2311. The messages of eb02d59 and add5474 name no test phase.
- Presence: CURRENT. Empty since 70fc363.
- Outcome: FAILED
- Outcome evidence: STATED. README.md:239: "`audioburger/tests/` contains only `__init__.py`. No unit, integration, or E2E tests exist." PROJECT-COMPLETION-ROADMAP.md:138 and fixes.md:1431 say the same. The current tree agrees.
- Why it turned out that way: UNKNOWN. Tests were planned for a later phase (README.md:312-318) and no reason is given for their absence after eb02d59.
- Depends on: the phased plan.
- Open questions: NONE
- Confidence: HIGH. STATED evidence that the current tree agrees with.

### R2-S11-02  Manual test results stated in the skeleton commit message
- Question: S11.1
- What was attempted: commit 70fc363 message has a "TESTING:" list of six items: the application starts and runs; all components initialise and log lifecycle events; API endpoints respond; shutdown on SIGTERM and SIGINT; configuration loaded from TOML; log level changed at runtime through the API. It covers the lifecycle, the configuration, the logger and the three endpoints that existed at that commit. No audio path and no hardware is in scope, since the three audio components were stubs. No output is attached.
- Presence: CURRENT (commit message in the history of `main`).
- Outcome: WORKED
- Outcome evidence: STATED. Commit 70fc363 message: "Application starts and runs successfully"; "All code compiles and runs. System can start, run, and shut down cleanly."
- Why it turned out that way: UNKNOWN. No cause is discussed.
- Depends on: the tree at 70fc363; main.py, api_server.py and the three component modules changed in later commits.
- Open questions: On what system was this run? Was any run made after eb02d59?
- Confidence: MEDIUM. STATED evidence alone.

### R2-S11-03  Test procedures written with expected results and no recorded results
- Question: S11.1
- What was attempted: patch1.md has "Testing" blocks for C-2 (:63-83), C-3 (:140-164), C-4 (:248-269), C-5 (:312-322) and H-8 (:400-423); each gives commands and "# Expected:" comments. patch1.md:537-548 lists "Regression Testing Required" and "Security Testing Required". fixes.md has test commands in Appendix A (:1449-1509) and unchecked checklists in Appendices B and C (:1513-1548). PROJECT-COMPLETION-ROADMAP.md:1031-1045 is an unchecked verification checklist. AI-AGENT-IMPLEMENTATION-GUIDE.md has a verification loop (:1438-1536) and an unchecked completion checklist (:1540-1570). The installer's `verify_installation` checks the import, the console script, the config file, the assets, the service state and a curl of the status endpoint (install.sh:1026-1099). No output of any of these is tracked: `git ls-files` shows no log, report or result file, and `.gitignore:46` ignores `*.log`. The guide's first step requires that searches for "STUB", "TODO" and "Future implementation" in the Python files return nothing (AI-AGENT-IMPLEMENTATION-GUIDE.md:1442-1454); the current tree contains those strings (audio_pipeline.py:229, :361, :407; api_server.py:10).
- Presence: CURRENT (document text and installer code).
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: a running instance for every procedure; systemd for the H-8 procedure.
- Open questions: Was any of these procedures run? Was the guide's verification loop run before eb02d59 and add5474 were committed?
- Confidence: MEDIUM. INFERRED from the documents and a file listing.

### R2-S11-04  NOT FOUND
- Question: S11.2
- Searched: `git grep -n -i -E 'iphone|android|pixel|galaxy|ipad|macbook|samsung|turntable|audio-technica|sony|bose|windows phone'` over all tracked files except the lockfile; `git grep -n -i -E 'tested (on|with)|was tested|test(s)? pass|verified (on|with)|manually tested|soak'`; read in full README.md, patch1.md, INSTALLER.md, project-context.md; read fixes.md:1-90 and :1420-1587, PROJECT-COMPLETION-ROADMAP.md:1-250 and :920-1064; read every commit message (`git log`). The hits name device types in plans, mock-ups and sample log lines only. No source device is named as tested.
- Nearest related material: README.md:23 ("tested on Ubuntu 22.04+, Raspberry Pi OS", which names operating systems and no source device); architecture-and-build-plan.md:1605-1606 (hardware testing and a list of tested adapters and source devices, written as planned mitigations); architecture-and-build-plan.md:1935 ("Pair with real Bluetooth device (phone, turntable)", a planned test); bluetooth-a2dp-sink-research.md:370 (a call to test with Android, iOS and dedicated transmitters); an invented device name in sample messages (architecture-and-build-plan.md:1203; logging-observability-report.md:44).

### R2-S11-05  NOT FOUND
- Question: S11.3
- Searched: `git ls-files` for log, CSV, result, benchmark or measurement files (none among the 42 tracked files); `git grep -n -i -E 'latency|drift|clock|synchron|lip.?sync|in sync|out of sync'` over tracked files except the lockfile; `git grep -n -i -E 'measured|benchmark|cpu usage|%.?cpu|memory usage|uptime|dropout|drop-out|\bMB\b'` over the same files; `git grep -n -i -E 'latency|drift|clock|underrun|overrun|xrun' -- audioburger` (one docstring line). Every figure found is an estimate, a budget, a target or a number quoted from another project. No measurement of this project is recorded.
- Nearest related material: research-chromecast-output.md:296-303 (table headed "Measured Latency Characteristics", rows for other implementations); architecture-and-build-plan.md:707-717 (processor and memory budget), :2377-2381 (targets, including "Buffer underruns: <1 per hour"); architecture-and-build-plan.md:1692 ("Measure actual CPU usage in Phase 4", a plan); INSTALLER.md:659 ("Installation time: ~10-15 minutes" on Raspberry Pi, with no source); fixes.md:1439-1445 (D-8, "Add Performance Benchmarks", deferred).

### R2-S11-06  Security and reliability issue list (fixes.md) and its partial remediation (patch1.md)
- Question: S11.4
- What was attempted: fixes.md, dated 2026-01-21 and written against the skeleton code (fixes.md:3-5), lists six critical items C-1 to C-6 (:31-517), eight high items H-1 to H-8 (:520-1087), eight medium items M-1 to M-8 (:1090-1254), eight low items L-1 to L-8 (:1257-1357) and eight deferred items D-1 to D-8 (:1360-1447). Its summary gives a different count: "12 critical security vulnerabilities" (fixes.md:16). patch1.md records code changes for C-2, C-3, C-4, C-5, H-8 and L-1, with C-6 and H-5 in part (patch1.md:21-527), and lists as not included C-1, H-1, H-2, H-3, H-4, H-6, H-7, M-1 to M-8 and L-2 to L-8 (patch1.md:552-566). The endpoints added later in add5474 do not have the kind of validation that C-2 added: they use a bare `except:` around JSON parsing (api_server.py:446, :474, :517, :541). fixes.md:1587 notes that later phases "will require additional security review".
- Presence: CURRENT. Documents from d51fcef and b61e1ec; code changes from b61e1ec still present (api_server.py:32-36, :286-326, :360-400; config_manager.py:126-262; main.py:124-135, :280-331).
- Outcome: PARTIAL
- Outcome evidence: STATED. patch1.md:14 gives the count of fixes implemented as "7 issues (1 Low Priority, 4 Critical, 1 High Priority, 1 Critical related)". patch1.md:554: "The following issues from fixes.md are **NOT** included in this patch". Open items are those in patch1.md:556-564. The code agrees for the implemented items.
- Why it turned out that way: STATED. Each deferred item carries a reason in patch1.md:556-562, for example C-1 "requires design decision on auth method".
- Depends on: the assessment having been made on the skeleton code; code added in eb02d59 and add5474 has no assessment.
- Open questions: Were the implemented fixes exercised? patch1.md:537-548 lists the tests as required.
- Confidence: HIGH. STATED evidence that the code agrees with.

### R2-S11-07  Status lists in the README and the roadmap that name the audio, Bluetooth and Chromecast modules as stubs
- Question: S11.4
- What was attempted: README.md:7 is headed "Current Status: Phase 4, Step 1 - Foundation & Skeleton (NOT a working application)" and README.md:187-240 lists what is functional, what is inaccurate, what is stub only and what is not started. PROJECT-COMPLETION-ROADMAP.md:17-30 is a status table and :78-138 is headed "What's NOT Working (Stubs)". Both were last changed before eb02d59 and add5474, which rewrote the files they describe. No document was updated afterwards, so the README and roadmap say stub while the two later commit messages say functional.
- Presence: CURRENT (document text). The code state described was replaced in eb02d59 and add5474.
- Outcome: FAILED
- Outcome evidence: STATED. README.md:9: "All audio, Bluetooth, and Chromecast modules are stubs that log events but do nothing." The files at 70fc363 and b61e1ec agree. This outcome is for the state the documents describe.
- Why it turned out that way: STATED. The skeleton came first by plan (README.md:351: "This is a **foundation skeleton**.").
- Depends on: the tree as it stood at deb899b and 1491214.
- Open questions: Which of the listed gaps remain after eb02d59 and add5474? No document answers; R2-S11-08 lists the markers still in the code.
- Confidence: HIGH for the state described. STATED evidence that the code of that date agrees with.

### R2-S11-08  Placeholder markers that remain in the current code
- Question: S11.4
- What was attempted: the author's own in-code statements of unfinished parts are: "For now, this is a placeholder that returns silence." and "# TODO: Implement Bluetooth audio capture via PipeWire" (audio_pipeline.py:227, :229); "Future implementation will" lists in four docstrings (audio_pipeline.py:361-365, :392-395, :424-428, :445-449); "# STUB: Future implementation will stop audio graph here" (audio_pipeline.py:407); "CURRENT STATUS: STUB - Basic HTTP server with minimal endpoints." (api_server.py:10); future lists in two docstrings (api_server.py:172-176, :353-356); "# Future: validate and parse as JSON command" (api_server.py:404); "Main Application Component (STUB)" and the footer note (App.svelte:3, :87-89).
- Presence: CURRENT.
- Outcome: FAILED
- Outcome evidence: STATED, by the in-code comments quoted above, for the parts they name: Bluetooth capture, stream teardown, WebSocket command handling and the full UI. The marker at api_server.py:10 dates from 70fc363 and stands above code added since.
- Why it turned out that way: STATED as deferred work ("For now", "Future implementation"). No further cause is given.
- Depends on: the markers being current; some date from 70fc363.
- Open questions: Which markers describe the present state and which are left over from the skeleton?
- Confidence: MEDIUM. STATED in code; the age of some markers is ambiguous.

### R2-S11-09  Anticipated issues and risks recorded in the research and architecture documents
- Question: S11.4
- What was attempted: before any code was written the author recorded expected problems. architecture-and-build-plan.md:1555-1761 has seven risks with acceptance criteria: the Chromecast pull model, Bluetooth reconnection, browser streaming latency and compatibility, PipeWire maturity, FFT processor load on Raspberry Pi, service reliability, Chromecast firmware. architecture-and-build-plan.md:528-536 and :951-960 are tables of Bluetooth and Chromecast issues with mitigations. research-chromecast-output.md:446-548 lists ten known issues, among them buffer delays with PyChromecast (:448), router multicast filtering (:467), localhost URL rejection (:486), sample rates above 96 kHz (:496) and long-session disconnections (:528). bluetooth-a2dp-sink-research.md:272-343 lists BlueZ and WirePlumber bugs and :344-416 lists operational risks. research-web-ui-visualization.md:544-555 lists latency accumulation. All are drawn from outside sources; none is reported as met in this project.
- Presence: CURRENT as document text (2026-01-20 and 2026-01-21).
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: third-party sources listed in each report's references.
- Open questions: Did any of these issues occur in practice? No note records one.
- Confidence: MEDIUM. STATED document text.

---

## S12 Security and exposure

### R2-S12-01  API and WebSocket server bound to all interfaces by default, with no authentication
- Question: S12.1
- What was attempted: the main aiohttp server binds `web_ui.bind_address` and `web_ui.http_port` (api_server.py:102-107). The defaults are "0.0.0.0" and 8080 (config.toml:27-28; config_manager.py:57-58). `bind_address` is taken from the file with no check (config_manager.py:356). There is no authentication, token, session, TLS or CORS handling: grep of Python files under `audioburger/`, config.toml and audioburger.service for ssl, tls, cors, middleware, basic_auth, allowed_ips returns nothing, and grep of tracked files other than `.md` files and the lockfile for password, passwd, secret, token, api key, private key, credential and auth returns only the word "author" (setup.py:35). The 14 API routes, `/ws`, `/` and `/assets` are open to any host that can reach the port (api_server.py:179-217). Actions exposed include making the adapter discoverable and pairable, connecting devices, reading the log buffer and changing the log level. Limits present: request body 10 KB (api_server.py:33, :92); WebSocket 10 clients, 64 KB per message, 100 messages per second (api_server.py:34-36, :360-400). There is no HTTP rate limit.
- Presence: CURRENT. Bind from 70fc363; limits from b61e1ec; device and stream routes from add5474.
- Outcome: FAILED
- Outcome evidence: STATED, measured against the project's own requirement. fixes.md:35: "The Web UI binds to `0.0.0.0:8080` by default with zero authentication." fixes.md:47 sets this against project-context.md:166 ("No unauthenticated remote access unless explicitly enabled"). patch1.md:556 lists C-1 as not included. The current code agrees.
- Why it turned out that way: STATED. patch1.md:556: "requires design decision on auth method". The design documents had set no authentication: architecture-and-build-plan.md:1177 gives authentication as "None (single-user, local network only per project-context.md §7)" and project-context.md:243 ("Single user only, so does not require authentication").
- Depends on: the LAN being trusted; the operator changing `bind_address` (INSTALLER.md:616-624 gives "127.0.0.1" as a hardening option).
- Open questions: Which of the two document positions (project-context.md:166 or :243) was meant to govern?
- Confidence: HIGH. STATED evidence that the code agrees with.

### R2-S12-02  Chromecast stream server bound to all interfaces, with no authentication
- Question: S12.1
- What was attempted: the second aiohttp server binds the literal '0.0.0.0' at `chromecast.http_port`, default 8081 (chromecast_output.py:118; config.toml:21). The bind address cannot be set in config. It has one GET route, `/stream.<format>`, with no authentication (chromecast_output.py:113, :126-154). Any host that reaches the port can pull the audio, and a pull empties the shared buffer (chromecast_output.py:142-144). The server starts only when `chromecast.enabled` is true and pychromecast can be imported (chromecast_output.py:86-92). fixes.md does not cover this server; it was written against the skeleton (fixes.md:5). The design text reads "Bind: 0.0.0.0:8081 (accessible on LAN)" (architecture-and-build-plan.md:734).
- Presence: CURRENT. Added in eb02d59.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. No result is evidenced.
- Depends on: the Cast pull model, which needs a port the device can reach (architecture-and-build-plan.md:759-767).
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from clear code; no result evidence.

### R2-S12-03  Other network activity: Cast discovery and connection, local IP lookup, development server, installer downloads
- Question: S12.1
- What was attempted: pychromecast discovery and the Cast connection are outbound from the application (chromecast_output.py:224, :263, :299-304). `_get_local_ip` connects a UDP socket toward a public address literal on port 80 to read the local address (chromecast_output.py:412-420). It is called from `start_playback` and `get_stream_url` (chromecast_output.py:376, :432); `start_playback` has no caller and `get_stream_url` is called only from `get_status`, which has no caller, so INFERRED: no route reaches the lookup in the current tree. The Vite development server listens on 5173 and proxies to 8080 (vite.config.js:19-31); it is used in development only (README.md:333-339). The installer fetches a NodeSource setup script over HTTPS and pipes it to bash (install.sh:421) and downloads from the pip and npm registries (install.sh:725, :740, :817). The installer sets no firewall rule (grep of install.sh for ufw, firewall: no hit). Documents give iptables examples for ports 5353, 8081 and 8008-8009 (architecture-and-build-plan.md:919-929) and ufw and firewall-cmd examples for 8080 (INSTALLER.md:523-528).
- Presence: CURRENT.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. No result is evidenced.
- Depends on: multicast reaching the LAN; network access at install time.
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from clear code and searches.

### R2-S12-04  Authentication, TLS and network-restriction designs in the documents (document only)
- Question: S12.1
- What was attempted: project-context.md:153-167 sets the threat model and five requirements. architecture-and-build-plan.md:1414-1537 describes binding to a LAN address, a rate limit of 100 requests per minute, a `[security]` config section with `bind_interface = "lan"`, and optional HTTP Basic Auth. fixes.md C-1 proposes Basic Auth with a token file, an IP allowlist, or a default bind of 127.0.0.1 (fixes.md:55-71); H-7 proposes TLS with certificate and key files (fixes.md:936-1010); M-6 proposes CORS settings (fixes.md:1169-1199). INSTALLER.md:616-642 lists a localhost bind, a reverse proxy, TLS in the proxy and authentication as operator steps. No `[security]` or `[web_ui.security]` section exists in config.toml and no TLS or authentication code exists (R2-S12-01).
- Presence: CURRENT as document text only.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: STATED for the deferral. patch1.md:556 and :562 give the reasons "requires design decision on auth method" and "requires cert management design".
- Depends on: these being plans.
- Open questions: NONE
- Confidence: MEDIUM. STATED design text; absence of code INFERRED from searches.

### R2-S12-05  Privilege level of the service process
- Question: S12.2
- What was attempted: the tracked unit runs the process as `User=audioburger`, `Group=audio`, with `NoNewPrivileges=yes`, `ProtectSystem=strict`, `ProtectHome=yes` and two writable paths (audioburger.service:10-11, :20-24). The installer unit runs it as `audioburger` when the dedicated-user prompt is accepted (default yes) and as root when it is declined or user creation fails (install.sh:936-954, :973-974), with `NoNewPrivileges`, `PrivateTmp`, `ProtectSystem=strict` and `ProtectHome` (install.sh:982-986). A manual run has the privileges of the invoking user (README.md:55-73); a troubleshooting line shows `sudo audioburger --config ...` (INSTALLER.md:488). One process and one event loop host the API, the WebSocket, the stream server, the Bluetooth D-Bus calls and the audio loop (main.py:165-244). The separation of control plane and audio plane into processes described in architecture-and-build-plan.md:1503-1510 is not present.
- Presence: CURRENT.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. No result is evidenced.
- Depends on: which unit is deployed; the answers given to the installer prompts.
- Open questions: Under which user was the application ever run?
- Confidence: MEDIUM. INFERRED from clear configuration; no result evidence.

### R2-S12-06  Privilege level of the installer
- Question: S12.2
- What was attempted: the installer refuses to run unless the effective user is root (install.sh:193-197). As root it runs the package manager (install.sh:349-380), `pip install` into the system interpreter's site-packages (install.sh:725, :740, :752-753), a remote setup script piped to bash (install.sh:421), `useradd` (install.sh:551) and writes under `/etc`, `/var/www`, `/var/log` and `/var/lib` (install.sh:30-36). The npm steps run as the user who invoked sudo (install.sh:213-216, :817, :823). The inline Python in `update_config_value` is built by placing shell variable values inside Python source text (install.sh:639-657).
- Presence: CURRENT. From 70b5a5a.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. No result is evidenced.
- Depends on: sudo; network sources at install time.
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from clear code; no result evidence.

### R2-S12-07  NOT FOUND
- Question: S12.3
- Searched: `git grep -n -i -E 'password|passwd|secret|token|api[_-]?key|private[_-]?key|credential|auth'` over tracked files except the lockfile and `.md` files (one hit, the word "author" at setup.py:35); `git ls-files | grep -i -E 'pem|key|crt|cert|\.env|secret|token'` (no file); address pattern `([0-9A-Fa-f]{2}:){5}[0-9A-Fa-f]{2}` outside `.md` files (one hit, a placeholder example in a docstring at a2dp_sink.py:285; not copied here) and inside `.md` files (one distinct value, a placeholder used in sample log lines in architecture-and-build-plan.md and logging-observability-report.md; not copied here); config.toml:1-35 (no credential key); install.sh in full (it creates no key, token or password); `git grep -n -E 'open\(|\.write\('` under `audioburger/` (no file is written by the application). The project stores no secret and reads none.
- Nearest related material: fixes.md:58-64 (a proposed token file under the state directory and a `[web_ui.security]` section, not implemented); fixes.md:1527 (unchecked item "No secrets in config.toml (use separate auth file)"); fixes.md:965-1003 (proposed TLS certificate and key paths, not implemented); INSTALLER.md:131 (the state directory is described as "Application state (future use)"). Bluetooth link keys would be held by BlueZ on the host, outside the repository; no code here reads or writes them.

---

## Tally

| Stage | Questions | Answered with findings | Answered NOT FOUND |
|---|---|---|---|
| S07 | 3 | 3 | 0 |
| S08 | 4 | 4 | 0 |
| S09 | 4 | 4 | 0 |
| S10 | 6 | 6 | 0 |
| S11 | 4 | 2 | 2 (S11.2, S11.3) |
| S12 | 3 | 2 | 1 (S12.3) |
| Total | 24 | 21 | 3 |

Questions answered with findings: 21. Questions answered NOT FOUND: 3. Total: 24.

Records: 63 finding records and 3 NOT FOUND records (66 in all).
