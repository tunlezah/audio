# R5 stage audit: Audio stages S07 to S12

- Repo: /home/user/awdeo, repo ID R5
- State audited: commit e556801 on branch `awdeo_x86_kube`, which is also the commit of the working tree (git log --all --oneline; git status --short printed nothing). All four refs point at e556801 (git branch -a; git log --all).
- History read with `git show REF:PATH`, `git log --all` and `git diff` only. Nothing was built or run.
- Scope note: the project in this repo names itself "Cast2Air" and describes itself as a "Chromecast to AirPlay Bridge" (README.md:1). Its audio input is an HTTP `POST /stream` request (src/chromecast/receiver.ts:146) and its audio output is a TCP socket to an AirPlay device (src/airplay/sender.ts:183-187, 210). A search of every tracked file except package-lock.json for `bluetooth`, `bluez`, `a2dp`, `alsa`, `pulse`, `pipewire`, `aplay`, `speaker` returned zero hits, and a search of package-lock.json for `bluetooth`, `bluez`, `a2dp`, `alsa`, `pulse`, `speaker` returned zero hits. The records below describe stages S07 to S12 for the pipeline that is in the repo.
- Line numbers for a file at an older commit are written as "commit HASH, path:lines".

## S07 Processing

### R5-S07-01  Format conversion step (`transcode`) between the HTTP input and the AirPlay sender
- Question: S07.1
- What was attempted: the bridge calls `audioPipeline.transcode(stream, contentType, this.config.audio)` under the comment "Transcode the audio to PCM format required by AirPlay" (src/bridge.ts:155-160). The method body creates a `PassThrough` and pipes the input into it; it holds no decode, resample or encode step (src/audio/pipeline.ts:184-197). The `outputFormat` argument (defaults: sample rate 44100, bit depth 16, 2 channels, src/config.ts:30-34) is used in one log call and nowhere else in the method (src/audio/pipeline.ts:182). The README describes the step as "Real-time Transcoding: Automatic audio format conversion using FFmpeg" (README.md:14) and "AudioPipeline: FFmpeg-based audio transcoding and buffering" (README.md:207). ffmpeg is installed by Dockerfile:5 and .github/workflows/ci.yml:29. No code starts a process: a search of src/ for `ffmpeg`, `spawn`, `child_process`, `exec(` gives one hit, the comment at src/audio/pipeline.ts:190.
- Presence: CURRENT. The call is on the run path (src/bridge.ts:156-160) and the method is at src/audio/pipeline.ts:177-212. The body has been a pipe into a `PassThrough` since the first commit (commit e8801c3, src/audio/pipeline.ts:19-51).
- Outcome: FAILED
- Outcome evidence: STATED. Log text inside the method: "Note: Audio transcoding not implemented. Audio may not play correctly on some devices." (src/audio/pipeline.ts:195). Commit e8801c3 body: "Full protocol implementation and audio transcoding (using FFmpeg) can be added in future iterations."
- Why it turned out that way: STATED. The conversion was left out of the first version by the author: "For the initial version, we'll just pass through the audio without transcoding." (src/audio/pipeline.ts:188-189). The later commits changed listener tracking and added the broadcaster in this file but added no conversion (git diff e8801c3 a744781 -- src/audio/pipeline.ts; commit 2db4cb5 body items 1 to 3).
- Depends on: the design choice in commit e8801c3 to ship a pass-through; ffmpeg as an apt package with no version pin (Dockerfile:5); the `audio` settings in src/config.ts:30-34, which reach only a log line.
- Open questions: Which input formats did the author expect at `POST /stream`? Which format did the author expect an AirPlay device to accept on the socket? Was any audio ever sent through this method?
- Confidence: HIGH. STATED evidence that the code agrees with.

### R5-S07-02  Byte pass-through chain from the request body to the output socket
- Question: S07.1
- What was attempted: the request body chunks are pushed into a `Readable` (src/chromecast/receiver.ts:153-155, 166-168); that stream is piped into a `PassThrough` (src/audio/pipeline.ts:185, 197); a `data` listener writes each chunk to one `PassThrough` per output device (src/audio/pipeline.ts:58-65, 98-114); each of those is piped into a TCP socket (src/airplay/sender.ts:210). No step on this chain changes the bytes. The request content type is read from the header with default `application/octet-stream` (src/chromecast/receiver.ts:150) and is used in log calls only (src/bridge.ts:56; src/audio/pipeline.ts:182).
- Presence: CURRENT. All four hops are on the run path from src/bridge.ts:146-176.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No log, test output or note records a run of the chain.
- Depends on: a sender of `POST /stream` that is outside the repo (search of all tracked files except package-lock.json for `/stream` gives one hit, the route at src/chromecast/receiver.ts:146); Node.js stream classes (src/audio/pipeline.ts:1); at least one output device added before the request arrives (src/bridge.ts:148-151).
- Open questions: Was the chain ever run with real audio? What produced the request body in the author's intended setup?
- Confidence: MEDIUM. INFERRED from clear code: each hop is a push, pipe or write of the same chunk object.

### R5-S07-03  Volume value held, converted to a decibel figure and logged
- Question: S07.1
- What was attempted: a volume number from 0 to 100 is taken from the config default 50 (src/config.ts:28; src/bridge.ts:252) or from the prompt command `volume <0-100>` (src/index.ts:131-137, 238-251), passed through `bridge.setVolume` (src/bridge.ts:269-271) to `AirPlaySender.setVolume`, which clamps it to 0-100 (src/airplay/sender.ts:84) and calls `sendVolumeCommand` for each connection that has a socket (src/airplay/sender.ts:88-92). `sendVolumeCommand` computes `-30 + (this.volume * 30 / 100)` and writes it to a debug log line (src/airplay/sender.ts:99-104). No code scales audio samples and no bytes are written to the socket for volume (src/airplay/sender.ts:98-107).
- Presence: CURRENT. Reachable from the prompt (src/index.ts:249).
- Outcome: FAILED
- Outcome evidence: STATED. Comments in the function body: "Volume command would be sent via RTSP here" and "For simplicity, we'll just log it" (src/airplay/sender.ts:105-106).
- Why it turned out that way: STATED. The same comments give the reason as simplicity; the RTSP session it would use is itself a log-only function (src/airplay/sender.ts:264-282; record R5-S08-04).
- Depends on: an RTSP control channel that the repo does not hold (src/airplay/sender.ts:278-279); the 0-100 scale and the -30 to 0 dB mapping at src/airplay/sender.ts:99-100.
- Open questions: NONE
- Confidence: HIGH. STATED evidence that the code agrees with. The manual checklist item "Volume control works" is unchecked (CONTRIBUTING.md:132).

### R5-S07-04  `AudioBuffer` transform that re-blocks audio into fixed 8192-byte chunks
- Question: S07.1
- What was attempted: a `Transform` subclass collects incoming chunks, emits blocks of `bufferSize` bytes (default 8192) and flushes the remainder at end of stream (src/audio/pipeline.ts:254-303). It is in the first commit (commit e8801c3, src/audio/pipeline.ts:76-112).
- Presence: DISABLED. Unreachable: a search of all tracked files for `AudioBuffer` returns only the class definition at src/audio/pipeline.ts:254. No code constructs it.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No commit message, comment or note says why the class is not wired in.
- Depends on: nothing on the run path. README.md:207 names "buffering" as part of the pipeline.
- Open questions: Where in the chain was this class meant to sit? Was it ever connected in a version that was not committed?
- Confidence: MEDIUM. INFERRED from clear code and a repo-wide search.

### R5-S07-05  NOT FOUND
- Question: S07.2
- Searched: all tracked files except package-lock.json, case-insensitive, for `latency`, `delay`, `jitter`, `timestamp`, `ntp`, `clock`, `highWaterMark`, `drain`, `backpressure`; commit message bodies of e8801c3, a744781, 2db4cb5 and e556801 (git log --all); README.md and CONTRIBUTING.md read in full. `latency`, `jitter`, `ntp`, `clock` and `highWaterMark`: zero hits. `drain`: one hit, a shutdown comment (src/index.ts:270). `backpressure`: hits in the unreferenced `AudioBuffer` class only (src/audio/pipeline.ts:271, 275). `delay`: hits are the mDNS reconnect delay only (src/airplay/discovery.ts:20, 125-143). `timestamp`: log formatting and the health report only (src/utils/logger.ts:22-38; src/bridge.ts:290). No latency figure, measurement or compensation was found.
- Nearest related material: places where audio is held in memory, none with a stated figure: the per-device `PassThrough` created at src/audio/pipeline.ts:98-114, which receives chunks from src/audio/pipeline.ts:58-65 and is piped to the socket only after the TCP connection opens (src/airplay/sender.ts:199-210); the connection timeout of 10000 ms (src/airplay/sender.ts:19); the unreferenced `AudioBuffer` class (src/audio/pipeline.ts:254-303).

### R5-S07-06  NOT FOUND
- Question: S07.3
- Searched: all tracked files except package-lock.json, case-insensitive, for `drift`, `sync` (with `async` and `...Sync(` calls excluded), `clock`, `ntp`, `timestamp`, `resampl`, `jitter`; src/airplay/sender.ts and src/audio/pipeline.ts read in full; commit message bodies (git log --all). No code for clock drift or for synchronisation between the source and an output, or between outputs, was found.
- Nearest related material: README.md:242, an unchecked roadmap item reading "Audio synchronization across multiple devices"; CONTRIBUTING.md:141, "Audio synchronization" listed under the "High Priority" heading at CONTRIBUTING.md:138. The comment "3. Handle sample rate conversion" at src/audio/pipeline.ts:192 sits in a list of steps the code does not perform.

## S08 Output

### R5-S08-01  AirPlay devices found by mDNS browse and selected by name at the prompt
- Question: S08.1
- What was attempted: one kind of output destination exists. The process browses for mDNS services of type `raop` with `bonjour-service` (src/airplay/discovery.ts:1, 56, 61) and keeps each one in a Map keyed by service name with host, port and TXT data (src/airplay/discovery.ts:64-78). The operator lists them with `devices` (src/index.ts:107-109, 182-198) and selects one with `connect <name>` (src/index.ts:111-117, 200-208), which looks the name up (src/bridge.ts:246-250; src/airplay/discovery.ts:199-201) and stores a connection entry with `socket: null` and `connected: false` (src/airplay/sender.ts:26-51). No socket is opened at that point: "Actual connection will happen when streaming starts" (src/airplay/sender.ts:42). The command then logs `Connected to <name>` (src/index.ts:207). `getConnectedDevices()` returns every stored entry (src/airplay/sender.ts:314-316). No local sound device, file, browser or other output type exists: searches listed in the scope note, plus a search of src/ for `createServer`, `dgram`, `publish`, `sendFile` (zero hits beyond the express listener at src/chromecast/receiver.ts:247).
- Presence: CURRENT (src/bridge.ts:207, 246-254).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No record of a discovery or selection run exists in the repo. The manual checklist items "Device discovery works" and "Can connect to AirPlay devices" are unchecked (CONTRIBUTING.md:129-130).
- Depends on: `bonjour-service` range `^1.0.0` (package.json:26); mDNS reachability on the local network (README.md:167-169, 215); an attached terminal for the prompt (src/index.ts:89-94); exact match of the service name (src/airplay/discovery.ts:199-201).
- Open questions: Does the README example `connect "Living Room AirPort"` (README.md:87) match a device, given that the prompt splits the line on spaces and rejoins the arguments without removing quote characters (src/index.ts:99, 115)? Was discovery ever run against a real device?
- Confidence: MEDIUM. INFERRED from clear code.

### R5-S08-02  `airplay.targetDevices` list for automatic connection
- Question: S08.1
- What was attempted: the config type holds `airplay.targetDevices: string[]` (src/config.ts:11), default empty (src/config.ts:27; config.example.json:7), validated as an array of strings on load (src/config.ts:129-131). README.md:136 describes it as "List of AirPlay devices to auto-connect to".
- Presence: DISABLED. The value is loaded but unreachable from any connect path: a search of src/ for `targetDevices` gives hits in src/config.ts only (lines 11, 27, 129-131). The only caller of `addAirPlayDevice` is the prompt command (src/index.ts:206).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No comment or commit message mentions the setting.
- Depends on: nothing on the run path.
- Open questions: Was auto-connect ever implemented outside the committed history?
- Confidence: MEDIUM. INFERRED from a repo-wide search and clear code.

### R5-S08-03  Unframed byte stream over one TCP connection to the device's advertised port
- Question: S08.2
- What was attempted: when a stream starts, the sender opens `net.createConnection` to the host and port from the mDNS record, with port default 5000 and a 10000 ms timeout (src/airplay/sender.ts:183-187; src/airplay/discovery.ts:65-70). After the `connect` event and the session function of record R5-S08-04, the per-device stream is piped to the socket with `audioStream.pipe(socket, { end: false })` (src/airplay/sender.ts:210). The bytes on the socket are the bytes of the HTTP request body (record R5-S07-02). No RTP packetising, encryption or header is added (src/airplay/sender.ts:167-262). On stop, the code calls `connection.socket.end()` under the comment "Send RTSP TEARDOWN command" (src/airplay/sender.ts:300-305); no TEARDOWN bytes are written.
- Presence: CURRENT (src/bridge.ts:166-168; src/airplay/sender.ts:110-165).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No record of a connection to an AirPlay device exists. The manual checklist item "Audio streams correctly" is unchecked (CONTRIBUTING.md:131).
- Depends on: the port advertised in the device's `_raop._tcp` record (src/airplay/discovery.ts:61, 68); the format of whatever was posted to `/stream`; Node.js `net` (src/airplay/sender.ts:4).
- Open questions: Did any AirPlay device accept or play bytes sent this way? After the source stream ends, the consumers are ended (src/audio/pipeline.ts:67-76) while the pipe was made with `end: false` (src/airplay/sender.ts:210): do the sockets stay open until a STOP or PAUSE command arrives?
- Confidence: MEDIUM. INFERRED from clear code.

### R5-S08-04  RTSP/RAOP session setup function
- Question: S08.2
- What was attempted: `initializeRTSPSession` is called on socket connect under the comment "Send RTSP SETUP and RECORD commands" (src/airplay/sender.ts:206-207). Its body lists five steps in a comment (OPTIONS, ANNOUNCE with SDP, SETUP, RECORD, RTP packets; src/airplay/sender.ts:265-271), writes one debug log line with the device name and a random session ID (src/airplay/sender.ts:273-276) and returns a resolved promise (src/airplay/sender.ts:281). The README states "AirPlaySender: Audio streaming to AirPlay endpoints using RAOP protocol" (README.md:206). Commit e8801c3 body lists "AirPlay sender with pure Node.js RTSP implementation" as a feature and, in the same message, notes "simplified RTSP/RAOP protocol handling". README.md:233 credits `airtunes2`; it is in neither package.json:25-35 nor package-lock.json (search for `airtunes`, `raop`: zero hits).
- Presence: CURRENT. Called at src/airplay/sender.ts:207; defined at src/airplay/sender.ts:264-282.
- Outcome: FAILED
- Outcome evidence: STATED. Comments in the function: "For this proof of concept, we'll just log the initialization" and "A full implementation would require proper RTSP/RTP/RAOP protocol handling" (src/airplay/sender.ts:278-279).
- Why it turned out that way: STATED. The author labels the code a proof of concept (src/airplay/sender.ts:278) and defers the protocol to "future iterations" (commit e8801c3 body). The function body is the same in the first commit (commit e8801c3, src/airplay/sender.ts:196-198 shows the same closing lines).
- Depends on: no RTSP, RTP or RAOP library in package.json:25-35.
- Open questions: NONE
- Confidence: HIGH. STATED evidence that the code agrees with.

### R5-S08-05  Connection timeout and per-device failure handling when a stream starts
- Question: S08.3
- What was attempted: each device connection gets a 10000 ms socket timeout that destroys the socket and rejects (src/airplay/sender.ts:19, 183-197). Connections are attempted in parallel, each result recorded as success or failure (src/airplay/sender.ts:118-137). Failed devices are removed from the table (src/airplay/sender.ts:140-150). If every device fails, an error is thrown (src/airplay/sender.ts:152-154); the bridge catches it and logs "Failed to handle audio stream" (src/bridge.ts:173-175). A `settleOnce` guard stops a second settlement of the promise (src/airplay/sender.ts:172-179). Added by commit a744781 (FIX #6, FIX #7) and commit 2db4cb5 (item 7).
- Presence: CURRENT (src/airplay/sender.ts:110-262).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The statements in the commits are about compilation only: "All fixes tested and verified with successful TypeScript compilation." (commit a744781 body).
- Why it turned out that way: UNKNOWN
- Depends on: Node.js `net` timeout behaviour; the device table filled by the `connect` command.
- Open questions: Was a failed or timed-out device connection ever exercised? After a failed device is removed at src/airplay/sender.ts:147-149, how does the operator learn that it has to be added again?
- Confidence: MEDIUM. INFERRED from clear code.

### R5-S08-06  Sequential device connection with no timeout (first version)
- Question: S08.3
- What was attempted: the first version awaited `connectAndStream` for each device in turn inside one `try` block, with no socket timeout (commit e8801c3, src/airplay/sender.ts:93-121, 129-132). A rejection from one device left the loop.
- Presence: REMOVED. Replaced in commit a744781 (git diff e8801c3 a744781 -- src/airplay/sender.ts).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. Commit a744781 gives labels and no observed failure: "FIX #6: Add graceful error handling for device connection failures" and "FIX #7: Add 10-second connection timeout for AirPlay devices", both under "HIGH Severity Fixes".
- Depends on: the code of commit e8801c3 only.
- Open questions: Was a hang or an aborted start observed before commit a744781, or were the changes made from a code review alone?
- Confidence: MEDIUM. INFERRED from the diff; the commit message agrees on what changed.

### R5-S08-07  Audio arriving when no output device has been added
- Question: S08.3
- What was attempted: `handleAudioStream` checks the device table first; when it is empty it logs "No AirPlay devices connected, cannot stream audio" and returns (src/bridge.ts:148-151). The handler does not read from or destroy the incoming stream on that path (src/bridge.ts:147-151). The HTTP handler keeps pushing request chunks into that stream and answers `{ status: 'ok' }` at end of request (src/chromecast/receiver.ts:166-174).
- Presence: CURRENT (src/bridge.ts:148-151).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: the order of operator actions: `connect` before the first `POST /stream` (startup instructions at src/index.ts:66-68).
- Open questions: What happens to the request data pushed into the unread stream on this path? Does the HTTP client receive any sign that the audio was dropped?
- Confidence: MEDIUM. INFERRED from clear code.

### R5-S08-08  Output socket closing, or a device disappearing, during playback
- Question: S08.3
- What was attempted: on socket `error` the connection is marked not connected and the error is logged (src/airplay/sender.ts:227-234). On socket `close` the connection is marked not connected, the stream error listener is removed and the stream is unpiped from the socket (src/airplay/sender.ts:236-252). The entry stays in the device table (no `connections.delete` on that path; the only deletes are at src/airplay/sender.ts:75 and 321). No code reconnects an output socket: a search of src/ for `reconnect` gives hits in src/airplay/discovery.ts only. When mDNS reports a device as gone, the discovery class removes it from its own Map (src/airplay/discovery.ts:80-84) and the bridge handler writes one log line (src/bridge.ts:81-83).
- Presence: CURRENT.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: operator action at the prompt to re-add or remove a device (src/index.ts:111-125).
- Open questions: After a socket closes mid-stream, does the per-device stream keep receiving chunks from the broadcaster (src/audio/pipeline.ts:58-65) with nothing reading it? Was a mid-stream disconnect ever exercised?
- Confidence: MEDIUM. INFERRED from clear code and a search.

### R5-S08-09  Device added or removed at the prompt while a stream is active
- Question: S08.3
- What was attempted: `connect <name>` during a stream stores a new table entry only (src/airplay/sender.ts:32-43). Streams and sockets are created in `startStreaming`, whose one caller is `handleAudioStream` (src/bridge.ts:166-168), so the new device is served from the next `audioStream` event. `disconnect <name>` destroys that device's stream and socket and deletes the entry (src/airplay/sender.ts:53-80; src/bridge.ts:256-259). A PAUSE command calls the same stop routine as STOP for the outputs, ending every socket (src/bridge.ts:178-184; src/airplay/sender.ts:284-312); the PLAY handler writes a log line only (src/bridge.ts:60-62).
- Presence: CURRENT.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: a new `POST /stream` request to start output again after a pause or after adding a device.
- Open questions: After PAUSE then PLAY, does any audio reach an output without a new `POST /stream`?
- Confidence: MEDIUM. INFERRED from clear code.

### R5-S08-10  Slow output: fan-out writes and per-device stream
- Question: S08.3
- What was attempted: the broadcaster's `data` listener calls `consumer.write(chunk)` for every per-device `PassThrough` and does not use the return value (src/audio/pipeline.ts:58-65). Each `PassThrough` is piped to its socket (src/airplay/sender.ts:210). No code pauses the source, drops data or measures the queue for a slow device: a search of src/ for `highWaterMark` and `pause(` gives zero hits, `drain` appears only in a shutdown comment (src/index.ts:270), and `backpressure` appears only in the unreferenced class of record R5-S08-11 (src/audio/pipeline.ts:271, 275). README.md:183 lists "Try reducing the number of simultaneous AirPlay targets" under audio quality troubleshooting.
- Presence: CURRENT (src/audio/pipeline.ts:58-65).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: Node.js stream buffering defaults; the speed of each output socket.
- Open questions: What happens to memory use when one output socket is slower than the input? Was this path ever run with a slow device?
- Confidence: LOW. INFERRED from code; the runtime effect is not evidenced.

### R5-S08-11  1 MB size cap in `AudioBuffer` (FIX #11)
- Question: S08.3
- What was attempted: commit a744781 added a `maxBufferSize` argument, default 1024 * 1024, to `AudioBuffer` (src/audio/pipeline.ts:258, 260, 265). When the held size plus the incoming chunk would exceed it, the transform logs "Buffer size limit reached, applying backpressure" and calls its callback with `new Error('Buffer overflow')` (src/audio/pipeline.ts:270-278). The commit lists it under "CRITICAL Fixes": "FIX #11: Add bounded buffer (1MB limit) to prevent unbounded memory growth".
- Presence: DISABLED. The class is unreachable: `AudioBuffer` has no reference outside its definition (search of all tracked files; src/audio/pipeline.ts:254 is the only hit).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: nothing on the run path.
- Open questions: The log text and comment name backpressure (src/audio/pipeline.ts:271, 275) and the code raises an error (src/audio/pipeline.ts:276): which behaviour did the author intend? Did the author know the class was not in use when the cap was added?
- Confidence: MEDIUM. INFERRED from clear code and a repo-wide search.

### R5-S08-12  mDNS discovery recovery with exponential backoff
- Question: S08.3
- What was attempted: commit e556801 (item 3) added an `error` listener on the mDNS browser that tears down the browser and schedules a restart (src/airplay/discovery.ts:89-93, 101-110). The delay doubles from 5000 ms to a ceiling of 60000 ms, for at most 10 attempts (src/airplay/discovery.ts:19-20, 112-144). At the limit it emits `discoveryFailed` (src/airplay/discovery.ts:117-121); no listener for that event exists (search of src/ for `discoveryFailed`: one hit, the emit). `isHealthy()` feeds the health report (src/airplay/discovery.ts:191-193; src/bridge.ts:314-320).
- Presence: CURRENT.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The commit's test statement covers the build only: "Tested: x86 build successful, all TypeScript checks passing" (commit e556801 body).
- Why it turned out that way: UNKNOWN
- Depends on: the `bonjour-service` browser emitting an `error` event (package.json:26; lockfile version 1.3.0, package-lock.json:447-448).
- Open questions: Does the `bonjour-service` browser emit `error` events at all? Was a discovery failure ever produced to exercise this path? The commit says it "Prevents permanent discovery failure on network glitches": was such a failure observed first?
- Confidence: MEDIUM. INFERRED from clear code; the commit message agrees on what was added.

### R5-S08-13  `AudioBroadcaster` fan-out to one stream per output device
- Question: S08.4
- What was attempted: the bridge holds one `AudioBroadcaster` (src/bridge.ts:39). For each incoming stream it sets the pass-through output as the source (src/bridge.ts:163; src/audio/pipeline.ts:40-96) and hands the sender a factory that creates one `PassThrough` per device (src/bridge.ts:166-168; src/audio/pipeline.ts:98-114; src/airplay/sender.ts:122-124). Every source chunk is written to every consumer (src/audio/pipeline.ts:58-65). Commit 2db4cb5 added tracked listeners, a guard flag for repeated `setSource` calls and a listener limit of 50 (src/audio/pipeline.ts:19-37, 41-47, 129-161). The README lists "Multi-Room Audio: Stream to multiple AirPlay devices simultaneously" as a feature (README.md:13). `AudioPipeline` also constructs a second broadcaster of its own, exposed by `getBroadcaster()`, which has no caller (src/audio/pipeline.ts:167, 173, 214-216; search of src/ for `getBroadcaster`: one hit).
- Presence: CURRENT (src/bridge.ts:163-168). Introduced by commit a744781.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No record of output to two devices exists.
- Depends on: the per-device outputs of record R5-S08-03; no timing alignment between devices (record R5-S07-06).
- Open questions: Was output to more than one device ever run? Does each device start at the same point in the audio, given that each socket connects at its own time (src/airplay/sender.ts:199-210)?
- Confidence: MEDIUM. INFERRED from clear code.

### R5-S08-14  One shared stream read by every device (first version)
- Question: S08.4
- What was attempted: the first version passed the same `Readable` to every device. Each device attached its own `data` listener that wrote chunks to its socket (commit e8801c3, src/airplay/sender.ts:103-105, 145-149), and the sender kept the stream in a `currentStream` field (commit e8801c3, src/airplay/sender.ts:16, 100).
- Presence: REMOVED. Replaced by the broadcaster in commit a744781 (git diff e8801c3 a744781 -- src/airplay/sender.ts).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. The replacing commit names a problem and records no observed failure: "FIX #1: Implement AudioBroadcaster pattern to prevent stream reuse across multiple AirPlay devices", listed under "CRITICAL Fixes" (commit a744781 body). The class comment reads "Solves the stream reuse problem for multi-device audio" (src/audio/pipeline.ts:13).
- Depends on: the code of commit e8801c3 only.
- Open questions: What failure did "stream reuse" produce, and was it seen in a run or found by reading the code?
- Confidence: MEDIUM. INFERRED from the diff; the commit message agrees on what changed.

## S09 User interface and control surfaces

### R5-S09-01  Command-line program `cast2air` with commands `start` and `config`
- Question: S09.1
- What was attempted: a `commander` program named `cast2air`, version `1.0.0` (src/index.ts:30-33), with a `start` command (src/index.ts:35-78) and a `config` command (src/index.ts:80-87), dispatched by `program.parse()` (src/index.ts:348). It is reached through `npm start` (`node dist/index.js`, package.json:8), `npm run dev` (package.json:9) or the Dockerfile CMD `["npm", "start", "start"]` (Dockerfile:43). package.json has no `bin` field (package.json:1-46), so no `cast2air` executable name is installed.
- Presence: CURRENT.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: `commander` range `^11.0.0` (package.json:29); a compiled `dist/` directory, which is not tracked (.gitignore:2).
- Open questions: README.md:59 gives `npm start` as the run command and README.md:76 gives `npm start start`; what does the program do when no command name is passed?
- Confidence: MEDIUM. INFERRED from clear code.

### R5-S09-02  Interactive prompt `cast2air> ` on standard input
- Question: S09.1
- What was attempted: after `bridge.start()` the process opens a `readline` interface on stdin and stdout with the prompt `cast2air> ` (src/index.ts:73, 89-96) and handles one command per line (src/index.ts:98-161). When the readline interface closes, the process shuts the bridge down and exits with code 0 (src/index.ts:163-166). The compose file sets `stdin_open: true` and `tty: true` (docker-compose.yml:16-17). The README `docker run` line carries no `-i` or `-t` flag (README.md:69).
- Presence: CURRENT.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. The manual checklist item "CLI commands respond correctly" is unchecked (CONTRIBUTING.md:133).
- Depends on: an attached standard input. This prompt is the only route by which an output device is added (src/index.ts:206 is the one caller of `addAirPlayDevice`).
- Open questions: What happens when the process starts with no standard input attached, given the exit on readline close at src/index.ts:163-166? How does an operator reach the prompt of a container started by the compose file?
- Confidence: MEDIUM. INFERRED from clear code.

### R5-S09-03  HTTP endpoints on the receiver port
- Question: S09.1
- What was attempted: an express app listens on the configured port, default 8009 (src/chromecast/receiver.ts:25, 247; src/config.ts:24). Routes: `GET /health`, `GET /healthz`, `GET /ready` (src/chromecast/receiver.ts:67-97), `GET /ssdp/device-desc.xml` (src/chromecast/receiver.ts:100-103), `POST /receiver/:sessionId` (src/chromecast/receiver.ts:106-143) and `POST /stream` (src/chromecast/receiver.ts:146-189). No HTML page is served (search of all tracked files except package-lock.json for `html`, `sendFile`, `static(`, `websocket`: no hit in src/). README.md:241 and README.md:245 list "Web UI for easier device management" and "Mobile app for remote control" as unchecked roadmap items. No button, light or display code exists (search for `gpio`, `led`, `button`, `display`, `lcd`, `oled`: zero hits).
- Presence: CURRENT.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: `express` range `^4.18.0` (package.json:30).
- Open questions: Which client was meant to call `POST /receiver/:sessionId` and `POST /stream`? No caller is in the repo.
- Confidence: MEDIUM. INFERRED from clear code.

### R5-S09-04  JSON configuration file and the `LOG_LEVEL` variable
- Question: S09.1
- What was attempted: a `config.json` file in the working directory, or a path given with `--config`, is read at start (src/config.ts:48-108; src/index.ts:38, 43). The `LOG_LEVEL` environment variable sets the log level (src/utils/logger.ts:11, 20; README.md:156-161). Details of settings are in record R5-S10-06.
- Presence: CURRENT.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: the working directory of the process (src/config.ts:49-50).
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from clear code.

### R5-S09-05  Prompt actions and status items
- Question: S09.2
- What was attempted: actions: `devices` lists discovered devices as name, host and port (src/index.ts:182-198); `connect <name>` and `disconnect <name>` add and remove a device (src/index.ts:200-218); `connected` lists table entries (src/index.ts:220-236); `volume <0-100>` (src/index.ts:238-251; record R5-S07-03); `status` (src/index.ts:253-268); `help` (src/index.ts:169-180); `exit` or `quit` (src/index.ts:143-148). Status items printed by `status`: Streaming yes or no, count of available devices, count of connected devices, and the current media title when a session exists (src/index.ts:261-266; src/bridge.ts:273-280). The same list is in README.md:99-108. The word "connected" in these outputs means "present in the device table": `connect` opens no socket (src/airplay/sender.ts:41-43) and the count comes from the table keys (src/airplay/sender.ts:314-316).
- Presence: CURRENT.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: record R5-S09-02.
- Open questions: Does `Streaming: Yes` remain after the source stream ends? `isStreaming` is set at src/bridge.ts:170 and cleared only in the pause and stop routines (src/bridge.ts:181, 191).
- Confidence: MEDIUM. INFERRED from clear code.

### R5-S09-06  HTTP actions and status items
- Question: S09.2
- What was attempted: actions: `POST /receiver/:sessionId` with JSON `type` of `LOAD`, `PLAY`, `PAUSE` or `STOP` (src/chromecast/receiver.ts:118-137); `POST /stream` with an audio body (src/chromecast/receiver.ts:146-189). Status: `GET /health` returns `healthy`, `status` and a `details` object holding receiver state, discovery state, device counts, connected device names, streaming flag, pipeline flags, an issues list and a timestamp (src/bridge.ts:283-365; src/chromecast/receiver.ts:67-80); `GET /healthz` returns `{ alive: true }` (src/chromecast/receiver.ts:83-85); `GET /ready` returns `ready` and `status` (src/chromecast/receiver.ts:88-97); `GET /ssdp/device-desc.xml` returns a device description with the receiver name (src/chromecast/receiver.ts:192-208). No HTTP route lists, adds or removes output devices or sets volume (src/chromecast/receiver.ts:51-190).
- Presence: CURRENT. The three health routes in their present form came with commit e556801 (item 2); the first version had one `/health` route returning `{ status: 'ok', name }` (commit e8801c3, src/chromecast/receiver.ts:35-38).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: record R5-S09-03.
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from clear code.

### R5-S09-07  Command-line options and the `config` command output
- Question: S09.2
- What was attempted: `start` takes `-c/--config <path>`, `-n/--name <name>` and `-p/--port <port>` with default `8009` (src/index.ts:38-40). Name and port override the loaded config (src/index.ts:46-53); the port is checked for a number in 1-65535 and the process exits with code 1 otherwise (src/index.ts:14-28). `config` prints the loaded configuration as JSON (src/index.ts:84-87). README.md:142-154 documents the options.
- Presence: CURRENT.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: record R5-S09-01.
- Open questions: The `--port` option has a default value of `'8009'` (src/index.ts:40) and the override at src/index.ts:50-53 runs whenever the option has a value: does a `chromecast.port` value from config.json ever take effect?
- Confidence: MEDIUM. INFERRED from clear code.

### R5-S09-08  First-time setup as documented and as printed at start
- Question: S09.3
- What was attempted: the steps, in order: (1) install Node.js >= 16, FFmpeg and Avahi or Bonjour (README.md:34-41); (2) clone, `npm install`, `npm run build`, `npm start` (README.md:47-60), or `docker build -t cast2air .` and `docker run --network host --name cast2air cast2air` (README.md:64-70); (3) `npm start start` (README.md:74-77); (4) at start the process logs a numbered instruction list giving the receiver name and port and naming the `devices` and `connect` commands (src/index.ts:58-71); (5) type `devices` (README.md:79-83); (6) type `connect "<name>"` (README.md:85-88); (7) open a Chromecast-compatible app, find "Cast2Air Bridge" in its device list and cast (README.md:90-93). A config file is optional; no step tells the reader to create one (README.md:110-130; config.example.json exists and no document names it, per a search for `config.example`). No pairing, account or credential step exists.
- Presence: CURRENT (README.md:72-93; src/index.ts:58-71).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: step 7 depends on the process being visible to casting apps. The advertisement function writes three log lines and opens no socket (src/chromecast/receiver.ts:285-292), under the comment "Note: Full SSDP/DIAL implementation would require additional libraries" (src/chromecast/receiver.ts:286). `castv2`, `castv2-client` and `node-ssdp` are declared (package.json:27-28, 32) and no file in src/ imports them (search of every `import` and `require(` line in src/). README.md:212 states: "This implementation uses a simplified Chromecast receiver. Full DIAL/SSDP support may require additional work".
- Open questions: Did any casting app ever list the receiver? Does step 6 as written in the README find a device, given the quote handling noted in record R5-S08-01?
- Confidence: MEDIUM. STATED steps in the README; INFERRED from clear code for the dependency on advertisement.

### R5-S09-09  Log lines as the channel for errors and state changes
- Question: S09.4
- What was attempted: every component reports through one `winston` logger with a console transport and a rotating file transport (src/utils/logger.ts:19-48). State changes logged include device found and lost (src/bridge.ts:74-83), commands received (src/bridge.ts:60-72), stream start and stop (src/bridge.ts:172, 182, 192), connection results (src/airplay/sender.ts:140-159) and shutdown stages (src/index.ts:281-300). Errors in the stream path are caught and logged without reaching the HTTP client or the prompt (src/bridge.ts:173-175). Prompt command errors are logged as "Command failed" (src/index.ts:156-158). Prompt replies are a mix of logger lines and `console.log` output on the same terminal (src/index.ts:113, 170-179, 190-196).
- Presence: CURRENT.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: `winston` range `^3.10.0` (package.json:33); the operator watching the terminal or the log file.
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from clear code.

### R5-S09-10  HTTP status codes and JSON bodies for command errors and health
- Question: S09.4
- What was attempted: `POST /receiver/:sessionId` answers 400 with an `error` field for a missing type, missing media or unknown type (src/chromecast/receiver.ts:114-116, 121-123, 136), 500 with "Internal server error" on an exception (src/chromecast/receiver.ts:139-142) and `{ status: 'ok' }` otherwise (src/chromecast/receiver.ts:138). `GET /health` answers 503 when `healthy` is false (src/chromecast/receiver.ts:70-71); a `degraded` status keeps `healthy` true (src/bridge.ts:317-327, 354-356). `POST /stream` answers `{ status: 'ok' }` at end of request whatever happened to the audio downstream (src/chromecast/receiver.ts:170-174; src/bridge.ts:148-151, 173-175). The rate limiter answers with the text "Too many requests, please try again later" (src/chromecast/receiver.ts:59).
- Presence: CURRENT.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: record R5-S09-03.
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from clear code.

## S10 Lifecycle and operations

### R5-S10-01  Install from source, by hand
- Question: S10.1
- What was attempted: manual steps in the README: install Node.js >= 16, FFmpeg and Avahi or Bonjour with the platform commands given (README.md:34-41, 189-198); `git clone`, `npm install`, `npm run build`, `npm start` (README.md:47-60). The `prepare` script runs the build during `npm install` (package.json:12). The clone URL is a placeholder, `yourusername/cast2air` (README.md:49). No install script exists (git ls-files shows no `.sh` file and no Makefile).
- Presence: CURRENT (README.md:43-60).
- Outcome: UNVERIFIED
- Outcome evidence: NONE for an install. For compilation alone see record R5-S11-03.
- Why it turned out that way: UNKNOWN
- Depends on: Node.js `>=16.0.0` (package.json:43-45); apt package names in README.md:39; npm versions locked in package-lock.json.
- Open questions: Was the documented sequence ever followed on a clean machine?
- Confidence: MEDIUM. STATED steps; no result recorded.

### R5-S10-02  Docker image build
- Question: S10.1
- What was attempted: a Dockerfile on `node:20-bullseye-slim` (Dockerfile:1) installs ffmpeg, avahi-daemon, avahi-discover, libavahi-compat-libdnssd-dev and dbus (Dockerfile:4-10), copies the package files and tsconfig.json (Dockerfile:16-17), runs `npm ci --only=production` (Dockerfile:20), copies `src` (Dockerfile:23) and runs `npm run build` (Dockerfile:26). The compose file builds from it (docker-compose.yml:5). The CI workflow has a job that builds the image without pushing (.github/workflows/ci.yml:40-56). The README gives `docker build` and `docker run` commands (README.md:64-70) and does not mention the compose file (search of README.md and CONTRIBUTING.md for `compose`: zero hits).
- Presence: CURRENT.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No image build log or CI result is in the repo.
- Why it turned out that way: UNKNOWN
- Depends on: `tsc` being available at Dockerfile:26; `typescript` is a devDependency (package.json:41) and Dockerfile:20 installs with `--only=production`. The `prepare` script (package.json:12) runs the build during the install at Dockerfile:20, before `src` is copied at Dockerfile:23. The image starts no avahi or dbus daemon (Dockerfile:43 runs npm only).
- Open questions: Does the image build complete with the install flag at Dockerfile:20 and the `prepare` script at package.json:12? Commit e556801 says "x86 build successful": does that refer to the image or to `tsc`?
- Confidence: MEDIUM. INFERRED from clear files; no build artefact.

### R5-S10-03  Container restart policy and image health check
- Question: S10.2
- What was attempted: the compose service sets `restart: unless-stopped` (docker-compose.yml:8). The Dockerfile declares a HEALTHCHECK every 30 s, timeout 10 s, start period 5 s, 3 retries, that requests `http://localhost:8009/health` and passes on status 200 (Dockerfile:39-40). No systemd unit, init script or other boot hook exists (git ls-files; search of all tracked files except package-lock.json for `systemd`, `.service`: zero hits).
- Presence: CURRENT.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: the Docker daemon starting at boot; port 8009 being the port in use, since the health check URL is fixed (Dockerfile:40) while the port is configurable (src/index.ts:40; src/config.ts:24).
- Open questions: Was the service ever started with the compose file? How does the restart policy interact with the exit on readline close at src/index.ts:163-166?
- Confidence: MEDIUM. INFERRED from clear files.

### R5-S10-04  In-process start order, signal handling and shutdown
- Question: S10.2
- What was attempted: start order: health callback registered, HTTP listener started, mDNS discovery started, then a fixed 2000 ms wait and a device count log (src/bridge.ts:196-224). A start failure is logged and the process exits with code 1 (src/index.ts:74-77). SIGINT and SIGTERM call `shutdown()` then exit 0 (src/index.ts:312-332). `shutdown()` waits 2000 ms, stops the bridge and has a 30000 ms forced-exit timer (src/index.ts:270-309). `uncaughtException` logs, shuts down and exits 1 (src/index.ts:341-346). `unhandledRejection` is logged and the process continues (src/index.ts:335-338). No in-process restart of the HTTP listener or of an output connection exists; discovery restart is in record R5-S08-12. The first version had SIGINT and SIGTERM handlers with no timer (commit e8801c3, src/index.ts:252-268); commit a744781 added the exception handlers (FIX #12) and commit e556801 added the wait and the timer (item 5). The start wait was 3000 ms before commit e556801 (commit 2db4cb5, src/bridge.ts:207).
- Presence: CURRENT.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. The manual checklist item "Graceful shutdown" is unchecked (CONTRIBUTING.md:134).
- Depends on: an outside supervisor for restart after exit (record R5-S10-03).
- Open questions: Commit e556801 states the shutdown change "Ensures clean shutdown on deployments": was a shutdown under load ever run?
- Confidence: MEDIUM. INFERRED from clear code; commit messages agree on what was added.

### R5-S10-05  Permissions set in the container files
- Question: S10.3
- What was attempted: the compose service runs with `network_mode: host` and `privileged: true` (docker-compose.yml:7, 15). The README `docker run` line uses `--network host` with the comment "host network mode for device discovery" (README.md:68-69) and no privileged flag. The README prerequisite commands use `sudo apt-get install` (README.md:39, 191). No file states why `privileged: true` is set (search of all tracked files for `privileged`: one hit, docker-compose.yml:15).
- Presence: CURRENT.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: mDNS multicast reaching the process (README.md:169, 176); the default port 8009 (src/config.ts:24).
- Open questions: Which operation of the process needs `privileged: true`?
- Confidence: MEDIUM. INFERRED from clear files.

### R5-S10-06  Configuration file: location, settings, defaults and validation
- Question: S10.4
- What was attempted: the config path defaults to `config.json` in the working directory (src/config.ts:49-54). A `--config` path is accepted only when it resolves inside the working directory and ends in `.json`; otherwise the default path is used with a warning (src/config.ts:56-81). Settings and defaults: `chromecast.name` "Cast2Air Bridge", `chromecast.port` 8009, `airplay.targetDevices` empty, `airplay.volume` 50, `audio.sampleRate` 44100, `audio.bitDepth` 16, `audio.channels` 2 (src/config.ts:21-35; config.example.json:1-15; README.md:114-140). Each loaded section is validated and bad values fall back to defaults (src/config.ts:110-159); a missing or unparsable file gives the defaults (src/config.ts:84-108). The loaded sections replace the default sections whole (`{ ...DEFAULT_CONFIG, ...validatedConfig }`, src/config.ts:102). `--name` and `--port` override the file (src/index.ts:46-53). `LOG_LEVEL` is the only environment variable read (search of src/ for `process.env`: src/utils/logger.ts:11, 20). `config.json` is git-ignored (.gitignore:6). The path validation came with commit a744781 (FIX #4).
- Presence: CURRENT.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: the working directory. In the image the working directory is `/app` (Dockerfile:13), the declared config volume is `/app/config` (Dockerfile:32; docker-compose.yml:10) and the start command passes no `--config` option (Dockerfile:43).
- Open questions: Is a `config.json` placed in the mounted `/app/config` directory ever read, given the default path at src/config.ts:49-50? README.md:147 shows `--config /path/to/config.json`: is such a path accepted by the check at src/config.ts:61-67? Which of the settings change behaviour, given that `targetDevices` and the three `audio` values reach no functional code (records R5-S08-02, R5-S07-01)?
- Confidence: MEDIUM. INFERRED from clear code.

### R5-S10-07  Config persistence methods `saveConfig` and `updateConfig`
- Question: S10.4
- What was attempted: `saveConfig` writes the config object to the config path as JSON and `updateConfig` validates, merges and saves (src/config.ts:169-176, 182-186). The README lists "Configuration: Persistent configuration with JSON file support" as a feature (README.md:16).
- Presence: DISABLED. Unreachable: a search of all tracked files for `saveConfig` and `updateConfig` gives hits inside src/config.ts only (lines 169, 182, 185). No prompt command or route calls them.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: nothing on the run path.
- Open questions: Was a command that changes and saves settings planned?
- Confidence: MEDIUM. INFERRED from a repo-wide search.

### R5-S10-08  Logging with daily rotation
- Question: S10.5
- What was attempted: a `winston` logger at level `LOG_LEVEL` or `info` (src/utils/logger.ts:19-20) writes to the console with timestamps and colour (src/utils/logger.ts:33-45) and to a `winston-daily-rotate-file` transport named `cast2air-%DATE%.log`, rotating daily or at 20 MB, kept 14 days, compressed (src/utils/logger.ts:5-12). No directory option is set on the file transport (src/utils/logger.ts:5-12). The Dockerfile and compose file set `LOG_LEVEL=info` (Dockerfile:36; docker-compose.yml:14) and the compose file mounts `./logs:/app/logs` (docker-compose.yml:11). `*.log` is git-ignored (.gitignore:3). Added by commit e556801 (item 1). The commit body names "winston-daily-rotate-file@^4.x"; package.json:34 declares `^5.0.0` and the lockfile holds 5.0.0 (package-lock.json:1775-1776).
- Presence: CURRENT.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No log file is in the repo.
- Why it turned out that way: UNKNOWN
- Depends on: `winston` `^3.10.0` and `winston-daily-rotate-file` `^5.0.0` (package.json:33-34); a writable working directory.
- Open questions: Do the log files land in the mounted `/app/logs` directory, given the file name at src/utils/logger.ts:6 and the working directory `/app` (Dockerfile:13)?
- Confidence: MEDIUM. INFERRED from clear code; the commit message agrees on what was added.

### R5-S10-09  Single log file with no rotation (first version)
- Question: S10.5
- What was attempted: the first version logged to the console and to one file, `cast2air.log`, through `winston.transports.File` (commit e8801c3, src/utils/logger.ts:16-19). It was unchanged through commits a744781 and 2db4cb5 (git show a744781:src/utils/logger.ts and 2db4cb5:src/utils/logger.ts, line 18).
- Presence: REMOVED. Replaced in commit e556801 (git diff 2db4cb5 e556801 -- src/utils/logger.ts).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. The replacing commit asserts a future failure and records no occurrence: "Fix: Prevents guaranteed crash after 30 days in production" and, under "Before Fixes", "MTBF: 3-7 days (disk full crash guaranteed)" (commit e556801 body).
- Depends on: the code of commits e8801c3 to 2db4cb5 only.
- Open questions: Did a disk-full event ever occur? The repo history spans 2025-11-18 to 2025-11-21 (git log --all): on what basis were the 30-day and 3-7-day figures given?
- Confidence: MEDIUM. INFERRED from the diff; the commit message agrees on what changed.

### R5-S10-10  Health, liveness and readiness endpoints as diagnostics
- Question: S10.5
- What was attempted: `getHealthStatus` builds a report from four checks, each in its own `try` block: receiver session getter, discovery state and device count, connected device list, pipeline and broadcaster flags (src/bridge.ts:283-365). The receiver serves it at `/health` with 200 or 503, with `/healthz` and `/ready` beside it (src/chromecast/receiver.ts:67-97). The Docker HEALTHCHECK reads `/health` (Dockerfile:39-40). Added by commit e556801 (item 2). No other diagnostic command, metrics endpoint or debug dump exists (src/ read in full).
- Presence: CURRENT.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: the HTTP listener being up; port 8009 for the Docker check.
- Open questions: Under what condition do the checked getters throw, so that `healthy` becomes false and 503 is returned (src/bridge.ts:298-306, 328-332)?
- Confidence: MEDIUM. INFERRED from clear code; the commit message agrees on what was added.

### R5-S10-11  Packaging as an npm project and a Docker image
- Question: S10.6
- What was attempted: an npm manifest named `cast2air`, version `1.0.0`, `main` `dist/index.js`, licence MIT, with no `bin`, `files` or `publishConfig` field (package.json:2-5, 24). A lockfile is tracked (package-lock.json:4). A Dockerfile and a compose file exist (records R5-S10-02, R5-S10-03). The CI workflow builds on Node 16.x, 18.x and 20.x and builds the image with `push: false` (.github/workflows/ci.yml:15, 49-54). No publish step, registry name, release tag or update mechanism exists: git tag printed nothing, and a search of all tracked files except package-lock.json for `publish`, `registry`, `release`, `upgrade`, `watchtower` gives no hit beyond the licence text (LICENSE:8). README.md:251-252 links to issue and wiki pages under the placeholder `yourusername/cast2air`.
- Presence: CURRENT.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: a local build from a clone for every install or update.
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from clear files and searches.

## S11 Testing and measured behaviour

### R5-S11-01  Manual testing checklist
- Question: S11.1
- What was attempted: CONTRIBUTING.md states "Currently, testing is manual. Automated tests are welcome contributions!" (CONTRIBUTING.md:126) and gives a six-item checklist: device discovery, connecting to AirPlay devices, audio streaming, volume control, CLI commands, graceful shutdown (CONTRIBUTING.md:128-134). All six boxes are unchecked. The contributor test step is `npm run build` then `npm start start` (CONTRIBUTING.md:48-52). No test file, test directory or test script exists: git ls-files shows 20 files with no test or spec file, package.json:6-13 has no `test` script, and a search for `jest`, `mocha`, `spec` finds no test tooling.
- Presence: CURRENT (CONTRIBUTING.md:124-134).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No filled-in checklist, test log or result is recorded.
- Why it turned out that way: UNKNOWN
- Depends on: a person with a casting client and an AirPlay device.
- Open questions: Was the checklist ever worked through?
- Confidence: HIGH for the absence of automated tests: STATED at CONTRIBUTING.md:126 and the file list agrees.

### R5-S11-02  CI workflow: build, type check and image build
- Question: S11.1
- What was attempted: a GitHub Actions workflow with three jobs. `build` runs `npm ci`, `npm run build` and `npx tsc --noEmit` on Node 16.x, 18.x and 20.x (.github/workflows/ci.yml:10-38). `docker` builds the image without pushing (.github/workflows/ci.yml:40-56). `lint` runs prettier only when a `.prettierrc` file exists and is marked `continue-on-error` (.github/workflows/ci.yml:58-80); no such file is tracked (git ls-files). No job runs a test. The workflow triggers on pushes and pull requests to `main` and `develop` (.github/workflows/ci.yml:3-7); the branches in the repo are `awdeo_x86_kube` and `claude/inspiring-galileo-a9dlia` (git branch -a).
- Presence: CURRENT (.github/workflows/ci.yml:1-80).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No CI run result, badge or log is in the repo.
- Why it turned out that way: UNKNOWN
- Depends on: a push or pull request to a branch named `main` or `develop`.
- Open questions: Did the workflow ever run for this repo, given the branch names?
- Confidence: MEDIUM. INFERRED from the workflow file and the branch list.

### R5-S11-03  Compile checks reported in commit messages
- Question: S11.1
- What was attempted: two commit messages report a compile check as the test of their changes. Commit a744781 body: "All fixes tested and verified with successful TypeScript compilation." Commit e556801 body: "Tested: x86 build successful, all TypeScript checks passing" and, in its readiness list, "All builds passing". Commit a744781's subject carries the label "(ARM version)".
- Presence: CURRENT. The statements are in the history of the default branch (git log --all).
- Outcome: WORKED
- Outcome evidence: STATED. The two commit bodies quoted above. The result stated is compilation only.
- Why it turned out that way: UNKNOWN. No compiler output is recorded.
- Depends on: `typescript` range `^5.3.3`, lockfile 5.9.3 (package.json:41; package-lock.json:1693-1694); the machine the author compiled on, described only as "x86" (commit e556801 body).
- Open questions: Was anything beyond compilation run for these commits? What does "(ARM version)" in the subject of commit a744781 refer to?
- Confidence: MEDIUM. STATED evidence alone; nothing was compiled for this audit.

### R5-S11-04  NOT FOUND
- Question: S11.2
- Searched: all tracked files except package-lock.json, case-insensitive, for `iphone`, `android`, `pixel`, `samsung`, `macbook`, `ipad`, `homepod`, `airport`, `apple tv`, `spotify`, `youtube`, `tested`; commit message bodies of all four commits (git log --all); README.md and CONTRIBUTING.md read in full. No source device, casting client or output device is named as tested, and no per-device result exists.
- Nearest related material: README.md:91 names "Spotify, YouTube Music, etc." as examples of apps to cast from; README.md:7 names "AirPort Express, HomePods, Apple TVs, etc." as output devices; README.md:87 uses "Living Room AirPort" as an example device name. None of these lines reports a test. The word "Tested" in commit e556801 refers to a build (record R5-S11-03).

### R5-S11-05  Reliability and memory figures given in commit messages
- Question: S11.3
- What was attempted: two commit bodies give figures. Commit 2db4cb5: "Memory leak rate reduced from ~50-100MB/day to near zero". Commit e556801: under "Before Fixes", "MTBF: 3-7 days (disk full crash guaranteed)"; under "After Fixes", "MTBF: Indefinite (log rotation prevents crash)"; and, for log rotation, "Fix: Prevents guaranteed crash after 30 days in production". No measurement method, tool, log or data file accompanies them: git ls-files shows no log, results or benchmark file, and a search of all tracked files except package-lock.json for `latency`, `dropout`, `uptime`, `MTBF`, `cpu` gives zero hits. No latency, dropout, processor load or range figure exists anywhere in the repo or its commit messages.
- Presence: CURRENT. The statements are in the history of the default branch (git log --all).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The figures are stated; no artefact shows that a measurement was taken.
- Why it turned out that way: UNKNOWN
- Depends on: the author's statements alone.
- Open questions: Were the memory and MTBF figures measured or estimated? The whole history spans 2025-11-18 07:29 to 2025-11-21 08:48 (git log --all): how was an MTBF of 3 to 7 days, or a crash after 30 days, arrived at within that span? Commit e556801 gives both "after 30 days" and "3-7 days" for the same disk-full cause: which applies?
- Confidence: LOW. INFERRED: the figures have no supporting artefact, and the commit dates leave under four days between the first commit and the last.

### R5-S11-06  README "Limitations" list
- Question: S11.4
- What was attempted: the author lists four limitations: simplified Chromecast receiver, with "Full DIAL/SSDP support may require additional work"; video not supported; DRM-protected content "may not stream"; all devices on the same local network (README.md:210-215). The troubleshooting section adds causes for devices not found, receiver not visible and audio quality problems (README.md:163-183).
- Presence: CURRENT (README.md:210-215).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. This record lists author statements; no run result is attached to them.
- Why it turned out that way: UNKNOWN
- Depends on: the README text of commit e8801c3, unchanged since (git log --all --name-status shows README.md in the first commit only).
- Open questions: The list does not name the missing transcoding or the missing RAOP protocol handling that the code states (record R5-S11-07): was the README written before or after those code comments?
- Confidence: MEDIUM. STATED evidence alone.

### R5-S11-07  Statements in code and the first commit that parts are not implemented
- Question: S11.4
- What was attempted: the author marks four parts as not done. Transcoding: "Audio transcoding not implemented." in a warning logged on every stream (src/audio/pipeline.ts:195). RAOP session: "A full implementation would require proper RTSP/RTP/RAOP protocol handling" (src/airplay/sender.ts:279). Volume: "Volume command would be sent via RTSP here" (src/airplay/sender.ts:105). Receiver advertisement: "Note: Full SSDP/DIAL implementation would require additional libraries" (src/chromecast/receiver.ts:286), with the log line "SSDP advertisement started (simplified mode)" (src/chromecast/receiver.ts:289). Commit e8801c3 body ends: "Note: This is an initial implementation with simplified RTSP/RAOP protocol handling." No `TODO`, `FIXME`, `HACK`, `XXX`, `BUG` or `WORKAROUND` marker exists (search of all tracked files except package-lock.json: zero marker hits; `bug` appears only in CONTRIBUTING.md prose and a file name pattern).
- Presence: CURRENT. All four comments are in the working tree at the lines cited.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. This record lists author statements. The outcomes of the parts themselves are in records R5-S07-01, R5-S07-03 and R5-S08-04.
- Why it turned out that way: UNKNOWN
- Depends on: nothing beyond the cited lines.
- Open questions: The last commit's body carries a "PRODUCTION READINESS" list (commit e556801) while these four statements remain in the code: did the author regard the audio path as working at that point?
- Confidence: HIGH. STATED in code at the cited lines.

### R5-S11-08  Roadmap and contribution-area lists of open work
- Question: S11.4
- What was attempted: the README roadmap holds six unchecked items: full DIAL/SSDP implementation, web UI, audio synchronisation across devices, more audio codecs, a plugin system, a mobile app (README.md:238-245). CONTRIBUTING.md lists, under "High Priority": automated testing suite, full DIAL/SSDP implementation, audio synchronization, web UI (CONTRIBUTING.md:138-142); and under "Medium Priority": additional audio codec support, performance optimizations, error handling, documentation (CONTRIBUTING.md:144-148).
- Presence: CURRENT.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. This record lists author statements of open work; none of the items has code.
- Why it turned out that way: UNKNOWN
- Depends on: the document text of commit e8801c3, unchanged since (git log --all --name-status).
- Open questions: NONE
- Confidence: MEDIUM. STATED evidence alone.

## S12 Security and exposure

### R5-S12-01  HTTP listener on port 8009 with no authentication
- Question: S12.1
- What was attempted: the express app listens with `this.app.listen(this.port, ...)` and no host argument (src/chromecast/receiver.ts:247); the start log gives the address as `http://0.0.0.0:<port>` (src/chromecast/receiver.ts:290). Default port 8009 (src/config.ts:24; src/index.ts:40; Dockerfile:29). The compose service uses host networking (docker-compose.yml:7). No route checks a credential (src/chromecast/receiver.ts:51-190), and the protocol is plain HTTP (imports at src/chromecast/receiver.ts:1-6 include `http` and no TLS module). The process logs "NOTE: No authentication required - designed for local network use only" (src/chromecast/receiver.ts:291), added by commit a744781 as "FIX #13: Document authentication expectations for local network use". The unauthenticated routes accept an audio body (`POST /stream`), accept LOAD, PLAY, PAUSE and STOP commands, and return the names of connected devices in the health report (src/bridge.ts:335-339).
- Presence: CURRENT.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. The stated design position is local-network use only (src/chromecast/receiver.ts:291).
- Depends on: the trust placed in every host that can reach the port; README.md:174 asks for the port to be open in the firewall.
- Open questions: NONE
- Confidence: HIGH for the absence of authentication: STATED at src/chromecast/receiver.ts:291 and the route code agrees.

### R5-S12-02  Request limits and server timeouts
- Question: S12.1
- What was attempted: JSON bodies are limited to 1 MB (src/chromecast/receiver.ts:53). A rate limiter allows 100 requests per minute per IP on every route (src/chromecast/receiver.ts:56-64). The server sets a 30000 ms request timeout, 65000 ms keep-alive timeout, 66000 ms headers timeout, 100 headers and 100 connections (src/chromecast/receiver.ts:258-265), and answers malformed requests with 400 (src/chromecast/receiver.ts:273-278). The `POST /stream` handler reads the body through `data` events and sets no size limit of its own (src/chromecast/receiver.ts:166-168). Added by commit a744781 (FIX #2, FIX #10) and commit e556801 (item 4).
- Presence: CURRENT.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: `express-rate-limit` range `^8.2.1` (package.json:31) with type definitions for major version 5 (package.json:38).
- Open questions: Commit e556801 says the timeouts protect "against slow loris and resource exhaustion": was either case exercised? How does the 30000 ms timeout at src/chromecast/receiver.ts:259 interact with a long-running `POST /stream` request?
- Confidence: MEDIUM. INFERRED from clear code; the commit messages agree on what was added.

### R5-S12-03  Input validation and sanitisation
- Question: S12.1
- What was attempted: the receiver name is XML-escaped before it is placed in the device description (src/chromecast/receiver.ts:28-29, 41-49, 202). Receiver commands are checked for a string `type`, a present `media` object and a string `contentId` (src/chromecast/receiver.ts:113-123, 213-216). The config path is confined to the working directory and to `.json` files (src/config.ts:47-82). Config values and the port are type- and range-checked (src/config.ts:110-167; src/index.ts:13-28). The stream content type gets a default (src/chromecast/receiver.ts:149-150). All were added by commit a744781 (FIX #3, #4, #8, #9, #15).
- Presence: CURRENT.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The commit's statement covers compilation only (record R5-S11-03).
- Why it turned out that way: UNKNOWN
- Depends on: nothing beyond the cited code.
- Open questions: The commit subject counts 16 vulnerabilities and the body lists items numbered FIX #1 to FIX #15 (commit a744781): what was the sixteenth? Were the listed problems found by a tool, a review or a run?
- Confidence: MEDIUM. INFERRED from clear code; the commit message agrees on what was added.

### R5-S12-04  mDNS browsing and outbound TCP connections
- Question: S12.1
- What was attempted: the process browses mDNS for `raop` services through `bonjour-service` (src/airplay/discovery.ts:56, 61); README.md:169 names UDP port 5353 for this. It publishes no service of its own: the advertisement function writes log lines only (src/chromecast/receiver.ts:285-292), and a search of src/ for `dgram`, `createServer`, `publish` gives zero hits. Outbound, it opens one TCP connection per selected device to the host and port taken from the mDNS answer (src/airplay/discovery.ts:65-70; src/airplay/sender.ts:183-187). No authentication, pairing or encryption step with the AirPlay device exists (src/airplay/sender.ts:264-282).
- Presence: CURRENT.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: the content of mDNS answers on the local network; `bonjour-service` 1.3.0 and `multicast-dns` 7.2.5 in the lockfile (package-lock.json:447-448, 1207-1208).
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from clear code and searches.

### R5-S12-05  Process privilege in the container definition
- Question: S12.2
- What was attempted: the Dockerfile has no `USER` instruction (Dockerfile:1-43), so the process runs as whichever user the base image `node:20-bullseye-slim` sets. The compose service adds `privileged: true` and host networking (docker-compose.yml:7, 15). Outside a container the process runs as the user who types `npm start start` (README.md:76); the default port 8009 is above 1024 (src/config.ts:24). No code changes user, drops privileges or asks for a capability (search of all tracked files except package-lock.json for `setuid`, `USER `, `cap_add`, `sudo`: hits are the apt commands at README.md:39, 191 and .github/workflows/ci.yml:28-29 only).
- Presence: CURRENT.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No file states a reason for `privileged: true`.
- Depends on: the default user of the base image; the compose file being the route used to start the container.
- Open questions: Which user does the base image run as? Was the container ever started without `privileged: true`?
- Confidence: MEDIUM. INFERRED from clear files.

### R5-S12-06  No stored secrets; ignore rules for `.env` and `config.json`
- Question: S12.3
- What was attempted: no key, token, password or link key value is present in any tracked file: a case-insensitive search of all tracked files except package-lock.json for `password`, `secret`, `token`, `api_key`, `credential`, `cert` gives hits only in the log-sanitising code at src/bridge.ts:129-138. The config type has no secret field (src/config.ts:5-19). `.env` and `config.json` are git-ignored (.gitignore:4, 6) and `.env` is excluded from the image build context (.dockerignore:7); no code reads a `.env` file (search of src/ for `dotenv`, `process.env`: `LOG_LEVEL` only, src/utils/logger.ts:11, 20). A random 16-byte session identifier is generated per device entry (src/airplay/sender.ts:30), kept in memory and written to a debug log line (src/airplay/sender.ts:273-276); it is not sent to the device.
- Presence: CURRENT.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: the design having no pairing or account step (record R5-S09-08).
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from repo-wide searches.

### R5-S12-07  Removal of token parameters from media URLs before logging
- Question: S12.3
- What was attempted: the media URL received in a LOAD command is passed through `sanitizeUrl` before it is logged (src/bridge.ts:46-52). The function deletes the query parameters `token`, `auth`, `key` and `api_key`, blanks the URL password and returns the text `[URL]` when parsing fails (src/bridge.ts:129-144). The LOAD handler logs the session ID alone (src/chromecast/receiver.ts:211); the first version logged the whole `media` object and the raw URL (commit e8801c3, src/chromecast/receiver.ts:120; commit e8801c3, src/bridge.ts:35-40). Added by commit a744781 as "FIX #14: Sanitize URLs in logs to prevent credential exposure". The URL itself is held in memory in `currentSession` (src/chromecast/receiver.ts:218-223) and is not written to disk.
- Presence: CURRENT (src/bridge.ts:48, 129-144).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN
- Depends on: the four parameter names at src/bridge.ts:134-137; log files of record R5-S10-08 being the only place a URL is written.
- Open questions: Are credentials carried under other parameter names, or in the URL user name, left in the logged text?
- Confidence: MEDIUM. INFERRED from clear code; the commit message agrees on what was added.

## Tally

| Stage | Questions | Answered with findings | Answered NOT FOUND | Finding records | NOT FOUND records |
|-------|-----------|------------------------|--------------------|-----------------|-------------------|
| S07 | 3 | 1 (S07.1) | 2 (S07.2, S07.3) | 4 | 2 |
| S08 | 4 | 4 | 0 | 14 | 0 |
| S09 | 4 | 4 | 0 | 10 | 0 |
| S10 | 6 | 6 | 0 | 11 | 0 |
| S11 | 4 | 3 (S11.1, S11.3, S11.4) | 1 (S11.2) | 7 | 1 |
| S12 | 3 | 3 | 0 | 7 | 0 |
| Total | 24 | 21 | 3 | 53 | 3 |

- Questions answered with findings: 21
- Questions answered NOT FOUND: 3
- Total: 24
