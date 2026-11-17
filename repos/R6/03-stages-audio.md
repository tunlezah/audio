# R6 stage audit, audio stages S07 to S12

Repo: /home/user/SonicFlow. Audited read-only on 2026-11-17 at commit 3046b9e (branches `main` and `claude/inspiring-galileo-a9dlia` point at the same commit; no tags; no stashes; working tree clean before and after the audit).

Conventions used in this file:

- All paths are relative to /home/user/SonicFlow.
- "Commit HASH message" means the text of that commit's message, read with `git log`.
- No artefact in the repo holds test output, a log file, a recorded measurement or a CI result (`git ls-files | grep -iE 'log|result|report'` returns nothing; `.gitignore:4` ignores `*.log`). The only artefacts that show a result directly are one screenshot (docs/screenshot.png) and the tracked files themselves; OBSERVED is used only for those. All other results come from commit messages, documents and comments (STATED) or from reading code (INFERRED).
- SOUNDSYNC-SPEC.md describes a predecessor system held in a different repository (SOUNDSYNC-SPEC.md:1-8). Where it is cited, it is cited as documentation carried in this repo, not as a description of this repo's code.
- Rule 10: sonicflow-design-spec.md:4 and SOUNDSYNC-SPEC.md:46-48 contain text addressed to an AI agent. It was read as material and not acted on.
- Rule 11: no secret values, device addresses or e-mail addresses are copied into this file.

---

## S07 Processing

### R6-S07-01  A2DP decode left to WirePlumber and PipeWire, with no codec code in the repo
- Question: S07.1
- What was attempted: the application does no Bluetooth decoding. The Bluetooth module header states the crate "MUST NOT register `org.bluez.MediaEndpoint1` objects — WirePlumber owns codec negotiation and transport acquisition" (src/bluetooth/mod.rs:4-5). The installer writes a WirePlumber fragment listing `bluez5.codecs = [ sbc sbc_xq aac ldac aptx aptx_hd ]` (install.sh:199-205). Decoded audio appears in the graph as a `bluez_input.*` node (README.md:54-55; src/bluetooth/manager.rs:820-825).
- Presence: CURRENT. install.sh:185-212; src/bluetooth/mod.rs:1-8.
- Outcome: PARTIAL. Endpoints registered for sbc, sbc_xq, aptx, aptx_hd and ldac; the aac entry has no effect.
- Outcome evidence: STATED. forinstall.md:103-104 says `bluetoothctl show` "advertises Audio Sink 0000110b and endpoints for sbc/sbc_xq/aptx/aptx_hd/ldac". forinstall.md:107-108: "\"aac\" in the codecs list is inert; phones fall back to SBC-XQ/LDAC/aptX". Commit 0ad9f4e message: "iPhone now transitions to audio_active."
- Why it turned out that way: STATED. The AAC entry is inert because "Ubuntu ships no AAC codec .so" for the SPA bluez5 plugin directory, attributed to licensing (forinstall.md:107-108). Endpoint registration depended on disabling the WirePlumber seat-monitoring profile feature (forinstall.md:97-104).
- Depends on: WirePlumber 0.5.13 and PipeWire 1.6.2 on the development machine (forinstall.md:61-62); `libspa-0.2-bluetooth` (install.sh:99); the WirePlumber `MemoryDenyWriteExecute=no` override for ldac/aptx plugins (install.sh:220-227); Ubuntu 26.04 packaging (DECISIONS.md:4-5).
- Open questions: Which codec did the tested iPhone negotiate? The `codec` field of the stream-start event is always `None` (src/bluetooth/manager.rs:759-762). Were LDAC or aptX streams ever received?
- Confidence: MEDIUM. STATED evidence; the decode step itself runs outside the repo's code.

### R6-S07-02  Ten-band equaliser and preamp as a separate PipeWire filter-chain process
- Question: S07.1
- What was attempted: a generated SPA-JSON config defines a `linear` preamp stage (Mult = 10^(dB/20)) followed by a low-shelf, eight peaking and a high-shelf biquad at 31 Hz to 16 kHz, gains clamped to ±12 dB (src/dsp/equalizer.rs:7-13, 55-72, 78-153). The config is written to `$XDG_RUNTIME_DIR/sonicflow/filter-chain.conf` and a child process (`pipewire-filter-chain --config` or `pipewire -c`) is spawned; every change kills and respawns it (src/audio/filter_chain.rs:6-9, 35-70). After a 500 ms sleep the default sink is set to `effect_input.sonicflow-eq`; the chain's playback node targets `sonicflow-capture` (src/audio/pipeline.rs:66-81, 110-129; src/dsp/equalizer.rs:127-140). Ten built-in presets and file-based custom presets supply gain sets (src/dsp/presets.rs:11-43, 143-177).
- Presence: CURRENT. src/main.rs:67-73 calls `pipeline.initialize` with the persisted EQ state; default `enabled: true` (src/state/config.rs:137-146).
- Outcome: PARTIAL. Stated as verified for preset apply, band gains, preamp, respawn and persistence, and for a test tone reaching the spectrum through the EQ sink. An audible check with a Bluetooth source is stated as pending and no later record closes it.
- Outcome evidence: STATED. Commit 174ec2f message: "Preset apply, positional band gains, preamp gain stage (Mult math correct), kill-and-respawn filter-chain per change" and "Audible check pending a Bluetooth source." Commit b19435c message: "Verified live in headless Chromium: 440Hz tone through the EQ sink lights the correct bars end-to-end".
- Why it turned out that way: STATED for one defect met on the way: commit d8b17a3 message says the standalone `pipewire -c` instance "needs client modules (protocol-native etc.) or the EQ never registers"; the modules are listed at src/dsp/equalizer.rs:113-121. The respawn design is stated as forced: "there is no live coefficient update in the filter-chain module" (src/audio/filter_chain.rs:8-9).
- Depends on: a `pipewire` binary on PATH (Ubuntu ships no `pipewire-filter-chain` binary per src/audio/filter_chain.rs:46-48); the default sink staying on the EQ sink (README.md:252-253; deploy/doctor.sh:43-47); node names shared with the sweep script and diagnostics (src/dsp/equalizer.rs:75-77).
- Open questions: Is audio interrupted during the kill, respawn and 500 ms wait on each slider move (src/audio/pipeline.rs:110-116)? BUILD-PLAN.md:42 sets the check "slider audibly applies <1 s"; where is its result? Does the stream stay on the null sink or follow the default sink when the EQ process is absent?
- Confidence: MEDIUM. STATED evidence that the code agrees with, limited by the stated pending audible check.

### R6-S07-03  Fixed 48 kHz stereo requests at every capture point, with no resampling code in the repo
- Question: S07.1
- What was attempted: the in-process capture asks `parec` for `--format=float32 --channels=2 --rate=48000` (src/audio/capture.rs:10-11, 72-80; `pw-cat` equivalent at 86-104); the per-client HTTP capture asks for `--format=s16le --rate=48000 --channels=2` (src/audio/cast_stream.rs:53-60); the filter-chain sink declares `audio.rate = 48000`, 2 channels FL/FR (src/dsp/equalizer.rs:127-133). The repo holds no resampler: the only DSP crates are `opus` and `rustfft` (Cargo.toml:21, 28). Any conversion from the Bluetooth stream's rate or format is done by PipeWire or pipewire-pulse.
- Presence: CURRENT. Citations above.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No statement about sample-rate conversion or its quality appears in the repo.
- Depends on: PipeWire's own conversion between the A2DP stream format, the graph rate and the 48 kHz requests; the spectrum analyser constant `SAMPLE_RATE` "MUST match capture" (src/audio/spectrum.rs:8).
- Open questions: What sample rate did the tested source deliver, and was a conversion taking place? What is the PipeWire graph rate on the reference machine?
- Confidence: MEDIUM. INFERRED from clear code; the search for a resampler covered Cargo.toml and `grep -rniE 'resampl' src`.

### R6-S07-04  Line-in source summed into the same capture sink by a loopback module
- Question: S07.1
- What was attempted: `pactl load-module module-loopback source=<alsa_input.*> sink=sonicflow-capture latency_msec=50 source_dont_move=true sink_dont_move=true` (src/audio/line_in.rs:45-57). The first `alsa_input.*` source found is used (src/audio/line_in.rs:111-125). Because the target is the null sink, the line-in signal joins whatever the Bluetooth path delivers there; the loopback targets the null sink directly, after the EQ node (src/dsp/equalizer.rs:134-140 against src/audio/line_in.rs:50).
- Presence: CURRENT. Routes at src/web/mod.rs:72-77; toggle at webui/src/components/Settings.tsx:683-691.
- Outcome: PARTIAL. Module load and unload are stated as verified; no statement records audio passing through it.
- Outcome evidence: STATED. Commit 9d619ed message: "line-in loopback load/unload verified against iec958 source".
- Why it turned out that way: UNKNOWN for the audio result. STATED for the source: "Only capture source currently exposed is `alsa_input.pci-0000_00_1b.0.iec958-stereo` (optical)" (forinstall.md:134-135).
- Depends on: the Mac mini's optical input being the only exposed capture source (forinstall.md:133-135; src/audio/line_in.rs:8-10); `pactl`.
- Open questions: README.md:39-40 says line-in is looped "through the same EQ and outputs", and src/audio/line_in.rs:8-9 says it shares "EQ, spectrum, all outputs", while the loopback's sink argument is the null sink that sits after the EQ node. Does line-in audio pass through the EQ? What happens when Bluetooth and line-in play together?
- Confidence: MEDIUM. STATED evidence for load/unload; the EQ-bypass reading is INFERRED from the two cited node definitions.

### R6-S07-05  Opus encoding of 20 ms frames for the WebRTC output
- Question: S07.1
- What was attempted: one `opus::Encoder` per WebRTC session, 48 kHz stereo, `Application::Audio`, 128 kbps, encoding each 1920-sample interleaved f32 frame on a blocking thread (src/audio/opus_encoder.rs:1-27; src/audio/webrtc_audio.rs:212-250).
- Presence: CURRENT. src/audio/webrtc_audio.rs:185, 207-280.
- Outcome: WORKED
- Outcome evidence: STATED. Commit 2641e55 message: "Verified over LAN in headless Chromium and real Firefox 154: WebRTC connected, unmuted, signal present in the received track". Commit 9d619ed message: "Local Player WebRTC verified in headless Chromium (audio element readyState 4, playing; clean session stop)".
- Why it turned out that way: UNKNOWN beyond the design notes at src/audio/webrtc_audio.rs:202-206.
- Depends on: `opus` crate 0.3 with `libopus-dev` (Cargo.toml:21; install.sh:103); 7680-byte frames from capture (src/audio/capture.rs:11); headless Chromium and Firefox 154 as the receiving clients.
- Open questions: Was a result recorded for Safari or iOS (BUILD-PLAN.md:68 names them)? Was the "10 min without stutter" check of BUILD-PLAN.md:68 run?
- Confidence: HIGH. STATED evidence that the code agrees with.

### R6-S07-06  AAC encoding by an ffmpeg child process per HTTP client
- Question: S07.1
- What was attempted: `parec` (s16le, 48 kHz, stereo) piped by file descriptor into `ffmpeg -f s16le -ar 48000 -ac 2 -i pipe:0 -c:a aac -b:a 256k -f adts -flush_packets 1 pipe:1`; ffmpeg stdout becomes the HTTP response body (src/audio/cast_stream.rs:21-29, 49-127). Served at `/api/stream/audio.aac` (src/web/mod.rs:105). This stream is the Chromecast source and the browser's first HTTP fallback (src/audio/chromecast.rs:139-140; webui/src/webrtc.ts:195).
- Presence: CURRENT. src/web/routes.rs:590-592.
- Outcome: PARTIAL. Response headers are stated as verified; a behaviour at silence is documented; no statement records a client decoding the stream.
- Outcome evidence: STATED. Commit 3d4afd4 message: "AAC stream headers + icy-name verified". API.md:92-93: "the AAC encoder compresses digital silence to ~350 B/s, so a stream opened while nothing is playing trickles until audio starts".
- Why it turned out that way: UNKNOWN. Design reason for the pipe chain is STATED at src/audio/cast_stream.rs:12-15.
- Depends on: `ffmpeg` and `parec` on PATH (install.sh:99-100); ffmpeg's built-in `aac` encoder; one `parec` plus one `ffmpeg` process per connected client (src/audio/cast_stream.rs:12-13).
- Open questions: Did a Chromecast or a browser play this stream audibly? ffmpeg stderr goes to null and log level is quiet (src/audio/cast_stream.rs:62, 75-76, 93): how would an encoder failure after start be seen?
- Confidence: MEDIUM. STATED evidence covers headers and the silence behaviour only.

### R6-S07-07  MP3 encoding by ffmpeg as the second HTTP stream format
- Question: S07.1
- What was attempted: the same pipe chain with `-c:a libmp3lame -b:a 192k -f mp3` (src/audio/cast_stream.rs:31-39), served at `/api/stream/audio.mp3` (src/web/mod.rs:106). It is used when the AAC pipeline fails to spawn (src/audio/cast_stream.rs:24-27) and when a browser's audio element raises an error on the AAC URL (webui/src/webrtc.ts:196-202).
- Presence: CURRENT. src/web/routes.rs:594-596.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Commit 2641e55 message states a change ("fix the Firefox ADTS-AAC fallback to actually replay after swapping to MP3") and its verification line names WebRTC only.
- Why it turned out that way: UNKNOWN.
- Depends on: an ffmpeg build with `libmp3lame`. forinstall.md:33-35 records that `libmp3lame-dev` was dropped from the installer because "ffmpeg does MP3"; BUILD-PLAN.md:10-11 records "drop mp3lame-encoder" from the crate list.
- Open questions: Was the MP3 route ever played by a client?
- Confidence: MEDIUM. INFERRED from clear code; no result recorded.

### R6-S07-08  Host output volume set on the hardware sink with pactl
- Question: S07.1
- What was attempted: `pactl set-sink-volume <sink> <pct>%` on the active hardware sink, level clamped to 0.0 to 1.0, default 0.75, re-applied on activation (src/audio/host_out.rs:117-122, 141-161; src/state/mod.rs:201). Exposed as `POST /api/host/volume` (src/web/routes.rs:563-574) and as a slider in 5 % steps (webui/src/components/Casting.tsx:260-275).
- Presence: CURRENT. Citations above.
- Outcome: WORKED
- Outcome evidence: STATED. Commit 9d619ed message: "Phase 7: Host PC Audio verified — pw-link FL/FR graph links, sink RUNNING, volume 40% via API, deactivate removes all links".
- Why it turned out that way: UNKNOWN.
- Depends on: `pactl`; the volume is a property of the hardware sink, applied after the shared capture point, so it does not affect other outputs. `host_volume` is held in memory only and is not in `RuntimeState` (src/state/mod.rs:158 against src/state/config.rs:152-173).
- Open questions: BUILD-PLAN.md:72 lists "volume + mute"; no mute command exists in src/audio/host_out.rs:12-17. Was mute dropped deliberately?
- Confidence: HIGH. STATED evidence that the code agrees with.

### R6-S07-09  Volume commands for Chromecast, AirPlay and the browser player
- Question: S07.1
- What was attempted: Chromecast: `cast.receiver.set_volume(level)` inside the session thread (src/audio/chromecast.rs:68-72, 246-250). AirPlay: `pactl set-sink-volume` on the RAOP sink (src/audio/airplay.rs:268-276). Browser: `audio.volume` on the element (webui/src/webrtc.ts:207-209). REST routes exist for the first two (src/web/mod.rs:88, 97).
- Presence: DISABLED in the web UI: `castVolume` and `airplayVolume` are defined at webui/src/api.ts:133, 138 and `setVolume` at webui/src/webrtc.ts:207, and `grep -rnE 'castVolume|airplayVolume|setVolume' webui/src` finds no caller. The two REST routes are CURRENT and reachable by any HTTP client.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: an active Cast session or RAOP sink; without one the commands are dropped silently (src/audio/chromecast.rs:69; src/audio/airplay.rs:269).
- Open questions: BUILD-PLAN.md:83-84 lists Chromecast "volume readback (fix D20)"; src/audio/chromecast.rs holds a set call and no read of the receiver volume. Was readback implemented elsewhere? BUILD-PLAN.md:67 lists "client volume" for the Local Player; where is its control?
- Confidence: MEDIUM. INFERRED from clear code and a repo-wide search.

### R6-S07-10  Spectrum analysis side path: mono downmix, 2048-point FFT, 64 log bands
- Question: S07.1
- What was attempted: frames from the broadcast channel are downmixed to mono, Hann-windowed, transformed (FFT 2048, hop 1024), grouped into 64 log-spaced bands from 20 Hz to 20 kHz, scaled from -80 dB to 0..1 and smoothed with alpha 0.35, then published as `spectrum_data` at about 47 Hz (src/audio/spectrum.rs:7-14, 40-107; src/web/ws.rs:192). The data drives the visualiser only and is not an audio output.
- Presence: CURRENT. src/main.rs:66, 76.
- Outcome: WORKED
- Outcome evidence: STATED. Commit b19435c message: "Verified live in headless Chromium: 440Hz tone through the EQ sink lights the correct bars end-to-end".
- Why it turned out that way: UNKNOWN.
- Depends on: the in-process capture running; `rustfft` 6.2 (Cargo.toml:28); the publish rate was raised from the predecessor's value per DECISIONS.md:95-97.
- Open questions: NONE
- Confidence: HIGH. STATED evidence that the code agrees with.

### R6-S07-11  Capture buffer of 50 ms requested on both parec paths
- Question: S07.2
- What was attempted: `--latency-msec=50` on the in-process capture (src/audio/capture.rs:13-15, 79) and on each HTTP client's capture (src/audio/cast_stream.rs:17-19, 59). The stated reason is that the buffer "MUST exceed the PipeWire quantum (default 1024 ≈ 21.3 ms)" (src/audio/capture.rs:13-14). The line-in loopback uses `latency_msec=50` (src/audio/line_in.rs:51). The capture delivers 20 ms frames (src/audio/capture.rs:10-11).
- Presence: CURRENT. Citations above.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. BUILD-PLAN.md:22-23 and DECISIONS.md:99 describe the value as a correction of predecessor defect D2; neither records a measurement.
- Why it turned out that way: UNKNOWN.
- Depends on: the PipeWire quantum on the host (stated default 1024 at 48 kHz); `parec` from `pulseaudio-utils` (install.sh:99). The `pw-cat` fallback path passes no latency argument (src/audio/capture.rs:86-104).
- Open questions: What end-to-end delay results from this buffer plus the Bluetooth, EQ and output stages? Was the quantum on the reference machine checked?
- Confidence: MEDIUM. INFERRED from clear code; reasons are STATED, results are not.

### R6-S07-12  Separate OS pipe per HTTP client and low-buffer encoder flags
- Question: S07.2
- What was attempted: HTTP streams bypass the in-process broadcast channel: "Deliberately NOT the broadcast channel — the channel adds timing jitter that manifests as audible stuttering" (src/audio/cast_stream.rs:13-14). ffmpeg is started with `-fflags +nobuffer` and `-flush_packets 1` (src/audio/cast_stream.rs:77-78, 89), and its output is forwarded in reads of up to 8192 bytes (src/audio/cast_stream.rs:107-118).
- Presence: CURRENT. src/audio/cast_stream.rs:49-127.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. The stuttering claim is carried over from the predecessor's lessons table (SOUNDSYNC-SPEC.md:2697); no test in this repo compares the two approaches.
- Depends on: ffmpeg flag behaviour; one capture and one encoder process per listener.
- Open questions: How much delay does the receiving device add on top? Was stutter on the broadcast-channel route ever reproduced in this repo?
- Confidence: MEDIUM. INFERRED from clear code; STATED rationale only.

### R6-S07-13  Latency between outputs stated in documents and UI copy, with no compensation
- Question: S07.2
- What was attempted: no delay alignment exists between outputs. The author states the gap in three places: README.md:46-47 ("Multiple outputs play together but may be seconds apart (Chromecast alone buffers 1–3 s). The UI says so."), DECISIONS.md:79, and the "Multiple outputs" row description in the UI (webui/src/components/Settings.tsx:513-516). README.md:75-76 also says the subprocess design "trades a little latency". Buffer sizes present in code: broadcast channel capacity 256 frames, described as "5.12 s — sized for the slowest consumer" (src/audio/capture.rs:12).
- Presence: CURRENT. Citations above.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No latency figure in the repo is presented as measured. The 1–3 s Cast figure also appears in the predecessor document (SOUNDSYNC-SPEC.md:2950, 3017).
- Why it turned out that way: STATED as a scope decision: "Not a multi-room *sync* system" (README.md:46) and DECISIONS.md:75-80.
- Depends on: the receiving device's own buffering; the design decision D7.
- Open questions: What is the measured delay from phone to each output? sonicflow-design-spec.md:419 words the caveat as "a fraction of a second apart" while DECISIONS.md:79 says "several seconds apart": which was observed on the reference machine?
- Confidence: MEDIUM. STATED evidence alone.

### R6-S07-14  WebRTC pump paced only by the capture read, with RTP counters advanced over dropped frames
- Question: S07.3
- What was attempted: the capture task's `read_exact` on 20 ms frames is treated as the only clock: "read_exact blocking until a full 20 ms frame exists IS the system's pacer — do not add another clock downstream" (src/audio/capture.rs:126-127). The WebRTC pump sends each packet as soon as it is encoded, with no timer (src/audio/webrtc_audio.rs:202-206, 251-263). When the broadcast receiver reports `Lagged(n)`, the RTP timestamp is advanced by n×960 and the sequence number by n (src/audio/webrtc_audio.rs:227-233). The spectrum task clears its buffer on lag (src/audio/spectrum.rs:60-63).
- Presence: CURRENT. Citations above.
- Outcome: UNVERIFIED
- Outcome evidence: NONE for drift or stutter over time. The statements in commits 9d619ed and 2641e55 record that a WebRTC session connected and carried signal, not how it behaved over a period.
- Why it turned out that way: UNKNOWN. The three pump rules are reproduced from the predecessor document (SOUNDSYNC-SPEC.md:1551-1571, 2698).
- Depends on: the browser's jitter buffer absorbing the difference between the capture clock and its own playout clock; `parec` delivering at a steady rate.
- Open questions: Was the check "Chrome + Firefox + Safari/iOS play 10 min without stutter; no Lagged warnings" (BUILD-PLAN.md:68) run, and with what result? The pump logs sent, dropped and expected frame counts every 60 s (src/audio/webrtc_audio.rs:270-276): was any such log kept?
- Confidence: MEDIUM. INFERRED from clear code; no result recorded.

### R6-S07-15  No drift or synchronisation handling between the Bluetooth source clock and the output clocks in repo code
- Question: S07.3
- What was attempted: nothing in the repo measures or corrects clock drift. Host and AirPlay outputs are plain graph links made with `pw-link` (src/audio/host_out.rs:101-114; src/audio/airplay.rs:207-221), so any rate matching between the Bluetooth node, the null sink and the hardware or RAOP sink is whatever PipeWire does. Chromecast and HTTP listeners pull a continuous encoded stream and buffer on their side (src/audio/cast_stream.rs:100-127). No timestamps are sent to Cast or HTTP clients. Search: `grep -rniE 'drift|resync|clock|rate.?match' src webui/src` returns only the WebRTC `clock_rate` field and the pacer comments cited in R6-S07-14.
- Presence: CURRENT (absence of code, with the delegation visible at the cited link calls).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. The predecessor document lists "no clock synchronisation between outputs" and "Latency compensation between outputs" as gaps G3 and G6 (SOUNDSYNC-SPEC.md:2947, 2950); DECISIONS.md:75-80 keeps multi-output without sync.
- Depends on: PipeWire's handling of nodes driven by different clocks; receiver-side buffering on Cast and browser clients.
- Open questions: Does a long session show buffer growth, dropouts or pitch change on any output? Was any session longer than a few minutes recorded?
- Confidence: MEDIUM. INFERRED from clear code and a stated scope decision.

---

## S08 Output

### R6-S08-01  Host PC Audio: the machine's own sound device
- Question: S08.1
- What was attempted: `HostOutputManager` lists sinks with `pactl list short sinks`, excluding names containing `sonicflow`, `raop`, `bluez` or starting with `effect_` (src/audio/host_out.rs:164-200), resumes the chosen sink, and links the monitor ports of `sonicflow-capture` to the sink's playback ports with `pw-link` (src/audio/host_out.rs:68-127). Selection: the UI chip sends no sink name, so the first enumerated hardware sink is used (webui/src/components/Casting.tsx:229-240; src/audio/host_out.rs:79); `POST /api/host/activate` accepts a sink name (src/web/routes.rs:542-556). Activated at start-up when `default_output` is `host`, or `last` with host as the last output (src/main.rs:192-199).
- Presence: CURRENT. src/main.rs:86.
- Outcome: WORKED
- Outcome evidence: STATED. Commit 9d619ed message: "Phase 7: Host PC Audio verified — pw-link FL/FR graph links, sink RUNNING, volume 40% via API, deactivate removes all links".
- Why it turned out that way: UNKNOWN.
- Depends on: Intel HDA device with internal speaker on the Macmini5,3 (forinstall.md:133; DECISIONS.md:21-23); `pactl` and `pw-link`; port names ending FL/FR, with positional pairing otherwise (src/audio/airplay.rs:448-464).
- Open questions: BUILD-PLAN.md:74 sets the check "music out of the Mac mini's internal speaker, EQ audibly applied"; the commit records link and sink state, so was sound heard? No sink picker exists in the UI (`grep -rn host_sinks webui/src/components` shows use for the chip label and enabled state only): how is a second sound card chosen without the API?
- Confidence: HIGH. STATED evidence that the code agrees with.

### R6-S08-02  Local Player: a browser on the LAN over WebRTC
- Question: S08.1
- What was attempted: clicking the Local Player chip creates an audio element inside the click handler, opens an `RTCPeerConnection` with a receive-only audio transceiver and sends the offer over the status WebSocket (webui/src/webrtc.ts:64-90, 102-159; webui/src/components/Casting.tsx:161-175). The server answers, adds an Opus track and starts the pump (src/audio/webrtc_audio.rs:124-199). Any number of browsers can listen; each WebSocket connection is one session (src/web/ws.rs:17-20; DECISIONS.md:76).
- Presence: CURRENT. src/main.rs:87-99.
- Outcome: WORKED
- Outcome evidence: STATED. Commit 2641e55 message: "Verified over LAN in headless Chromium and real Firefox 154: WebRTC connected, unmuted, signal present in the received track".
- Why it turned out that way: UNKNOWN. One cause of an earlier client defect is STATED in code: "webrtc-rs add_track() associates no stream: streams[0] is undefined" (webui/src/webrtc.ts:123).
- Depends on: `webrtc` and `rtp` crates 0.12 (Cargo.toml:31-32); headless Chromium and Firefox 154 as tested clients; a user click to start (webui/src/webrtc.ts:64); page served over plain HTTP from the LAN address.
- Open questions: Was Safari or iOS tested? The client carries four rules written for Safari (README.md:94-98; webui/src/webrtc.ts:1-9) and no commit records a Safari result.
- Confidence: HIGH. STATED evidence that the code agrees with.

### R6-S08-03  Local Player fallback: the same browser over an HTTP audio stream
- Question: S08.1
- What was attempted: when WebRTC does not produce playing audio, the same audio element's `src` is set to `/api/stream/audio.aac`, then to `/api/stream/audio.mp3` on an element error (webui/src/webrtc.ts:191-205). Selection is automatic; the chip label shows "WebRTC" or "HTTP stream" (webui/src/components/Casting.tsx:181-189).
- Presence: CURRENT. Citations above.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Commit 2641e55 describes the fallback changes; its verification sentence covers the WebRTC route.
- Why it turned out that way: UNKNOWN.
- Depends on: browser support for raw ADTS AAC; a comment states "Firefox cannot decode raw ADTS AAC" (webui/src/webrtc.ts:197); ffmpeg on the server.
- Open questions: Has any browser played either HTTP stream through this fallback?
- Confidence: MEDIUM. INFERRED from clear code.

### R6-S08-04  Chromecast: one Cast device pulling the AAC stream
- Question: S08.1
- What was attempted: discovery with `avahi-browse -t -r -p -k _googlecast._tcp`, then the `mdns-sd` crate if avahi returns nothing (src/audio/chromecast.rs:295-317, 392-441). Connecting spawns an OS thread that opens a CASTv2 connection, launches the Default Media Receiver and loads `http://<ip>:<port>/api/stream/audio.aac` (src/audio/chromecast.rs:125-174, 195-240). Selection: dropdown chip listing discovered devices (webui/src/components/Casting.tsx:198-211) or `POST /api/cast/connect` (src/web/routes.rs:452-463). With `auto_reconnect` on, the last Cast device is rejoined at start-up after a discovery pass and a 14 s wait (src/main.rs:201-219).
- Presence: CURRENT. src/main.rs:84.
- Outcome: PARTIAL. Discovery is stated as verified. A session-thread panic was found and changed. No commit states that a Cast device played the stream; README.md:283-285 makes a general end-to-end claim.
- Outcome evidence: STATED. Commit 9d619ed message: "Cast/AirPlay discovery verified on the real LAN (22 + 19 devices" and "live sessions left for a supervised test". README.md:283-285: "verified end-to-end on real hardware: real phones, a house full of real Chromecasts, and one very patient 2011 Mac mini".
- Why it turned out that way: UNKNOWN for playback. The earlier failure and its cause are in R6-S08-05.
- Depends on: `rust_cast` 0.21 (Cargo.toml:24); avahi-daemon; the Cast device reaching the host's first `hostname -I` address on the HTTP port (src/audio/cast_stream.rs:148-169); the explicit rustls provider (src/main.rs:33-42).
- Open questions: Which Cast device models played audio, and for how long? On a host with several addresses, is the first `hostname -I` token the one the Cast device can reach?
- Confidence: MEDIUM. STATED evidence; the playback claim is general, not tied to a commit or device.

### R6-S08-05  Chromecast session thread before the rustls provider was pinned
- Question: S08.1
- What was attempted: the first version spawned `session_thread` directly, with no crypto provider installed and no panic guard (`git show 2641e55 -- src/audio/chromecast.rs src/main.rs`, removed lines).
- Presence: REMOVED. Commit 2641e55 (src/audio/chromecast.rs, src/main.rs).
- Outcome: FAILED
- Outcome evidence: STATED. Commit 2641e55 message: "the cast session thread PANICKED on TLS connect; pin ring at startup and wrap the session thread in catch_unwind". src/main.rs:35-36: "observed: cast-session thread died in rustls crypto/mod.rs".
- Why it turned out that way: STATED. "Both ring and aws-lc-rs end up in the dependency graph (webrtc pulls one, rust_cast's TLS the other), so rustls cannot auto-select a crypto provider" (src/main.rs:33-35).
- Depends on: the crate set in Cargo.toml:24, 31-34 (rust_cast 0.21, webrtc 0.12, rustls 0.23 with the `ring` feature).
- Open questions: After the change, was a full Cast session completed?
- Confidence: HIGH. STATED evidence that the code and the diff agree with.

### R6-S08-06  AirPlay: one receiver through PipeWire's RAOP sink
- Question: S08.1
- What was attempted: discovery with `avahi-browse ... _raop._tcp`, or a scrape of existing RAOP sinks (src/audio/airplay.rs:89-127, 303-372). Connecting loads `module-raop-discover`, finds the sink matching the device by address or name, resumes it and links the capture monitor ports to it with `pw-link` (src/audio/airplay.rs:129-251). Selection: dropdown chip (webui/src/components/Casting.tsx:213-226) or `POST /api/airplay/connect` by name (src/web/routes.rs:500-509).
- Presence: CURRENT. src/main.rs:85.
- Outcome: PARTIAL. Discovery is stated as verified; no statement records an AirPlay session carrying audio.
- Outcome evidence: STATED. Commit 9d619ed message: "Cast/AirPlay discovery verified on the real LAN (22 + 19 devices" and "live sessions left for a supervised test".
- Why it turned out that way: UNKNOWN.
- Depends on: PipeWire's `libpipewire-module-raop-sink` being present (src/audio/airplay.rs:279-301); avahi-daemon; receivers of the AirPlay-1 class (src/audio/airplay.rs:13-14; DECISIONS.md:103-104).
- Open questions: Was any AirPlay receiver played to? The last AirPlay name is stored (src/main.rs:160-162) and `grep -rn last_airplay_name src` shows no reader: is AirPlay rejoin at start-up intended?
- Confidence: MEDIUM. STATED evidence for discovery only.

### R6-S08-07  Output selection: chips, REST calls and start-up defaults
- Question: S08.1
- What was attempted: four chips (Local Player, Chromecast, AirPlay, Host PC Audio) toggle outputs (webui/src/components/Casting.tsx:138-285). Settings holds "Default output" (Local Player, Host PC Audio, Last used), "Auto-reconnect" and "Multiple outputs" (webui/src/components/Settings.tsx:494-522), stored through `POST /api/settings` in `state.toml` (src/web/routes.rs:45-78; src/state/config.rs:161-172). At start-up, after a 3 s sleep, discovery runs for Cast and AirPlay and the host output is activated when wanted (src/main.rs:168-199). The value `local` causes no server action (src/main.rs:192-193); the Local Player starts only from a click in a browser (webui/src/webrtc.ts:64).
- Presence: CURRENT. src/main.rs:104-112.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Commit 1e20b41 message lists the feature without a result.
- Why it turned out that way: UNKNOWN.
- Depends on: persisted `RuntimeState` (src/state/config.rs:148-173); the 3 s and 14 s waits (src/main.rs:175, 207).
- Open questions: Does start-up activation succeed after a reboot, when PipeWire and the hardware sink may appear later than 3 s?
- Confidence: MEDIUM. INFERRED from clear code.

### R6-S08-08  Host output format: PCM carried by PipeWire port links
- Question: S08.2
- What was attempted: no protocol or encoding. `pw-link <sonicflow-capture monitor port> <hardware sink playback port>` per channel (src/audio/host_out.rs:92-114, 222-236). The sample format and rate at the hardware sink are not set by the repo.
- Presence: CURRENT. Citations above.
- Outcome: WORKED
- Outcome evidence: STATED. Commit 9d619ed message, quoted in R6-S08-01.
- Why it turned out that way: UNKNOWN.
- Depends on: PipeWire port naming (`monitor`, `playback`, FL/FR suffixes) as parsed at src/audio/host_out.rs:222-236.
- Open questions: NONE
- Confidence: HIGH. STATED evidence that the code agrees with.

### R6-S08-09  WebRTC output format: Opus in RTP, signalled over the status WebSocket
- Question: S08.2
- What was attempted: track `audio/opus`, clock rate 48000, 2 channels (src/audio/webrtc_audio.rs:142-151); RTP version 2, payload type 111, 960-sample timestamp steps, random SSRC (src/audio/webrtc_audio.rs:216-218, 252-268); Opus at 128 kbps (src/audio/opus_encoder.rs:1-2). Signalling messages `webrtc_offer`, `webrtc_answer`, `webrtc_ice_candidate`, `webrtc_stop` travel on `/ws/status` and are scoped to the owning session (src/web/ws.rs:98-141, 206-233; API.md:117-118, 142-149). Both ends are configured with a public STUN server (src/audio/webrtc_audio.rs:128-134; webui/src/webrtc.ts:117-119).
- Presence: CURRENT. Citations above.
- Outcome: WORKED
- Outcome evidence: STATED. Commit 2641e55 message, quoted in R6-S08-02.
- Why it turned out that way: UNKNOWN. A casing rule is STATED as required for Safari: `sdpMLineIndex` (src/web/ws.rs:87-88; API.md:138-140).
- Depends on: `register_default_codecs` and default interceptors in webrtc-rs (src/audio/webrtc_audio.rs:55-65); a WebSocket connection per listener.
- Open questions: NONE beyond those in R6-S08-02.
- Confidence: HIGH. STATED evidence that the code agrees with.

### R6-S08-10  HTTP stream format: chunked ADTS AAC at 256 kbps or MP3 at 192 kbps
- Question: S08.2
- What was attempted: `GET /api/stream/audio.aac` returns `audio/aac`; `GET /api/stream/audio.mp3` returns `audio/mpeg`. Headers: `Cache-Control: no-cache, no-store`, `Connection: keep-alive`, `Access-Control-Allow-Origin: *`, `icy-name: SonicFlow` (src/audio/cast_stream.rs:21-39, 119-126; API.md:89-90). No authentication (API.md:14).
- Presence: CURRENT. src/web/mod.rs:105-106.
- Outcome: PARTIAL. Headers stated as verified; decode by a client not recorded (see R6-S07-06 and R6-S07-07).
- Outcome evidence: STATED. Commit 3d4afd4 message: "AAC stream headers + icy-name verified".
- Why it turned out that way: UNKNOWN.
- Depends on: ffmpeg; HTTP/1.1 streaming body from axum 0.7 (Cargo.toml:37).
- Open questions: NONE beyond those in R6-S07-06.
- Confidence: MEDIUM. STATED evidence covers headers only.

### R6-S08-11  Chromecast protocol: CASTv2 sender, Default Media Receiver, live AAC URL
- Question: S08.2
- What was attempted: `CastDevice::connect_without_host_verification(address, port)`, connect to `receiver-0`, launch `CastDeviceApp::DefaultMediaReceiver` (app id CC1AD845), then `media.load` with content type `audio/aac`, `StreamType::Live`, no duration and no metadata (src/audio/chromecast.rs:8-11, 209-239). The media URL is plain HTTP on the host's LAN address (src/audio/chromecast.rs:139-140).
- Presence: CURRENT. Citations above.
- Outcome: PARTIAL. As R6-S08-04.
- Outcome evidence: STATED. As R6-S08-04.
- Why it turned out that way: UNKNOWN.
- Depends on: `rust_cast` 0.21 (DECISIONS.md:105); Cast devices accepting an ADTS AAC live stream; IPv4 only, since IPv6 rows are skipped in discovery (src/audio/chromecast.rs:329-332).
- Open questions: Does the Default Media Receiver play raw ADTS on every Cast model in the house?
- Confidence: MEDIUM. STATED evidence is general; code is clear.

### R6-S08-12  AirPlay protocol: RAOP implemented by PipeWire, linked in the graph
- Question: S08.2
- What was attempted: "AirPlay is implemented entirely by delegating to PipeWire's RAOP support and wiring the graph with pw-link — zero AirPlay protocol code in Rust" (src/audio/airplay.rs:10-11). The repo loads `module-raop-discover` through `pactl` and links ports (src/audio/airplay.rs:138-150, 197-221). Encoding, timing and encryption toward the receiver are done by the PipeWire module.
- Presence: CURRENT. Citations above.
- Outcome: PARTIAL. As R6-S08-06.
- Outcome evidence: STATED. As R6-S08-06.
- Why it turned out that way: UNKNOWN.
- Depends on: PipeWire RAOP module version on the host; "AirPlay-2-pairing-only devices (HomePod) are unsupported by PipeWire RAOP" (src/audio/airplay.rs:13-14).
- Open questions: NONE beyond those in R6-S08-06.
- Confidence: MEDIUM. STATED evidence for discovery only.

### R6-S08-13  Host output: failure at activation, and no watch on the sink afterwards
- Question: S08.3
- What was attempted: an unknown sink name, an empty sink list, a SonicFlow-internal sink, missing ports or a failed `pw-link` each return an error; the manager then removes its links and publishes `HostError`, which the UI shows as a toast while clearing the chip (src/audio/host_out.rs:40-48, 73-100, 107-111; webui/src/state.tsx:266-269). "already linked" and "File exists" from `pw-link` are accepted (src/audio/host_out.rs:109). After activation nothing polls the sink; `grep -n 'spawn\|sleep' src/audio/host_out.rs` finds no monitor task.
- Presence: CURRENT. Citations above.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: PipeWire keeping or dropping links when a device is unplugged or the null sink is recreated.
- Open questions: What does the UI show when the hardware sink disappears during playback, or when the EQ process respawn changes the graph?
- Confidence: MEDIUM. INFERRED from clear code.

### R6-S08-14  Chromecast: heartbeat, status poll and crash guard during a session
- Question: S08.3
- What was attempted: the session thread waits on its command channel with a 5 s timeout; each timeout sends a ping, and three failed pings end the session with "Cast device stopped responding"; every second tick reads receiver status and ends the session with "Playback stopped on device" when the media app is gone (src/audio/chromecast.rs:10, 242-292). Connect, launch and load errors end the session with an error text (src/audio/chromecast.rs:209-239). A panic in the thread is caught and reported as "Cast session crashed (see logs)" (src/audio/chromecast.rs:145-162). The manager clears `cast_active`, publishes `CastError` when flagged, then `CastSessionStopped` (src/audio/chromecast.rs:75-87). A new connect first disconnects the old session (src/audio/chromecast.rs:126).
- Presence: CURRENT. Citations above.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Commit 2641e55 states the change and what it addresses; no later statement records a device going offline during a session.
- Why it turned out that way: UNKNOWN.
- Depends on: `rust_cast` blocking calls returning errors within the heartbeat interval; the device being marked active before the connection result is known (src/audio/chromecast.rs:164-173).
- Open questions: How long does the UI show an unreachable device as playing before the error arrives? No reconnect follows a mid-session loss: is that intended?
- Confidence: MEDIUM. INFERRED from clear code.

### R6-S08-15  Chromecast chip left active after a dead session, before commit 2641e55
- Question: S08.3
- What was attempted: the earlier `disconnect` cleared state only when a live session object existed, and a panicking session thread sent no end event (removed lines in `git show 2641e55 -- src/audio/chromecast.rs`).
- Presence: REMOVED. Commit 2641e55 (src/audio/chromecast.rs).
- Outcome: FAILED
- Outcome evidence: STATED. Commit 2641e55 message: "Cast disconnect now always clears + publishes stopped, even with a dead session — fixes the chip you could never toggle off".
- Why it turned out that way: STATED. "a crashed session thread may have left the client believing it is active" (src/audio/chromecast.rs:177-178).
- Depends on: the TLS panic recorded in R6-S08-05 as the trigger.
- Open questions: NONE
- Confidence: HIGH. STATED evidence that the diff agrees with.

### R6-S08-16  AirPlay: sink matching limits and a five-second sink watch
- Question: S08.3
- What was attempted: after loading the discover module the manager polls up to 10 times at 500 ms for a matching RAOP sink; with no match it uses the only RAOP sink if exactly one exists, and otherwise returns an error naming the candidates (src/audio/airplay.rs:152-177). It waits up to 10×200 ms for playback ports (src/audio/airplay.rs:185-195). During a session a task checks every 5 s that the sink still exists and, when it is gone, clears `airplay_active` and publishes `AirPlaySessionStopped` (src/audio/airplay.rs:223-241). Errors reach the UI as `air_play_error` toasts (src/audio/airplay.rs:68-74; webui/src/state.tsx:250-253).
- Presence: CURRENT. Citations above.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. The exactly-one rule is labelled "D3 fix" (src/audio/airplay.rs:161), a reference to predecessor defect D3 (DECISIONS.md:99-100; BUILD-PLAN.md:85).
- Depends on: RAOP sink names containing the device address or name (src/audio/airplay.rs:403-417); `avahi-daemon` being active (src/audio/airplay.rs:37-48).
- Open questions: The watch task clears shared state but the manager's own `links` and `active_sink` fields stay set until the next command (src/audio/airplay.rs:227-241 against 253-266): does that matter on reconnect? Was a receiver ever switched off during a session?
- Confidence: MEDIUM. INFERRED from clear code.

### R6-S08-17  Local Player: fallback chain when WebRTC or autoplay fails
- Question: S08.3
- What was attempted: four triggers move the player to the HTTP stream: a 5 s timer finding the element not ready or paused, a peer connection state of `failed` or `disconnected`, an exception while negotiating, and a failure applying the answer (webui/src/webrtc.ts:79-89, 130-138, 155-158, 170-173). The HTTP stream tries AAC, then MP3 on an element error (webui/src/webrtc.ts:191-205). When unmuted play is refused the element plays muted and the chip reads "muted by browser — tap to unmute" (webui/src/webrtc.ts:42-62; webui/src/components/Casting.tsx:165-189). On the server, a WebSocket close always stops the session (src/web/ws.rs:69-75).
- Presence: CURRENT. Citations above.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The verification sentence in commit 2641e55 reports a WebRTC connection that was unmuted, which does not exercise the fallback branches.
- Why it turned out that way: UNKNOWN.
- Depends on: browser autoplay policy; a fresh user click for unmute (webui/src/webrtc.ts:6-9, 92-100).
- Open questions: Has each trigger been exercised? After falling back to HTTP, does anything return the player to WebRTC?
- Confidence: MEDIUM. INFERRED from clear code.

### R6-S08-18  Local Player behaviour on connection loss before commit 2641e55
- Question: S08.3
- What was attempted: the first client had only the 5 s readiness timer, an inline "muted → play → unmute retry", and an MP3 swap that cleared its own error handler without calling play (removed lines in `git show 2641e55 -- webui/src/webrtc.ts`).
- Presence: REMOVED. Commit 2641e55 (webui/src/webrtc.ts).
- Outcome: FAILED
- Outcome evidence: STATED. Commit 2641e55 message: "fall back to HTTP stream on ICE failure/disconnect (was: silent dead session)" and "fix the Firefox ADTS-AAC fallback to actually replay after swapping to MP3".
- Why it turned out that way: STATED in part. "programmatic unmute outside a gesture is exactly what Safari blocks" (webui/src/webrtc.ts:8-9). No cause is given for the ICE failures themselves.
- Depends on: the browsers used at the time; commit 2641e55 names headless Chromium and Firefox 154.
- Open questions: Under what network condition did ICE fail or disconnect on the LAN?
- Confidence: HIGH. STATED evidence that the diff agrees with.

### R6-S08-19  HTTP stream: encoder start failure and client disconnect
- Question: S08.3
- What was attempted: if the AAC pipeline cannot be spawned the handler serves the MP3 pipeline under the AAC URL's request; if that also fails it returns 503 (src/audio/cast_stream.rs:21-39). Both child processes are held inside the response stream with `kill_on_drop`, so a client disconnect ends them (src/audio/cast_stream.rs:41-47, 105-118). End of ffmpeg output or a read error ends the body (src/audio/cast_stream.rs:110-112). The capture device is the null-sink monitor when present, otherwise a Bluetooth source, otherwise `@DEFAULT_MONITOR@` (src/audio/cast_stream.rs:129-146).
- Presence: CURRENT. Citations above.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: `kill_on_drop` semantics of tokio processes; the Cast receiver's handling of an MP3 body under an `audio/aac` media load when the fallback branch runs (src/audio/chromecast.rs:230-233).
- Open questions: What does a slow client do to the pipe chain: does `parec` block, or drop? Is a stream that ends mid-session re-requested by the Cast device?
- Confidence: MEDIUM. INFERRED from clear code.

### R6-S08-20  WebRTC server side: slow consumer and failed packet write
- Question: S08.3
- What was attempted: each session reads from a broadcast channel of 256 frames; a receiver that falls behind gets `Lagged(n)`, the frames are counted as dropped and RTP counters move forward (src/audio/webrtc_audio.rs:224-233; src/audio/capture.rs:12). A failed `write_rtp` logs a warning and ends that session's pump (src/audio/webrtc_audio.rs:263-266). A new offer on the same session id replaces the old session (src/audio/webrtc_audio.rs:125-126). If the WebRTC manager cannot be built at start-up the command channel is drained so senders never block (src/main.rs:91-98).
- Presence: CURRENT. Citations above.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: tokio broadcast semantics; the client's own state watch to notice a dead pump (webui/src/webrtc.ts:130-138).
- Open questions: When the pump exits after a write failure, the session entry stays in the manager's map until a stop or a new offer (src/audio/webrtc_audio.rs:116-122, 187-193): does the client learn of it?
- Confidence: MEDIUM. INFERRED from clear code.

### R6-S08-21  Fan-out: every output taps the same capture sink independently
- Question: S08.4
- What was attempted: all outputs read the monitor of `sonicflow-capture`: the in-process `parec` feeding WebRTC sessions and the spectrum, one `parec | ffmpeg` per HTTP or Cast client, and `pw-link` links for host and AirPlay (README.md:60-70; src/audio/capture.rs:61-84; src/audio/cast_stream.rs:49-98; src/audio/host_out.rs:92-114; src/audio/airplay.rs:197-221). The planned matrix is "N browser listeners + max 1 Chromecast + max 1 AirPlay + Host PC Audio" (DECISIONS.md:76). The "Multiple outputs" toggle defaults to on (src/state/config.rs:166-167, 179-181).
- Presence: CURRENT. Citations above.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. "Cast+AirPlay concurrency gets a validation spike (SoundSync G2: untested)." (DECISIONS.md:77); BUILD-PLAN.md:86 repeats the spike; no result is recorded in any commit or document.
- Why it turned out that way: UNKNOWN.
- Depends on: PipeWire allowing several consumers on one monitor; processor capacity for one encoder process per HTTP listener.
- Open questions: Which combinations of outputs were run together, and for how long? How far apart did they play?
- Confidence: MEDIUM. INFERRED from clear code; the design is STATED, the result is not.

### R6-S08-22  One target per output family, and exclusive mode handled in the browser
- Question: S08.4
- What was attempted: each manager holds a single session: Chromecast `connect` begins with `self.disconnect()` and the comment "single active session" (src/audio/chromecast.rs:126); AirPlay and host do the same (src/audio/airplay.rs:130; src/audio/host_out.rs:69). "Multiple simultaneous Chromecasts or AirPlay targets: out of scope." (DECISIONS.md:78). When "Multiple outputs" is off, the browser stops the other outputs before starting one (webui/src/components/Casting.tsx:142-148). The server stores the flag and no manager reads it: `grep -rn multi_output src` shows only the settings route, the snapshot and the persisted struct (src/web/routes.rs:64-66; src/state/mod.rs:236; src/state/config.rs:167).
- Presence: CURRENT. Citations above.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: all control passing through the web UI for exclusivity to hold; REST clients and the start-up activation path are not bound by the flag (src/main.rs:192-219).
- Open questions: With two browsers open and the toggle off, does one browser's Local Player stop when the other starts an output?
- Confidence: MEDIUM. INFERRED from clear code and a repo-wide search.

---

## S09 User interface and control surfaces

### R6-S09-01  Web page: a single-page app served by the same binary
- Question: S09.1
- What was attempted: a Preact and TypeScript app built with Vite (webui/package.json:6-19) and served from the directory named by `SONICFLOW_WEBUI`, default `webui/dist`, with unknown paths falling back to `index.html` (src/main.rs:126; src/web/mod.rs:41-42, 109). Layouts for desktop, tablet and phone, a settings dialog and a phone bottom sheet (webui/src/app.tsx:57-58, 166-229). It keeps one WebSocket to `/ws/status` and sends commands by REST (webui/src/state.tsx:278-307; webui/src/api.ts:97-152). Keyboard: Space for play/pause, Escape to close dialogs (webui/src/app.tsx:85-101; webui/src/components/Settings.tsx:57-59; webui/src/components/Casting.tsx:30-32).
- Presence: CURRENT. Citations above.
- Outcome: WORKED
- Outcome evidence: OBSERVED for the idle page: docs/screenshot.png shows the rendered page at version tag v0.1.0 with the visualiser, Now Playing card, two chips and the text "No source — open Settings to pair". STATED for live data: commit b19435c message: "Verified live in headless Chromium: 440Hz tone through the EQ sink lights the correct bars end-to-end".
- Why it turned out that way: UNKNOWN.
- Depends on: a browser with WebSocket and canvas; the built `webui/dist` (absent from the tree, .gitignore:3); Node 22 for the build (install.sh:120-122).
- Open questions: The screenshot shows no Chromecast or AirPlay chip, while the current code always renders both dropdown chips (webui/src/components/Casting.tsx:198-226): was the image taken before commit 2641e55? Is there a rendering of the current version?
- Confidence: HIGH. OBSERVED artefact for the idle state, plus STATED evidence that the code agrees with.

### R6-S09-02  HTTP REST API and one WebSocket, documented as a frozen contract
- Question: S09.1
- What was attempted: an axum router with 42 `.route(` entries: 41 REST paths, one of which carries both GET and POST, and `/ws/status` (src/web/mod.rs:44-112). The contract is written in API.md:16-149, titled "SonicFlow API contract (v0.1.0 — FROZEN)" (API.md:1). README.md:192-201 presents `curl` as a client.
- Presence: CURRENT. src/main.rs:128-132.
- Outcome: WORKED
- Outcome evidence: STATED. Commit 3d4afd4 message: "All 40 REST routes + WS smoke-tested via curl (200/400/404 as documented, WS upgrade 101, AAC stream headers + icy-name verified)".
- Why it turned out that way: UNKNOWN.
- Depends on: axum 0.7, tower-http 0.6 (Cargo.toml:37, 39); the bind address and port in config (src/web/mod.rs:115-124).
- Open questions: README.md:192 and commit 3d4afd4 say 40 routes; the router registers 41 REST paths (42 method and path pairs) at HEAD and had the same 42 `.route(` entries at commit 3d4afd4 (`git show 3d4afd4:src/web/mod.rs | grep -c '\.route('`). Which routes does the figure 40 leave out, and were they smoke-tested? API.md:1 carries version 0.1.0 while the package version is 0.2.1 (Cargo.toml:3).
- Confidence: HIGH. STATED evidence that the code agrees with.

### R6-S09-03  Command line: three shell scripts; the binary itself takes no arguments
- Question: S09.1
- What was attempted: `install.sh` with `--user`, `--no-build`, `--port` (install.sh:5, 43-50); `uninstall.sh` with `--user`, `--purge`, `--disable-linger`, `--list-packages` (uninstall.sh:5-21, 38-57); `deploy/doctor.sh`, installed as `/opt/sonicflow/doctor.sh`, with no flags (deploy/doctor.sh:1-10; install.sh:304). `main` parses no arguments; it reads the environment variables `SONICFLOW_WEBUI`, `RUST_LOG` and `XDG_RUNTIME_DIR` (src/main.rs:26-31, 126; src/audio/filter_chain.rs:17). No code for physical buttons, lights or a display: `grep -rniE 'gpio|evdev|/dev/input|framebuffer|lcd' src deploy install.sh uninstall.sh` returns nothing.
- Presence: CURRENT. Citations above.
- Outcome: PARTIAL. The doctor script is stated as run with all checks passing; no statement records the output of a complete run of the current installer or uninstaller (see R6-S10-01, R6-S10-03).
- Outcome evidence: STATED. Commit 1e20b41 message: "deploy/doctor.sh diagnostics (all-green on this box)".
- Why it turned out that way: UNKNOWN.
- Depends on: bash; root for the two installers (install.sh:37; uninstall.sh:59); the service user's session for doctor.sh (deploy/doctor.sh:2-3).
- Open questions: NONE
- Confidence: MEDIUM. STATED evidence for one of three scripts.

### R6-S09-04  Configuration files as a control surface
- Question: S09.1
- What was attempted: hand-edited TOML at `/etc/sonicflow/config.toml`, `$XDG_CONFIG_HOME/sonicflow/config.toml` and `./config.toml`, merged field by field (src/state/config.rs:5-15, 104-111). README.md:171 and README.md:263-264 direct the user to edit `/etc/sonicflow/config.toml` for `art_online` and `bind_address`. `Config::load()` is called once in `main` (src/main.rs:44), so edits take effect at the next start.
- Presence: CURRENT. Citations above.
- Outcome: UNVERIFIED
- Outcome evidence: NONE for a user editing the file on an installed system. Parsing has unit tests (src/state/config.rs:245-299); see R6-S10-11.
- Why it turned out that way: UNKNOWN.
- Depends on: a service restart after an edit; the app-owned `state.toml` overriding `device_name`, `adapter` and `auto_pair` (src/state/config.rs:89-100).
- Open questions: After a name or auto-pair change made in the UI, an edit of the same key in `config.toml` is overridden by `state.toml`: is that stated anywhere for the user?
- Confidence: MEDIUM. INFERRED from clear code.

### R6-S09-05  Web page: the actions and status items it offers
- Question: S09.2
- What was attempted: Player screen: spectrum visualiser; title, artist, artwork and a read-only progress bar; previous, play/pause, next, disabled when no AVRCP player exists (webui/src/components/Transport.tsx:17-43; webui/src/components/NowPlaying.tsx:53-103); a source indicator (webui/src/app.tsx:104-133); version tag (webui/src/app.tsx:152-154); output chips with a host volume slider and a rescan button (webui/src/components/Casting.tsx:158-284). Settings, four sections (webui/src/components/Settings.tsx:85-103): theme mode, palette, bar glow, density, reduced motion (180-267); EQ presets, ten sliders, preamp, save, rename, delete, reset (271-423); discovered Cast and AirPlay devices, rescan, default output, auto-reconnect, multiple outputs (427-530); Bluetooth name, adapter choice when more than one, scan, device list with connect, disconnect and forget, "Identify unnamed devices", auto-pair, line in, connected source (545-704).
- Presence: CURRENT. Citations above.
- Outcome: PARTIAL. Stated verification covers the visualiser with live data and the dropdown layout; no result is recorded for the other controls.
- Outcome evidence: STATED. Commit b19435c message, quoted in R6-S09-01. Commit 3629c21 message: "Verified: vertical overflow now 0px with the AirPlay list open."
- Why it turned out that way: UNKNOWN.
- Depends on: decisions D1 (no shuffle, repeat or seek; DECISIONS.md:8-15) and D3 (fourth settings section; DECISIONS.md:28-35).
- Open questions: The page offers no control for Cast, AirPlay or Local Player volume (R6-S07-09), no EQ on/off switch (DECISIONS.md:51) and no host sink picker. sonicflow-design-spec.md:447 lists shortcuts for seek, volume and mute; the code handles Space only (webui/src/app.tsx:90). Were these left out on purpose in every case?
- Confidence: MEDIUM. STATED evidence covers part of the surface.

### R6-S09-06  REST API: the actions it offers
- Question: S09.2
- What was attempted: system: status, device list, settings (src/web/mod.rs:46-48). Bluetooth: scan on/off, connect, disconnect, remove device, set name, resolve names, list adapters, select adapter (50-63). EQ: get, set bands/preamp/enabled, list presets, apply, save, rename, delete preset (65-70). Line-in: status, activate, deactivate (72-77). AVRCP: play, pause, next, previous (79-82). Chromecast and AirPlay: list, discover, connect, disconnect, volume (84-97). Host: sinks, activate, deactivate, volume (99-102). Media: artwork, AAC stream, MP3 stream (104-106). Request and response shapes are in API.md:18-93. `GET /api/status` returns Bluetooth status, device count, uptime in seconds and version (src/web/routes.rs:22-30).
- Presence: CURRENT. Citations above.
- Outcome: WORKED
- Outcome evidence: STATED. Commit 3d4afd4 message, quoted in R6-S09-02.
- Why it turned out that way: UNKNOWN.
- Depends on: JSON bodies; no authentication (API.md:14).
- Open questions: NONE beyond those in R6-S09-02.
- Confidence: HIGH. STATED evidence that the code agrees with.

### R6-S09-07  WebSocket: the status items it pushes
- Question: S09.2
- What was attempted: on connect the server sends a full `state_snapshot` (src/web/ws.rs:23-32; fields at src/state/mod.rs:257-282), then one message per internal event: Bluetooth status, adapters, device state and removal, stream stop, EQ change, 64 spectrum bands, track, playback status, AVRCP availability, line-in, device name, WebRTC answer and candidates (to the owning session only), Cast, AirPlay and host session events and errors, settings changed, generic error, service stopping (src/web/ws.rs:145-271; API.md:101-132). Client to server: three WebRTC signalling messages (src/web/ws.rs:98-141).
- Presence: CURRENT. Citations above.
- Outcome: WORKED
- Outcome evidence: STATED. Commit 3d4afd4 message: "WS upgrade 101"; commit b19435c message reports spectrum data driving the bars in a browser (quoted in R6-S09-01).
- Why it turned out that way: UNKNOWN.
- Depends on: an event channel of 256 entries; a lagging socket loses events with a server-side warning and no resend (src/state/mod.rs:397; src/web/ws.rs:46-48).
- Open questions: With about 47 spectrum messages per second on the same channel as state events (src/audio/spectrum.rs:12-14), are state events ever lost to lag on a slow client?
- Confidence: MEDIUM. STATED evidence covers the upgrade and the spectrum message; other events are INFERRED from code.

### R6-S09-08  First-time setup as implemented
- Question: S09.3
- What was attempted: step 1, clone the repo and run `sudo ./install.sh` (README.md:113-116). Step 2, the installer runs its twelve steps and prints `DONE — open http://<first address>:<port>` (install.sh:78-400, 402-405). Step 3, open that URL; the page shows "Connecting to SonicFlow…" or "Loading…" until the first snapshot (webui/src/app.tsx:135-142). Step 4, on the phone, pick the device named "SonicFlow" in Bluetooth settings; the adapter is set discoverable with timeout 0 and pairable at start (src/bluetooth/manager.rs:181-199), and `auto_pair` defaults to true so no confirmation is asked on this side (src/state/config.rs:41; src/bluetooth/agent.rs:56-67). Step 5, "press play, and pick your outputs" (README.md:138-140). The idle page prompts for pairing: "No source — open Settings to pair" (webui/src/app.tsx:132) and `PAIR A DEVICE WITH "<name>"` (webui/src/components/NowPlaying.tsx:59). Manual items outside the script: Node 22 when building (install.sh:120-122), stopping a competing PulseAudio or bluealsa (install.sh:80-85), a reboot or re-login after group changes (install.sh:269-272), a reboot when the Class of Device has not updated (install.sh:389-392).
- Presence: CURRENT. Citations above.
- Outcome: PARTIAL. The idle prompt is visible in the screenshot and individual steps are stated as performed on the reference machine; no record shows the whole flow run from a clean machine.
- Outcome evidence: OBSERVED for the prompt (docs/screenshot.png shows both prompt texts). STATED for the steps: install.sh:2-3 "every step was performed and verified on the reference box"; forinstall.md:9-10 "Items below marked [PENDING] are performed by install.sh at install time (they need root; dev on this box ran user-level equivalents)".
- Why it turned out that way: UNKNOWN.
- Depends on: Ubuntu 26.04 with apt; a sudo-capable user that also owns the PipeWire session (install.sh:40, 56-64); one reference machine (install.sh:3).
- Open questions: BUILD-PLAN.md:100 sets the check "clean install path replayed on the box; reboot → everything works untouched": where is its result? No in-page setup wizard exists; is the README the only first-run guidance?
- Confidence: MEDIUM. One OBSERVED artefact and STATED evidence; the complete flow is not recorded.

### R6-S09-09  State changes and errors: internal event bus to WebSocket to toasts and chip state
- Question: S09.4
- What was attempted: managers publish `SystemEvent` values (src/state/mod.rs:284-387, 408-411); each WebSocket forwards them (src/web/ws.rs:35-52); the page applies them to its snapshot (webui/src/state.tsx:87-276). Error events (`cast_error`, `air_play_error`, `host_error`, `error`, and a Bluetooth status of `error`) raise a toast and clear the matching chip (webui/src/state.tsx:104-106, 223-226, 250-253, 266-272). Session starts raise an info toast (webui/src/state.tsx:217, 244). Toasts auto-dismiss after 4 s and the last four are kept (webui/src/state.tsx:53-57; webui/src/components/Toasts.tsx:1-17). The Local Player chip shows "WebRTC", "HTTP stream" or "muted by browser — tap to unmute" (webui/src/components/Casting.tsx:181-189). The socket reconnects with a delay doubling from 1 s to 15 s (webui/src/state.tsx:286-294).
- Presence: CURRENT. Citations above.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. BUILD-PLAN.md:87 sets the check "errors surface as toasts + chip rollback" with no recorded result.
- Why it turned out that way: UNKNOWN.
- Depends on: an open WebSocket; a page that is closed sees nothing, and errors are not stored for later display.
- Open questions: After the first snapshot, the `connected` flag is read only on the boot screen (webui/src/app.tsx:139): what does the page show when the server goes away mid-use?
- Confidence: MEDIUM. INFERRED from clear code.

### R6-S09-10  REST commands answer 200 before the outcome is known
- Question: S09.4
- What was attempted: "Command-style endpoints return `200 {"ok": true}` and are fire-and-forget: failures surface later as WebSocket events, never as HTTP errors." (API.md:11-12; src/web/mod.rs:21-23). Handlers send on a channel and discard the send result (for example src/web/routes.rs:115-121, 456-462); only the scan route reports a closed channel, as a Bluetooth status event (src/web/routes.rs:96-102). Routes that do return HTTP errors: settings 400, Bluetooth name 400, preset 404/400/500, line-in 500, streams 503 (src/web/routes.rs:52-57, 157-159, 286-288, 399-404; src/audio/cast_stream.rs:36).
- Presence: CURRENT. Citations above.
- Outcome: WORKED
- Outcome evidence: STATED. Commit 3d4afd4 message: "(200/400/404 as documented".
- Why it turned out that way: STATED as a convention kept from the predecessor contract (API.md:4-12; DECISIONS.md:93-94).
- Depends on: the caller also holding a WebSocket to learn of failures; a `curl` caller receives `ok` for a command that later fails.
- Open questions: NONE
- Confidence: HIGH. STATED evidence that the code agrees with.

### R6-S09-11  Two server events with no handler in the page: settings changed and service stopping
- Question: S09.4
- What was attempted: the server publishes `settings_changed` after `POST /api/settings` and `service_stopping` on shutdown (src/web/routes.rs:76; src/main.rs:136; src/web/ws.rs:267, 269). API.md:130 describes the first as "(client re-fetches or applies optimistically)". The page's message switch has no case for either (webui/src/state.tsx:87-276; `grep -rn 'settings_changed\|service_stopping' webui/src` returns nothing). The Settings toggles for default output, auto-reconnect, multiple outputs and auto-pair read `snap.settings` and only send the REST call (webui/src/components/Settings.tsx:494-522, 673-682), and `snap.settings` is replaced only by a new `state_snapshot` (webui/src/state.tsx:89-93).
- Presence: CURRENT on the server; DISABLED in the page in the sense that the messages arrive and are dropped at webui/src/state.tsx:273-274.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: a page reload or socket reconnect to refresh `snap.settings`.
- Open questions: Does a toggle in Settings change its displayed position after a click, before a reconnect? Is the user told when the service is stopping?
- Confidence: MEDIUM. INFERRED from clear code and a repo-wide search.

### R6-S09-12  Script output: one status line per check
- Question: S09.4
- What was attempted: the installer prints `ok`, `fixed` or `FAIL` per step, counts failures, and ends with either the URL or "finished with N problem(s)" and exit status 1 (install.sh:27-31, 402-409). The doctor script prints `[ ok ]`, `[FAIL]` or `[warn]` per check with a cause hint in the text (deploy/doctor.sh:5-8, 12-63). The uninstaller prints `removed`, `ok`, `warn` and a leftover audit (uninstall.sh:28-31, 173-186). Installer failures point to `journalctl -u sonicflow` and the doctor script (install.sh:240, 381, 407).
- Presence: CURRENT. Citations above.
- Outcome: PARTIAL. Doctor output is stated as all passing on the reference machine; no installer or uninstaller run output is recorded.
- Outcome evidence: STATED. Commit 1e20b41 message: "deploy/doctor.sh diagnostics (all-green on this box)".
- Why it turned out that way: UNKNOWN.
- Depends on: a terminal; ANSI colour codes in the output (install.sh:27-31).
- Open questions: NONE
- Confidence: MEDIUM. STATED evidence for one script.

---

## S10 Lifecycle and operations

### R6-S10-01  Installer, second version: twelve check, apply, verify steps
- Question: S10.1
- What was attempted: one root-run script. Step 1 environment checks for PulseAudio, bluealsa and WirePlumber version (install.sh:78-91). Step 2 apt packages, Node check, rustup for the service user, firmware note (93-132). Step 3 linger and user services (134-149). Step 4 five keys in `/etc/bluetooth/main.conf` with a backup, then a Bluetooth restart (151-181). Step 5 WirePlumber fragment, removal of a user-level copy, `MemoryDenyWriteExecute=no` override, restart, check for the Audio Sink UUID with one retry (183-241). Step 6 real-time limits (243-256). Step 7 groups (258-272). Step 8 ufw rule for mDNS and avahi (274-284). Step 9 `cargo build --release` and the web build as the service user (286-297). Step 10 copy to `/opt/sonicflow` (299-309). Step 11 choose a free port, write `/etc/sonicflow/config.toml`, stop a development instance, install and enable the unit, restart and poll `/api/status` up to five times with port renegotiation (311-382). Step 12 acceptance checks and version record (384-400). All steps are automated; the manual items are those listed in R6-S09-08. Changes outside `/opt/sonicflow` are recorded in `/var/lib/sonicflow/changes.log` (install.sh:21-23, 33-35).
- Presence: CURRENT. install.sh:1-409, introduced by commit 26a79c2, later edited by commits 07b63e5, 3624d19, 738f12c and 3046b9e.
- Outcome: PARTIAL. The steps are stated as performed and verified one by one on the reference machine during development; several were done there as user-level equivalents; no record shows a complete run of this script.
- Outcome evidence: STATED. install.sh:2-3 "every step was performed and verified on the reference box"; forinstall.md:9-10 (quoted in R6-S09-08); forinstall.md:55-65, 72-78, 84-88, 109-128 still carry [PENDING] markers.
- Why it turned out that way: STATED. Development ran without root for the system-level items (forinstall.md:9-10).
- Depends on: Ubuntu 26.04, apt, systemd, WirePlumber 0.5 or later (install.sh:86-91); the reference machine "awdio", an Apple Macmini5,3 (install.sh:3); network access for apt, rustup, cargo and npm.
- Open questions: Has the script been run from start to finish on any machine, and with what output? forinstall.md:64-65 lists an adapter enumeration pre-flight; no such step is in install.sh: was it dropped?
- Confidence: MEDIUM. STATED evidence, with the documents themselves marking items as pending.

### R6-S10-02  Installer, first version
- Question: S10.1
- What was attempted: a linear script under `set -euo pipefail`: warnings for competing daemons, port probe from 8080, unconditional `apt-get install`, rustup, linger, `main.conf` edits without backup, WirePlumber fragment, limits, groups, ufw, build, copy, unit `enable --now`, a warning-only check for the Audio Sink UUID (`git show 1e20b41:install.sh`, lines 1-231). Commit 8d2e000 added a stop of the development instance before the unit start.
- Presence: REMOVED. Rewritten by commit 26a79c2 (install.sh, 414 lines changed).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Commit 1e20b41 message describes the script without a run result.
- Why it turned out that way: UNKNOWN. The replacement is described in commit 26a79c2 message as "audited, self-healing, privilege-split, recorded reverts"; no defect of the first version is stated beyond the port clash implied by commit 8d2e000 ("install.sh stops a running sonicflow-dev/dev binary before claiming the port").
- Depends on: the same platform as R6-S10-01.
- Open questions: Was the first version ever run on the reference machine?
- Confidence: MEDIUM. INFERRED from the removed file and two commit messages.

### R6-S10-03  Uninstaller driven by the installer's change record
- Question: S10.1
- What was attempted: stops and removes the unit, stops a development instance, unloads SonicFlow null-sink and loopback modules, removes `/opt/sonicflow`, the WirePlumber fragment and limits files, reverts recorded `main.conf` keys, removes recorded ufw rules, optionally disables linger and purges config, state, cache and records, then lists leftovers (uninstall.sh:75-186). apt packages are never removed (uninstall.sh:15-21, 43-54).
- Presence: CURRENT. uninstall.sh:1-186, added by commit 26a79c2.
- Outcome: PARTIAL. The `main.conf` revert is stated as tested in a sandbox; the remaining actions have no recorded result.
- Outcome evidence: STATED. Commit 26a79c2 message: "main.conf keys restored byte-identically — sandbox-tested roundtrip".
- Why it turned out that way: UNKNOWN.
- Depends on: `/var/lib/sonicflow/changes.log` written by install.sh (uninstall.sh:24-33); without it `main.conf` is left untouched (uninstall.sh:137).
- Open questions: Was a full uninstall run on the reference machine?
- Confidence: MEDIUM. STATED evidence for one part.

### R6-S10-04  Start on boot and restart after a crash: a systemd system unit
- Question: S10.2
- What was attempted: `/etc/systemd/system/sonicflow.service`, rendered from deploy/sonicflow.service and enabled (install.sh:353-356). `WantedBy=multi-user.target`; `After=network-online.target bluetooth.service avahi-daemon.service`; `Requires=bluetooth.service`; `Restart=on-failure`, `RestartSec=5`; `ExecStartPre=-/opt/sonicflow/sweep-modules.sh` unloads leftover SonicFlow null-sink and loopback modules (deploy/sonicflow.service:6-8, 24-30; deploy/sweep-modules.sh:5-14). The unit runs as the session user and reaches that user's PipeWire through `XDG_RUNTIME_DIR`, `DBUS_SESSION_BUS_ADDRESS` and `PULSE_RUNTIME_PATH` (deploy/sonicflow.service:12-18). The PipeWire user services are not in the unit's ordering.
- Presence: CURRENT. Citations above.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. BUILD-PLAN.md:25 ("survives reboot") and BUILD-PLAN.md:100 ("reboot → everything works untouched") are checks with no recorded result.
- Why it turned out that way: UNKNOWN.
- Depends on: the user's PipeWire session existing at boot through linger (R6-S10-05); the in-app readiness probe (R6-S10-06); `/run/user/<uid>` being present when the unit starts.
- Open questions: forinstall.md:121 describes the unit as "After=bluetooth/pipewire/avahi" while deploy/sonicflow.service:6 has no PipeWire entry: which was run? What happens at boot if `/run/user/<uid>` appears after the unit starts? `Restart=on-failure` reacts to a non-zero exit; does a hang or a dead child process lead to a restart?
- Confidence: MEDIUM. INFERRED from clear configuration.

### R6-S10-05  Linger, so the user's PipeWire session exists without a login
- Question: S10.2
- What was attempted: `loginctl enable-linger <user>` (install.sh:134-141), recorded for optional revert (uninstall.sh:152-161). The doctor script checks it (deploy/doctor.sh:15-16).
- Presence: CURRENT. Citations above.
- Outcome: PARTIAL. Enabling it is stated as done on the reference machine after a stated failure without it; survival across logout or reboot afterwards is not recorded.
- Outcome evidence: STATED. forinstall.md:81-83: "Was Linger=no; the whole PipeWire user stack died when the last SSH session ended." Commit bb2c661 message: "Linger enabled."
- Why it turned out that way: STATED. On a headless machine the user services stop with the last session (forinstall.md:81-83).
- Depends on: systemd-logind; the PipeWire stack running as user services (install.sh:142-149).
- Open questions: Was the stack seen running after a reboot with no login?
- Confidence: MEDIUM. STATED evidence.

### R6-S10-06  In-app start-up: readiness probe, null-sink reuse and retry, non-fatal failures
- Question: S10.2
- What was attempted: before building the pipeline the app polls `pactl info` (or `pw-cli info 0`) once a second for up to 30 s, with the comment "systemd After= guarantees start ordering, not readiness. Probe explicitly." (src/audio/pipeline.rs:212-241). It reuses an existing `sonicflow-capture` module if found, otherwise tries `pactl load-module module-null-sink` up to 10 times with a growing wait, confirming the sink is listed each time; `pw-loopback` is the fallback without `pactl` (src/audio/pipeline.rs:131-194). Failures are logged and start-up continues "so the web UI still comes up for diagnostics" (src/audio/pipeline.rs:51-86). Orphaned line-in loopbacks are unloaded (src/audio/line_in.rs:92-108). The Bluetooth manager retries initialisation every 10 s (src/bluetooth/manager.rs:70-86).
- Presence: CURRENT. src/main.rs:63-74, 101-102.
- Outcome: PARTIAL. The normal bring-up is stated as working in a live run; the retry, fallback and after-crash branches have no recorded result.
- Outcome evidence: STATED. Commit b19435c message: "440Hz tone through the EQ sink lights the correct bars end-to-end" (null sink, EQ process and capture were all running for that to hold).
- Why it turned out that way: UNKNOWN.
- Depends on: `pactl` from `pulseaudio-utils`; pipewire-pulse readiness after boot (src/audio/pipeline.rs:140).
- Open questions: The unit's pre-start sweep unloads the null sink before each start (deploy/sweep-modules.sh:5-14), so when does the reuse branch at src/audio/pipeline.rs:134-139 run under systemd?
- Confidence: MEDIUM. STATED evidence for the main branch; the reading of the commit is partly INFERRED.

### R6-S10-07  Capture restart command that nothing sends
- Question: S10.2
- What was attempted: `PipelineCommand::RestartCapture` restarts the `parec` child (src/audio/pipeline.rs:99-103). The variant is marked `#[allow(dead_code)] // sent by the Bluetooth control plane (Phase 2)` (src/audio/pipeline.rs:20-21), and `grep -rn RestartCapture src` finds only those two places. When the capture read fails, the task logs "Capture read ended" and returns (src/audio/capture.rs:138-141).
- Presence: DISABLED. No sender exists; src/audio/pipeline.rs:20-21.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: `parec` staying alive for the life of the service. The spectrum and WebRTC outputs read from this capture (src/main.rs:65-66, 87); the HTTP, host and AirPlay routes do not (src/audio/cast_stream.rs:49-64; src/audio/host_out.rs:92-114).
- Open questions: If `parec` exits while the main process keeps running, does anything bring the browser output and the visualiser back short of a service restart?
- Confidence: MEDIUM. INFERRED from clear code and a repo-wide search.

### R6-S10-08  Shutdown path on SIGINT or SIGTERM
- Question: S10.2
- What was attempted: `main` waits for Ctrl-C or SIGTERM, publishes `ServiceStopping`, aborts the HTTP task, sleeps 300 ms and returns; "Managers tear down when their command channels drop with main." (src/main.rs:134-140, 223-231). The pipeline's `shutdown`, which unloads the null sink and stops the EQ process, runs when its command channel closes (src/audio/pipeline.rs:106-107, 196-209). Child processes are spawned with `kill_on_drop` (src/audio/capture.rs:83; src/audio/filter_chain.rs:58).
- Presence: CURRENT. Citations above.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: the pre-start sweep to clear anything left behind (deploy/sonicflow.service:23-24).
- Open questions: Does the pipeline task get to unload the null sink within the 300 ms before the process exits?
- Confidence: MEDIUM. INFERRED from clear code.

### R6-S10-09  Permissions used at install time and at run time
- Question: S10.3
- What was attempted: root for install and uninstall (install.sh:37; uninstall.sh:59), with user-session work delegated through `sudo -u <user> ... bash -c` (install.sh:56-64). The service runs as the named user with supplementary groups `audio` and `bluetooth` (deploy/sonicflow.service:12-14); the installer adds the user to both groups (install.sh:258-268). `/opt/sonicflow` is owned by that user (install.sh:308). The WirePlumber user unit gets `MemoryDenyWriteExecute=no` (install.sh:220-227). The app calls `btmgmt` for the Class of Device knowing it "usually needs CAP_NET_ADMIN, so failure is expected and only logged" (src/bluetooth/manager.rs:215-246); no capability is granted to the service.
- Presence: CURRENT. Citations above.
- Outcome: PARTIAL. BlueZ control as an unprivileged user is stated as verified in a development run; the `btmgmt` call is stated as hanging without the capability; the installed unit's permissions have no recorded result.
- Outcome evidence: STATED. Commit 4505247 message: "Verified live: scan found 19 devices, names resolved, alias=SonicFlow, discoverable/pairable on" and "btmgmt HANGS without CAP_NET_ADMIN instead of erroring".
- Why it turned out that way: STATED for btmgmt (same commit). UNKNOWN otherwise.
- Depends on: group membership being picked up by the running user manager, which may need a reboot (install.sh:269-272); `main.conf` carrying the Class of Device in place of the `btmgmt` call (src/bluetooth/manager.rs:215-217).
- Open questions: forinstall.md:112-114 describes checking `/proc/<pid>/status` of the user manager for the groups; install.sh:269-272 prints a note without that check: was the check dropped?
- Confidence: MEDIUM. STATED evidence from a development run.

### R6-S10-10  Real-time scheduling limits
- Question: S10.3
- What was attempted: `/etc/security/limits.d/99-sonicflow-rt.conf` (rtprio 95, memlock unlimited, nice -15 for group `audio`) and `/etc/systemd/user.conf.d/99-sonicflow-rt.conf` with the matching `DefaultLimit*` values (install.sh:243-256); `LimitRTPRIO=95` and `LimitMEMLOCK=infinity` on the service (deploy/sonicflow.service:21-22); the EQ process loads `libpipewire-module-rt` with `nice.level = -11` (src/dsp/equalizer.rs:114-117). The doctor script prints the scheduling of the `pipewire` process (deploy/doctor.sh:65-66).
- Presence: CURRENT. Citations above.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. forinstall.md:109-111 marks the item [PENDING] and names the verification command without a result.
- Why it turned out that way: UNKNOWN.
- Depends on: a new login or reboot for the user-manager limits to apply (install.sh:255); Ubuntu's own `25-pw-rlimits.conf` (forinstall.md:46-47).
- Open questions: What scheduling class did `pipewire` run with on the reference machine?
- Confidence: MEDIUM. INFERRED from clear configuration.

### R6-S10-11  User configuration: layered TOML files read at start
- Question: S10.4
- What was attempted: keys and defaults: `port` 8080, `bind_address` "0.0.0.0", `adapter` unset (first adapter), `device_name` "SonicFlow", `auto_pair` true, `art_online` true (src/state/config.rs:16-45). Layers in order, later wins per field: defaults, `/etc/sonicflow/config.toml`, `$XDG_CONFIG_HOME/sonicflow/config.toml`, `./config.toml` in the working directory, then app-persisted overrides for name, adapter and auto-pair (src/state/config.rs:5-15, 81-112). A malformed file is skipped with a warning (src/state/config.rs:114-123). The installer writes the system file once and afterwards edits only the `port` line (install.sh:329-346). "The app never writes these files." (src/state/config.rs:12).
- Presence: CURRENT. src/main.rs:44.
- Outcome: WORKED
- Outcome evidence: STATED. Commit d8b17a3 message: "Pre-commit gate: version sync + fmt + clippy -D warnings + tests (63 passing)"; the six config tests are at src/state/config.rs:245-299 and were present at that commit (`git show d8b17a3:src/state/config.rs`).
- Why it turned out that way: STATED. The split between user config and app state is described as the correction of predecessor defects D13 and D21 (src/state/config.rs:12-15; DECISIONS.md:46-48).
- Depends on: `WorkingDirectory=/opt/sonicflow` for the fourth layer (deploy/sonicflow.service:15); the `dirs` crate for the user path.
- Open questions: `art_online` was added in commit 3046b9e; no test count is stated after commit d8b17a3. EQ, outputs and codec settings have no key in `config.toml`: is that deliberate?
- Confidence: MEDIUM. STATED test result from an earlier commit; tests cover parsing and merging, not a running service.

### R6-S10-12  App-owned state: state.toml and preset files
- Question: S10.4
- What was attempted: `~/.config/sonicflow/state.toml` holds UI-set device name, adapter, auto-pair, EQ (enabled, ten gains, preamp, preset name), default output ("local"), auto-reconnect (false), multi-output (true), last Cast id, last AirPlay name, last output (src/state/config.rs:125-201). It is rewritten whole through a temporary file and rename (src/state/config.rs:224-238; src/state/mod.rs:243-254). Custom presets are one TOML file each under `~/.config/sonicflow/presets/`, with names sanitised to alphanumerics, `-` and `_` (src/dsp/presets.rs:64-79, 143-155).
- Presence: CURRENT. Citations above.
- Outcome: WORKED
- Outcome evidence: STATED. Commit 174ec2f message: "custom preset save/list/delete, full state persistence across service restart."
- Why it turned out that way: UNKNOWN.
- Depends on: a writable home config directory for the service user; `uninstall.sh --purge` removes it (uninstall.sh:164-168).
- Open questions: Host volume is not in the persisted struct (src/state/config.rs:152-173): is the 0.75 default after every restart intended?
- Confidence: HIGH. STATED evidence that the code agrees with.

### R6-S10-13  Per-browser display preferences in localStorage
- Question: S10.4
- What was attempted: theme mode (default system), palette (default aurora), bar glow (on), bar density (comfortable), reduced motion (system) are stored under the key `sonicflow-prefs` in the browser (webui/src/app.tsx:16-36, 68-78; webui/src/main.tsx:7-24). They are not sent to the server.
- Presence: CURRENT. Citations above.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: browser storage being available; a `try/catch` covers private mode (webui/src/app.tsx:71-75).
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from clear code.

### R6-S10-14  Logging: tracing output to standard output, read through the journal
- Question: S10.5
- What was attempted: `tracing_subscriber::fmt()` with the filter from `RUST_LOG`, default `sonicflow=info,tower_http=warn` (src/main.rs:26-31); the unit sets `RUST_LOG=sonicflow=info` (deploy/sonicflow.service:20). The installer reads the output with `journalctl -u sonicflow` (install.sh:368, 381). The capture child's stderr is logged line by line: "stderr is captured and logged, never discarded — capture failures were previously invisible" (src/audio/capture.rs:106-115). An HTTP trace layer is attached (src/web/mod.rs:111). Each WebRTC session logs sent, dropped and expected frame counts every 60 s (src/audio/webrtc_audio.rs:270-276). For HTTP streams, `parec` and `ffmpeg` stderr go to null and ffmpeg runs with `-loglevel quiet` (src/audio/cast_stream.rs:62, 75-76, 93). No log file is written by the app (`grep -rniE 'File::create|\.log' src` shows no log path).
- Presence: CURRENT. Citations above.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No log sample is stored in the repo (.gitignore:4).
- Why it turned out that way: UNKNOWN.
- Depends on: systemd-journald for retention; `RUST_LOG` for verbosity.
- Open questions: What do the logs of a real session look like, and were any `Lagged` or capture warnings seen?
- Confidence: MEDIUM. INFERRED from clear code.

### R6-S10-15  Diagnostics script covering the chain from linger to the API
- Question: S10.5
- What was attempted: read-only checks for `pactl`, pipewire-pulse, linger, WirePlumber, the Audio Sink UUID, adapter power and discoverability, `bluez-obexd`, the `Experimental` key, the SPA bluez5 plugin directory, the null sink, the EQ sink, the default sink, live Bluetooth sources, the API on the configured port, competing daemons, the default route, and the `pipewire` scheduling line (deploy/doctor.sh:12-66). Installed to `/opt/sonicflow/doctor.sh` (install.sh:304). The struct method `FilterChainManager::is_running` is marked "diagnostics hook, wired in the doctor script phase" and has no caller (src/audio/filter_chain.rs:26-32).
- Presence: CURRENT. Citations above.
- Outcome: WORKED
- Outcome evidence: STATED. Commit 1e20b41 message: "deploy/doctor.sh diagnostics (all-green on this box)".
- Why it turned out that way: UNKNOWN.
- Depends on: being run as the service user with its session environment (deploy/doctor.sh:2-3); x86_64 or aarch64 library paths (deploy/doctor.sh:35).
- Open questions: The stated run predates the checks added in commit 738f12c (deploy/doctor.sh:28-33): were those run?
- Confidence: HIGH. STATED evidence that the code agrees with.

### R6-S10-16  Packaging: CI-built tarball, attached to a release on a version tag
- Question: S10.6
- What was attempted: a GitHub Actions job checks version sync, formatting, clippy and tests, builds the release binary and web UI, and packs `sonicflow-v<version>-x86_64-linux.tar.gz` with a SHA-256 file, laid out so `sudo ./install.sh --no-build` runs from the unpacked directory; on a `v*` tag the files are attached to a release (.github/workflows/build.yml:3-8, 37-95). `--no-build` skips the toolchain steps (install.sh:117-119, 288-297).
- Presence: CURRENT. Added by commit 07b63e5.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No CI result is stored in the repo, and `git tag` prints nothing, so no release tag exists in this clone.
- Why it turned out that way: UNKNOWN.
- Depends on: GitHub-hosted `ubuntu-latest` runners; the binary being linked on the runner's distribution and run on the target's; x86_64 only (.github/workflows/build.yml:69).
- Open questions: Has the workflow run, and has a tarball been installed with `--no-build` on any machine?
- Confidence: MEDIUM. INFERRED from clear configuration.

### R6-S10-17  Version kept in four files by a hook, CI and a checklist
- Question: S10.6
- What was attempted: the version string lives in Cargo.toml:3, webui/package.json:4, webui/src/version.ts:3 and install.sh:20. The pre-commit hook compares the first three (.githooks/pre-commit:7-14); CI compares all four (.github/workflows/build.yml:38-45); CHECKLIST.md:9-11 requires a bump in all four with every change.
- Presence: CURRENT. Citations above. `.git/config` in this clone has no `core.hooksPath` entry (`grep -n hooksPath .git/config` returns nothing), so the hook is not active in this clone.
- Outcome: WORKED
- Outcome evidence: OBSERVED. The four tracked files carry the same value, 0.2.1, at HEAD (Cargo.toml:3; webui/package.json:4; webui/src/version.ts:3; install.sh:20).
- Why it turned out that way: UNKNOWN.
- Depends on: the hook being enabled by hand with `git config core.hooksPath .githooks` (README.md:212-213).
- Open questions: API.md:1 still reads v0.1.0: is the contract version meant to track the package version?
- Confidence: HIGH. OBSERVED in the tracked files.

### R6-S10-18  Update approach: re-run the installer; no self-update
- Question: S10.6
- What was attempted: the installer reads `/var/lib/sonicflow/installed-version`, announces "RE-INSTALL / repair (previous: v… → v…)", keeps an existing config and port, replaces the binary and web files, and writes the new version (install.sh:69-76, 313-316, 343-346, 398). `/opt/sonicflow/.sonicflow-version` is also written (install.sh:307). The app holds no update check.
- Presence: CURRENT. Citations above.
- Outcome: UNVERIFIED
- Outcome evidence: NONE for an upgrade run. STATED for the absence of a runtime check: commit 3624d19 message: "Confirmed nothing compares versions at runtime (display-only)".
- Why it turned out that way: UNKNOWN.
- Depends on: the user fetching a new checkout or tarball by hand; `state.toml` fields carrying serde defaults for forward compatibility (src/state/config.rs:159-167).
- Open questions: Has an upgrade from one installed version to another been performed?
- Confidence: MEDIUM. INFERRED from clear code; one STATED point.

---

## S11 Testing and measured behaviour

### R6-S11-01  Rust unit tests on parsers, generators and data shapes
- Question: S11.1
- What was attempted: 70 `#[test]` or `#[tokio::test]` attributes in 16 files (`grep -rcE '#\[(tokio::)?test\]' src`): src/dsp/equalizer.rs 8 (band table, clamp, generated config text, preamp multiplier; lines 160-243), src/dsp/presets.rs 7 (183-254), src/bluetooth/device.rs 8, src/state/config.rs 6 (245-326), src/audio/pipeline.rs 6 (`wpctl` and module-list parsing; 337-377), src/web/ws.rs 6 (wire shapes; 278-340), src/audio/airplay.rs 4 (470-514), src/state/mod.rs 4 (418-478), src/bluetooth/constants.rs 4, src/art.rs 4 (434-485, one marked `#[ignore]` at 468), src/audio/chromecast.rs 3 (447-475), src/bluetooth/manager.rs 3, src/audio/opus_encoder.rs 2 (encode silence and a tone; 34-54), src/audio/spectrum.rs 2 (1 kHz tone lands in bands 33 to 39; silence near zero; 114-141), src/audio/line_in.rs 2 (151-174), src/audio/host_out.rs 1 (247-256). No test starts the audio pipeline, a subprocess, the HTTP server or a WebRTC session: src/audio/capture.rs, cast_stream.rs, filter_chain.rs, webrtc_audio.rs, src/web/routes.rs, src/web/mod.rs, src/main.rs, src/bluetooth/agent.rs and avrcp.rs hold no test attribute. The web UI has no tests (`git ls-files | grep -i test` returns nothing).
- Presence: CURRENT. Citations above.
- Outcome: WORKED
- Outcome evidence: STATED. Commit d8b17a3 message: "Pre-commit gate: version sync + fmt + clippy -D warnings + tests (63 passing)". The attribute count at that commit is 63 (same grep over `git show d8b17a3:<file>`). README.md:208 gives "66 unit tests".
- Why it turned out that way: UNKNOWN.
- Depends on: `cargo test` on a machine with `libopus-dev` and `libdbus-1-dev` (.github/workflows/build.yml:19-23); the tests exercise pure functions only.
- Open questions: The count at HEAD is 70 with one ignored; README.md:208 says 66 and the last stated pass count is 63. Were the tests added after commit d8b17a3 run, and with what result? No test output is stored.
- Confidence: MEDIUM. STATED evidence from the first commit; no later result recorded.

### R6-S11-02  Automated gates: pre-commit hook and CI job
- Question: S11.1
- What was attempted: `.githooks/pre-commit` runs version sync, `cargo fmt --check`, `cargo clippy --all-targets --quiet -- -D warnings` and `cargo test --quiet` (.githooks/pre-commit:7-23). The CI job runs the same checks plus the release and web builds on pushes to `main`, tags, pull requests and manual dispatch (.github/workflows/build.yml:3-8, 37-63). The web build includes `tsc --noEmit` (webui/package.json:8).
- Presence: CURRENT. The hook is not enabled in this clone (no `core.hooksPath` in `.git/config`).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No CI run result, badge or log is in the repo.
- Why it turned out that way: UNKNOWN.
- Depends on: the developer enabling the hook (README.md:212-213); GitHub Actions for CI.
- Open questions: Did the workflow ever run on the remote, and did it pass at commit 3046b9e?
- Confidence: MEDIUM. INFERRED from clear configuration.

### R6-S11-03  Manual live checks: the plan's per-phase checks and the results written into commit messages
- Question: S11.1
- What was attempted: BUILD-PLAN.md gives a "Check:" line for each of twelve phases (BUILD-PLAN.md:16, 25-26, 34, 42, 50, 62, 68, 74, 79-80, 87, 94, 100). Results appear only as sentences in commit messages: bb2c661 (Audio Sink UUID advertised), 4505247 (scan, names, alias, discoverable), 174ec2f (EQ apply and persistence), 3d4afd4 (routes smoke-tested with curl), b19435c (tone lights bars in headless Chromium), 9d619ed (WebRTC in headless Chromium; host links; line-in module; Cast and AirPlay discovery), 0ad9f4e (iPhone reaches audio-active; AVRCP metadata), 2641e55 (WebRTC in headless Chromium and Firefox 154), 3629c21 (dropdown overflow), 26a79c2 (`main.conf` revert in a sandbox), 738f12c (online art lookups). A headless-browser cache from "UI testing" is noted on the development machine (forinstall.md:38-39). BUILD-PLAN.md:94 also names the predecessor's manual checklist (SOUNDSYNC-SPEC.md:3057-3103) as a Phase 10 check.
- Presence: CURRENT (documents and commit history).
- Outcome: PARTIAL. Stated as passed: the items listed above. Stated as not yet done: "Audible check pending a Bluetooth source." (commit 174ec2f) and Cast/AirPlay "live sessions left for a supervised test" (commit 9d619ed). With no recorded result: survives reboot (BUILD-PLAN.md:25), adapter switch (34), all palettes AA (62), ten minutes without stutter and Safari/iOS (68), adapter reset and non-AVRCP source (79-80), Cast and AirPlay playing EQ'd audio and the concurrency spike (86-87), the end-to-end checklists (94), clean install replay and reboot (100).
- Outcome evidence: STATED. The commit messages named above; README.md:283-285: "verified end-to-end on real hardware: real phones, a house full of real Chromecasts, and one very patient 2011 Mac mini".
- Why it turned out that way: UNKNOWN.
- Depends on: one development machine and its LAN; all of the dated work falls on 2026-08-29 and 2026-08-30 (`git log --date=iso`).
- Open questions: Which checks does the README's "verified end-to-end" sentence cover beyond those in commit messages? Where are the results of the unrecorded checks?
- Confidence: MEDIUM. STATED evidence alone.

### R6-S11-04  iPhone named as a tested Bluetooth source
- Question: S11.2
- What was attempted: an iPhone playing to the device during development.
- Presence: CURRENT (commit history).
- Outcome: PARTIAL. Stream detection and AVRCP metadata are stated as working with it; no model or iOS version is given, and no sentence states audio from it heard at a named output.
- Outcome evidence: STATED. Commit 0ad9f4e message: "iPhone now transitions to audio_active." and "Live-verified: track title/artist/album/position from a playing iPhone."
- Why it turned out that way: STATED for the detection defect found with it: "PipeWire >=1.x creates the phone's A2DP node as a STREAM (role=playback), invisible to 'pactl list short sources'" (commit 0ad9f4e message; src/bluetooth/manager.rs:820-825).
- Depends on: PipeWire 1.6.2 and BlueZ 5.85 on the reference machine (forinstall.md:61-62, 146-147); the adapter in use is not named in the commit.
- Open questions: Which iPhone model and iOS version? Which Bluetooth adapter, the onboard Broadcom or a dongle? Which codec? Was audio heard on each output while it played?
- Confidence: MEDIUM. STATED evidence alone.

### R6-S11-05  Other Bluetooth sources mentioned without a test statement
- Question: S11.2
- What was attempted: README.md:283-285 says "real phones" (plural, unnamed). Turntables, cassette decks and CD players are named as intended sources (README.md:20-22, 164-165; DECISIONS.md:13; commit 3624d19 message). install.sh:152 asserts that the Class of Device "is what makes iPhones list us as a speaker". A code comment refers to a past event with a turntable, "a silent alias failure once had a turntable rejecting the adapter because it advertised the hostname" (src/bluetooth/manager.rs:186-187), without saying on which build it happened. No Android device is named: `grep -rniE 'android|samsung|galaxy'` over tracked text files outside SOUNDSYNC-SPEC.md returns nothing, and "Pixel 9" appears only as a unit-test fixture name (src/bluetooth/device.rs:272-275).
- Presence: CURRENT (documents).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: NONE recorded.
- Open questions: Were any phones other than one iPhone used? Was a turntable or other outbound-connect source ever paired?
- Confidence: MEDIUM. Based on a repo-wide search with negative result.

### R6-S11-06  Non-Bluetooth test inputs: a 440 Hz tone and the optical line input
- Question: S11.2
- What was attempted: a 440 Hz tone played into the EQ sink, and the Mac mini's optical (iec958) capture source used for the line-in loopback.
- Presence: CURRENT (commit history).
- Outcome: PARTIAL. The tone is stated as reaching the visualiser; for the optical input only module load and unload are stated.
- Outcome evidence: STATED. Commit b19435c message: "440Hz tone through the EQ sink lights the correct bars end-to-end". Commit 9d619ed message: "line-in loopback load/unload verified against iec958 source".
- Why it turned out that way: UNKNOWN.
- Depends on: the reference machine's HDA device (forinstall.md:133-135).
- Open questions: How was the tone generated and routed? Was any signal present on the optical input?
- Confidence: MEDIUM. STATED evidence alone.

### R6-S11-07  Figures written down by the author; none is a measured latency, dropout count, load, memory, range or uptime
- Question: S11.3
- What was attempted: the repo records these figures. Stream bitrate at silence: "~350 B/s" (API.md:92). Chromecast buffering: "1–3 s" (README.md:47; DECISIONS.md:79), also present in the predecessor document (SOUNDSYNC-SPEC.md:2950, 3017). Opus encode time: "0.5–2 ms per frame" in a comment (src/audio/webrtc_audio.rs:204). Devices found: "22 + 19 devices" for Cast and AirPlay (commit 9d619ed), "scan found 19 devices" for Bluetooth (commit 4505247). Web bundle size: "~20 kB gzipped" (README.md:77). Host volume set to 40% (commit 9d619ed). Class of Device service bits before and after the WirePlumber change (forinstall.md:163-164). Disk use of toolchains and caches (forinstall.md:37-39). Layout overflow "0px" (commit 3629c21). Searches for measurements: `grep -rniE 'latency|dropout|cpu|memory|rssi|uptime'` over README.md, API.md, DECISIONS.md, BUILD-PLAN.md, forinstall.md, CHECKLIST.md and sonicflow-design-spec.md, which returns design text, field names and warnings and no measured value; and `git ls-files | grep -iE 'log|result|report|bench|csv'`, which returns nothing.
- Presence: CURRENT (documents, comments and commit history).
- Outcome: PARTIAL. Some figures are recorded; none comes with a method, a date of measurement or conditions, and none covers end-to-end latency, dropouts, processor load, memory, radio range or uptime.
- Outcome evidence: STATED. Citations above.
- Why it turned out that way: UNKNOWN.
- Depends on: the reference machine and its LAN for the counts.
- Open questions: Which of the figures were measured on this build and which were carried over from the predecessor document? What is the delay from phone to each output?
- Confidence: MEDIUM. STATED evidence; the absence is based on searches.

### R6-S11-08  Runtime counters and probes that could yield measurements, with no captured output
- Question: S11.3
- What was attempted: per WebRTC session, a log line every 60 s with frames sent, frames dropped, uptime and the expected count at 50 frames per second (src/audio/webrtc_audio.rs:219-222, 270-276). `GET /api/status` returns `uptime_secs` (src/web/routes.rs:27). The doctor script prints the scheduling policy of `pipewire` and the count of live Bluetooth sources (deploy/doctor.sh:49-50, 65-66). Device RSSI is read and shown in the device list (src/bluetooth/manager.rs:632; webui/src/components/Settings.tsx:628). The spectrum task logs dropped frames at debug level (src/audio/spectrum.rs:60-62).
- Presence: CURRENT. Citations above.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No output of any of these is stored.
- Why it turned out that way: UNKNOWN.
- Depends on: a running service and journal access.
- Open questions: What did the 60 s health lines show during the live checks?
- Confidence: MEDIUM. INFERRED from clear code.

### R6-S11-09  Known issue: outputs are not synchronised with each other
- Question: S11.4
- What was attempted: the author lists it under "What it is not": "Multiple outputs play together but may be seconds apart (Chromecast alone buffers 1–3 s). The UI says so." (README.md:46-47). The UI copy is at webui/src/components/Settings.tsx:515.
- Presence: CURRENT. Citations above.
- Outcome: PARTIAL. Stated by the author as playing together with an offset of up to seconds.
- Outcome evidence: STATED. README.md:46-47; DECISIONS.md:79.
- Why it turned out that way: STATED. Buffering in the Cast receiver; scope decision D7 (DECISIONS.md:75-80).
- Depends on: which outputs are combined.
- Open questions: See R6-S07-13 and R6-S08-21.
- Confidence: MEDIUM. STATED evidence alone.

### R6-S11-10  Known issue: the AAC Bluetooth codec entry does nothing on Ubuntu
- Question: S11.4
- What was attempted: `aac` is in the WirePlumber codec list (install.sh:201).
- Presence: CURRENT. install.sh:201.
- Outcome: FAILED for AAC; other codecs registered (R6-S07-01).
- Outcome evidence: STATED. forinstall.md:107-108: "\"aac\" in the codecs list is inert; phones fall back to SBC-XQ/LDAC/aptX".
- Why it turned out that way: STATED. "Ubuntu ships no AAC codec .so in /usr/lib/x86_64-linux-gnu/spa-0.2/bluez5/ (licensing)" (forinstall.md:107-108).
- Depends on: Ubuntu 26.04 packaging of `libspa-0.2-bluetooth`.
- Open questions: Which codec does an iPhone use with this device, given that AAC is absent?
- Confidence: MEDIUM. STATED evidence alone; the entry is still in the installed fragment.

### R6-S11-11  Known issue: AirPlay-2-only receivers are out of scope
- Question: S11.4
- What was attempted: AirPlay output is limited to what PipeWire's RAOP module handles: "AirPlay-2-pairing-only devices (HomePod) are unsupported by PipeWire RAOP and documented as such" (src/audio/airplay.rs:13-14); DECISIONS.md:103-104; README.md:31 ("One AirPlay-1-class target").
- Presence: CURRENT. Citations above.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The limit is stated as a property of PipeWire; no attempt against such a device is recorded.
- Why it turned out that way: UNKNOWN.
- Depends on: the PipeWire RAOP module version.
- Open questions: Of the 19 AirPlay devices discovered on the LAN (commit 9d619ed), how many fall in the unsupported class?
- Confidence: MEDIUM. STATED limitation without a test.

### R6-S11-12  Known issue: Bluetooth and 2.4 GHz WiFi on the same machine
- Question: S11.4
- What was attempted: the author warns that "Bluetooth and 2.4 GHz WiFi share a band and streaming over both causes dropouts" (README.md:107-109). The installer and doctor print an advisory when the default route is a wireless interface (install.sh:393-396; deploy/doctor.sh:59-63). On the reference machine the wired port was "currently unplugged" (forinstall.md:136-138).
- Presence: CURRENT. Citations above.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No dropout observed on this build is recorded; the statement is general.
- Why it turned out that way: UNKNOWN.
- Depends on: the reference machine's Broadcom WiFi (forinstall.md:22-23, 136) and onboard Bluetooth sharing the band.
- Open questions: Were dropouts heard during the live checks, which ran with the wired port unplugged?
- Confidence: MEDIUM. STATED warning without a recorded observation.

### R6-S11-13  Known issue: the 2011 onboard Broadcom controller
- Question: S11.4
- What was attempted: "The 2011-era Broadcom BT controller is supported best-effort; an RTL8761B-class USB dongle (TP-Link UB500 etc.) is the recommended radio" (README.md:105-106); "Old Broadcom BT firmware has known unpatched weaknesses (KNOB/BIAS class)" (README.md:276-277); DECISIONS.md:65-69; forinstall.md:131-132. The installer only checks for the dongle's firmware file (install.sh:130-132).
- Presence: CURRENT. Citations above.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No statement says which radio carried the tested streams or how the onboard controller behaved.
- Why it turned out that way: UNKNOWN.
- Depends on: Apple/Broadcom Bluetooth 4.0 controller on the Macmini5,3 (forinstall.md:131).
- Open questions: Was a dongle present during development? Did the onboard controller show any fault?
- Confidence: MEDIUM. STATED caution without a recorded result.

### R6-S11-14  Known issues listed for browsers and streams
- Question: S11.4
- What was attempted: the author records three behaviours. Autoplay policy leaves the player muted until a second tap (README.md:253-254; webui/src/webrtc.ts:6-9). "Firefox cannot decode raw ADTS AAC" (webui/src/webrtc.ts:197). An AAC stream opened during silence "trickles until audio starts. Normal." (API.md:92-93).
- Presence: CURRENT. Citations above.
- Outcome: PARTIAL. Each is handled by a described workaround (muted state with a chip, MP3 swap, documentation); results of the workarounds are not recorded (R6-S08-17).
- Outcome evidence: STATED. Citations above; commit 2641e55 message.
- Why it turned out that way: STATED. Browser autoplay policy and codec support (webui/src/webrtc.ts:6-9, 197); encoder behaviour at digital silence (API.md:92).
- Depends on: browser versions; ffmpeg's AAC encoder.
- Open questions: Does a Cast device tolerate the trickle when a session starts before the phone plays?
- Confidence: MEDIUM. STATED evidence alone.

### R6-S11-15  README troubleshooting list
- Question: S11.4
- What was attempted: README.md:236-254 lists: on a headless machine WirePlumber's Bluetooth monitor stops and "phones see no audio sink" unless seat monitoring is disabled as a profile feature; phones list the device but do not route audio when the Class of Device is not Audio/Video, which "may need one reboot"; paired but silent when `libspa-0.2-bluetooth` is missing "or the default sink got moved off the EQ/capture sink". The doctor script checks each (deploy/doctor.sh:20-24, 35-47).
- Presence: CURRENT. Citations above.
- Outcome: PARTIAL. The seat-monitoring cause is stated as found and corrected on the reference machine; the other entries are listed without a recorded occurrence in this repo.
- Outcome evidence: STATED. forinstall.md:94-104; commit bb2c661 message: "Audio Sink 110b now advertised with sbc/sbc_xq/aptx/aptx_hd/ldac endpoints."
- Why it turned out that way: STATED. No logind seat is active on an SSH-only machine (forinstall.md:101-103).
- Depends on: WirePlumber 0.5 configuration format (install.sh:86-91); the default sink remaining on the SonicFlow sinks.
- Open questions: What else on the machine can move the default sink, and how often did it happen? The app re-sets it only at EQ changes and at stream start (src/audio/pipeline.rs:110-129; src/bluetooth/manager.rs:763-771).
- Confidence: MEDIUM. STATED evidence alone.

### R6-S11-16  Open items the author marks as pending or untested
- Question: S11.4
- What was attempted: items left open in the author's own words: "Audible check pending a Bluetooth source." (commit 174ec2f); Cast and AirPlay "live sessions left for a supervised test" (commit 9d619ed); "Cast+AirPlay concurrency gets a validation spike (SoundSync G2: untested)." (DECISIONS.md:77); analog line-in "needs an ALSA profile/route change if ever required" (forinstall.md:134-135; BUILD-PLAN.md:90-91); [PENDING] markers on root-level install items (forinstall.md:55-65, 72-78, 84-88, 109-128). No known-issues, TODO or changelog file exists (`git ls-files | grep -iE 'changelog|issue|todo|notes'` returns nothing).
- Presence: CURRENT (documents and commit history).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No later commit or document closes these items by name.
- Why it turned out that way: UNKNOWN.
- Depends on: NONE recorded.
- Open questions: Which of these were completed after the commits that mention them, given the README's later "verified end-to-end" sentence (README.md:283-285, commit beead5a)?
- Confidence: MEDIUM. STATED items; closure is not evidenced.

### R6-S11-17  Predecessor defect register carried in the repo, with a list of defects to be addressed in this build
- Question: S11.4
- What was attempted: SOUNDSYNC-SPEC.md:2638-2971 is the predecessor's section "Issues, defects and design debt" (25 open defects per SOUNDSYNC-SPEC.md:36). DECISIONS.md:99-100 lists "SoundSync defects D1, D2 (parec 50 ms), D3, D7, D10, D11, D13, D20, D21 (+ D18 per D5 above)" under the heading "Fix during rebuild". Code comments tag the matching places: D2 (src/audio/capture.rs:14), D3 (src/audio/airplay.rs:161), D5 (src/audio/chromecast.rs:21-22), D8 (src/state/mod.rs:22-24), D11 (webui/src/webrtc.ts:2-4, 77), D13 and D21 (src/state/config.rs:15), D18 (src/audio/cast_stream.rs:15), D10 (API.md:131), D1 (BUILD-PLAN.md:47).
- Presence: CURRENT. Citations above.
- Outcome: UNVERIFIED
- Outcome evidence: NONE for the result of each change. The tags state intent; no test or statement shows a predecessor defect reproduced and then gone in this build.
- Why it turned out that way: UNKNOWN.
- Depends on: the accuracy of the predecessor document, whose source repo is not part of this one (SOUNDSYNC-SPEC.md:1-8).
- Open questions: D20 is listed, and no volume readback code is present (R6-S07-09): was it handled another way? Which of the remaining predecessor defects apply to this code?
- Confidence: LOW. INFERRED from tags and a document about a different codebase.

---

## S12 Security and exposure

### R6-S12-01  HTTP listener: web page, REST, WebSocket and audio streams on one port, no authentication
- Question: S12.1
- What was attempted: one TCP listener on `bind_address:port` (src/web/mod.rs:115-124), defaults "0.0.0.0" and 8080 (src/state/config.rs:37-38); the installer writes `bind_address = "0.0.0.0"` and a port it finds free (install.sh:316-334). Plain HTTP; the listener is a bare `TcpListener` passed to `axum::serve` with no TLS layer (src/web/mod.rs:121-123). "No auth, no rate limiting." (API.md:14). CORS is permissive for every route and the streams add `Access-Control-Allow-Origin: *` (src/web/mod.rs:110; src/audio/cast_stream.rs:123). Through this port an unauthenticated client can control playback, outputs and the EQ, listen to the live audio streams, and operate Bluetooth: scan, connect, disconnect, remove a paired device, rename the adapter, switch adapter and toggle auto-pair (src/web/mod.rs:46-106; src/web/routes.rs:45-78, 87-188).
- Presence: CURRENT. Citations above.
- Outcome: WORKED. The listener answers as designed, with the exposure the author describes.
- Outcome evidence: STATED. README.md:261-262: "The HTTP API has **no auth** and binds `0.0.0.0` by default. Anyone on the LAN can control playback, outputs, EQ and Bluetooth pairing." Commit 3d4afd4 message (routes smoke-tested).
- Why it turned out that way: STATED as a design choice: "SonicFlow trades authentication for zero-friction listening on a **trusted LAN**" (README.md:258-259); "The audio streams are unauthenticated by protocol necessity (Chromecast pulls them over plain HTTP)" (README.md:268-269); DECISIONS.md:59-61.
- Depends on: the LAN being trusted; `bind_address` as the only restriction offered (README.md:263-264); with `bind_address = "127.0.0.1"` the Cast pull URL, built from the LAN address, would not be reachable (src/audio/chromecast.rs:139-140).
- Open questions: With permissive CORS and no authentication, can a web page from another origin opened on a LAN browser call the API? Is any client limit applied to the stream routes, each of which starts two processes (src/audio/cast_stream.rs:49-98)?
- Confidence: HIGH. STATED evidence that the code agrees with.

### R6-S12-02  WebRTC transport: library-chosen UDP ports and a public STUN server
- Question: S12.1
- What was attempted: each Local Player session creates a peer connection with the ICE server `stun:stun.l.google.com:19302` on the server and in the browser (src/audio/webrtc_audio.rs:128-134; webui/src/webrtc.ts:117-119). The API is built with default media engine and interceptors and no setting for port range or interfaces (src/audio/webrtc_audio.rs:55-65), so the UDP ports and candidate addresses are whatever the `webrtc` crate picks. Sessions are keyed by a UUID minted per WebSocket connection, and signalling is delivered only to that connection (src/web/ws.rs:18-19, 206-233). No authentication applies to opening a session.
- Presence: CURRENT. Citations above.
- Outcome: WORKED on the LAN used for testing.
- Outcome evidence: STATED. Commit 2641e55 message: "Verified over LAN in headless Chromium and real Firefox 154: WebRTC connected".
- Why it turned out that way: UNKNOWN.
- Depends on: outbound UDP to the STUN host being possible or harmlessly failing; the firewall rules the installer adds cover the HTTP port and mDNS only (install.sh:277, 378).
- Open questions: With ufw active, do WebRTC media packets pass? Is the STUN request needed on a single LAN, and what does the session do with no internet access?
- Confidence: MEDIUM. STATED evidence for connectivity; port behaviour is INFERRED from the absence of settings.

### R6-S12-03  mDNS discovery traffic for Cast and AirPlay
- Question: S12.1
- What was attempted: the installer enables `avahi-daemon` (install.sh:284). The app runs `avahi-browse` for `_googlecast._tcp` and `_raop._tcp` (src/audio/chromecast.rs:307-317; src/audio/airplay.rs:303-313). When avahi returns no Cast device, an in-process `mdns-sd` daemon browses for up to 10 s (src/audio/chromecast.rs:295-305, 392-441), with the comment that "a second responder in this process is unreliable" (src/audio/chromecast.rs:295-296). The app advertises no service of its own (`grep -rn 'register\|publish' src/audio/chromecast.rs src/audio/airplay.rs` shows only state events).
- Presence: CURRENT. Citations above.
- Outcome: PARTIAL. Discovery through avahi is stated as verified; the `mdns-sd` branch has no recorded result.
- Outcome evidence: STATED. Commit 9d619ed message: "Cast/AirPlay discovery verified on the real LAN (22 + 19 devices, avahi escape decoding + IPv6 skip + dedup all correct)".
- Why it turned out that way: UNKNOWN.
- Depends on: avahi-daemon owning UDP 5353 (src/audio/chromecast.rs:295); `avahi-utils` (install.sh:100).
- Open questions: NONE
- Confidence: MEDIUM. STATED evidence for one branch.

### R6-S12-04  Firewall changes made by the installer
- Question: S12.1
- What was attempted: only when `ufw` is installed and active: `ufw allow 5353/udp` and, after the service answers, `ufw allow <port>/tcp`, both recorded for removal (install.sh:274-283, 375-379; uninstall.sh:145-150). The rules carry no source restriction. forinstall.md:115-116 describes the requirement as "allow inbound TCP <http-port> from LAN" for "ufw/firewalld"; install.sh contains no firewalld handling.
- Presence: CURRENT. Citations above.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. forinstall.md:115-116 marks the item [PENDING]; no statement says whether ufw was active on the reference machine.
- Why it turned out that way: UNKNOWN.
- Depends on: ufw as the firewall front end.
- Open questions: Was a firewall active during any of the live checks?
- Confidence: MEDIUM. INFERRED from clear script code.

### R6-S12-05  Outbound connections made by the app and the installer
- Question: S12.1
- What was attempted: album-art lookups through `curl` to the iTunes search API, Deezer, MusicBrainz and the Cover Art Archive, sending artist with title or album; on by default and switched by `art_online` (src/art.rs:87-107, 253-337; src/state/config.rs:28-31, 42). A TLS connection to the Cast device made with `CastDevice::connect_without_host_verification` (src/audio/chromecast.rs:209-213). STUN requests (R6-S12-02). A UDP socket "connected" to a public address to learn the local address, used only when `hostname -I` gives nothing (src/audio/cast_stream.rs:150-168). The installer fetches apt packages, the rustup script piped to `sh` as the service user, crates and npm packages (install.sh:111-112, 125, 289, 292).
- Presence: CURRENT. Citations above.
- Outcome: PARTIAL. The art lookups are stated as tested live; the other connections have no recorded result of their own.
- Outcome evidence: STATED. Commit 738f12c message: "Live-tested online cascade (iTunes 600px art downloaded; Deezer fielded-query quirk fixed; MB release found)".
- Why it turned out that way: UNKNOWN.
- Depends on: internet access; `curl` (src/art.rs:263). README.md:270-273 states that track metadata leaves the LAN unless `art_online = false`.
- Open questions: Is host verification skipped for the Cast device because such devices present certificates that do not match their address, or for another reason? The code gives none.
- Confidence: MEDIUM. STATED evidence for one item; the rest INFERRED from clear code.

### R6-S12-06  Service process: an ordinary user account with audio and bluetooth groups
- Question: S12.2
- What was attempted: `User=` and `Group=` are the installing user (by default `$SUDO_USER`), with `SupplementaryGroups=audio bluetooth`, `LimitRTPRIO=95` and `LimitMEMLOCK=infinity` (deploy/sonicflow.service:12-14, 21-22; install.sh:40, 353-354). The unit has no sandboxing or capability directives (`grep -nE 'NoNewPrivileges|Protect|Private|Capabilit|ReadOnly|SystemCall' deploy/sonicflow.service` returns nothing). The process and its children (`pactl`, `parec`, `ffmpeg`, `pipewire -c`, `pw-link`, `curl`, `avahi-browse`, `btmgmt`, `hcitool` and others; `grep -rhoE 'Command::new\("[a-z-]+"\)' src`) run as that user inside its session environment (deploy/sonicflow.service:16-18). The binary, the pre-start sweep script and the web files under `/opt/sonicflow` are owned by the same user (install.sh:302-308). Subprocess arguments are passed as argument vectors; `grep -rnE '"sh"|"bash"|sh -c' src` finds only a comment (src/audio/cast_stream.rs:15).
- Presence: CURRENT. Citations above.
- Outcome: UNVERIFIED
- Outcome evidence: NONE for the installed unit. README.md:274-275 states: "No shell string interpolation anywhere — subprocess arguments are always passed as vectors."
- Why it turned out that way: STATED for the user choice: the unit "Runs as the PipeWire session user and talks to that user's PipeWire/pipewire-pulse over /run/user/<uid>" (deploy/sonicflow.service:1-3).
- Depends on: the service user being the owner of the PipeWire session and normally a sudo-capable login account (install.sh:40); membership of the `bluetooth` group for BlueZ D-Bus access.
- Open questions: Is the service user on the reference machine the administrator's own login account? The process is reachable without authentication from the LAN (R6-S12-01) and runs with that account's file access.
- Confidence: MEDIUM. INFERRED from clear configuration and a repo-wide search.

### R6-S12-07  Installer and uninstaller: root, with shell strings for user-session work
- Question: S12.2
- What was attempted: both scripts refuse to run without root (install.sh:37; uninstall.sh:59). User-session commands go through `run_as_user`, which calls `sudo -u <user> ... bash -c "$*"` (install.sh:56-64; uninstall.sh:65-71); several calls embed paths in the string, such as the repo directory and the override path (install.sh:222-223, 289, 292). Root writes go to `/etc/bluetooth/main.conf`, `/etc/wireplumber`, `/etc/security/limits.d`, `/etc/systemd`, `/etc/sonicflow`, `/opt/sonicflow` and `/var/lib/sonicflow` (install.sh:153-181, 185-212, 245-256, 301-308, 329-346, 353-354, 66-67). Rustup is installed by piping a downloaded script to `sh` as the service user (install.sh:125).
- Presence: CURRENT. Citations above.
- Outcome: UNVERIFIED
- Outcome evidence: NONE for a full run (R6-S10-01).
- Why it turned out that way: STATED for the split: "runs as root, delegates user-session work (systemctl --user, cargo, per-user config) to the service user" (install.sh:11-13).
- Depends on: `sudo`; a checkout path without characters that break the quoted strings.
- Open questions: The README's sentence on shell interpolation (README.md:274-275) sits in the app's security section: does it cover the scripts?
- Confidence: MEDIUM. INFERRED from clear script code.

### R6-S12-08  Secrets in the repo and in the app's own files: none found; locations of sensitive-kind items
- Question: S12.3
- What was attempted: the app has no accounts, passwords, tokens or API keys: the config struct has six fields, none a credential (src/state/config.rs:16-32), and the album-art services are used without keys (src/art.rs:279, 298, 315). Search: `grep -rnIiE 'password|passwd|secret|token|api[_-]?key|BEGIN [A-Z ]*PRIVATE'` over tracked files outside lock files returned only code identifiers, comments and one test string (src/dsp/presets.rs:235). Items of a sensitive kind, by location only: device-address-shaped unit-test fixtures (src/bluetooth/manager.rs:875-877, 895-896; src/bluetooth/constants.rs:66-93; src/audio/airplay.rs:473-492), placeholder address strings (src/state/mod.rs:137; src/web/ws.rs:306; src/audio/host_out.rs:255), a development-machine path said to hold command-line credentials for the code host (forinstall.md:49-52; kind: CLI auth credentials, value not in the repo), a local account name (forinstall.md:156, 165; kind: user name), and the author field of each commit (git metadata; kind: e-mail address).
- Presence: CURRENT. Citations above.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The finding rests on a search by this audit, not on an artefact or statement of the author.
- Why it turned out that way: UNKNOWN. README.md:44 states the product has "no accounts".
- Depends on: the no-authentication design (R6-S12-01).
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from a repo-wide search with negative result.

### R6-S12-09  Bluetooth pairing keys: left to BlueZ, never read or written by the repo
- Question: S12.3
- What was attempted: pairing, trust and removal go through BlueZ over D-Bus (src/bluetooth/agent.rs:111-126; src/bluetooth/manager.rs:436-505). The repo names no BlueZ storage path: `grep -rn 'var/lib/bluetooth\|link.key\|LinkKey'` over tracked files returns nothing. Link keys therefore sit wherever the BlueZ daemon keeps them, under that daemon's protection. The uninstaller reverts `main.conf` keys and does not touch pairing records (uninstall.sh:113-137; `grep -n bluetooth uninstall.sh`).
- Presence: CURRENT (absence of handling in the repo).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: BlueZ defaults on the host; the agent's `NoInputNoOutput` capability and the default-on auto-pair setting decide which devices obtain a key (src/bluetooth/agent.rs:7-9, 122; src/state/config.rs:41).
- Open questions: After `uninstall.sh --purge`, do paired phones remain paired with the host?
- Confidence: MEDIUM. INFERRED from a repo-wide search with negative result.

### R6-S12-10  App state, config and runtime key material: default file permissions, nothing encrypted
- Question: S12.3
- What was attempted: `/etc/sonicflow/config.toml` is written by root with a shell redirect (install.sh:329-342). `~/.config/sonicflow/state.toml` and preset files are written with `std::fs::write` (src/state/config.rs:224-238; src/dsp/presets.rs:153-154). The art cache is under `~/.cache/sonicflow/artcache` (src/art.rs:349-351) and a temporary image is written to the system temp directory under a name built from the process id (src/art.rs:180). The installer's change log and the `main.conf` backup are under `/var/lib/sonicflow` (install.sh:21-23, 66-67, 155). No file mode is set anywhere except `install -m 755` for the three installed executables (`grep -rnE 'chmod|umask|set_permissions|install -m' src install.sh uninstall.sh deploy`). `state.toml` holds a device name, an adapter id, the last Cast device id, the last AirPlay name and EQ values (src/state/config.rs:152-173), which are identifiers and settings. WebRTC and Cast TLS key material is created in memory by the `webrtc` and `rust_cast`/`rustls` crates; the repo supplies no certificate or key file (src/audio/webrtc_audio.rs:128-140; src/main.rs:37-42).
- Presence: CURRENT. Citations above.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: the umask of root and of the service user at write time.
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from clear code and a search for permission calls.

---

## Tally

| Stage | Questions | Answered with findings | Answered NOT FOUND | Finding records |
|---|---|---|---|---|
| S07 Processing | 3 | 3 | 0 | 15 |
| S08 Output | 4 | 4 | 0 | 22 |
| S09 User interface and control surfaces | 4 | 4 | 0 | 12 |
| S10 Lifecycle and operations | 6 | 6 | 0 | 18 |
| S11 Testing and measured behaviour | 4 | 4 | 0 | 17 |
| S12 Security and exposure | 3 | 3 | 0 | 10 |
| Total | 24 | 24 | 0 | 94 |

Questions answered with findings: 24. Questions answered NOT FOUND: 0. Total: 24, equal to the 24 questions in the brief.

Finding records: 94. NOT FOUND records: 0.
