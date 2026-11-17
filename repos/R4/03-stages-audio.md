# R4 stage audit: audio stages S07 to S12 (Aurilis)

- Repo: /home/user/Aurilis, HEAD d740bc2b39836a0af00d8ad35144d31511bc9060 (branch `main` at the same commit).
- Audited 2026-11-17 with read-only commands only (file reads, `git log`, `git show`, `git diff --stat`, `git grep`, `git ls-files`). Nothing was built or run.
- All paths are relative to the repo root unless they start with `/`.
- History facts that bear on every record: all code arrived in one commit, c98d8d3; merge commit d740bc2 has the same tree (`git diff --stat c98d8d3 d740bc2` prints nothing). Presence CURRENT therefore means "in the tree at d740bc2".
- Result evidence available in the repo: none OBSERVED. The tree holds no log file, test output, CI configuration or measurement file (`git ls-files` filtered for `log|result|report|bench|ci|workflow`: no hits). The only result statement is in the message of commit c98d8d3: "43 passing tests (unit + integration)". Owner notes for R4: "none" (/home/user/audio/00-manifest.md:37-39).
- No text addressed to an AI agent was found in the files read.

---

## S07 Processing

### R4-S07-01  Bluetooth transport bytes read as 16-bit PCM with no decode step
- Question: S07.1
- What was attempted: the A2DP endpoint is registered with the SBC codec id and SBC capability bytes (internal/bluetooth/a2dp_sink.go:15, 110-121) and `SelectConfiguration` returns SBC parameters (internal/bluetooth/a2dp_sink.go:156-206). The reader then takes the bytes from the acquired transport fd, skips 13 bytes when the first byte is 0x80 (internal/audio/bluetooth_input.go:204-209), and interprets the remainder as S16_LE PCM scaled to float64 (internal/audio/bluetooth_input.go:211-220) before `ringBuf.Write` (line 222). No decoder is called between `f.Read` (line 189) and the PCM interpretation. The repo holds no SBC decoder: `git grep -i` for `sbc`, `decode`, `libsbc` finds only comments and the endpoint code; go.mod:5-10 lists no codec module; the only cgo link flag is `-lasound` (internal/audio/alsa.go:4).
- Presence: CURRENT (internal/audio/bluetooth_input.go:166-224; started from cmd/aurilis/main.go:131-136 and internal/audio/bluetooth_input.go:106)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. The comments disagree on what the fd delivers: "It reads SBC-encoded audio from BlueZ, decodes it, and writes PCM to the ring buffer." (internal/audio/bluetooth_input.go:16) against "The transport FD provides decoded PCM data in S16_LE format." (internal/audio/bluetooth_input.go:165). No statement in the repo records what was heard.
- Depends on: the endpoint codec being SBC (a2dp_sink.go:15, 119); the assumption written at bluetooth_input.go:163-165; the 13-byte header length and 0x80 first-byte test at bluetooth_input.go:204-209; BlueZ stated only as "BlueZ 5.x" (README.md:83).
- Open questions: What byte format does the acquired fd deliver for an endpoint registered with codec 0x00? Was audio from any Bluetooth source ever heard through `/audio/stream`? What happens to an odd trailing byte, given `samples := (n - offset) / 2` (bluetooth_input.go:212) carries nothing over to the following read?
- Confidence: MEDIUM. INFERRED from clear code and searches; no result evidence exists.

### R4-S07-02  Sample format conversion around a float64 internal format
- Question: S07.1
- What was attempted: both inputs convert signed 16-bit samples to float64 by dividing by 32768.0 (Bluetooth: internal/audio/bluetooth_input.go:217-220; line-in: internal/audio/alsa.go:245-248) and write interleaved float64 into one ring buffer (internal/pipeline/ringbuffer.go:8-9, 44-55). At output, `floatToPCM` clamps to [-1.0, 1.0] and converts to S16_LE or S24_LE (internal/stream/http_stream.go:179-221); any `bit_depth` other than 16 or 24 takes the 16-bit path (http_stream.go:185-187).
- Presence: CURRENT (internal/stream/http_stream.go:99, 144)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: `bit_depth` default 16 (internal/config/config.go:43); input assumed 16-bit at both sources (alsa.go:23; bluetooth_input.go:172).
- Open questions: With a `bit_depth` value other than 16 or 24, the WAV header carries the configured value (http_stream.go:171) while data is 16-bit (http_stream.go:185-187); was any value other than 16 ever used?
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S07-03  Ten-band peaking equaliser built from cascaded biquad filters
- Question: S07.1
- What was attempted: ten peaking filters, one per configured band, each a Direct Form II Transposed biquad with per-channel state (internal/dsp/equalizer.go:20-38; internal/dsp/biquad.go:46-72, 129-145). Default bands 31 Hz to 16 kHz, gain 0, Q 1.41 (internal/config/config.go:52-65). Gain is clamped to ±12 dB (equalizer.go:81-86). Processing runs inside each HTTP stream handler, in place on the chunk just read (internal/stream/http_stream.go:92, 140), and is skipped when the EQ is disabled (equalizer.go:46-48). Low-shelf and high-shelf coefficient functions exist (biquad.go:74-122) and are called only from tests (internal/dsp/biquad_test.go:121, 130).
- Presence: CURRENT (internal/stream/http_stream.go:92; created at cmd/aurilis/main.go:118-119)
- Outcome: UNVERIFIED
- Outcome evidence: NONE for received audio. The only result statement is commit c98d8d3, "43 passing tests (unit + integration)", which covers synthetic-signal tests of this unit (internal/dsp/equalizer_test.go:36-161; internal/dsp/biquad_test.go:8-147; test/integration_test.go:143-180).
- Why it turned out that way: UNKNOWN.
- Depends on: coefficients computed for `cfg.SampleRate` (main.go:118), default 48000 (config.go:41); one Equalizer instance shared by every listener (main.go:181; see R4-S08-12).
- Open questions: Was the EQ ever heard on live audio? Is the filter math still correct when the source negotiates 44.1 kHz (see R4-S07-06)?
- Confidence: MEDIUM. INFERRED from clear code; the test-pass statement is STATED and covers isolated signals only.

### R4-S07-04  Software volume with per-sample ramp
- Question: S07.1
- What was attempted: a gain between 0.0 and 1.0 multiplied into every sample after the EQ (internal/dsp/volume.go:28-55; call at internal/stream/http_stream.go:93, 141). Changes ramp by 0.001 per interleaved sample (volume.go:23, 34-48); the comment calls this a "Smooth 1ms-scale ramp" (volume.go:23). Default 0.8 (internal/config/config.go:44). Set from the WebSocket command `cmd_set_volume` (internal/websocket/hub.go:438-448).
- Presence: CURRENT (internal/stream/http_stream.go:93)
- Outcome: UNVERIFIED
- Outcome evidence: NONE for received audio. Commit c98d8d3 states "43 passing tests (unit + integration)"; the unit tests for this file use constant-value buffers (internal/dsp/volume_test.go:8-118).
- Why it turned out that way: UNKNOWN.
- Depends on: one VolumeProcessor instance shared by every listener (cmd/aurilis/main.go:121, 181). No link to source-device volume was found (searched `avrcp`, `MediaPlayer1`, `Volume` in `internal/bluetooth/`: only the unused constant at internal/bluetooth/bluez.go:18).
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S07-05  Mono mixdown and FFT for visualisation
- Question: S07.1
- What was attempted: after EQ and volume, each stream handler passes the chunk to an analyser (internal/stream/http_stream.go:96, 142) that averages channels to mono, applies a Hann window, runs a radix-2 FFT of size 2048 and stores a dB spectrum and waveform (internal/dsp/analyzer.go:45-87, 150-186; size at cmd/aurilis/main.go:123). The audio sent to the listener is not altered by this step (analyzer.go:55-66 writes to its own buffers). The WebSocket hub reads the stored data every 50 ms (internal/websocket/hub.go:273-303).
- Presence: CURRENT (internal/stream/http_stream.go:96)
- Outcome: UNVERIFIED
- Outcome evidence: NONE for received audio. Commit c98d8d3 states "43 passing tests (unit + integration)"; analyser tests use generated sine waves (internal/dsp/analyzer_test.go:29-63, 104-124).
- Why it turned out that way: UNKNOWN.
- Depends on: an HTTP listener being attached. INFERRED: `Analyze` is called only from the two stream handlers (http_stream.go:96, 142), so with no listener the spectrum and waveform are not refreshed.
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S07-06  Single configured sample rate through the whole pipeline, no resampler
- Question: S07.1
- What was attempted: one `sample_rate` value (default 48000, internal/config/config.go:41) sizes the ring buffer, sets EQ coefficients, opens the ALSA device and labels the output (cmd/aurilis/main.go:114, 118, 127, 181-182). No resampling code exists (`git grep -i resampl`: no hits). The A2DP endpoint selects 48 kHz when the source offers it and otherwise 44.1 kHz (internal/bluetooth/a2dp_sink.go:165-172); the selected bytes are logged (a2dp_sink.go:204) and not passed on. The transport callback receives the properties and uses only the path (main.go:131-136).
- Presence: CURRENT (cmd/aurilis/main.go:114-127, 181-182)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: the source offering 48 kHz. INFERRED: when the 44.1 kHz branch at a2dp_sink.go:168-169 is taken, nothing changes the 48000 value written into the WAV header (internal/stream/http_stream.go:168), because the header reads only `s.sampleRate` set at start-up. The ALSA open uses `set_rate_near` and does not return the granted rate (internal/audio/alsa.go:29).
- Open questions: Did any tested source negotiate 44.1 kHz, and what was heard?
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S07-07  Exclusive source selection, no mixing
- Question: S07.1
- What was attempted: Bluetooth and line-in inputs share one ring buffer (cmd/aurilis/main.go:126-127). Only one is started at boot, by `audio_source` (main.go:148-157). A source change stops both, resets EQ and volume state, and starts the chosen one (main.go:160-178). No mixing code exists; the ring buffer is documented as "single-producer" (internal/pipeline/ringbuffer.go:8).
- Presence: CURRENT (cmd/aurilis/main.go:160-178)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: source values `"bluetooth"` and `"linein"` (main.go:149, 153, 169, 173); any other value stops both inputs and starts none (INFERRED from the switch at main.go:168-177, which has no default branch).
- Open questions: The ring buffer is not reset on a source change (`RingBuffer.Reset`, internal/pipeline/ringbuffer.go:136-147, has no caller outside tests); was a switch during playback ever tried?
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S07-08  Buffering stages that add delay; no figure and no compensation
- Question: S07.2
- What was attempted: delay arises at these points in the code. (a) Ring buffer sized from `buffer_size_ms` multiplied by 10 (cmd/aurilis/main.go:114); with defaults 100 ms, 48000 Hz, 2 channels this is 96000 samples, one second (INFERRED by arithmetic from internal/pipeline/ringbuffer.go:34-41 and internal/config/config.go:41-42, 48). (b) A new listener starts at the current write position (ringbuffer.go:59-69). (c) Each stream handler wakes on a 50 ms ticker and reads up to 100 ms of samples per wake (internal/stream/http_stream.go:73-75, 84). (d) The browser `Audio` element (web/static/js/app.js:366), whose buffering is outside the repo. (e) Line-in capture period of 1024 frames (main.go:127). No latency figure is measured or stated anywhere, and no compensation code exists (`git grep -i latency`: one hit, README.md:297).
- Presence: CURRENT (cmd/aurilis/main.go:114; internal/stream/http_stream.go:73-75)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: `buffer_size_ms` (config.go:31, 48) and the fixed factor of 10 (main.go:114, mirrored in test/integration_test.go:21); the fixed 50 ms ticker; the browser used.
- Open questions: What end-to-end delay did the author experience between the source device and the browser? Does the ring buffer size affect delay at all for a listener that keeps up, given readers start at the write position?
- Confidence: MEDIUM. INFERRED from clear code; no measurement exists.

### R4-S07-09  README troubleshooting entry for latency and dropouts
- Question: S07.2
- What was attempted: the README has a heading "Audio latency/dropouts" with three remedies: "Increase buffer size in config (`buffer_size_ms`)", "Reduce WiFi/Bluetooth interference", "On Raspberry Pi, use a USB Bluetooth adapter instead of built-in" (README.md:297-301). No figure accompanies it.
- Presence: CURRENT (README.md:297-301)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. The README does not say whether latency or dropouts were observed.
- Depends on: `buffer_size_ms` being read only at start-up (cmd/aurilis/main.go:114), so a changed value takes effect after a restart (INFERRED: no other reader of the field exists per `git grep BufferSizeMs`).
- Open questions: Was the entry written from an observed problem or as general guidance? Was a Raspberry Pi ever used?
- Confidence: MEDIUM. STATED in documentation only.

### R4-S07-10  NOT FOUND
- Question: S07.3
- Searched: `git grep -n -i` over the whole tree (go.sum and style.css excluded) for `drift`, `clock`, `resampl`, `jitter`, `underrun`, `overrun`: no hits. `sync`: 23 hits, all the Go `sync` package or `sync.` types. Read in full: internal/pipeline/ringbuffer.go, internal/stream/http_stream.go, internal/audio/bluetooth_input.go, internal/audio/alsa.go, README.md.
- Nearest related material: internal/pipeline/ringbuffer.go:95-99 (a reader more than one buffer behind is moved forward, dropping samples); internal/stream/http_stream.go:84-87 (nothing is written when no samples are available); internal/stream/http_stream.go:75 (output paced by a wall-clock ticker, not by the source).

---

## S08 Output

### R4-S08-01  HTTP WAV stream endpoint
- Question: S08.1
- What was attempted: route `/audio/stream` serves a continuous WAV response fed from the ring buffer through EQ and volume (internal/server/server.go:56; internal/stream/http_stream.go:44-107). README lists it as "Browser-compatible WAV streaming" (README.md:244). It is always registered; there is no enable switch and no output selector in the config (internal/config/config.go:16-32). A listener selects it by requesting the URL.
- Presence: CURRENT (internal/server/server.go:56)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: the HTTP port (default 8080, config.go:36); an HTTP client that plays an open-ended WAV.
- Open questions: Was the stream ever played in a browser or another client, and in which?
- Confidence: MEDIUM. INFERRED from clear code. The package has no test file (internal/stream/ holds only http_stream.go).

### R4-S08-02  HTTP raw PCM endpoint
- Question: S08.1
- What was attempted: route `/audio/raw` serves the same processed samples without a WAV header (internal/server/server.go:57; internal/stream/http_stream.go:109-151). README: "Raw L16 PCM for advanced consumers" (README.md:245). No client for it exists in the repo (`git grep -n "audio/raw"`: server.go:57 and README.md:245 only).
- Presence: CURRENT (internal/server/server.go:57)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: the same port and processing chain as R4-S08-01.
- Open questions: Was any consumer ever pointed at this endpoint?
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S08-03  Browser playback from the Web UI
- Question: S08.1
- What was attempted: a playback bar with a play button (web/static/index.html:121-135) creates `new Audio('/audio/stream')` and calls `play()` (web/static/js/app.js:359-394). The sound device is whatever the browser uses on the viewing machine. Pressing the button again pauses and clears the element (app.js:396-404).
- Presence: CURRENT (web/static/js/app.js:366, 386)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: a user gesture, per the comment "Start playback (requires user gesture for autoplay policy)" (app.js:385); the browser's handling of a WAV with a declared maximum length. The comment "Use AudioContext for Safari/iOS compatibility" (app.js:365) sits above code that uses an `Audio` element; the `audioCtx` variable (app.js:9) is never assigned.
- Open questions: Which browsers were tried? Was the `AudioContext` path written and dropped, or never written?
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S08-04  Output interface naming local playback, with no implementation
- Question: S08.1
- What was attempted: an `AudioOutput` interface with `Start`, `Stop`, `Name`, commented "All audio outputs (HTTP stream, local playback) must implement this interface." (internal/pipeline/interfaces.go:16-25), also listed in README.md:69. No type implements or references it (`git grep -n AudioOutput`: interfaces.go:16, 18 and README.md:69 only); `HTTPStreamer` has no `Start`, `Stop` or `Name` method (internal/stream/http_stream.go:19-151). No local sound-device playback exists: the only `snd_pcm_open` is for capture (internal/audio/alsa.go:13), and `aplay` appears only as a presence check in the installer (scripts/install.sh:192).
- Presence: DISABLED. Declared and unreferenced (internal/pipeline/interfaces.go:16-25)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. The commit is titled "Phase 1" (commit c98d8d3); no later phase is described anywhere in the repo.
- Depends on: nothing at run time; the interface is not on any call path.
- Open questions: Was local playback planned for a later phase?
- Confidence: MEDIUM. INFERRED from searches over the whole tree.

### R4-S08-05  WAV-over-HTTP format
- Question: S08.2
- What was attempted: HTTP response with `Content-Type: audio/wav`, `Cache-Control: no-cache, no-store`, `Connection: keep-alive`, `Access-Control-Allow-Origin: *` and a hand-set `Transfer-Encoding: chunked` header (internal/stream/http_stream.go:60-64). A 44-byte RIFF header declares PCM format 1 with the configured channels, rate and bit depth and a data size of 0x7FFFFFFF (http_stream.go:155-176). Body: interleaved little-endian PCM, 16-bit by default, written and flushed every 50 ms tick that has data (http_stream.go:73-105). Defaults: 48000 Hz, 2 channels, 16 bit (internal/config/config.go:41-43). The comment gives the reason for WAV: "for maximum browser compatibility (avoids autoplay blocking issues)" (http_stream.go:18). No compressed encoder exists (`git grep -i` for `mp3`, `opus`, `flac`, `aac`: no hits).
- Presence: CURRENT (internal/stream/http_stream.go:46-107)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: Go `net/http` with a `Flusher` (http_stream.go:47-51); `WriteTimeout: 0` on the server (internal/server/server.go:79).
- Open questions: How do browsers treat the 0x7FFFFFFF length when the stream runs past that many bytes? Does the hand-set `Transfer-Encoding` header interact with the header the Go server writes itself?
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S08-06  Raw PCM-over-HTTP format
- Question: S08.2
- What was attempted: HTTP response with `Content-Type: audio/L16;rate=<rate>;channels=<n>` (internal/stream/http_stream.go:120), no header bytes, body produced by the same `floatToPCM` call as the WAV route (http_stream.go:144), which writes little-endian samples (http_stream.go:201).
- Presence: CURRENT (internal/stream/http_stream.go:110-151)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: `bit_depth`; the content type says L16 whatever the configured depth (http_stream.go:120 against 144).
- Open questions: Does the little-endian byte order written at http_stream.go:201 match what a consumer of the declared `audio/L16` type expects?
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S08-07  Slow listener: reader moved forward when more than one buffer behind
- Question: S08.3
- What was attempted: each listener has its own read position (internal/pipeline/ringbuffer.go:19-21, 59-69). The writer never waits for readers and overwrites old samples (ringbuffer.go:44-55). When a reader is more than the buffer size behind, its position jumps to one buffer behind the writer and the skipped samples are lost (ringbuffer.go:95-99). A handler reads at most 100 ms per 50 ms tick (internal/stream/http_stream.go:73-75, 84).
- Presence: CURRENT (internal/pipeline/ringbuffer.go:95-99)
- Outcome: UNVERIFIED
- Outcome evidence: NONE for a live listener. Commit c98d8d3 states "43 passing tests (unit + integration)"; `TestConsumerFallsBehind` checks that 100 samples come back after 200 are written to a 100-sample buffer (internal/pipeline/ringbuffer_test.go:105-124).
- Why it turned out that way: UNKNOWN.
- Depends on: ring buffer size (R4-S07-08). INFERRED: `w.Write` at http_stream.go:101 blocks while the client's socket is full, since `WriteTimeout` is 0 (internal/server/server.go:79), so a stalled client holds its handler until the connection closes.
- Open questions: Is the skip audible, and was it ever triggered in use?
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S08-08  Listener gone: handler ends and its reader is removed
- Question: S08.3
- What was attempted: the handler returns when the request context is cancelled (internal/stream/http_stream.go:80-82, 131-132) or when a write fails (http_stream.go:101-103, 145-147); a deferred call removes the reader (http_stream.go:54, 118). Connect and disconnect are logged for the WAV route only (http_stream.go:56, 81).
- Presence: CURRENT (internal/stream/http_stream.go:53-54, 80-82)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: Go `net/http` request-context cancellation.
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S08-09  No input data: the handler writes nothing
- Question: S08.3
- What was attempted: when a tick finds no new samples the handler loops without writing (internal/stream/http_stream.go:84-87, 134-137). No silence is generated. With no listener attached, inputs keep writing and old samples are overwritten (internal/pipeline/ringbuffer.go:44-55).
- Presence: CURRENT (internal/stream/http_stream.go:85-87)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: the client's tolerance for an HTTP audio body that pauses. The server's `IdleTimeout` is 120 s (internal/server/server.go:80).
- Open questions: What does the browser `Audio` element do when the source pauses or disconnects and bytes stop arriving? Does playback resume when bytes return?
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S08-10  Browser-side handling of playback failure
- Question: S08.3
- What was attempted: the `Audio` element's `error` event shows a toast "Audio playback error: …" and resets the play button; `ended` resets the button; a rejected `play()` shows "Click play to start audio (autoplay blocked)" (web/static/js/app.js:369-393). No code restarts the element after an error (INFERRED: `startPlayback` is called only from `togglePlayback`, app.js:351-357). The WebSocket reconnects every 3 s after a close (app.js:41-45); that path does not touch the audio element.
- Presence: CURRENT (web/static/js/app.js:369-393)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: browser event behaviour for an open-ended WAV.
- Open questions: After a service restart, does the listener have to press play again?
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S08-11  Source change while a listener is attached
- Question: S08.3
- What was attempted: `cmd_set_source` stores the new source and calls `onSourceChange` (internal/websocket/hub.go:450-460), which stops both inputs, resets EQ filter state and the volume ramp, and starts the chosen input (cmd/aurilis/main.go:160-178). Listeners stay attached to the same ring buffer; its contents and positions are left as they are (no call to `RingBuffer.Reset` outside tests).
- Presence: CURRENT (cmd/aurilis/main.go:160-178)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: a start error is logged only (main.go:170-176); the stored `audio_source` is already changed by then (hub.go:456) and the status broadcast reports the new value (hub.go:460, 305-314).
- Open questions: When line-in fails to open, what does the UI show, given the error at main.go:175 goes to the log and not to the client?
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S08-12  Several HTTP listeners at once through per-request readers
- Question: S08.4
- What was attempted: every request to `/audio/stream` or `/audio/raw` registers its own reader on the shared ring buffer (internal/stream/http_stream.go:53, 117; internal/pipeline/ringbuffer.go:59-69), described as "single-producer, multi-consumer" (ringbuffer.go:8) and "Lock-free, multi-consumer" (README.md:35-36). There is no listener limit. Each handler runs EQ, volume and analyser on its own copy of the samples (http_stream.go:89-96), using the single Equalizer, VolumeProcessor and Analyzer instances created once (cmd/aurilis/main.go:118-123, 181). Those are the only simultaneous destinations; no second kind of output exists (R4-S08-04).
- Presence: CURRENT (internal/stream/http_stream.go:53, 117)
- Outcome: UNVERIFIED
- Outcome evidence: NONE for concurrent listeners. Commit c98d8d3 states "43 passing tests (unit + integration)"; `TestMultipleConsumers` covers two readers of the ring buffer without the DSP chain (internal/pipeline/ringbuffer_test.go:77-103).
- Why it turned out that way: UNKNOWN.
- Depends on: shared DSP state. INFERRED: biquad delay state is per filter and per channel, not per listener (internal/dsp/biquad.go:32-36, 137-142), and `Equalizer.Process` holds only a read lock (internal/dsp/equalizer.go:43-44), so two handlers advance the same filter state with different chunks; the volume ramp value is likewise shared (internal/dsp/volume.go:29-54).
- Open questions: Was the stream ever opened from two clients at once, and how did it sound?
- Confidence: MEDIUM. INFERRED from clear code.

---

## S09 User interface and control surfaces

Stage note: no physical control or indicator code was found. `git grep -n -i -E 'gpio|\bled\b|lcd|oled'` over the tree: no hits; `button` and `display` hit only the web UI and the pairing-agent method names (internal/bluetooth/pairing_agent.go:67-81).

### R4-S09-01  Embedded web UI
- Question: S09.1
- What was attempted: a single-page UI of one HTML file, one stylesheet and one script (web/static/index.html; web/static/css/style.css; web/static/js/app.js), compiled into the binary with `go:embed` (web/embed.go:5-6) and served at `/` and `/static/` (internal/server/server.go:44-54). README: "Access the Web UI at `http://<host>:<port>/`" (README.md:233). No external script or CDN is loaded (index.html:9, 140).
- Presence: CURRENT (internal/server/server.go:44-54)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: the HTTP port; a browser with WebSocket and canvas support (app.js:33, 267).
- Open questions: Was the UI ever loaded in a browser?
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S09-02  WebSocket control channel
- Question: S09.1
- What was attempted: route `/ws` upgrades to a WebSocket (internal/server/server.go:58; internal/websocket/hub.go:150-168) carrying JSON messages of the form `{"type": ..., "payload": ...}` (hub.go:59-63). The web UI is its only client in the repo (web/static/js/app.js:29-33). README documents it as an API: "Connect to `ws://<host>:<port>/ws` for real-time control." (README.md:249).
- Presence: CURRENT (internal/server/server.go:58)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: `github.com/gorilla/websocket v1.5.3` (go.mod:7).
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from clear code. The package has no test file.

### R4-S09-03  HTTP JSON endpoints
- Question: S09.1
- What was attempted: two GET-style JSON routes, `/api/version` and `/api/alsa/devices` (internal/server/server.go:60-70). Neither is called by the web UI (`git grep -n -E 'fetch\(|XMLHttpRequest'`: no hits) and neither is in the README (README.md:231-257 lists pages, stream URLs and WebSocket only).
- Presence: CURRENT (internal/server/server.go:60-70)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: cgo ALSA device hints for the second route (internal/audio/alsa.go:254-299).
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S09-04  Command-line flags
- Question: S09.1
- What was attempted: the binary takes `-config`, `-port` and `-version` (cmd/aurilis/main.go:33-36), documented at README.md:222-229. There is no interactive command-line control once running.
- Presence: CURRENT (cmd/aurilis/main.go:33-36)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: NONE beyond the Go `flag` package.
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S09-05  JSON configuration file as a control surface
- Question: S09.1
- What was attempted: a JSON file, default path `/etc/aurilis/config.json` (cmd/aurilis/main.go:33; README.md:189), read once at start (main.go:46). Hand edits take effect at the following start. Fields and defaults are in R4-S10-06.
- Presence: CURRENT (internal/config/config.go:100-116)
- Outcome: UNVERIFIED
- Outcome evidence: NONE for a deployed file. Commit c98d8d3 states "43 passing tests (unit + integration)"; save and load round-trip is tested in a temporary directory (internal/config/config_test.go:29-76).
- Why it turned out that way: UNKNOWN.
- Depends on: file permissions set by the installer (R4-S10-05).
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S09-06  Actions and status offered by the web UI
- Question: S09.2
- What was attempted: four panels (web/static/index.html:23-28). Audio: source choice Bluetooth A2DP or Line-In (index.html:35-42), volume slider 0 to 1 (index.html:50), spectrum and waveform canvases (index.html:57-66; web/static/js/app.js:284-347). Bluetooth: adapter list with select and Power On/Off (app.js:187-216), Scan and Stop (index.html:82-83), device list with name, address, badges Paired/Connected/Trusted and buttons Pair, Connect, Trust, Disconnect, Reconnect, Remove (app.js:141-185). Equalizer: enable toggle and ten sliders, ±12 dB in 0.5 steps (index.html:96-105; app.js:218-240). Settings: one "Save Configuration" button (index.html:111-118). Header: version text and a connection badge (index.html:16-20; app.js:122-139). Playback bar: play/pause and a second volume slider bound to the same server-side volume command (index.html:124, 132; app.js:526-532).
- Presence: CURRENT (web/static/index.html:12-136)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: status the UI does not show. The `status` handler reads only `audio_source` and `active_adapter` (app.js:109-113); the `transports` list sent with it (internal/websocket/hub.go:306-311) is not rendered, so no stream-active indicator, codec, or track metadata is displayed. INFERRED: the active adapter is not highlighted on first load, because the initial `config` message carries `bluetooth_adapter` (hub.go:236-242) while the UI looks for `active_adapter` (app.js:106). README states "All settings are editable from the Web UI at runtime." (README.md:220); the UI has no control for port, sample rate, bit depth, buffer size, ALSA device or auto-reconnect.
- Open questions: Does the Scan button's fixed 12-second re-enable timer (app.js:475-479) correspond to anything on the server, where discovery is started without a timeout (internal/bluetooth/bluez.go:405-416)?
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S09-07  Commands and messages on the WebSocket
- Question: S09.2
- What was attempted: sixteen client commands: `cmd_scan`, `cmd_stop_scan`, `cmd_pair`, `cmd_trust`, `cmd_connect`, `cmd_disconnect`, `cmd_remove`, `cmd_reconnect`, `cmd_set_eq`, `cmd_set_eq_enabled`, `cmd_set_volume`, `cmd_set_source`, `cmd_set_adapter`, `cmd_get_status`, `cmd_save_config`, `cmd_power_adapter` (internal/websocket/hub.go:41-56; handlers hub.go:317-494). Server messages: `version`, `adapters`, `devices`, `eq`, `volume`, `config` on connect and on `cmd_get_status` (hub.go:203-245, 486-487); `devices`, `adapters`, `status` on Bluetooth events (hub.go:247-271); `spectrum` (32 bands) and `waveform` (256 points) every 50 ms while any client is connected (hub.go:273-303); `error` (hub.go:496-499). README shows four sample commands (README.md:251-257).
- Presence: CURRENT (internal/websocket/hub.go:27-57)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: message type `alsa_devices` is declared (hub.go:37) and never sent (`git grep MsgTypeALSADevices`: declaration only). `cmd_set_adapter` changes the manager's active adapter without changing the stored `bluetooth_adapter` (hub.go:462-471).
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S09-08  Content of the JSON endpoints
- Question: S09.2
- What was attempted: `/api/version` returns `core`, `webui`, `name` (internal/server/server.go:60-66; values at internal/version/version.go:4-6). `/api/alsa/devices` returns name and description of ALSA PCM hints whose direction is empty or "Input" (server.go:68-70; internal/audio/alsa.go:254-299). Both are read-only.
- Presence: CURRENT (internal/server/server.go:60-70)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: NONE recorded.
- Open questions: The ALSA device list has no consumer in the UI; was a line-in device picker planned?
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S09-09  Actions of the command-line flags
- Question: S09.2
- What was attempted: `-config` sets the config path, default `/etc/aurilis/config.json`; `-port` overrides `web_port` when greater than 0; `-version` prints name, core version and web UI version and exits (cmd/aurilis/main.go:33-41, 52-54). INFERRED: a `-port` override is written back to the file at shutdown, because `cfg.WebPort` is changed in place (main.go:53) and the same struct is saved (main.go:213).
- Presence: CURRENT (cmd/aurilis/main.go:33-54)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: NONE recorded.
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S09-10  First-time setup through the installer
- Question: S09.3
- What was attempted: `sudo bash scripts/install.sh` (README.md:135). Steps in order (scripts/install.sh:457-486): root check; OS and architecture detection; apt dependency install; start and enable `bluetooth.service` if inactive (163-173); adapter count via `hciconfig` if present (176-186); ALSA tools check (189-204); four prompts: web port with range and in-use checks, data directory, install directory, systemd yes/no (221-263); `go build` (278-280); copy binary, write config if absent, create user and groups (290-355); write unit and D-Bus policy, `daemon-reload`, `enable` (357-420). The service is not started; the script prints "Start with: sudo systemctl start aurilis" (423). The closing text reads "Pair your phone/laptop via Bluetooth, then open the Web UI to start streaming." (450-451).
- Presence: CURRENT (scripts/install.sh:457-488)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: a Debian-family OS with `apt-get` (install.sh:50-59); network access for packages and Go modules; an interactive terminal for `read` (install.sh:228, 249, 254, 259).
- Open questions: Was the installer ever run to completion on any machine?
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S09-11  First-time setup by hand, as documented
- Question: S09.3
- What was attempted: README "Manual Installation": apt install of `bluez libasound2-dev libdbus-1-dev`, `go build`, create three directories, copy binary and `systemd/aurilis.service`, create user `aurilis` and add it to `bluetooth,audio`, `daemon-reload`, `enable --now` (README.md:146-167). A quick-run path is also given: build, then `sudo ./aurilis -port 8080` (README.md:99-102).
- Presence: CURRENT (README.md:146-167)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: steps present in the installer and absent here: the D-Bus policy file (scripts/install.sh:399-417) and the config file (install.sh:306-334). The troubleshooting text refers to the policy file (README.md:279). INFERRED: with `/etc/aurilis` created by `sudo mkdir` and no config file, `config.Load` tries to save defaults as user `aurilis` (internal/config/config.go:103-106) and the error path at cmd/aurilis/main.go:47-50 then runs with defaults. The clone URL in the README, `https://github.com/aurilis/aurilis.git` (README.md:95), differs from the clone's remote, tunlezah/Aurilis (/home/user/audio/repos/R4/01-map.md:183).
- Open questions: Does the service reach BlueZ over D-Bus without the policy file? Was the manual path ever followed?
- Confidence: MEDIUM. STATED in documentation; the gaps are INFERRED by comparing it with the installer.

### R4-S09-12  First use in the web UI: scan, pair, trust, connect, play
- Question: S09.3
- What was attempted: the UI flow is outbound from this device: Scan starts BlueZ discovery (web/static/js/app.js:468-481; internal/websocket/hub.go:319-322), then per-device buttons call `Pair`, set `Trusted`, and call `Connect` (app.js:165-173, 490-492; hub.go:329-361; internal/bluetooth/bluez.go:432-454). The listener then presses play in the playback bar (web/static/index.html:124). The README troubleshooting step reads "Ensure the device is paired and connected (check Web UI Bluetooth page)" (README.md:276).
- Presence: CURRENT (web/static/js/app.js:467-495)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: for the inbound direction named in the installer text ("Pair your phone/laptop via Bluetooth", scripts/install.sh:450), the adapter's discoverable and pairable state, which no code in the repo sets (`git grep -i -E 'discoverable|pairable'`: no hits).
- Open questions: Which direction did the author use to pair: from the phone, or from the UI's Scan and Pair buttons? No onboarding text exists in the UI; how does a first-time user learn the sequence?
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S09-13  Command errors sent to the requesting client and shown as toasts
- Question: S09.4
- What was attempted: a failed command produces an `error` message with a text such as "Pairing failed: …" sent to the one client that issued it (internal/websocket/hub.go:320-392, 496-499). The UI shows it as a toast for 5 seconds (web/static/js/app.js:114-116, 434-446). Audio element failures also raise toasts (app.js:369-373, 389-393).
- Presence: CURRENT (internal/websocket/hub.go:496-499)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: `sendToClient` discards the message when the client's queue is full (hub.go:197-200). The save button shows "Configuration saved" at once, before any reply (app.js:538-541); a save failure arrives separately as an error toast (hub.go:489-492). Errors that occur outside a command (transport acquire, input start, read errors) go to the log only (internal/audio/bluetooth_input.go:118, 133, 195; cmd/aurilis/main.go:134, 171, 175).
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S09-14  State changes broadcast from BlueZ signals
- Question: S09.4
- What was attempted: D-Bus `InterfacesAdded`, `InterfacesRemoved` and `PropertiesChanged` signals become events on one channel (internal/bluetooth/bluez.go:203-330). The hub turns device events into a full `devices` list, adapter events into `adapters`, and transport events into `status`, broadcast to all clients (internal/websocket/hub.go:247-271). EQ, volume and source changes are broadcast after each command (hub.go:412-418, 431-436, 447-448, 460).
- Presence: CURRENT (internal/websocket/hub.go:247-271)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: one event channel with two receivers. INFERRED: `Events()` returns the same channel to the hub (hub.go:248) and to the Bluetooth input monitor (internal/audio/bluetooth_input.go:112), so each event is taken by one of them and not the other; a device event taken by the input monitor is dropped there (its switch handles transport events only, bluetooth_input.go:128-157), and a transport event taken by the hub does not reach `SetTransport`. Events are also dropped when the 64-slot channel is full (bluez.go:104, 324-330). A client whose 256-message queue is full is closed and removed (hub.go:161, 180-186).
- Open questions: Did the device list in the UI ever fail to update while Bluetooth was the active source? Did a stream ever fail to start for the same reason?
- Confidence: MEDIUM. INFERRED from clear code; the channel-sharing consequence is reasoning about Go channel delivery, not an observation.

### R4-S09-15  Connection badge and play-button state
- Question: S09.4
- What was attempted: a header badge shows "Connected" or "Disconnected" from the WebSocket's open, close and error events, and "Connecting..." before the first event (web/static/index.html:18-20; web/static/js/app.js:35-50, 122-132). The play button switches glyph on the element's `playing`, `ended` and `error` events (app.js:375-383, 406-416).
- Presence: CURRENT (web/static/js/app.js:122-132)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: the badge reflects the browser-to-server link only; Bluetooth connection state appears only as per-device badges in the Bluetooth panel (app.js:159-161).
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from clear code.

---

## S10 Lifecycle and operations

### R4-S10-01  Automated installer script
- Question: S10.1
- What was attempted: one bash script run as root that automates dependency install, build, file copy, config creation, user creation, unit and D-Bus policy creation, and service enable (scripts/install.sh:457-486; step detail in R4-S09-10). apt packages, unpinned: `bluez`, `libbluetooth-dev`, `libasound2-dev`, `dbus`, `libdbus-1-dev`, `golang` with a snap fallback, `build-essential`, `pkg-config`, and `alsa-utils` (install.sh:119-145, 196). Manual steps left to the person: answering four prompts (221-263) and starting the service (423). The script writes its own copy of the unit (364-397) and does not copy `systemd/aurilis.service`.
- Presence: CURRENT (scripts/install.sh:1-488)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: distro IDs ubuntu, debian, raspbian, linuxmint, pop (install.sh:50-59); architectures x86_64, aarch64, armv7l/armv6l (62-82); the distro's `golang` package satisfying `go 1.24.7` (go.mod:3), where README says "Go 1.21+ (build only)" (README.md:86).
- Open questions: Does a distro `golang` package older than 1.24.7 build the module? On which OS, if any, was the script run?
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S10-02  Manual installation documented in the README
- Question: S10.1
- What was attempted: all steps manual, as shell commands (README.md:146-167), plus build and cross-compile commands for ARM64 and ARMv7 (README.md:89-115). Detail and gaps against the installer are in R4-S09-11.
- Presence: CURRENT (README.md:89-167)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: cross-compilers `aarch64-linux-gnu-gcc` and `arm-linux-gnueabihf-gcc` for the cross builds (README.md:109, 113); cgo and `libasound` headers for any build (internal/audio/alsa.go:3-6).
- Open questions: Was a cross-compiled binary ever produced or run?
- Confidence: MEDIUM. STATED in documentation only.

### R4-S10-03  systemd unit for boot start, ordering and restart
- Question: S10.2
- What was attempted: a unit with `WantedBy=multi-user.target`, `After=bluetooth.service dbus.service network.target sound.target`, `Wants=bluetooth.service`, `Requires=dbus.service`, `Type=simple`, `Restart=on-failure`, `RestartSec=5`, `StartLimitIntervalSec=60`, `StartLimitBurst=3` (systemd/aurilis.service:1-30; generated copy at scripts/install.sh:364-397). The installer enables it (install.sh:420). README lists "Systemd Service — Production deployment with automatic restart" (README.md:19).
- Presence: CURRENT (systemd/aurilis.service:1-30; scripts/install.sh:364-397)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: `bluetooth.service` being enabled; the installer enables it only when it found it inactive (install.sh:163-173). `StartLimitIntervalSec` and `StartLimitBurst` are placed in the `[Service]` section (systemd/aurilis.service:15-16).
- Open questions: Was the unit ever loaded, and did the service start at boot on any machine? Are the two start-limit settings honoured in the section where they sit?
- Confidence: MEDIUM. INFERRED from clear configuration.

### R4-S10-04  In-process start-up failure handling and shutdown
- Question: S10.2
- What was attempted: fatal exits on D-Bus or BlueZ object-load failure, server creation failure and listen failure (cmd/aurilis/main.go:57-64, 190-197; internal/server/server.go:85-88). Warnings with continued start-up for: config load error, no adapter, pairing agent failure, A2DP endpoint registration failure, input start failure (main.go:47-50, 90-92, 97-101, 140-141, 150-156). On SIGINT or SIGTERM the inputs stop and the config is saved (main.go:203-217). No in-process retry exists for any of these.
- Presence: CURRENT (cmd/aurilis/main.go:43-217)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: the adapter being present at process start. INFERRED: adapter choice and endpoint registration run once (main.go:68-92, 140), and later `EventAdapterChanged` events are used only to refresh the UI list (internal/websocket/hub.go:260-263), so an adapter that appears after start gets no A2DP endpoint until the process restarts. A warning-only start exits with no error, so `Restart=on-failure` is not triggered by it.
- Open questions: After a BlueZ restart, are the agent and endpoint registered again? Nothing in the code watches for `org.bluez` leaving the bus.
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S10-05  Privileges and file permissions set up for the service
- Question: S10.3
- What was attempted: the installer requires root (scripts/install.sh:26-31). It creates system user `aurilis` with a no-login shell, adds it to groups `bluetooth` and `audio` (341-348), gives it the data directory (351), sets install and config directories to `root:root` and the config file to mode 644 (352-354), and writes a D-Bus system policy allowing that user to own `org.aurilis` and send to `org.bluez` and five named interfaces (399-417). The unit runs as that user (systemd/aurilis.service:10-11) with `ReadWritePaths=/var/lib/aurilis /etc/aurilis` (line 24), `LimitRTPRIO=99` and `LimitMEMLOCK=infinity` (18-19).
- Presence: CURRENT (scripts/install.sh:340-355, 399-417)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: config writes by the service. INFERRED: the service saves the config on shutdown and on `cmd_save_config` (cmd/aurilis/main.go:213; internal/websocket/hub.go:489-492) as user `aurilis`, while the file is owned by root with mode 644 (install.sh:353-354), which grants write to the owner only; the unit's `ReadWritePaths` does not change ownership. The README has a "Permission errors" entry (README.md:303-314) covering groups and D-Bus reload, not the config file.
- Open questions: Did "Save Configuration" ever succeed under the installed service? Is membership of group `bluetooth` together with the policy file enough for `RegisterAgent` and `RegisterEndpoint`?
- Confidence: MEDIUM. INFERRED from clear code and script.

### R4-S10-06  JSON configuration: location, fields, defaults, load and save
- Question: S10.4
- What was attempted: file path from `-config`, default `/etc/aurilis/config.json` (cmd/aurilis/main.go:33). Fields and defaults: `web_port` 8080, `data_dir` /var/lib/aurilis, `bluetooth_adapter` hci0, `audio_source` bluetooth, `alsa_device` default, `sample_rate` 48000, `channels` 2, `bit_depth` 16, `volume` 0.8, `eq_enabled` true, ten `eq_bands`, `auto_reconnect` true, `buffer_size_ms` 100 (internal/config/config.go:16-65; same values at README.md:191-217 and scripts/install.sh:308-333). A missing file is created from defaults (config.go:103-106); a load error falls back to defaults in memory (main.go:47-50). Changed at run time and held in memory: volume, EQ bands, EQ enabled, audio source (internal/websocket/hub.go:395-460). Written to disk on `cmd_save_config` and at shutdown, mode 0644 (hub.go:489-492; main.go:213; config.go:97).
- Presence: CURRENT (internal/config/config.go:16-116)
- Outcome: UNVERIFIED
- Outcome evidence: NONE for a deployed file. Commit c98d8d3 states "43 passing tests (unit + integration)"; seven config tests cover defaults, round-trip, default creation and setters (internal/config/config_test.go:9-135).
- Why it turned out that way: UNKNOWN.
- Depends on: `buffer_size_ms` is multiplied by 10 where it is used (main.go:114). `sample_rate`, `channels`, `bit_depth`, `alsa_device`, `web_port` and `buffer_size_ms` are read once at start (main.go:114-127, 181-190). No validation of values exists beyond clamping volume and EQ gain (config.go:118-149).
- Open questions: Is the factor of 10 intended, given the field name says milliseconds?
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S10-07  Config fields with no consuming logic
- Question: S10.4
- What was attempted: `data_dir` and `auto_reconnect` are declared, defaulted, documented and prompted for (internal/config/config.go:20, 30, 37, 47; README.md:194, 215; scripts/install.sh:249-251, 310, 331). `git grep -n -E 'DataDir|AutoReconnect'` in Go code finds no reader of `DataDir`, and one reader of `AutoReconnect`, which copies it into the UI's `config` message (internal/websocket/hub.go:241); the UI does not read that key (web/static/js/app.js:104-108). README lists "Auto-reconnect — Retry logic for unstable Bluetooth sources" (README.md:18); the retry code present is `ReconnectDevice`, run only on the UI's `cmd_reconnect` (internal/bluetooth/bluez.go:478-495; hub.go:383-393).
- Presence: DISABLED. Declared and stored, with no code path that acts on the values (internal/config/config.go:20, 30)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: nothing at run time. The data directory is created, owned by the service user and used as its home (install.sh:295, 342, 351); the program writes nothing there.
- Open questions: What was the data directory meant to hold?
- Confidence: MEDIUM. INFERRED from searches over the whole tree.

### R4-S10-08  Logging through the Go standard logger to the journal
- Question: S10.5
- What was attempted: all components call `log.Printf` or `log.Println` with text prefixes `audio:`, `bluetooth:`, `stream:`, `server:`, `websocket:` (examples: internal/audio/bluetooth_input.go:104; internal/bluetooth/a2dp_sink.go:147, 204; internal/stream/http_stream.go:56; internal/server/server.go:83; internal/websocket/hub.go:154). The logger is left at its defaults (`git grep -n -E 'SetOutput|SetFlags|SetPrefix|log\.New'`: no hits). Under systemd the output is read with `sudo journalctl -u aurilis -f` (README.md:181; scripts/install.sh:443). There are no log levels, no log file, no debug flag (`git grep -i -E 'debug|verbose'`: no hits). Version is available from `-version` and `/api/version` (cmd/aurilis/main.go:38-41; server.go:60-66).
- Presence: CURRENT (cmd/aurilis/main.go:43)
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No captured log exists in the repo.
- Why it turned out that way: UNKNOWN.
- Depends on: systemd's journal for retention. Logged items include transport paths, selected SBC bytes, listener addresses, and pairing codes shown during pairing (internal/bluetooth/pairing_agent.go:69, 81, 87).
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S10-09  Diagnostic commands in the README troubleshooting section
- Question: S10.5
- What was attempted: shell commands for diagnosis, grouped by symptom: `systemctl status bluetooth`, `hciconfig -a`, `hciconfig hci0 up` (README.md:263-272); `journalctl -u bluetooth -f` and a pointer to the D-Bus policy file (276-279); `arecord -l` and a 5-second test capture (283-289); `ss -tlnp`, `systemctl status aurilis`, `ufw allow <port>/tcp` (293-295); group and D-Bus reload commands (305-314). The program itself has no health, metrics or profiling endpoint (`git grep -i -E 'health|metric|pprof'`: no hits).
- Presence: CURRENT (README.md:259-314)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: `hciconfig` and `arecord` being installed; the installer installs neither by name, though `alsa-utils` is installed when `aplay` is missing (scripts/install.sh:176-186, 192-196).
- Open questions: NONE
- Confidence: MEDIUM. STATED in documentation only.

### R4-S10-10  Source build to one binary with the web UI embedded; no package format
- Question: S10.6
- What was attempted: `CGO_ENABLED=1 go build -ldflags="-s -w" -o aurilis ./cmd/aurilis` (README.md:99; scripts/install.sh:278-280), with the web files embedded (web/embed.go:5-6). Built binaries are ignored by git (.gitignore:1-2). Version strings are constants: core 1.0.0 and web UI 1.0.0 (internal/version/version.go:4-5), installer 1.0.0 (install.sh:7), UI script header (web/static/js/app.js:2). No `.deb`, container file, release artefact, tag or CI exists (`git tag`: empty; `git grep -i -E '\.deb|docker'`: no hits; no `.github/` per /home/user/audio/repos/R4/01-map.md:40).
- Presence: CURRENT (scripts/install.sh:265-288)
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No build output is recorded.
- Why it turned out that way: UNKNOWN.
- Depends on: Go toolchain meeting go.mod:3; a C compiler and ALSA headers (internal/audio/alsa.go:3-6); module path `github.com/aurilis/aurilis` (go.mod:1).
- Open questions: Was the binary ever built? The commit says tests passed, which implies compilation of the tested packages only (config, dsp, pipeline, version).
- Confidence: MEDIUM. INFERRED from clear code and script.

### R4-S10-11  Installer re-run over an existing install; no update mechanism
- Question: S10.6
- What was attempted: no update, upgrade or uninstall command exists (`git grep -i -E 'uninstall|upgrade'`: only the WebSocket upgrader, internal/websocket/hub.go:18, 152, 154). The nearest path is running the installer again. INFERRED from the script: the binary is rebuilt and overwritten (scripts/install.sh:278-280, 301), an existing config file is kept (306, 336-338), an existing user is kept (341), the unit and D-Bus policy are rewritten (364, 400), and the service is enabled, not restarted (419-420).
- Presence: CURRENT (scripts/install.sh:290-424)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: the source tree being present on the target machine (install.sh:268-274).
- Open questions: NONE
- Confidence: LOW. INFERRED; the repo does not describe an update procedure at all.

---

## S11 Testing and measured behaviour

### R4-S11-01  Unit tests for config, DSP and ring buffer
- Question: S11.1
- What was attempted: 50 test functions in six files: config 7 (internal/config/config_test.go:9-135), analyser 7 (internal/dsp/analyzer_test.go:8-124), biquad 8 (internal/dsp/biquad_test.go:8-147), equaliser 9 (internal/dsp/equalizer_test.go:25-161), volume 8 (internal/dsp/volume_test.go:8-118), ring buffer 11 (internal/pipeline/ringbuffer_test.go:9-222). All inputs are generated in the test. Run with `go test ./internal/... -v` (README.md:121). No test file exists for packages `audio`, `bluetooth`, `server`, `stream`, `websocket` or `cmd/aurilis` (`git ls-files | grep _test.go`: seven files, none in those directories).
- Presence: CURRENT (the six files cited)
- Outcome: WORKED
- Outcome evidence: STATED. Commit c98d8d3 message: "43 passing tests (unit + integration)". No test output, log or CI result is stored.
- Why it turned out that way: UNKNOWN. The stated count does not match the tree: `git show c98d8d3:<file> | grep -c '^func Test'` gives 50 unit plus 4 integration functions, 54 in total, with no `t.Run` subtests.
- Depends on: Go toolchain per go.mod:3. The tested packages need no Bluetooth adapter, D-Bus, ALSA or network.
- Open questions: Which 43 tests does the commit message count? Were the tests run on the committed tree?
- Confidence: MEDIUM. STATED evidence alone, and the stated count differs from the count in the tree.

### R4-S11-02  Integration tests of the in-memory processing chain
- Question: S11.1
- What was attempted: four functions in test/integration_test.go. `TestSystemBoot` constructs config, ring buffer, EQ, volume and analyser and checks they are non-nil (lines 13-51). `TestAudioPipelineIntegration` writes one second of generated 440/880 Hz samples to the ring buffer, reads them back, runs EQ, volume and analyser, and checks for non-empty spectrum and waveform (54-140). `TestEQModifiesAudio` checks that a +12 dB band raises RMS (143-180). `TestVersionNumbersSet` checks the version constants (183-200). None starts the Bluetooth manager, the HTTP server, the WebSocket hub or either input (imports at lines 3-10).
- Presence: CURRENT (test/integration_test.go:1-233)
- Outcome: WORKED
- Outcome evidence: STATED. Commit c98d8d3 message: "43 passing tests (unit + integration)". No test output is stored.
- Why it turned out that way: UNKNOWN.
- Depends on: generated signals only; no hardware.
- Open questions: Was any test of the Bluetooth-to-HTTP path performed outside the repo?
- Confidence: MEDIUM. STATED evidence alone, with the count difference noted in R4-S11-01.

### R4-S11-03  NOT FOUND
- Question: S11.2
- Searched: `git grep -n -i` over the tree for `iPhone`, `Android`, `Samsung`, `tested`, `phone`, `laptop`; `Pixel` (three hits, all `devicePixelRatio` in web/static/js/app.js:278-281). Read README.md in full and all three commit messages (`git log --format=%B`). Owner notes: "none" (/home/user/audio/00-manifest.md:37-39).
- Nearest related material: README.md:9 ("Receive audio from phones, laptops, tablets via Bluetooth"), a generic statement naming no device; scripts/install.sh:450 ("Pair your phone/laptop via Bluetooth,").

### R4-S11-04  NOT FOUND
- Question: S11.3
- Searched: `git grep -n -i` over the tree for `latency`, `dropout`, `measure`, `cpu`, `memory`, `uptime`, `metric`, `func Benchmark`, `jitter`; `git ls-files` filtered for `log|result|report|bench`. Hits: `latency` and `dropout` once each, in the heading at README.md:297, with no figure.
- Nearest related material: README.md:297-301 (troubleshooting heading "Audio latency/dropouts", no numbers); commit c98d8d3 message ("43 passing tests (unit + integration)"), a count and not a measurement.

### R4-S11-05  README troubleshooting section; no list labelled known issues
- Question: S11.4
- What was attempted: no known-issues, limitations, changelog or notes file exists, and no TODO, FIXME, HACK, XXX, BUG or WORKAROUND comment (`git grep -n -i -E 'known issue|limitation'`: no hits; marker search per /home/user/audio/repos/R4/01-map.md:121). The author's only problem list is the README "Troubleshooting" section with six symptom headings: "Bluetooth adapter not found", "No audio from Bluetooth device", "ALSA capture not working", "Web UI not loading", "Audio latency/dropouts", "Permission errors" (README.md:259-314). One entry names hardware: "On Raspberry Pi, use a USB Bluetooth adapter instead of built-in" (README.md:301).
- Presence: CURRENT (README.md:259-314)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. The section gives remedies without saying that any symptom was observed.
- Depends on: NONE recorded.
- Open questions: Were these symptoms seen by the author, or written in advance? What lies behind the Raspberry Pi built-in adapter remark?
- Confidence: MEDIUM. STATED in documentation only.

---

## S12 Security and exposure

### R4-S12-01  One HTTP listener on all interfaces, without authentication or TLS
- Question: S12.1
- What was attempted: a single `http.Server` bound to `":<port>"`, with no host part, default port 8080 (internal/server/server.go:75-81; internal/config/config.go:36), started with plain `ListenAndServe` (server.go:86). Routes: `/`, `/static/`, `/audio/stream`, `/audio/raw`, `/ws`, `/api/version`, `/api/alsa/devices` (server.go:43-71). No route checks credentials (`git grep -n -i -E 'password|token|tls|cert'` outside go.sum: no hits; `auth` hits are the pairing-agent method names only). Both audio routes send `Access-Control-Allow-Origin: *` (internal/stream/http_stream.go:63, 122). README troubleshooting includes "Check firewall rules: `sudo ufw allow <port>/tcp`" (README.md:295).
- Presence: CURRENT (internal/server/server.go:74-92)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No design note on network exposure exists in the repo.
- Depends on: the network the host is attached to; the port value chosen at install (scripts/install.sh:227-246).
- Open questions: Was the service intended for a trusted home network only? Nothing in the repo says.
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S12-02  WebSocket control endpoint: unauthenticated commands, origin check off
- Question: S12.1
- What was attempted: the upgrader accepts any origin: `CheckOrigin: func(r *http.Request) bool { return true }` (internal/websocket/hub.go:18-22). Any client that reaches `/ws` may issue every command, including pair, trust, connect, remove device, power an adapter off, change source, EQ and volume, and write the config file (hub.go:317-494). Object paths in command payloads are passed to D-Bus calls on `org.bluez` as given (hub.go:329-338; internal/bluetooth/bluez.go:432-438, 388-394). Inbound message size is limited to 65536 bytes (hub.go:509).
- Presence: CURRENT (internal/websocket/hub.go:18-22, 150-168)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: same listener and port as R4-S12-01.
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S12-03  Objects on the D-Bus system bus and the installed bus policy
- Question: S12.1
- What was attempted: the process exports a pairing agent at `/org/aurilis/agent` and a media endpoint at `/org/aurilis/a2dp_sink` on the system bus (internal/bluetooth/pairing_agent.go:13, 26; internal/bluetooth/a2dp_sink.go:14, 49). The installer's policy lets user `aurilis` own the name `org.aurilis` and lets every bus user send to it: `<policy context="default"> <allow send_destination="org.aurilis"/>` (scripts/install.sh:404-415). The code never requests that name (`git grep -n RequestName`: no hits), so the objects live on the connection's unique name. No authentication of D-Bus callers is done in the exported methods (pairing_agent.go:56-107; a2dp_sink.go:146-221).
- Presence: CURRENT (scripts/install.sh:399-417; internal/bluetooth/pairing_agent.go:23-46)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: the system bus's default policy for method calls to a unique name, which is outside the repo.
- Open questions: Does the policy's `org.aurilis` name have any effect, given the name is never requested?
- Confidence: MEDIUM. INFERRED from clear code and script.

### R4-S12-04  Bluetooth pairing agent that accepts every request
- Question: S12.1
- What was attempted: the radio-facing entry point is BlueZ with this project's default agent, registered with capability `NoInputNoOutput` (internal/bluetooth/pairing_agent.go:32-39). The agent is described as "It accepts all pairing requests to function as a headless A2DP Sink." (pairing_agent.go:17); confirmation, authorization and service authorization all return success without a check (pairing_agent.go:86-101). README lists this as "Automatic Pairing — Headless pairing agent for seamless device connections" (README.md:17). No allow-list or pairing window exists in this code. Pairing detail belongs to S03.
- Presence: CURRENT (internal/bluetooth/pairing_agent.go:16-101; cmd/aurilis/main.go:95-105)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: STATED. The design reason is the headless use given at pairing_agent.go:16-17.
- Depends on: the adapter's discoverable and pairable state, not set by this repo (see R4-S09-12).
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from clear code, with the intent STATED in a comment.

### R4-S12-05  Service process under a dedicated unprivileged user
- Question: S12.2
- What was attempted: the unit runs the one process as `User=aurilis`, `Group=aurilis` (systemd/aurilis.service:10-11; scripts/install.sh:374-375), a system account with shell `/usr/sbin/nologin` and supplementary groups `bluetooth` and `audio` (install.sh:342-348). Unit settings under the comment "# Security hardening" (install.sh:385): `NoNewPrivileges=false`, `ProtectSystem=strict`, `ProtectHome=true`, `ReadWritePaths`, `PrivateTmp=true` (systemd/aurilis.service:21-25). No capability settings are present (`git grep -n -E 'CapabilityBoundingSet|AmbientCapabilities|setcap'`: no hits). The process starts no child processes (no `os/exec` import in any Go file; imports read in all 18 non-test files).
- Presence: CURRENT (systemd/aurilis.service:8-27)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: the D-Bus policy and group membership being sufficient for BlueZ access as a non-root user (R4-S10-05).
- Open questions: Did the service ever run as `aurilis` and register its agent and endpoint, or was it only run with `sudo`?
- Confidence: MEDIUM. INFERRED from clear configuration.

### R4-S12-06  Documented quick run as root
- Question: S12.2
- What was attempted: the README's build section runs the binary with `sudo ./aurilis -port 8080` (README.md:101-102). Run this way the HTTP listener, WebSocket command handler and D-Bus client all execute as root, and the config path defaults to `/etc/aurilis/config.json` (cmd/aurilis/main.go:33). The installer itself also runs as root (scripts/install.sh:26-31), including `go build` of the source tree (install.sh:278-280).
- Presence: CURRENT (README.md:101-102)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. The README gives no reason for `sudo` on the quick run.
- Depends on: NONE recorded.
- Open questions: Which of the two ways, `sudo` or the systemd user, did the author use when running it?
- Confidence: MEDIUM. STATED in documentation only.

### R4-S12-07  Fixed pairing-code replies in the agent, and pairing codes written to the log
- Question: S12.3
- What was attempted: the agent answers a legacy PIN request with a fixed PIN string constant (internal/bluetooth/pairing_agent.go:62-65) and a passkey request with a fixed numeric value (pairing_agent.go:74-77). Kind of item: hard-coded Bluetooth pairing PIN and passkey; values not copied here. Codes that BlueZ asks the agent to display or confirm are written to the process log (pairing_agent.go:69, 81, 87), which goes to the journal under systemd.
- Presence: CURRENT (internal/bluetooth/pairing_agent.go:61-89)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: STATED. The comments give the purpose: "RequestPinCode returns a default PIN for legacy pairing." (pairing_agent.go:61).
- Depends on: a source device that uses legacy PIN pairing or passkey entry for these paths to be reached.
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from clear code.

### R4-S12-08  No project-held secrets; config file world-readable
- Question: S12.3
- What was attempted: the config struct has no credential field (internal/config/config.go:16-32) and the file is written with mode 0644 (config.go:97; scripts/install.sh:354). The project stores no keys, tokens or passwords of its own (`git grep -n -i -E 'password|token|secret|api_key|private key'` outside go.sum: no hits) and no code reads or writes Bluetooth link keys; pairing and trust are delegated to BlueZ through `Device1.Pair` and the `Trusted` property (internal/bluetooth/bluez.go:432-446). No real device address is committed (`git grep -n -E '([0-9A-Fa-f]{2}[:_]){5}[0-9A-Fa-f]{2}'`: no hits; README.md:254 uses a placeholder pattern). Device addresses of discovered and paired devices are sent to every WebSocket client in the `devices` message (internal/websocket/hub.go:218-220; internal/bluetooth/bluez.go:34-46).
- Presence: CURRENT (internal/config/config.go:83-98)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: BlueZ's own storage for link keys, which is outside the repo and not referenced in it.
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from clear code and searches.

---

## Tally

| Stage | Questions | Answered with findings | Answered NOT FOUND | Finding records | NOT FOUND records |
|-------|-----------|------------------------|--------------------|-----------------|-------------------|
| S07 | 3 | 2 (S07.1, S07.2) | 1 (S07.3) | 9 | 1 |
| S08 | 4 | 4 | 0 | 12 | 0 |
| S09 | 4 | 4 | 0 | 15 | 0 |
| S10 | 6 | 6 | 0 | 11 | 0 |
| S11 | 4 | 2 (S11.1, S11.4) | 2 (S11.2, S11.3) | 3 | 2 |
| S12 | 3 | 3 | 0 | 8 | 0 |
| Total | 24 | 21 | 3 | 58 | 3 |

Questions answered with findings: 21. Questions answered NOT FOUND: 3. Total: 24.

Finding records: 58. NOT FOUND records: 3.
