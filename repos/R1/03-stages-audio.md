# R1 audio stage audit: S07 to S12

Audio stage auditor output for repo R1 at /home/user/a2dplaya. Read-only. All paths are relative to /home/user/a2dplaya. Line numbers refer to the working tree at commit 70ad071 (branch `main`, identical to the checked-out branch `claude/inspiring-galileo-a9dlia`; `git status --short` prints nothing). The repo has 4 commits (`git log --all`); all code arrived in one commit, 47701ce, and `git diff --stat 47701ce HEAD` shows only the removal of .pyc files and the addition of .gitignore.

Evidence position that applies to every record below: the repo holds no log file, no recorded test output, no CI configuration, no notes file and no issue list (`find` for *.log, *.yml, *.yaml, .github, CHANGELOG*, NOTES*, TODO*; listing of the 28 tracked files). For that reason most outcomes are UNVERIFIED under Rule 4.

Range: 24 questions (S07.1 to S12.3).

---

## S07 Processing

### R1-S07-01  PCM format conversion (bit depth, channels, sample rate) in pure Python
- Question: S07.1
- What was attempted: `feed_audio` compares the incoming format with the pipeline format and calls the resampler when they differ (a2dplaya/audio/pipeline.py:206-211). The resampler runs three steps in order: bit-depth conversion through normalised floats (a2dplaya/audio/resampler.py:53-56, 69-76, 152-202), channel conversion by duplication or averaging (a2dplaya/audio/resampler.py:58-61, 78-120), and sample-rate conversion by linear interpolation (a2dplaya/audio/resampler.py:63-65, 122-150). Each call works on one chunk; no state is kept on the object between calls beyond the three target values (a2dplaya/audio/resampler.py:24-27). The pipeline target is 44100 Hz, 2 channels, 2 bytes (a2dplaya/config.py:40-42; a2dplaya/audio/pipeline.py:52-54). The module docstring names `audioop` as an alternative (a2dplaya/audio/resampler.py:5-6); no import of `audioop` exists (`git grep -n audioop` returns only those docstring lines). No decode step for a Bluetooth codec exists in the tree: `git grep -n -i -E "sbc|aptx|ldac|decoder|gstreamer"` returns no hits, and `decode` hits are the resampler's PCM helpers and UTF-8 text decoding only.
- Presence: CURRENT. Called from a2dplaya/audio/pipeline.py:209 on main (commit 70ad071).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No result is recorded.
- Depends on: the pipeline format constants (a2dplaya/config.py:40-42); callers passing a format that differs from the pipeline format. The line-in capture passes the pipeline constants themselves (a2dplaya/audio/line_in.py:38-40, 169-174), and the Bluetooth callback has no caller (`_audio_callback` occurs in a2dplaya/bluetooth/manager.py only at line 71), so in the current tree the conversion branch is reached only when `config.audio.sample_rate`, `channels` or `sample_width` is set away from 44100/2/2 (a2dplaya/audio/pipeline.py:52-54; a2dplaya/config.py:212). Unit tests exist (tests/test_resampler.py:19-89) with no recorded result (see R1-S11-01).
- Open questions: Was the resampler ever run on live audio? What happens at chunk boundaries, given that interpolation uses only the samples inside one chunk (a2dplaya/audio/resampler.py:136-148)? Which format would Bluetooth audio have arrived in?
- Confidence: MEDIUM. Description INFERRED from clear code; no evidence of the result.

### R1-S07-02  Software volume scaling of 16-bit samples in the pipeline thread
- Question: S07.1
- What was attempted: `_process_loop` passes every chunk through `_apply_volume` (a2dplaya/audio/pipeline.py:244). A volume of 0.999 or more returns the chunk unchanged (288-289); 0.001 or less returns zero bytes (291-292); otherwise each 16-bit sample is unpacked, multiplied, clamped and repacked in Python (295-303). On `struct.error` the chunk is returned unchanged (304-305). The volume value is set from config at start (a2dplaya/audio/pipeline.py:57; default 1.0 at a2dplaya/config.py:80) and through `POST /api/volume` (a2dplaya/web/server.py:165-170), clamped to 0.0-1.0 (a2dplaya/audio/pipeline.py:99-102).
- Presence: CURRENT. a2dplaya/audio/pipeline.py:244, 275-305 on main.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No result is recorded.
- Depends on: 2-byte samples; the function unpacks as `h` regardless of the configured `sample_width` (a2dplaya/audio/pipeline.py:295-296). Unit tests exist (tests/test_pipeline.py:80-101) with no recorded result.
- Open questions: Is this volume linked in any way to the source device's volume? (No AVRCP code is present; `git grep -n -i -E "avrcp|MediaPlayer1|MediaControl"` returns no hits.) Is a volume change audible at once or after the output buffers drain?
- Confidence: MEDIUM. Description INFERRED from clear code; no evidence of the result.

### R1-S07-03  Ten-band spectrum analysis for display, with no change to the audio
- Question: S07.1
- What was attempted: after volume, each chunk is passed to `FFTAnalyzer.analyze` (a2dplaya/audio/pipeline.py:247). The analyser rate-limits itself to 30 updates per second (a2dplaya/audio/fft_analyzer.py:43, 79-81; a2dplaya/config.py:48), mixes to mono (126-148), pads or trims to 2048 samples (87-92; a2dplaya/config.py:47), applies a Hann window (150-156), computes a direct DFT in Python loops (158-181), averages bins into ten bands (183-206) and smooths the levels (104-114). The class states "Uses a simple DFT implementation to avoid external numpy dependency" (a2dplaya/audio/fft_analyzer.py:21). `analyze` returns nothing and the chunk passed on to consumers is the volume-scaled chunk (a2dplaya/audio/pipeline.py:244-260), so the bands are read-only: the items named "EQ" in the README and the UI (README.md:13; a2dplaya/web/static/index.html:370-384) are a visualiser. No equalisation filter is applied to audio anywhere (`git grep -n -i -E "biquad|filter|gain"` in *.py returns no hits).
- Presence: CURRENT. a2dplaya/audio/pipeline.py:247 on main. The config field `eq_enabled` (a2dplaya/config.py:81) has no reader (`git grep -n eq_enabled` returns that line only), so the analysis has no off switch.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No result is recorded.
- Depends on: 2-byte samples (other widths give an empty sample list, a2dplaya/audio/fft_analyzer.py:128-129); the scaling constant described as "empirical scaling factor" (a2dplaya/audio/fft_analyzer.py:202-203). The analysis runs on the pipeline thread before consumers are called (a2dplaya/audio/pipeline.py:247 precedes 253-260). INFERRED arithmetic: the DFT loop covers bins 1 to 1024 over 2048 samples, 2,097,152 inner iterations per analysis, each with one `cos` and one `sin` (a2dplaya/audio/fft_analyzer.py:165-179, with band ranges from 46-70).
- Open questions: How long does one analysis take on the target hardware, and does the pipeline thread keep up with real time while it runs? Was the "empirical" factor derived from a measurement that is not in the repo?
- Confidence: MEDIUM. Description INFERRED from clear code; no evidence of the result.

### R1-S07-04  MP3 encoding step for the Chromecast stream
- Question: S07.1
- What was attempted: documentation and configuration name an MP3 encode step: "AudioPipeline -> MP3 encoder -> HTTP stream server -> Chromecast fetches URL" (a2dplaya/cast/manager.py:10); "encodes audio to MP3 format" (a2dplaya/cast/manager.py:135); `CHROMECAST_FORMAT = "mp3"` with the comment "Most compatible format" (a2dplaya/config.py:30) and `stream_format` defaulting to it (a2dplaya/config.py:89). In code the encoder is a placeholder attribute, `self._mp3_encoder = None` under the comment "MP3 encoder (lazy init)" (a2dplaya/cast/manager.py:163-164), with no other occurrence (`git grep -n _mp3_encoder`). The buffer the MP3 route reads, `_stream_buffer`, is created, cleared and read (a2dplaya/cast/manager.py:149, 260, 287-290) and never written. No encoder library or tool is named anywhere (`git grep -n -i -E "lame|ffmpeg|pydub"` returns no hits).
- Presence: DISABLED. The encode step has no code; the consumer callback stores PCM only (a2dplaya/cast/manager.py:269-282).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No commit, comment or note says why the encoder was left out. Commit 47701ce describes the shipped path as "HTTP stream server serving WAV audio to Cast devices".
- Depends on: an encoder that is not in the dependency lists (requirements.txt:1-17; setup.py:16-25).
- Open questions: Was an MP3 encoder tried and dropped before the single code commit, or never written? Why do the docstring and the `stream_format` default still name MP3 while the cast URL is `/stream.wav` (a2dplaya/cast/manager.py:224)?
- Confidence: MEDIUM. INFERRED from clear code: every occurrence of the two names is listed above.

### R1-S07-05  AirPlay encode step: ALAC named in documentation, unencoded PCM packetised in code
- Question: S07.1
- What was attempted: the module docstring says "AirPlay audio streaming uses ALAC (Apple Lossless) encoded audio" (a2dplaya/airplay/manager.py:7) and gives the flow "AudioPipeline -> ALAC/PCM encoder -> RAOP/RTP -> AirPlay receiver" (a2dplaya/airplay/manager.py:13). The code performs no encoding: the consumer callback appends pipeline PCM to a byte buffer (a2dplaya/airplay/manager.py:191-203), and the stream loop cuts 1408-byte slices (352 frames of 4 bytes) and prepends a 12-byte header (a2dplaya/airplay/manager.py:360-378). The SDP sent to the receiver announces `L16` at the configured rate and channel count (a2dplaya/airplay/manager.py:327). No ALAC code or library is present (`git grep -n -i alac` returns only a2dplaya/airplay/manager.py:7, 13 and a2dplaya/config.py:33).
- Presence: CURRENT for the PCM packetising (a2dplaya/airplay/manager.py:355-384, started at 341-346). No ALAC encoder exists in any commit (`git log --all --stat`).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. The handshake docstring calls itself "a simplified RAOP handshake" (a2dplaya/airplay/manager.py:292); no result is stated.
- Depends on: a receiver that accepts the announced `L16` payload; pipeline format 44100 Hz, 16-bit, stereo (a2dplaya/config.py:35-37, 98-99).
- Open questions: Did any AirPlay receiver play this stream? Is the little-endian byte order of the pipeline PCM (a2dplaya/audio/pipeline.py:15) the order a receiver expects for the announced payload type?
- Confidence: MEDIUM. Description INFERRED from clear code; no evidence of the result.

### R1-S07-06  Points where latency arises: buffers and polling loops, with no figure and no compensation
- Question: S07.2
- What was attempted: no latency figure is measured or stated anywhere in the repo, and no compensation code exists (`git grep -n -i -E "latenc|delay|jitter"` returns only a2dplaya/config.py:55 and 64, both Bluetooth reconnect settings). The places in code where audio waits are: (a) the pipeline deque, capped at 100 chunks, commented "Max ~100 chunks buffered" (a2dplaya/audio/pipeline.py:76-77), polled with a 1 ms sleep when empty (228-233); (b) the spectrum analysis that runs on the pipeline thread before output (R1-S07-03); (c) the Chromecast PCM buffer, capped at 1 MB (a2dplaya/cast/manager.py:150-152, 278-282), drained in slices of up to 8192 bytes with a 10 ms sleep when empty (a2dplaya/cast/manager.py:116-122, 293-300); (d) the AirPlay PCM buffer, capped at 512 KB (a2dplaya/airplay/manager.py:73-75, 200-203), drained 1408 bytes at a time with a 4 ms sleep when short (a2dplaya/airplay/manager.py:358-368); (e) line-in capture in periods of `chunk_size` frames, default 4096 (a2dplaya/audio/line_in.py:161, 201; a2dplaya/config.py:43). `chunk_duration` is computed in `_process_loop` and not used afterwards (a2dplaya/audio/pipeline.py:227).
- Presence: CURRENT. All cited lines are on main.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No measurement is recorded.
- Depends on: INFERRED arithmetic from the constants, at 44100 Hz, 16-bit, stereo (176,400 bytes per second): one 4096-frame chunk is about 93 ms, so 100 chunks is about 9.3 s; 1 MB is about 5.9 s; 512 KB is about 3.0 s; 352 frames is about 8 ms, while the comment at a2dplaya/airplay/manager.py:367 says "~4ms for 352 frames at 44100Hz". These are upper bounds on backlog, not measured delays. Buffering inside the Chromecast or AirPlay receiver is outside the repo.
- Open questions: What end-to-end delay did a listener experience? How full do the output buffers run in steady state? Does the Chromecast add its own buffering for an HTTP WAV stream of unknown length?
- Confidence: MEDIUM. INFERRED from clear code and constants; no evidence of the result.

### R1-S07-07  NOT FOUND
- Question: S07.3
- Searched: `git grep -n -i -E "latenc|delay|jitter|drift|clock|sync|ntp|timestamp|pts"` across all tracked files (hits: `shairport-sync` as a program name, the two Bluetooth reconnect settings, one RTP header comment, and incidental substring matches at a2dplaya/__main__.py:17 and setup.py:28); read in full a2dplaya/audio/pipeline.py, a2dplaya/audio/resampler.py, a2dplaya/cast/manager.py, a2dplaya/airplay/manager.py, a2dplaya/audio/line_in.py; README.md; commit messages of all 4 commits.
- Nearest related material: the RTP-style header carries a constant zero timestamp, commented "Timestamp (simplified)" (a2dplaya/airplay/manager.py:375). Both output buffers discard their oldest bytes when the reader falls behind the writer (a2dplaya/cast/manager.py:281-282; a2dplaya/airplay/manager.py:202-203), and both readers sleep and send nothing when the buffer is empty (a2dplaya/cast/manager.py:121-122; a2dplaya/airplay/manager.py:366-368). The resampler ratio is fixed from the nominal rates (a2dplaya/audio/resampler.py:132).

---

## S08 Output

### R1-S08-01  Chromecast as an output destination, chosen by name through the web API
- Question: S08.1
- What was attempted: Chromecast devices are discovered on request with pychromecast (a2dplaya/cast/manager.py:186-190, 346-364) or, when that import fails, with zeroconf browsing `_googlecast._tcp.local.` (a2dplaya/cast/manager.py:192-196, 366-397). Results are held in a dict keyed by device name (a2dplaya/cast/manager.py:145, 353-362). A destination is chosen by posting its name to `/api/cast/play` (a2dplaya/web/server.py:253-263), which the page does from a per-device "Cast" button (a2dplaya/web/static/index.html:685-686, 693-700). Discovery is started from the page's "Discover" button (a2dplaya/web/static/index.html:419, 664-670). No default destination, no saved destination and no automatic start exist: `cast_to` has one caller (a2dplaya/web/server.py:262) and the config has no destination field (a2dplaya/config.py:84-91). No local sound device output and no file output exist in the tree: `git grep -n -i -E "aplay|PCM_PLAYBACK|paplay|sounddevice|pyaudio|pw-play|pacat"` returns no hits, and the only `open(` calls are the config file read and write (a2dplaya/config.py:127, 145).
- Presence: CURRENT. Registered as pipeline consumer "chromecast" (a2dplaya/app.py:84-87).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. Commit 47701ce states the feature exists ("Chromecast output via pychromecast/zeroconf with HTTP stream server serving WAV audio to Cast devices") and states no result.
- Depends on: pychromecast >=13.0 or zeroconf >=0.80 (requirements.txt:8, 11); the Chromecast being on the same network and able to reach this host on the stream port (a2dplaya/cast/manager.py:222-224); a person pressing Discover and then Cast on each run.
- Open questions: Was casting to a real device ever carried out? Two devices with the same friendly name share one dict key (a2dplaya/cast/manager.py:356): which one is cast to?
- Confidence: MEDIUM. Description INFERRED from clear code; STATED existence only.

### R1-S08-02  AirPlay receiver as an output destination through a built-in RAOP client
- Question: S08.1
- What was attempted: AirPlay receivers are discovered on request with zeroconf browsing `_raop._tcp.local.` and `_airplay._tcp.local.` (a2dplaya/airplay/manager.py:101-104, 216-255) or, when that import fails, by running `avahi-browse -rpt --no-db-lookup _raop._tcp` (a2dplaya/airplay/manager.py:106-110, 257-284). A destination is chosen by posting its name to `/api/airplay/play` (a2dplaya/web/server.py:290-300) from a per-device "Play" button (a2dplaya/web/static/index.html:729-730, 737-744). `stream_to` then tries the direct RAOP connection first (a2dplaya/airplay/manager.py:133-140).
- Presence: CURRENT. Registered as pipeline consumer "airplay" (a2dplaya/app.py:94-97).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. Commit 47701ce states "AirPlay output via RAOP protocol with zeroconf discovery and shairport-sync fallback" and states no result.
- Depends on: zeroconf, or the `avahi-browse` program (installed as `avahi-utils` or `avahi-tools`, install.sh:38, 47); a receiver that answers RTSP OPTIONS with 200 (a2dplaya/airplay/manager.py:314-316); the wire format in R1-S08-09.
- Open questions: Did any receiver accept this connection and play audio? When both service types are browsed, both use the port from their own record (a2dplaya/airplay/manager.py:230, 249-253): which port ends up stored for a device seen under both?
- Confidence: MEDIUM. Description INFERRED from clear code; STATED existence only.

### R1-S08-03  AirPlay output through shairport-sync as a second route
- Question: S08.1
- What was attempted: when the direct RAOP route does not succeed, `stream_to` calls `_connect_shairport` (a2dplaya/airplay/manager.py:142-149). That function runs `shairport-sync --version` to see whether the program exists (392-399), then logs a message and returns False (401-404). An attribute for an external process is declared and is only ever terminated, never assigned a process (a2dplaya/airplay/manager.py:81-82, 168-174).
- Presence: DISABLED. The function is reachable and always returns False (a2dplaya/airplay/manager.py:404).
- Outcome: FAILED
- Outcome evidence: STATED. Log text "shairport-sync based AirPlay not yet fully implemented" (a2dplaya/airplay/manager.py:403); the code agrees (unconditional `return False` at 404).
- Why it turned out that way: STATED. "This is a simplified approach - in practice you'd configure shairport-sync as a named pipe sink" (a2dplaya/airplay/manager.py:401-402). No further reason is given.
- Depends on: the `shairport-sync` program, which install.sh does not install (install.sh:32-47).
- Open questions: The class docstring lists "shairport-sync pipe (if available as external tool)" as a streaming method (a2dplaya/airplay/manager.py:57) and commit 47701ce lists a "shairport-sync fallback": was it expected to send audio to a receiver, given that the program is named in the repo only as an external tool?
- Confidence: HIGH. STATED evidence that the code agrees with.

### R1-S08-04  Browser playback through a WebSocket audio consumer
- Question: S08.1
- What was attempted: the README lists "Browser Playback - Listen directly in the browser via Web Audio API" (README.md:14) and draws "Web Browser (WebSocket)" as an output target (README.md:28-29). The server docstring lists "Browser-based audio playback via WebSocket audio streaming" and "Uses Flask for HTTP and flask-sock for WebSocket support" (a2dplaya/web/server.py:8, 10). The web server is registered as pipeline consumer "webui" (a2dplaya/app.py:120); its callback loops over `self._ws_clients` and calls `ws.send_audio(pcm_data)` on each (a2dplaya/web/server.py:96-112). Nothing ever adds a client to that list (occurrences: a2dplaya/web/server.py:51, 104, 112), no WebSocket route is defined (routes at a2dplaya/web/server.py:125-397), `flask_sock` is not imported and is not in requirements.txt or setup.py, and the page has no audio code (`git grep -n -i -E "AudioContext|<audio|WebSocket"` on a2dplaya/web/static/index.html returns no hits). The loop named for WebSocket broadcast only sleeps (a2dplaya/web/server.py:430-433).
- Presence: DISABLED. The consumer is registered and runs on every chunk over an always-empty list (a2dplaya/web/server.py:51, 104).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No comment or commit says why the WebSocket side is absent.
- Depends on: a WebSocket library that is not in the dependency lists (requirements.txt:1-17; setup.py:16-25).
- Open questions: Was a WebSocket route written and removed before the single code commit, or never written? The README feature list presents browser playback as available: was it ever heard in a browser?
- Confidence: MEDIUM. INFERRED from clear code: every occurrence of the client list is cited.

### R1-S08-05  Browser playback through an HTTP WAV route
- Question: S08.1
- What was attempted: route `GET /api/audio/stream` is documented in code as "PCM audio stream for browser-based playback via Web Audio API" (a2dplaya/web/server.py:364-366). Its generator yields one WAV header built from the pipeline format (371-386), then enters a loop commented "Stream PCM data" that reads the band levels into an unused variable and sleeps 20 ms, with no further `yield` (388-391). The page never requests this route (its `/api/` calls are at a2dplaya/web/static/index.html:468, 501, 562, 590, 599, 609, 637, 646, 655, 666, 694, 703, 710, 738, 747, 754, 776, 784) and the README API table omits it (README.md:89-102).
- Presence: CURRENT. The route is registered (a2dplaya/web/server.py:364).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No result is recorded.
- Depends on: Flask being installed (the route exists only inside `_create_flask_app`, a2dplaya/web/server.py:60-63, 114).
- Open questions: Was the PCM `yield` left out by oversight or pending a design choice? Does a client of this route ever receive anything after the 44-byte header?
- Confidence: MEDIUM. INFERRED from clear code: the generator body is seven statements.

### R1-S08-06  Chromecast transport: endless WAV over HTTP, started with pychromecast
- Question: S08.2
- What was attempted: `cast_to` builds the URL `http://{local_ip}:{stream_port}/stream.wav` (a2dplaya/cast/manager.py:222-224), where the local address is found by connecting a UDP socket toward the device and reading the socket name, with `127.0.0.1` on failure (449-458), and the port defaults to 8099 (a2dplaya/config.py:87). With pychromecast the code rediscovers all Chromecasts, matches by name, and calls `mc.play_media(stream_url, "audio/wav")` then `mc.block_until_active()` (a2dplaya/cast/manager.py:399-417). The Chromecast then fetches the URL from the in-process HTTP server (a2dplaya/cast/manager.py:55-61), which answers with `Content-Type: audio/wav` (90-94), a 44-byte RIFF/WAVE header for PCM format 1, 2 channels, 44100 Hz, 16 bits with both length fields set near 0xFFFFFFFF (99-113), and then raw pipeline PCM in slices of up to 8192 bytes for as long as `_streaming` is true (115-122, 293-300).
- Presence: CURRENT. a2dplaya/cast/manager.py:224 selects this route on main.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. Commit 47701ce states the design ("HTTP stream server serving WAV audio to Cast devices") and no result.
- Depends on: header values hard-coded as 44100/2/16 (a2dplaya/cast/manager.py:100-102) while the pipeline rate is configurable (a2dplaya/config.py:74, 212); the Chromecast accepting a WAV of declared maximum length; the Chromecast reaching this host on port 8099; pychromecast >=13.0 (requirements.txt:8).
- Open questions: Does a Chromecast play a WAV whose data length is 0xFFFFFFFF minus 44? If `A2DPLAYA_AUDIO_SAMPLE_RATE` is set to another rate, does the header still say 44100? What does the Chromecast do when `_get_local_ip` returns 127.0.0.1?
- Confidence: MEDIUM. Description INFERRED from clear code; STATED design only.

### R1-S08-07  Chromecast transport: MP3 over HTTP at /stream.mp3
- Question: S08.2
- What was attempted: the same HTTP server has a second route, `/stream.mp3`, that answers with `Content-Type: audio/mpeg` and an `icy-name` header (a2dplaya/cast/manager.py:56-57, 63-70) and then loops on `get_stream_chunk` (75-82), which reads up to 4096 bytes from `_stream_buffer` (284-291). Nothing writes to `_stream_buffer` (occurrences: a2dplaya/cast/manager.py:149, 260, 287, 289, 290). No code builds a URL ending in `/stream.mp3` (`git grep -n "stream.mp3"` returns only a2dplaya/cast/manager.py:56). The module docstring says "Audio is served as an HTTP MP3 stream" (a2dplaya/cast/manager.py:5) and a test asserts the default `stream_format` is "mp3" (tests/test_config.py:40); `stream_format` has no reader outside config and that test (`git grep -n stream_format`).
- Presence: DISABLED. The route is reachable by a manual request and has no data source.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. See R1-S07-04.
- Depends on: the MP3 encoder that is absent (R1-S07-04).
- Open questions: Which of the two formats did the author intend as the shipped one, given the docstring says MP3 and the commit message says WAV?
- Confidence: MEDIUM. INFERRED from clear code: all occurrences are cited.

### R1-S08-08  Chromecast control without pychromecast: HTTP POST to a DIAL-style URL
- Question: S08.2
- What was attempted: when importing pychromecast raises ImportError, `cast_to` calls `_cast_dial` (a2dplaya/cast/manager.py:226-238). It posts an XML body containing `<play><url>…</url><type>audio/wav</type></play>` with `Content-Type: application/xml` to `http://{device.host}:{device.port}/apps/CC1AD845` (424-438), using the host and port stored at discovery (27-34, 393-395). Any non-exception reply marks the device "playing" (440-443). The docstring calls it "DIAL REST protocol (basic fallback)" (423).
- Presence: CURRENT as a fallback branch (a2dplaya/cast/manager.py:232-238); reached only when pychromecast is not importable, and pychromecast is installed by `pip install -r requirements.txt` (install.sh:67; requirements.txt:8).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No result is recorded.
- Depends on: the port recorded by zeroconf discovery for `_googlecast._tcp` (a2dplaya/cast/manager.py:380, 388) being one that accepts this HTTP request; the receiver interpreting the XML body.
- Open questions: Does any Chromecast accept a plain HTTP POST with this body on the port advertised for `_googlecast._tcp`? Was this branch ever executed?
- Confidence: LOW. INFERRED; the branch is clear in code, and whether the request matches what a device accepts is not evidenced in the repo.

### R1-S08-09  AirPlay wire format: RTSP OPTIONS and ANNOUNCE, then PCM packets on the same TCP socket
- Question: S08.2
- What was attempted: `_connect_raop` opens a TCP socket to the receiver's host and port with a 10 s timeout (a2dplaya/airplay/manager.py:299-301), sends `OPTIONS * RTSP/1.0` and requires "RTSP/1.0 200" in the reply (303-316), then sends `ANNOUNCE rtsp://{host}/{port} RTSP/1.0` with an SDP body naming `m=audio 0 RTP/AVP 96` and `a=rtpmap:96 L16/{rate}/{channels}` (318-337). The ANNOUNCE reply is read and not checked (338). The docstring lists four steps, including "RTSP SETUP (establish audio channel)" and "RTSP RECORD (start streaming)" (292-296); no SETUP or RECORD request is sent between lines 298 and 349. The function then starts a thread (340-346) that writes, on the same TCP socket, a 12-byte header (version 2, payload type 96, 16-bit sequence number, timestamp fixed at zero, SSRC 1) followed by 1408 bytes of pipeline PCM (355-379). There is no pacing after a send: the loop sleeps only when fewer than 1408 bytes are buffered (359-368). No encryption, key exchange or authentication header appears in either request.
- Presence: CURRENT. First route tried by `stream_to` (a2dplaya/airplay/manager.py:133-140).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. The author labels the handshake "simplified" (a2dplaya/airplay/manager.py:292) and the timestamp "simplified" (375); no result is stated.
- Depends on: a receiver that accepts unencrypted `L16` audio written to the RTSP control connection after ANNOUNCE alone; the port taken from the mDNS record (a2dplaya/airplay/manager.py:230) or defaulting to 7000 in the avahi parser (279).
- Open questions: Does any AirPlay receiver play audio after OPTIONS and ANNOUNCE without SETUP and RECORD? What did the ANNOUNCE reply contain in practice? Does the function return True, and the UI show "playing" (a2dplaya/airplay/manager.py:136-138), even when the receiver rejected ANNOUNCE?
- Confidence: MEDIUM. Description INFERRED from clear code; STATED "simplified" labels agree with the code.

### R1-S08-10  Browser-facing formats: band levels as Server-Sent Events; audio formats declared and not delivered
- Question: S08.2
- What was attempted: the one stream the page consumes is `GET /api/eq/stream`, `text/event-stream`, one JSON array of ten floats per event about 30 times per second (a2dplaya/web/server.py:341-360), read with `EventSource` (a2dplaya/web/static/index.html:466-474). For audio, two formats are declared: raw pipeline PCM bytes passed to a client object's `send_audio` (a2dplaya/web/server.py:104-107), and `audio/wav` with a streaming RIFF header on `/api/audio/stream` (a2dplaya/web/server.py:379-397). Neither delivers PCM (R1-S08-04, R1-S08-05). The server docstring also lists a "WebSocket endpoint for real-time EQ visualization data" (a2dplaya/web/server.py:6), while the README diagram and API table name SSE for that data (README.md:36, 102).
- Presence: CURRENT for SSE band levels (a2dplaya/web/server.py:341); DISABLED for both audio formats (R1-S08-04, R1-S08-05).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No result is recorded.
- Depends on: Flask (a2dplaya/web/server.py:28-33); a browser with `EventSource`; the page's fallback to polling `/api/status` every 500 ms when the event stream errors (a2dplaya/web/static/index.html:475-480, 494-497, 541-544).
- Open questions: Each SSE client runs an endless generator on a server thread (a2dplaya/web/server.py:343-351, 409): how many clients were ever connected at once?
- Confidence: MEDIUM. Description INFERRED from clear code; no evidence of the result.

### R1-S08-11  Chromecast path when the device is missing, slow, silent or changed
- Question: S08.3
- What was attempted: behaviours present in code, each INFERRED from the cited lines. Unknown device name: logged, returns False (a2dplaya/cast/manager.py:213-217). Device not found during the pychromecast rediscovery: status "error", returns False (419-420). `_streaming` is set True before the attempt (220) and is not reset on either failure path (236-238, 419-420), so the consumer keeps filling the PCM buffer up to its cap (275-282). Exceptions other than ImportError raised inside `_cast_pychromecast` are not caught in `cast_to` (227-230). Slow reader: when the buffer passes 1 MB the oldest bytes are dropped (281-282). No input audio: the HTTP loop sleeps 10 ms and writes nothing; no silence is generated (116-122). Reader disconnects: BrokenPipeError and ConnectionResetError are caught and logged at debug level (123-124); `_streaming`, `_active_device` and the device status are left as they were. No listener for Chromecast status or disconnection is registered (`git grep -n -i -E "status_listener|register_.*listener"` returns no hits). Stream server failing to bind: logged, and `start` still logs that the manager started (166-169, 317-330). Casting to a second device while one is active: `cast_to` does not call `stop_casting` first, and replaces `_cast_connection` and `_active_device` (413-414). Stop: `stop_casting` clears flags and buffers and calls `quit_app` or `disconnect` on the stored connection (240-263).
- Presence: CURRENT. All cited lines are on main.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No result is recorded.
- Depends on: the single shared PCM buffer and the single-threaded stream server (R1-S08-15).
- Open questions: What does a listener hear after the 1 MB trim? Does the Chromecast keep the HTTP connection open through a long silence with no bytes sent? After a Chromecast drops the stream, does the page still show it as "playing" until Stop is pressed?
- Confidence: MEDIUM. INFERRED from clear code; no evidence of the result.

### R1-S08-12  AirPlay path when the receiver is missing, slow or drops
- Question: S08.3
- What was attempted: behaviours present in code, each INFERRED from the cited lines. Unknown device name: logged, returns False (a2dplaya/airplay/manager.py:123-127). Connection or OPTIONS failure: `_connect_raop` returns False (314-316, 351-353), the shairport-sync route is tried and returns False (R1-S08-03), then status is set to "error", `_streaming` to False and `_active_device` to None (151-154). Slow receiver: `sendall` blocks the stream thread, and the buffer drops its oldest bytes past 512 KB (202-203). Send error during streaming: logged, loop exits and `_streaming` is set False (380-384); the device status and `_active_device` are not changed in that path. The socket keeps the 10 s timeout set at connect (300). No reconnect code exists (`git grep -n -i reconnect` returns only Bluetooth settings and one docstring). Stop: socket closed, thread joined for up to 5 s, buffer cleared, all statuses set to "idle" (156-186).
- Presence: CURRENT. All cited lines are on main.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No result is recorded.
- Depends on: the RAOP route of R1-S08-09.
- Open questions: After a send error, does `/api/status` report `streaming: false` together with an `active_device` whose status is still "playing" (a2dplaya/web/server.py:152-155; a2dplaya/airplay/manager.py:205-212)?
- Confidence: MEDIUM. INFERRED from clear code; no evidence of the result.

### R1-S08-13  Pipeline isolation of a failing output consumer
- Question: S08.3
- What was attempted: the pipeline copies the consumer table under a lock, then calls each consumer in turn inside its own try/except; an exception is logged with the consumer's name and the remaining consumers are still called (a2dplaya/audio/pipeline.py:249-264). The calls are synchronous on the one pipeline thread, so time spent inside one consumer delays the others and the next chunk (INFERRED from the plain loop at 253-260). The three registered consumers only append to a buffer or iterate an empty list (a2dplaya/cast/manager.py:275-282; a2dplaya/airplay/manager.py:197-203; a2dplaya/web/server.py:102-112). A subsystem whose start-up raises is logged and left as None, and the rest of the application continues (a2dplaya/app.py:82-101, 117-125). `unregister_consumer` exists (a2dplaya/audio/pipeline.py:170-175) and is called only by a test (tests/test_pipeline.py:45).
- Presence: CURRENT. a2dplaya/audio/pipeline.py:249-264 on main.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No result is recorded.
- Depends on: consumers returning quickly.
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from clear code; no evidence of the result.

### R1-S08-14  Every processed chunk is offered to all registered outputs at once
- Question: S08.4
- What was attempted: the pipeline delivers each chunk to every registered consumer (a2dplaya/audio/pipeline.py:249-260), and three are registered at start: "chromecast", "airplay", "webui" (a2dplaya/app.py:87, 97, 120). The Chromecast and AirPlay managers hold separate `_streaming` flags and separate buffers (a2dplaya/cast/manager.py:148-150; a2dplaya/airplay/manager.py:69-73), and neither play route stops the other (a2dplaya/web/server.py:253-263, 290-300), so one Chromecast and one AirPlay receiver can both be active (INFERRED from those lines). `/api/status` reports both states side by side (a2dplaya/web/server.py:148-155). The README diagram draws the pipeline feeding three targets (README.md:20-30). No code aligns timing between the two outputs (see R1-S07-07).
- Presence: CURRENT. All cited lines are on main.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No result is recorded.
- Depends on: each output's own buffering (R1-S07-06).
- Open questions: Were a Chromecast and an AirPlay receiver ever played together, and how far apart in time were they?
- Confidence: MEDIUM. INFERRED from clear code; no evidence of the result.

### R1-S08-15  One stream per output type: single buffer, single connection, single-threaded stream server
- Question: S08.4
- What was attempted: within each output type the code holds one active device name and one connection: `_active_device` and `_cast_connection` for Chromecast (a2dplaya/cast/manager.py:159-161), `_active_device`, `_raop_socket` and `_stream_thread` for AirPlay (a2dplaya/airplay/manager.py:70, 78-79). The Chromecast PCM buffer is one bytearray whose bytes are removed as they are read (a2dplaya/cast/manager.py:293-300), so two HTTP readers would each receive part of the audio (INFERRED). The stream server is the standard-library `HTTPServer` driven by `handle_request()` on one thread (a2dplaya/cast/manager.py:19, 318-338), and a `/stream.wav` response loops inside the handler until streaming stops (115-122), so a second request is not served while the first is in progress (INFERRED from the single thread). No Cast group, stereo pair or multi-room code exists (`git grep -n -i -E "group|multi|simultan"` in *.py and *.md returns only the classifier word "Multimedia" at setup.py:37).
- Presence: CURRENT. All cited lines are on main.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No result is recorded.
- Depends on: the design choice of one buffer per manager.
- Open questions: What happens to the first AirPlay socket and thread when `stream_to` is called for a second receiver without Stop (a2dplaya/airplay/manager.py:129-131, 340-346)? Does a Chromecast that opens a second HTTP connection for the same URL (a probe followed by a play request) stall on this server?
- Confidence: MEDIUM. INFERRED from clear code; no evidence of the result.

---

## S09 User interface and control surfaces

### R1-S09-01  Single-page web UI served by the application
- Question: S09.1
- What was attempted: one HTML file with inline CSS and script (a2dplaya/web/static/index.html:1-805) is served at `/` (a2dplaya/web/server.py:125-127) on the configured host and port, default 0.0.0.0:8080 (a2dplaya/config.py:106-107). The README points the user to `http://<device-ip>:8080` (README.md:51-52) and calls it a "Mobile-friendly control panel with 10-band EQ visualizer" (README.md:13). No hardware control surface exists: `git grep -n -i -E "gpio|\bled\b|lcd|oled|RPi|button"` in *.py, *.sh, *.md and *.txt returns no hits.
- Presence: CURRENT. WebServer is started last in `AppController.start` (a2dplaya/app.py:117-121).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No screenshot, log or note records the page in use.
- Depends on: Flask >=2.0 (setup.py:17); a browser on the same network; the page being shipped as package data (setup.py:11-14).
- Open questions: Was the page ever opened against a running instance?
- Confidence: MEDIUM. Description INFERRED from clear code; STATED existence only.

### R1-S09-02  REST API over HTTP
- Question: S09.1
- What was attempted: 21 JSON or stream routes under `/api/` are defined in one Flask app (a2dplaya/web/server.py:131-397; `grep -n "@app.route" a2dplaya/web/server.py` lists 22 routes, one of them `/`). The README lists "REST API - Full API for integration and automation" (README.md:15) and tabulates 12 of them (README.md:89-102). The page uses the same routes (a2dplaya/web/static/index.html:468-784). A WebSocket endpoint is named in the server docstring (a2dplaya/web/server.py:6) and has no route (R1-S08-04).
- Presence: CURRENT. a2dplaya/web/server.py:114-399 on main.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No result is recorded; no test imports the web module (tests import only a2dplaya.config and a2dplaya.audio.*, tests/test_config.py:8, tests/test_fft_analyzer.py:7, tests/test_pipeline.py:8-9, tests/test_resampler.py:6).
- Depends on: Flask; without Flask no API route exists (a2dplaya/web/server.py:60-63, 411-412).
- Open questions: NONE
- Confidence: MEDIUM. Description INFERRED from clear code; STATED existence only.

### R1-S09-03  Command line of the entry module
- Question: S09.1
- What was attempted: `python -m a2dplaya` or the console script `a2dplaya` (a2dplaya/__main__.py:91-92; setup.py:27-30) parses six flags with argparse (a2dplaya/__main__.py:17-52), prints three lines with a version string and the web URL (83-86), and blocks until a signal (88; a2dplaya/app.py:166-174). The README shows five invocations (README.md:57-72). There is no interactive prompt and no subcommand.
- Presence: CURRENT. a2dplaya/__main__.py:16-88 on main.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No result is recorded.
- Depends on: Python >=3.8 (setup.py:15).
- Open questions: NONE
- Confidence: MEDIUM. Description INFERRED from clear code.

### R1-S09-04  JSON configuration file and environment variables
- Question: S09.1
- What was attempted: settings are read at start from `~/.config/a2dplaya/config.json`, or the path given with `--config` (a2dplaya/config.py:20-21, 131-153; a2dplaya/__main__.py:55), then overridden by nine `A2DPLAYA_*` environment variables (a2dplaya/config.py:199-230), then by command-line flags (a2dplaya/__main__.py:57-67). The README documents the file path and six of the variables (README.md:74-85). Detail is in R1-S10-05.
- Presence: CURRENT. a2dplaya/config.py:131-162 on main.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. Unit tests exist (tests/test_config.py:16-148) with no recorded result.
- Depends on: a person creating the file by hand; the application never writes it (R1-S10-05).
- Open questions: NONE
- Confidence: MEDIUM. Description INFERRED from clear code.

### R1-S09-05  Built-in static HTTP server used when Flask cannot be imported
- Question: S09.1
- What was attempted: when the Flask import fails, a flag is cleared (a2dplaya/web/server.py:27-33) and the server thread runs `_run_builtin_server`: a standard-library `HTTPServer` with `SimpleHTTPRequestHandler`, after assigning `handler.directory = self._static_dir` on the handler class (a2dplaya/web/server.py:411-428). The docstring says "Falls back to a built-in simple HTTP server if Flask is not available" (a2dplaya/web/server.py:11). No `/api/` route exists in this mode, so the page's requests to `/api/...` have no handler (INFERRED: the routes are defined only at a2dplaya/web/server.py:114-399).
- Presence: CURRENT as a fallback branch (a2dplaya/web/server.py:411-412). Flask is the one entry in `install_requires` (setup.py:16-18), so the branch is reached only in an environment without it.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No result is recorded.
- Depends on: how the standard-library handler resolves its directory.
- Open questions: Which directory does the handler serve, given that `directory` is assigned on the class (a2dplaya/web/server.py:417) and not passed to the handler's constructor? Does `/` return the page in this mode?
- Confidence: LOW. INFERRED; the branch is clear, and the directory actually served is not settled by the repo.

### R1-S09-06  Web page: actions and status items
- Question: S09.2
- What was attempted: status items: four dots for Bluetooth, Chromecast, AirPlay and Audio (a2dplaya/web/static/index.html:351-368), set from `/api/status` (510-539); ten level bars (370-384, 483-491); volume percentage (387-391, 546-555); a text line "Connecting...", "Connected" or "Disconnected - retrying..." (456-458, 504-506); per-device lines showing address or host and a state word (625, 682, 726). Actions: volume slider (559-567); Bluetooth "Scan" and "Make Discoverable" (404-405, 588-605), and per device "Pair", "Connect", "Disconnect" (628-630, 636-661); Chromecast "Discover", "Cast", "Stop" (419, 685-687, 693-705); AirPlay "Discover", "Play", "Stop" (431, 729-731, 737-749); a Bluetooth / Line In tab pair that shows or hides page sections and loads the capture device list (393-397, 569-579); line-in "Start", "Stop" and per-device "Use" (445-446, 768, 774-785). The tab switch sends no request that changes the audio source (570-579). The page has no control to turn discoverability off (it always sends `enabled: true`, 597-603), no trust, unpair or forget action, no track or source-device name display, and no destination-volume control.
- Presence: CURRENT. a2dplaya/web/static/index.html:345-803 on main.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No result is recorded.
- Depends on: the REST routes of R1-S09-07; polling every 500 ms (a2dplaya/web/static/index.html:494-497).
- Open questions: The Audio dot lights when `audio.source` is set (535-539), and the source label is set on the first chunk and never cleared (a2dplaya/app.py:179, 185; `git grep -n current_source`): does the dot stay lit after audio stops? "Start" for line-in calls a function that returns early unless `enable_line_in` is already true in config (a2dplaya/audio/line_in.py:68-70): does the button do anything on a default configuration?
- Confidence: MEDIUM. Description INFERRED from clear code; no evidence of the result.

### R1-S09-07  REST API: endpoints and the status document
- Question: S09.2
- What was attempted: endpoints defined (all in a2dplaya/web/server.py): `GET /api/status` (131-157); `GET/POST /api/volume` (159-172); Bluetooth `GET devices` (176-181), `POST scan` (183-189), `POST discoverable` (191-198), `POST pair` (200-210), `POST connect` (212-222), `POST disconnect` (224-234); Chromecast `GET devices` (238-243), `POST discover` (245-251), `POST play` (253-263), `POST stop` (265-271); AirPlay `GET devices` (275-280), `POST discover` (282-288), `POST play` (290-300), `POST stop` (302-308); line-in `GET devices` (312-317), `POST start` (319-329), `POST stop` (331-337); `GET /api/eq/stream` (341-360); `GET /api/audio/stream` (364-397). The status document holds: adapter info and connected device; pipeline counters under the key `pipeline_running`, volume, source label, line-in flag; cast and AirPlay active device and streaming flag; ten band levels (137-157). The README table (README.md:89-102) omits discoverable, disconnect, both `devices` lists for cast and AirPlay, `airplay/stop`, the three line-in routes and `audio/stream`. `trust_device` has no route and no caller (`git grep -n "trust_device("` returns only its definition, a2dplaya/bluetooth/manager.py:212). No route removes a paired device, changes configuration, or shuts the application down.
- Presence: CURRENT. a2dplaya/web/server.py:131-397 on main.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No result is recorded.
- Depends on: Flask; discovery routes block the request for the discovery timeout, default 10 s (a2dplaya/config.py:88, 97; a2dplaya/cast/manager.py:389; a2dplaya/airplay/manager.py:243).
- Open questions: NONE
- Confidence: MEDIUM. Description INFERRED from clear code; no evidence of the result.

### R1-S09-08  Command-line flags and what each sets
- Question: S09.2
- What was attempted: `--config/-c PATH` (a2dplaya/__main__.py:20-24, 55); `--port/-p` sets the web port (25-30, 58-59); `--debug/-d` sets log level DEBUG and `web.debug` (31-35, 60-62); `--bt-adapter` sets the preferred adapter (36-40, 63-64); `--line-in DEVICE` sets the capture device and enables line-in (46-50, 65-67); `--no-bluetooth`, described as "Disable Bluetooth (line-in only mode)", sets the preferred adapter to the string `__disabled__` (41-45, 79-81). `web.debug` has no reader (the Flask server is started with `debug=False`, a2dplaya/web/server.py:404-410). The command line offers no status query and no way to pick an output destination.
- Presence: CURRENT. a2dplaya/__main__.py:17-81 on main.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No result is recorded.
- Depends on: for `--no-bluetooth`, the adapter-selection code. INFERRED: with that value no D-Bus adapter path matches, a RuntimeError is raised (a2dplaya/bluetooth/manager.py:247-256), and the except branch calls the bluetoothctl set-up, which does not read the preferred adapter and runs `bluetoothctl power on` (a2dplaya/bluetooth/manager.py:103-106, 271-294).
- Open questions: Does `--no-bluetooth` leave the adapter untouched on a machine where `bluetoothctl` is present?
- Confidence: MEDIUM. INFERRED from clear code; no evidence of the result.

### R1-S09-09  First-time setup as implemented
- Question: S09.3
- What was attempted: the sequence in the repo is: (1) `chmod +x install.sh` and `./install.sh` (README.md:43-45); (2) answer "Continue? [Y/n]" (install.sh:145-151); (3) system packages, virtual environment and Bluetooth configuration run without further input, apart from sudo (install.sh:153-155); (4) answer "Install as systemd service? [Y/n]" (install.sh:157-161); (5) the installer prints start commands and a web URL built from `hostname -I` (install.sh:163-175); (6) start by hand with `source venv/bin/activate` and `python -m a2dplaya` (README.md:47-49) or with `sudo systemctl start a2dplaya` (install.sh:135, 172); (7) open the web UI (README.md:51-52). The README stops there. What the page then offers for a first connection: "Make Discoverable" (a2dplaya/web/static/index.html:405), which sets the adapter discoverable and pairable (a2dplaya/bluetooth/manager.py:168-183), "Scan", "Pair", "Connect" (404, 628-629), then "Discover" and "Cast" or "Play" for an output (419, 431, 686, 730). No setup wizard, no first-run detection and no prompt for a device name exist; the Bluetooth alias is fixed as "A2DPlaya" (a2dplaya/bluetooth/manager.py:264, 292). No configuration file is created by the installer or by the application (R1-S10-05).
- Presence: CURRENT. install.sh:138-175 and README.md:40-53 on main.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No record of an installation run exists in the repo.
- Depends on: a Debian/Ubuntu or Fedora/RHEL system (install.sh:4, 15-24); sudo rights; an interactive terminal for the two `read` prompts.
- Open questions: The README gives no pairing instructions: which sequence of page buttons and phone actions leads to audio arriving? The installer adds the user to the `bluetooth` group (install.sh:82-85) and prints nothing about logging in again: does the first manual start have that group membership?
- Confidence: MEDIUM. Description from STATED documentation and clear script text; no evidence of the result.

### R1-S09-10  Web page: how errors and state changes reach the user
- Question: S09.4
- What was attempted: state reaches the page by polling `/api/status` every 500 ms (a2dplaya/web/static/index.html:494-508) and by the SSE band stream (466-481). A failed status request changes the footer text to "Disconnected - retrying..." (505-507). Device lists carry a state word from the server: "idle", "connecting", "playing" or "error" for outputs (a2dplaya/cast/manager.py:36; a2dplaya/airplay/manager.py:37; a2dplaya/web/static/index.html:682, 726) and Connected/Paired/Available for Bluetooth (625). Lists are refreshed on fixed timers after an action (591-593, 642, 651, 660, 699, 704, 743, 748), and the output lists are refreshed by running discovery again (699, 743). Action failures are not shown: the Pair, Connect, Disconnect, Cast and Play handlers do not read the `success` field of the reply (636-661, 693-700, 737-744); caught exceptions go to `console.error` or are dropped (594, 604, 612, 669, 713, 771). The page has no alert, banner or message area (`git grep -n -i -E "alert\(|toast"` on index.html returns no hits).
- Presence: CURRENT. a2dplaya/web/static/index.html:460-803 on main.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No result is recorded.
- Depends on: server-side state being current. INFERRED: the Bluetooth dot uses `connected_device` (a2dplaya/web/static/index.html:517), which is derived from `audio_connected` (a2dplaya/bluetooth/manager.py:131-137), and that flag is refreshed only inside the D-Bus discovery loop (a2dplaya/bluetooth/manager.py:320, 390-395); no D-Bus signal handler exists (`git grep -n -E "PropertiesChanged|add_signal_receiver"` returns no hits).
- Open questions: Does the Bluetooth dot change when a phone connects or disconnects with no scan in progress? After a failed cast, does the user see anything other than the word "error" on the next rediscovery?
- Confidence: MEDIUM. INFERRED from clear code; no evidence of the result.

### R1-S09-11  API replies and log lines as the error channel
- Question: S09.4
- What was attempted: API routes answer `{"error": ...}` with HTTP 503 when a subsystem object is missing (for example a2dplaya/web/server.py:134-135, 179-180, 241-242) and 400 when a required field is absent (207-208, 219-220, 260-261, 297-298); action routes answer `{"success": true|false}` with HTTP 200 (210, 222, 234, 263, 300); several routes answer a fixed status without checking the result: `{"status": "scanning"}` (188-189), `{"status": "ok"}` (197-198), `{"status": "started"}` for line-in (328-329), `{"status": "stopped"}` (270-271, 307-308, 336-337). The cause of a failure is written to the Python log only (for example a2dplaya/cast/manager.py:216, 236, 445; a2dplaya/airplay/manager.py:126, 140, 352, 381; a2dplaya/bluetooth/manager.py:437, 473). Subsystem start-up failures are logged as warnings and the subsystem is absent from then on (a2dplaya/app.py:78-80, 89-91, 99-101, 113-115, 123-125).
- Presence: CURRENT. All cited lines are on main.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No result is recorded.
- Depends on: a person reading the process log (R1-S10-07).
- Open questions: An exception raised inside a route (for example from `_cast_pychromecast`, a2dplaya/cast/manager.py:227-230) is not caught by the route: what does the client receive?
- Confidence: MEDIUM. INFERRED from clear code; no evidence of the result.

---

## S10 Lifecycle and operations

### R1-S10-01  Interactive installer script
- Question: S10.1
- What was attempted: `install.sh` runs with `set -e` (install.sh:6), detects apt, dnf or yum (15-24) and performs four steps. Automated: system packages through sudo (27-51; lists at 33-39 and 42-47); a virtual environment at `<script dir>/venv` created with `python3 -m venv`, pip upgrade, `pip install -e` of the repo and `pip install -r requirements.txt` (54-70); `systemctl enable` and `start` of `bluetooth`, adding `$USER` to the `bluetooth` group, and appending `Class = 0x200414` and `AutoEnable=true` to /etc/bluetooth/main.conf when no line starts with those keys (73-104); writing, reloading and enabling a systemd unit (107-136). Manual: two yes/no prompts (145, 157), entering the sudo password, starting the application (135, 167-172), and the `chmod` in the README (README.md:44). The application runs in place from the cloned directory; nothing is copied elsewhere (57, 110, 122). With an unrecognised package manager the script prints a warning and still runs the later steps (21-24, 30-48). `shairport-sync` and `python3-alsaaudio` are not installed (32-47; requirements.txt:16-17).
- Presence: CURRENT. install.sh:1-175 on main, mode 100755 (`git ls-files -s install.sh`).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No install log or note exists.
- Depends on: Debian/Ubuntu or Fedora/RHEL package names (install.sh:4); network access for apt and pip; the venv being created without the `--system-site-packages` option (install.sh:61) while the D-Bus binding is installed as the system package `python3-dbus` (install.sh:37, 46) and `dbus-python` is commented out of the pip list (requirements.txt:13-14).
- Open questions: Can the venv interpreter named in the unit (install.sh:123) import `dbus`? If not, does every installed run take the bluetoothctl branch (a2dplaya/bluetooth/manager.py:97-106)? Was the script ever run to completion on either distribution family? Do the appended main.conf lines land in the section of that file where BlueZ reads them?
- Confidence: MEDIUM. Description INFERRED from clear script text; no evidence of the result.

### R1-S10-02  systemd unit generated by the installer: boot start and restart policy
- Question: S10.2
- What was attempted: the optional fourth installer step writes /etc/systemd/system/a2dplaya.service from a heredoc (install.sh:111-129): `After=bluetooth.target network.target sound.target`, `Wants=bluetooth.target` (116-117), `Type=simple`, `User=$USER`, `WorkingDirectory` set to the repo directory, `ExecStart=<repo>/venv/bin/python -m a2dplaya` (120-123), `Restart=on-failure`, `RestartSec=5` (124-125), `WantedBy=multi-user.target` (128). The unit is enabled and not started (131-135). No unit file is stored in the repo, and no watchdog, start limit, environment line or dependency on a user audio session appears in the heredoc.
- Presence: CURRENT. install.sh:107-136, 157-161 on main.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No boot log or service status output exists in the repo.
- Depends on: systemd; the user answering yes at install.sh:157; `network.target` ordering (not `network-online.target`); the process exiting with a failure status for a restart to occur.
- Open questions: Does the service start correctly at boot before a network address is assigned, given that the web and stream servers bind 0.0.0.0? If a worker thread dies while the main thread stays blocked (see R1-S10-03), does systemd ever see a failure?
- Confidence: MEDIUM. Description INFERRED from clear script text; no evidence of the result.

### R1-S10-03  In-process start order, fault tolerance at start, and shutdown
- Question: S10.2
- What was attempted: `AppController.start` brings subsystems up in a fixed order: pipeline, Bluetooth, Chromecast, AirPlay, line-in, web server (a2dplaya/app.py:65-125). Each of the last five is wrapped in try/except; on failure a warning is logged and the attribute is set to None (78-80, 89-91, 99-101, 113-115, 123-125). The pipeline start is not wrapped (66-67). SIGINT and SIGTERM call `stop` (128-129, 188-191), which stops subsystems in reverse order and sets the event the main thread waits on (136-164, 166-174). All worker threads are daemon threads (a2dplaya/audio/pipeline.py:139-143; a2dplaya/cast/manager.py:323-327; a2dplaya/web/server.py:69-81; a2dplaya/airplay/manager.py:341-345; a2dplaya/audio/line_in.py:73-77). The pipeline loop catches and logs per-chunk exceptions and continues (a2dplaya/audio/pipeline.py:242-273). No code restarts a subsystem after start-up, retries a failed output, or reconnects a Bluetooth device: the settings `reconnect_delay` and `reconnect_max_retries` (a2dplaya/config.py:55-56, 64-65) have no reader (`git grep -n -i reconnect`). INFERRED: because the main thread only waits on an event (a2dplaya/app.py:170) and the workers are daemon threads, the death of a worker thread does not end the process.
- Presence: CURRENT. a2dplaya/app.py:50-191 on main.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No result is recorded.
- Depends on: the systemd policy in R1-S10-02 for anything beyond the first start.
- Open questions: The Chromecast stream server is driven by `handle_request()` in a loop (a2dplaya/cast/manager.py:332-338) and stopped with `self._http_server.shutdown()` (340-344): does that call return, or does shutdown hang? `WebServer.stop` joins the Flask thread for 5 s and has no call that stops Flask (a2dplaya/web/server.py:89-94): does the process exit promptly on SIGTERM? Logging is configured after the first `logger.info` in `start` and a second time (a2dplaya/app.py:55-63; a2dplaya/__main__.py:70-74): does the second call have any effect?
- Confidence: MEDIUM. INFERRED from clear code; no evidence of the result.

### R1-S10-04  Permissions and privileges the design relies on
- Question: S10.3
- What was attempted: the installer calls sudo for package installation (install.sh:31-32, 41), for enabling and starting the Bluetooth service (78-79), for group membership (84), for appending to /etc/bluetooth/main.conf (92-94, 99) and for writing and enabling the unit (113, 131-132). The application is then run as an ordinary user: the unit sets `User=$USER` (install.sh:121) and the manual start uses the venv (README.md:48-49). The only access-rights step for that user is `usermod -aG bluetooth` when the group exists (install.sh:82-85). At run time the process needs: the D-Bus system bus and BlueZ adapter and device methods (a2dplaya/bluetooth/manager.py:98-99, 259-264) or the `bluetoothctl` program (274-294); TCP listen on 8080 and 8099, both above 1024 (a2dplaya/config.py:87, 107); mDNS browsing; ALSA capture device access for line-in (a2dplaya/audio/line_in.py:153-161, 183-198). No polkit rule, D-Bus policy file, udev rule, capability setting or `audio` group step exists in the repo (`git grep -n -i -E "polkit|setcap|capabilit|udev"` returns only the Bluetooth agent capability constant, a2dplaya/config.py:57, 68). The code has no check of the user id (`git grep -n -E "geteuid|getuid"` returns no hits).
- Presence: CURRENT. install.sh:27-136 on main.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No result is recorded.
- Depends on: the distribution's default D-Bus policy for BlueZ and its `bluetooth` group; `$USER` at install time.
- Open questions: Is membership of the `bluetooth` group sufficient for a system service user to power the adapter and pair over D-Bus on the target distributions? Does the service user have access to the ALSA capture device without the `audio` group?
- Confidence: MEDIUM. Description INFERRED from clear script and code; no evidence of the result.

### R1-S10-05  Configuration: location, settable items, defaults, precedence
- Question: S10.4
- What was attempted: location: `~/.config/a2dplaya/config.json`, JSON, or a path from `--config` (a2dplaya/config.py:19-21, 139; a2dplaya/__main__.py:55). Structure: five sections as dataclasses plus `log_level` and `data_dir` (a2dplaya/config.py:60-121). Defaults: Bluetooth discovery timeout 15 s, adapter unset (54, 63-66); audio 44100 Hz, 2 channels, 2 bytes, chunk 4096, line-in off, volume 1.0 (40-43, 74-81); cast stream port 8099, discovery timeout 10 s (87-88); AirPlay discovery timeout 10 s (97); web host 0.0.0.0, port 8080 (106-107); log level INFO (120). Loading: a missing file gives defaults; invalid JSON or a KeyError gives defaults with a warning (143-153); unknown keys inside a section are dropped (165-197). Precedence: file, then nine environment variables (206-216), then command-line flags (a2dplaya/__main__.py:57-67). The README states "Environment variables override config file values" and lists six variables (README.md:76-85); `A2DPLAYA_WEB_DEBUG`, `A2DPLAYA_WEB_SECRET_KEY` and `A2DPLAYA_AUDIO_SAMPLE_RATE` are in code only (a2dplaya/config.py:209-210, 212). `AppConfig.save` exists (123-129) and its only caller is a test (tests/test_config.py:62), so the application never writes the file and run-time changes such as volume (a2dplaya/web/server.py:169) are not persisted (INFERRED from the absence of a caller).
- Presence: CURRENT. a2dplaya/config.py:112-230 on main.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. Fourteen unit tests cover defaults, save/load, environment overrides and partial dicts (tests/test_config.py:16-148); no result is recorded (R1-S11-01).
- Depends on: `$HOME` of the user running the process (a2dplaya/config.py:20).
- Open questions: A value of the wrong type in the JSON file raises neither of the two caught exception types at load (a2dplaya/config.py:149): what happens at start with such a file?
- Confidence: MEDIUM. Description INFERRED from clear code; no evidence of the result.

### R1-S10-06  Configuration fields and constants that no code reads
- Question: S10.4
- What was attempted: the following settings are defined, can be set in the file, and have no reader outside a2dplaya/config.py and tests (each checked with `git grep -n <name>`): `bluetooth.reconnect_delay`, `bluetooth.reconnect_max_retries`, `bluetooth.auto_accept_pairing`, `bluetooth.agent_capability` (a2dplaya/config.py:64-65, 67-68); `audio.eq_enabled` (81); `cast.stream_format`, `cast.stream_sample_rate`, `cast.stream_channels` (89-91); `airplay.stream_buffer_size` (100); `web.debug` (108; set at a2dplaya/__main__.py:62, never read); `data_dir` (121, 195-196). The constants `CHROMECAST_SAMPLE_WIDTH` and `AIRPLAY_SAMPLE_WIDTH` (29, 37) are likewise unread. `audio.chunk_size` is read by line-in capture (a2dplaya/audio/line_in.py:41) and, in the pipeline, only to compute a variable that is not used (a2dplaya/audio/pipeline.py:227).
- Presence: DISABLED. The fields exist on main and changing them has no effect on behaviour.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No comment explains the unread fields.
- Depends on: NONE beyond the code state at commit 70ad071.
- Open questions: Were these fields placeholders for features not written (Bluetooth agent, reconnect, MP3 format, EQ switch), or remnants of code removed before the single code commit?
- Confidence: MEDIUM. INFERRED from grep results over the whole tree.

### R1-S10-07  Logging and run-time diagnostics
- Question: S10.5
- What was attempted: every module uses the standard `logging` module with a module-level logger (for example a2dplaya/audio/pipeline.py:30; a2dplaya/cast/manager.py:24). `logging.basicConfig` is called with level from `log_level` and the format `%(asctime)s [%(name)s] %(levelname)s: %(message)s` in the entry module (a2dplaya/__main__.py:70-74) and again in `AppController.start` (a2dplaya/app.py:58-63). The level comes from the file, `A2DPLAYA_LOG_LEVEL` (a2dplaya/config.py:215) or `--debug` (a2dplaya/__main__.py:60-61). No log file, rotation or syslog handler is configured; output goes to the process's standard streams, and the unit sets no output directive (install.sh:119-125). HTTP request lines of the stream server are routed to debug level (a2dplaya/cast/manager.py:126-128). Diagnostics beyond logs: `GET /api/status` returns adapter info, pipeline counters (`chunks_processed`, `bytes_processed`, `underruns`, `overruns`, `last_chunk_time`), source label and output state (a2dplaya/web/server.py:131-157; a2dplaya/audio/pipeline.py:82-89). `underruns` is never incremented, and `overruns` is incremented only if `deque.append` raises (a2dplaya/audio/pipeline.py:86-87, 214-218; `git grep -n -E "underruns|overruns"`). No health-check route, metrics export or debug dump exists (`git grep -n -i -E "health|watchdog|sd_notify"` returns no hits).
- Presence: CURRENT. All cited lines are on main.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No log output is stored in the repo.
- Depends on: the service manager capturing standard streams when run as a unit.
- Open questions: Does a full pipeline deque (maxlen 100, a2dplaya/audio/pipeline.py:77) ever raise on append, or does it discard the oldest chunk silently, leaving both counters at zero?
- Confidence: MEDIUM. Description INFERRED from clear code; no evidence of the result.

### R1-S10-08  Packaging as a setuptools project installed in editable mode; no update path
- Question: S10.6
- What was attempted: `setup.py` defines package `a2dplaya` version 1.0.0 (setup.py:5-7), includes `web/static/*` as package data (11-14), requires Python >=3.8 (15) and Flask >=2.0 (16-18), offers extras `chromecast`, `zeroconf` and `full` (19-26), a console script (27-31) and the classifier "Development Status :: 4 - Beta" (33). The same version string appears at a2dplaya/__init__.py:19 and a2dplaya/__main__.py:83. The installer installs the package in editable mode from the clone (install.sh:66). All version constraints are lower bounds (requirements.txt:5, 8, 11; setup.py:17-24) and no lock file exists. No built distribution, container file, Debian package, release tag or CI configuration is present (`git tag` prints nothing; `find` for Dockerfile*, *.toml, *.cfg, .github returns nothing). No update mechanism exists: no script pulls, upgrades or migrates (`git grep -n -i -E "upgrade|update|migrat"` returns only the pip self-upgrade at install.sh:65, `apt-get update` at install.sh:31, and identifiers or comments about refreshing band levels, statistics, mDNS records and page elements, for example a2dplaya/config.py:48 and a2dplaya/web/static/index.html:483). Re-running the installer reuses an existing venv (install.sh:60-62) and skips main.conf lines already present (91, 98).
- Presence: CURRENT. setup.py:1-39 on main.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No result is recorded.
- Depends on: unpinned dependency versions resolved at install time.
- Open questions: Which versions of Flask, pychromecast and zeroconf was the code written against? The pychromecast calls used (a2dplaya/cast/manager.py:350, 358-359, 403) match which release of that library?
- Confidence: MEDIUM. Description INFERRED from clear manifests; no evidence of the result.

---

## S11 Testing and measured behaviour

### R1-S11-01  Unit tests for config, resampler, spectrum analyser and pipeline; no recorded result
- Question: S11.1
- What was attempted: four `unittest` modules hold 44 test functions (`git grep -c "def test_" -- tests`: test_config.py 14, test_fft_analyzer.py 9, test_pipeline.py 12, test_resampler.py 9). Coverage: configuration defaults, save and load, environment overrides, dict parsing (tests/test_config.py:16-148); band count, silence, clamping, Hann window, mono extraction (tests/test_fft_analyzer.py:10-94); pipeline start/stop, volume property and scaling, consumer registration, delivery of one fed chunk to one consumer, stats keys, source label (tests/test_pipeline.py:12-118); pass-through, mono to stereo, stereo to mono, upsampling from 22050 Hz, 16-bit round trip, single sample, silence (tests/test_resampler.py:9-89). The tests import only `a2dplaya.config` and `a2dplaya.audio.{fft_analyzer,pipeline,resampler}` (tests/test_config.py:8; tests/test_fft_analyzer.py:7; tests/test_pipeline.py:8-9; tests/test_resampler.py:6). No test touches the Bluetooth, Chromecast, AirPlay, web, line-in or application modules, and no test uses a real or simulated Bluetooth source or output device. No test output, coverage report or CI result is stored. Related artefacts: commit 47701ce contained bytecode files named `test_*.cpython-311-pytest-9.0.2.pyc` under tests/__pycache__/ and bytecode for a2dplaya/__init__, config and the three audio modules only (`git ls-tree -r --name-only 47701ce`), removed in commit d78a903. The source-size field in each of those .pyc headers equals the byte size of the committed source file (read with `git show 47701ce:<path> | head -c 16 | od -An -tu4` and `git show 47701ce:<path> | wc -c`; for example tests/test_pipeline.py 4019 and a2dplaya/audio/pipeline.py 10227). `.pytest_cache/` is listed in .gitignore (.gitignore:8). pytest is not named in requirements.txt or setup.py.
- Presence: CURRENT. tests/ on main; the bytecode artefacts are REMOVED (commit d78a903).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. OBSERVED artefacts show that pytest 9.0.2 on CPython 3.11 imported the four test modules against sources of the committed size; they do not show pass or fail. Commit 47701ce states "Comprehensive test suite (44 tests) covering config, resampler, FFT analyzer, and audio pipeline" and states no result.
- Why it turned out that way: UNKNOWN. No result is recorded.
- Depends on: CPython 3.11 and pytest 9.0.2 at the time of the bytecode (file names in commit 47701ce); tests that wait on a live thread with a polling loop (tests/test_pipeline.py:66-70).
- Open questions: Did all 44 tests pass on the committed code? Were they run on any machine with a Bluetooth adapter? Why was bytecode produced for the four tested modules only: were the other modules ever imported in that environment?
- Confidence: MEDIUM. Test content INFERRED from clear code; the run is evidenced by OBSERVED artefacts; the result is not evidenced.

### R1-S11-02  NOT FOUND
- Question: S11.2
- Searched: README.md in full; all module docstrings and comments in a2dplaya/ (every .py file read in full); install.sh; requirements.txt; setup.py; the four test modules; commit messages of all 4 commits (`git log --all --format=%B`); `git grep -n -i -E "iphone|android|pixel|samsung|galaxy|\bios\b|macbook|tested|verified|works with"` across tracked files (no hits); search for MAC-address patterns `([0-9A-Fa-f]{2}:){5}[0-9A-Fa-f]{2}` (no hits).
- Nearest related material: generic source categories in the product description, "phones, record players, tape players, etc." (README.md:5), and output device families named as feature claims, "Chromecast/Google Home device" and "HomePod, Apple TV, etc." (README.md:10-11). None is presented as tested.

### R1-S11-03  NOT FOUND
- Question: S11.3
- Searched: `find` for *.log, *.csv, *.json, *.txt, *.md outside .git (only README.md and requirements.txt exist); README.md in full; commit messages of all 4 commits; `git grep -n -i -E "dropout|cpu|memory|uptime|benchmark|measur"` across tracked files (no hits); `git grep -n -i -E "latenc|delay|jitter"` (hits only at a2dplaya/config.py:55 and 64, Bluetooth reconnect settings); `git grep -n -E "underruns|overruns"` (counter definitions and one increment, listed below).
- Nearest related material: run-time counters kept in memory and returned by `/api/status`, never written to disk: `chunks_processed`, `bytes_processed`, `underruns`, `overruns`, `last_chunk_time` (a2dplaya/audio/pipeline.py:82-89, 266-270; a2dplaya/web/server.py:143). Unverified constants in comments: "~4ms for 352 frames at 44100Hz" (a2dplaya/airplay/manager.py:367), "30fps" (a2dplaya/web/server.py:351), "empirical scaling factor" (a2dplaya/audio/fft_analyzer.py:202).

### R1-S11-04  Author's statement that the shairport-sync AirPlay route is not implemented
- Question: S11.4
- What was attempted: the repo has no known-issues file, changelog or TODO marker (`find` for CHANGELOG*, TODO*, NOTES*; `git grep -n -E "TODO|FIXME|HACK|XXX|WORKAROUND"` returns no hits). One limitation is stated in code as a run-time log message: "shairport-sync based AirPlay not yet fully implemented" (a2dplaya/airplay/manager.py:403), preceded by the comment "This is a simplified approach - in practice you'd configure shairport-sync as a named pipe sink" (401-402).
- Presence: CURRENT. a2dplaya/airplay/manager.py:386-404 on main.
- Outcome: FAILED
- Outcome evidence: STATED. a2dplaya/airplay/manager.py:403; the function returns False on the next line (404).
- Why it turned out that way: STATED. The comment at a2dplaya/airplay/manager.py:401-402 says the named-pipe configuration was left out. No further cause is given.
- Depends on: NONE beyond the code state at commit 70ad071.
- Open questions: The README lists AirPlay output as a feature without qualification (README.md:11): is this limitation documented anywhere a user would read before running the program?
- Confidence: HIGH. STATED evidence that the code agrees with.

### R1-S11-05  Author's "simplified" labels on the RAOP handshake and packet timestamp
- Question: S11.4
- What was attempted: two in-code statements mark the direct AirPlay route as reduced: the docstring "Implements a simplified RAOP handshake" (a2dplaya/airplay/manager.py:292) and the header comment "Timestamp (simplified)" beside four zero bytes (375). The packet is described as "Simple RTP-like packet: header + PCM data" (371). The requirements file adds a general statement: "Most are optional; the application degrades gracefully when not available" (requirements.txt:2). No statement says what does not work as a consequence.
- Presence: CURRENT. a2dplaya/airplay/manager.py:288-384 on main.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. The statements describe the code, not a result.
- Depends on: the wire format in R1-S08-09.
- Open questions: Did the author know of a receiver that accepts the simplified handshake, or is "simplified" an acknowledgement that it is incomplete? Which of the four steps listed at a2dplaya/airplay/manager.py:293-296 did the author regard as done?
- Confidence: MEDIUM. STATED labels that the code agrees with; no evidence of the result.

---

## S12 Security and exposure

### R1-S12-01  Web UI and API server on all interfaces, port 8080, with no authentication
- Question: S12.1
- What was attempted: the web server binds `self._config.host` and `self._config.port` (a2dplaya/web/server.py:401-410), defaults `0.0.0.0` and `8080` (a2dplaya/config.py:106-107; README.md:80-81), using Flask's own `app.run` with `threaded=True` (a2dplaya/web/server.py:404-410). No route checks a credential, token, session, origin or client address: `git grep -n -i -E "auth|login|password|token|api.key|csrf|cors|https|ssl|tls"` returns no relevant hit (matches are substrings of unrelated words: "author", "HTTPServer", "classList", "Lossless", and the word "session" in two docstrings about cast and AirPlay sessions). Every client that can reach the port can read status including the adapter address (a2dplaya/web/server.py:137-141; a2dplaya/bluetooth/manager.py:222-230), set volume, make the adapter discoverable and pairable, request pairing or connection for an address it names (a2dplaya/web/server.py:200-222), start and stop casting and AirPlay, start line-in capture with a device string it names, and open any number of endless SSE or WAV-header streams (a2dplaya/web/server.py:159-397). Transport is plain HTTP. The start-up messages print `http://0.0.0.0:<port>` (a2dplaya/__main__.py:84; a2dplaya/app.py:131-134). In the page, device fields `address`, `host`, `port`, `model`, `status` and `id` are placed into `innerHTML` without the `escHtml` helper that is applied to `name` (a2dplaya/web/static/index.html:624-630, 681-682, 725-726, 765-768, 788-796); `host`, `port` and `model` for outputs originate from mDNS records of other devices (a2dplaya/cast/manager.py:377-380; a2dplaya/airplay/manager.py:227-230), and `status` is a fixed word set by the server (a2dplaya/cast/manager.py:36; a2dplaya/airplay/manager.py:37).
- Presence: CURRENT. a2dplaya/web/server.py:114-121, 401-410 on main.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No statement on network exposure or access control exists in the README or code comments.
- Depends on: the network the host is attached to; `A2DPLAYA_WEB_HOST` or the `web.host` file value when set (a2dplaya/config.py:208).
- Open questions: Was the server meant for a trusted home network only? The line-in route passes a client-supplied device string to `arecord -D` as one argument-list element (a2dplaya/web/server.py:324-327; a2dplaya/audio/line_in.py:183-191): what can a client reach through ALSA device strings?
- Confidence: MEDIUM. INFERRED from clear code and a whole-tree search; no evidence of the result.

### R1-S12-02  Audio stream HTTP server on all interfaces, port 8099, with no authentication
- Question: S12.1
- What was attempted: the Chromecast manager starts a second HTTP listener on `("0.0.0.0", self._stream_port)` (a2dplaya/cast/manager.py:313-321), default port 8099 (a2dplaya/config.py:87; README.md:83), at application start whether or not a cast is ever requested (a2dplaya/cast/manager.py:166-169; a2dplaya/app.py:84-85). It serves `/stream.wav` and `/stream.mp3` to any client (a2dplaya/cast/manager.py:55-61) with no check of the requester; any other path gets 404 (60-61). While a cast is active, a client that requests `/stream.wav` receives the live audio and removes those bytes from the shared buffer (a2dplaya/cast/manager.py:116-120, 293-300). Outbound, the process opens connections to Chromecast and AirPlay devices chosen by name from discovery results (a2dplaya/cast/manager.py:427-438; a2dplaya/airplay/manager.py:299-301) and browses mDNS (a2dplaya/cast/manager.py:388; a2dplaya/airplay/manager.py:239-242). No other listening socket is created by project code (`git grep -n -E "HTTPServer\(|\.bind\(|\.listen\(|app\.run|\.run\("` returns a2dplaya/cast/manager.py:318, a2dplaya/web/server.py:404, 419 and subprocess calls only).
- Presence: CURRENT. a2dplaya/cast/manager.py:313-330 on main.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No statement on this listener's exposure exists in the repo.
- Depends on: the Chromecast needing to fetch the URL from this host (a2dplaya/cast/manager.py:222-224); `A2DPLAYA_CAST_STREAM_PORT` when set (a2dplaya/config.py:211).
- Open questions: Because the server handles one request at a time (R1-S08-15), can one idle client connection hold it and prevent the Chromecast from fetching the stream?
- Confidence: MEDIUM. INFERRED from clear code; no evidence of the result.

### R1-S12-03  Privilege level of the application and installer
- Question: S12.2
- What was attempted: the generated unit runs the process as `User=$USER`, the account that ran the installer (install.sh:121); no `Group=`, sandboxing or capability directive is in the unit (install.sh:114-128). A manual start runs as whoever invokes it (README.md:48-49). Child programs (`bluetoothctl`, `arecord`, `avahi-browse`, `shairport-sync --version`) are started with `subprocess` without changing user (a2dplaya/bluetooth/manager.py:187-190; a2dplaya/audio/line_in.py:194-198; a2dplaya/airplay/manager.py:259-265, 394-397). The installer itself is written to run as an ordinary user and call sudo per command (install.sh:31-32, 41, 78-79, 84, 92-94, 99, 113, 131-132); pip runs inside the venv without sudo (install.sh:64-67). Privileged system changes made by the installer: package installation, Bluetooth service enablement, group membership, two lines in /etc/bluetooth/main.conf, one unit file. The code neither requires nor refuses root (`git grep -n -E "geteuid|getuid"` returns no hits).
- Presence: CURRENT. install.sh:119-125 on main.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No result is recorded.
- Depends on: the value of `$USER` when install.sh runs.
- Open questions: If the installer is itself started under sudo or as root, which account ends up in `User=` and in the `bluetooth` group, and which home directory then holds the config path (a2dplaya/config.py:20)?
- Confidence: MEDIUM. Description INFERRED from clear script text; no evidence of the result.

### R1-S12-04  Secrets: a Flask secret key held in memory, optionally supplied by file or environment
- Question: S12.3
- What was attempted: the only secret the project defines is the web `secret_key` field (a2dplaya/config.py:109). It can come from the JSON config file (a2dplaya/config.py:188-192) or from the environment variable `A2DPLAYA_WEB_SECRET_KEY` (a2dplaya/config.py:210); when empty after loading, a random value is generated in memory for that run (a2dplaya/config.py:158-160) and assigned to the Flask app (a2dplaya/web/server.py:121). The field comment reads "Generated at install time" (a2dplaya/config.py:109); install.sh contains no key generation (install.sh:1-175). The generated value is not written anywhere by the application, because `save` has no caller outside tests (R1-S10-05). If `save` were called, the key would be written in clear text within the JSON file created with default file mode (a2dplaya/config.py:123-129; no `chmod` in the repo). No route uses Flask sessions or signing (`git grep -n -i session` returns only docstrings about cast and AirPlay sessions). `.env` is listed in .gitignore (.gitignore:10) and no code loads such a file (`git grep -n -i dotenv` returns no hits). No key, token, password, Bluetooth link key or device address value is committed: searched tracked files for MAC-address patterns and for `password|token|api.key` with no hits. Bluetooth pairing keys are not read, written or referenced by project code; pairing is delegated to BlueZ through D-Bus or `bluetoothctl` (a2dplaya/bluetooth/manager.py:419-453). The AirPlay route uses no key material (R1-S08-09).
- Presence: CURRENT. a2dplaya/config.py:158-160; a2dplaya/web/server.py:121 on main.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No result is recorded.
- Depends on: the process restarting with a new random key each run when none is supplied.
- Open questions: Does anything in the application depend on the secret key, given no session or signed cookie is used? Where does BlueZ on the target system store link keys, and with what file permissions? (Not evidenced in the repo.)
- Confidence: MEDIUM. INFERRED from clear code and whole-tree searches.

---

## Tally

| Stage | Questions | Answered with findings | Answered NOT FOUND | Finding records | NOT FOUND records |
|---|---|---|---|---|---|
| S07 | 3 | 2 (S07.1, S07.2) | 1 (S07.3) | 6 | 1 |
| S08 | 4 | 4 | 0 | 15 | 0 |
| S09 | 4 | 4 | 0 | 11 | 0 |
| S10 | 6 | 6 | 0 | 8 | 0 |
| S11 | 4 | 2 (S11.1, S11.4) | 2 (S11.2, S11.3) | 3 | 2 |
| S12 | 3 | 3 | 0 | 4 | 0 |
| Total | 24 | 21 | 3 | 47 | 3 |

Questions answered with findings: 21. Questions answered NOT FOUND: 3. Total: 24.
