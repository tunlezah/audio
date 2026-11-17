# R5 map: /home/user/awdeo

- Repo: /home/user/awdeo (remote tunlezah/awdeo, per /home/user/audio/00-manifest.md:15)
- HEAD at mapping time: commit e556801 on branch claude/inspiring-galileo-a9dlia (git rev-parse, git branch -a)
- Mapped from the working tree, which matches HEAD (git status --short printed nothing)
- Note for readers of this map: the project names itself "Cast2Air" and describes itself as a "Chromecast to AirPlay Bridge" (README.md:1, package.json:2-4). No Bluetooth term appears in any tracked file. Search details are in section 3 (GAP A) and section 6.

## 1. Layout

Tracked files: 20 (git ls-files). Files on disk outside .git: 20 (find). No untracked or ignored files are present on disk; `dist/`, `node_modules/` and `config.json` are named in .gitignore:1-2,6 and are absent.

### Top-level directories

| Directory | Contents |
|-----------|----------|
| `.git/` | Git metadata. 66 loose objects, none packed (git count-objects -v). |
| `.github/` | One file: `workflows/ci.yml`, a GitHub Actions workflow (.github/workflows/ci.yml:1-80). |
| `src/` | TypeScript source, 8 files. Subdirectories: `airplay/` (2 files: discovery.ts, sender.ts), `audio/` (1 file: pipeline.ts), `chromecast/` (1 file: receiver.ts), `utils/` (1 file: logger.ts). Three files at `src/` root: index.ts, bridge.ts, config.ts. |

### Top-level files

| File | Lines | Contents |
|------|-------|----------|
| `.dockerignore` | 11 | Docker build context exclusions |
| `.gitignore` | 11 | Git exclusions, including `dist/`, `.env`, `config.json` (.gitignore:2,4,6) |
| `CONTRIBUTING.md` | 162 | Contributor guide |
| `Dockerfile` | 43 | Container image definition |
| `LICENSE` | 21 | MIT licence text (package.json:24 names "MIT") |
| `README.md` | 256 | Project README |
| `config.example.json` | 15 | Example configuration |
| `docker-compose.yml` | 17 | One compose service |
| `package-lock.json` | 1824 | npm lockfile, lockfileVersion 3 (package-lock.json:4), 170 `node_modules/` entries |
| `package.json` | 46 | npm manifest |
| `tsconfig.json` | 20 | TypeScript compiler settings |

### File counts by type (tracked files)

| Type | Count | Files |
|------|-------|-------|
| `.ts` | 8 | src/index.ts (348 lines), src/bridge.ts (366), src/config.ts (187), src/airplay/discovery.ts (202), src/airplay/sender.ts (327), src/audio/pipeline.ts (303), src/chromecast/receiver.ts (310), src/utils/logger.ts (50) |
| `.json` | 4 | package.json, package-lock.json, tsconfig.json, config.example.json |
| `.md` | 2 | README.md, CONTRIBUTING.md |
| `.yml` | 2 | docker-compose.yml, .github/workflows/ci.yml |
| no extension | 2 | Dockerfile, LICENSE |
| dotfile | 2 | .dockerignore, .gitignore |
| Total | 20 | 4599 lines (wc -l over git ls-files) |

File types with a count of zero, checked with git ls-files piped through a filename filter: `.sh`, `.bash`, `.py`, `.service`, `.timer`, `.socket`, `.yaml`, `.toml`, `.conf`, `.cfg`, `.ini`, `Makefile`, `requirements*`, any `docs/` directory, any test or spec file.

## 2. Entry points

### Executables and main functions

| Entry point | Location | What it starts |
|-------------|----------|----------------|
| Node shebang | src/index.ts:1 (`#!/usr/bin/env node`) | Marks src/index.ts as the CLI script. package.json has no `bin` field (package.json:1-46). |
| CLI program definition | src/index.ts:9, 30-33 | A `commander` program named `cast2air`, version `1.0.0`. |
| CLI dispatch | src/index.ts:348 (`program.parse();`) | Parses argv and runs the matching command action. This is the only top-level call that starts work. |
| Command `start` | src/index.ts:35-78 | Builds a ConfigManager (src/index.ts:43), builds `Cast2AirBridge` (src/index.ts:55), awaits `bridge.start()` (src/index.ts:56), then calls `startInteractiveMode()` (src/index.ts:73). Options `-c/--config`, `-n/--name`, `-p/--port` default `8009` (src/index.ts:38-40). |
| Command `config` | src/index.ts:80-87 | Builds a ConfigManager and prints the configuration as JSON. Starts nothing else. |
| Interactive prompt | src/index.ts:89-167 | A readline loop with prompt `cast2air> ` (src/index.ts:90-94). Commands: help, devices, connect, disconnect, connected, volume, status, exit, quit (src/index.ts:102-155). |
| Signal and process handlers | src/index.ts:312-346 | SIGINT (312-321), SIGTERM (323-332), unhandledRejection (335-338), uncaughtException (341-346). The first, second and fourth call `shutdown()` (src/index.ts:274-309). |
| Module-level logger | src/utils/logger.ts:5-12, 19-48 | Created at import time: a daily-rotating file transport `cast2air-%DATE%.log` and a console transport. |

There is no other file with a top-level call. bridge.ts, config.ts, receiver.ts, discovery.ts, sender.ts and pipeline.ts export classes only (src/bridge.ts:9, src/config.ts:37, src/chromecast/receiver.ts:15, src/airplay/discovery.ts:12, src/airplay/sender.ts:16, src/audio/pipeline.ts:15,164,254).

### npm scripts (package.json:6-13)

| Script | Command | Location |
|--------|---------|----------|
| build | `tsc` | package.json:7. Output directory `./dist`, root `./src` (tsconfig.json:6-7). |
| start | `node dist/index.js` | package.json:8. `dist/` is not tracked (.gitignore:2). |
| dev | `ts-node src/index.ts` | package.json:9 |
| watch | `tsc -w` | package.json:10 |
| clean | `rm -rf dist` | package.json:11 |
| prepare | `npm run build` | package.json:12 |

`main` is `dist/index.js` (package.json:5). There is no `test` script (package.json:6-13).

### Container and service definitions

| Item | Location | What it starts |
|------|----------|----------------|
| Dockerfile CMD | Dockerfile:43 (`CMD ["npm", "start", "start"]`) | Runs the npm `start` script with the extra argument `start`, which maps to package.json:8 and then to the CLI command at src/index.ts:35-36. |
| Dockerfile build steps | Dockerfile:1-26 | Base `node:20-bullseye-slim` (1); apt installs (4-10); `npm ci --only=production` (20); `npm run build` (26). |
| Dockerfile HEALTHCHECK | Dockerfile:39-40 | A `node -e` one-liner that requests `http://localhost:8009/health`. The route is at src/chromecast/receiver.ts:67-80. |
| Dockerfile EXPOSE / VOLUME / ENV | Dockerfile:29, 32, 35-36 | Port 8009; volume `/app/config`; `NODE_ENV=production`, `LOG_LEVEL=info`. |
| Compose service `cast2air` | docker-compose.yml:3-17 | `build: .` (5), `network_mode: host` (7), `restart: unless-stopped` (8), volumes `./config:/app/config` and `./logs:/app/logs` (9-11), `privileged: true` (15), `stdin_open` and `tty` true (16-17). Starts the image built from the Dockerfile. |
| CI workflow | .github/workflows/ci.yml:1-80 | Triggers on push and pull request to branches `main` and `develop` (3-7). Job `build` (10-38): Node 16.x, 18.x, 20.x matrix (15), apt install (26-29), `npm ci` (32), `npm run build` (35), `npx tsc --noEmit` (38). Job `docker` (40-56): builds the image with `push: false`, tag `cast2air:test`. Job `lint` (58-80): runs prettier only if a `.prettierrc` file exists (73-80). |

No systemd unit, init script, shell script, Kubernetes manifest or Helm chart is tracked (git ls-files filter in section 1; search for `kube`, `k8s`, `systemd` in all tracked files except package-lock.json returned no hits). The string `kube` appears only in the branch name `awdeo_x86_kube` (git branch -a).

### What starts what

1. `docker-compose.yml:5` builds from `Dockerfile`; `Dockerfile:43` runs npm `start` with argument `start`.
2. `package.json:8` runs `node dist/index.js`, the compiled form of `src/index.ts` (tsconfig.json:6-7).
3. `src/index.ts:348` dispatches to the `start` action (src/index.ts:41).
4. The `start` action constructs `Cast2AirBridge` (src/index.ts:55), which constructs `ChromecastReceiver`, `AirPlayDiscovery`, `AirPlaySender`, `AudioPipeline` and `AudioBroadcaster` (src/bridge.ts:31-39).
5. `bridge.start()` (src/bridge.ts:196-224) starts the HTTP listener (src/bridge.ts:204 to src/chromecast/receiver.ts:244-283) and mDNS browsing (src/bridge.ts:207 to src/airplay/discovery.ts:36-99).
6. `startInteractiveMode()` (src/index.ts:73, 89-167) starts the readline prompt.

## 3. Run path

The chain below is the only audio path in the repo. Its input is an HTTP request and its output is a TCP socket to an AirPlay device. No hop touches Bluetooth; see GAP A.

1. Process start: `Dockerfile:43` (`CMD ["npm", "start", "start"]`) or the README command `npm start start` (README.md:76).
2. `package.json:8`: script `start` runs `node dist/index.js`. `dist/index.js` is produced from `src/index.ts` by `tsc` (package.json:7, tsconfig.json:6-7).
3. `src/index.ts:348`: `program.parse()` selects the `start` command (src/index.ts:35-41).
4. `src/index.ts:43-44`: `new ConfigManager(options.config)` then `getConfig()`. Path validation at src/config.ts:48-82; file load at src/config.ts:84-108; defaults at src/config.ts:21-35.
5. `src/index.ts:46-53`: `--name` and `--port` override the config values; port check at src/index.ts:14-28.
6. `src/index.ts:55`: `new Cast2AirBridge(config)`. Constructor at src/bridge.ts:27-42 creates the five components (src/bridge.ts:31-39) and calls `setupEventHandlers()` (src/bridge.ts:41, 44-93).
7. `src/chromecast/receiver.ts:23-34`: receiver constructor creates the express app (25) and calls `setupRoutes()` (33, 51-190).
8. `src/bridge.ts:87`: the bridge registers its handler for the receiver's `audioStream` event. The handler body is at src/bridge.ts:55-58.
9. `src/index.ts:56`: `await bridge.start()`, defined at src/bridge.ts:196-224.
10. `src/bridge.ts:204` to `src/chromecast/receiver.ts:244-283`: `this.app.listen(this.port, ...)` at src/chromecast/receiver.ts:247. Default port 8009 (src/config.ts:24, src/index.ts:40). The listen callback calls `startSSDPAdvertisement()` (src/chromecast/receiver.ts:252), defined at src/chromecast/receiver.ts:285-292.
11. `src/bridge.ts:207` to `src/airplay/discovery.ts:36-52` to `src/airplay/discovery.ts:54-99`: `new Bonjour()` (56) and `this.bonjour.find({ type: 'raop' })` (61). Discovered services are stored in a Map keyed by service name (src/airplay/discovery.ts:64-78).
12. `src/bridge.ts:211`: a 2000 ms wait, then a device count log (src/bridge.ts:213-219).
13. `src/index.ts:73`: `startInteractiveMode()` (src/index.ts:89-167).
14. Output selection by the operator: the `connect <name>` command (src/index.ts:111-117) calls `connectDevice` (src/index.ts:200-208), which calls `bridge.addAirPlayDevice` (src/bridge.ts:246-254), which calls `airplaySender.addDevice` (src/airplay/sender.ts:26-51). `addDevice` stores a connection entry with `socket: null` and `connected: false` (src/airplay/sender.ts:32-43). Comment at src/airplay/sender.ts:41-42: "For now, we'll mark it as ready to connect / Actual connection will happen when streaming starts".
15. Audio enters the process: HTTP `POST /stream` handler at `src/chromecast/receiver.ts:146-189`. A `Readable` is created (153-155); each request body chunk is pushed into it (166-168); end of request pushes `null` (170-174); the content type is taken from the request header with default `application/octet-stream` (150).
16. `src/chromecast/receiver.ts:188`: `this.emit('audioStream', audioStream, contentType)`.
17. `src/bridge.ts:55-58`: the bridge handler calls `handleAudioStream(stream, contentType)`, defined at src/bridge.ts:146-176.
18. `src/bridge.ts:148-151`: if `airplaySender.getConnectedDevices()` is empty, the handler logs a warning and returns. `getConnectedDevices()` returns the keys of the connections Map (src/airplay/sender.ts:314-316).
19. `src/bridge.ts:156-160` to `src/audio/pipeline.ts:177-212`: `audioPipeline.transcode(stream, contentType, this.config.audio)`. The body pipes the input into a `PassThrough` at src/audio/pipeline.ts:197. Log lines at src/audio/pipeline.ts:194-195: "Audio pipeline: Pass-through mode (no transcoding)" and "Note: Audio transcoding not implemented. Audio may not play correctly on some devices."
20. `src/bridge.ts:163` to `src/audio/pipeline.ts:40-96`: `audioBroadcaster.setSource(transcodedStream)`. The `data` listener writes each chunk to every consumer (src/audio/pipeline.ts:58-65); listeners are attached at src/audio/pipeline.ts:90-92.
21. `src/bridge.ts:166-168` to `src/airplay/sender.ts:110-165`: `airplaySender.startStreaming(() => this.audioBroadcaster.createConsumer())`. For each stored device, `streamSource()` is called (src/airplay/sender.ts:122-124), which creates a `PassThrough` consumer (src/audio/pipeline.ts:98-114).
22. `src/airplay/sender.ts:126` to `src/airplay/sender.ts:167-262`: `connectAndStream(connection, deviceStream)`. A TCP connection is opened with `net.createConnection` to the device host and port, port default 5000, timeout 10000 ms (src/airplay/sender.ts:183-187, 19).
23. `src/airplay/sender.ts:199-207`: on `connect`, the socket is stored and `initializeRTSPSession(connection)` is called. That method is at src/airplay/sender.ts:264-282; its body is one debug log call (273-276) and `return Promise.resolve();` (281). Comments at src/airplay/sender.ts:278-279: "For this proof of concept, we'll just log the initialization / A full implementation would require proper RTSP/RTP/RAOP protocol handling".
24. Audio leaves the process: `src/airplay/sender.ts:210`, `audioStream.pipe(socket, { end: false });`. This is the last hop in the repo. The bytes written to the socket are the bytes received at hop 15, passed through hops 19 to 21.
25. `src/bridge.ts:170-172`: `isStreaming` is set true and a log line is written.

### GAPs

- **GAP A: no Bluetooth hop.** No code, configuration or dependency for Bluetooth, A2DP or a local sound device exists in the chain or anywhere in the repo. Searched, case-insensitive, across all tracked files: `bluetooth`, `bluez`, `a2dp`, `bluealsa`, `pipewire`, `pulseaudio`, `alsa`, `avrcp`, `hci`, `sbc`, `aac`. Searched package-lock.json for: `bluetooth`, `bluez`, `a2dp`, `alsa`, `pulse`, `speaker`, `airtunes`, `raop`, `ffmpeg`, `noble`, `dbus`. Zero hits in both searches. The only related string found by a separate read is the apt package `dbus` at Dockerfile:9. All four refs point at the same commit (section 7), and the history contains no deleted file (git log --all --name-status shows only A and M entries), so there is no other branch or earlier revision holding such code.
- **GAP B: what sends audio to hop 15.** No code in the repo issues a request to `/stream`. Searched `src/` and all other tracked files except package-lock.json for `/stream`, `fetch(`, `http.request`, `https.request`, `axios`: the only hit is the route definition at src/chromecast/receiver.ts:146. The sender of that request lies outside the repo.
- **GAP C: the LOAD command path stops at a log line.** `POST /receiver/:sessionId` with type `LOAD` (src/chromecast/receiver.ts:106-125) calls `handleLoad` (src/chromecast/receiver.ts:210-226), which stores `media.contentId` as `mediaUrl` and emits `mediaLoad`. The bridge handler for `mediaLoad` (src/bridge.ts:46-53) contains one log call. Searched for other uses of `mediaUrl`: hits are src/chromecast/receiver.ts:10, src/chromecast/receiver.ts:220 and src/bridge.ts:48 only. No code retrieves audio from that URL.
- **GAP D: Chromecast protocol libraries and advertisement.** `castv2`, `castv2-client` and `node-ssdp` are declared (package.json:27-28, 32). A search of every `import` and `require(` line in `src/` shows none of the three is imported. `startSSDPAdvertisement()` (src/chromecast/receiver.ts:285-292) contains three log calls and two comments, the comments reading "Note: Full SSDP/DIAL implementation would require additional libraries / For production, consider using node-ssdp or similar" (src/chromecast/receiver.ts:286-287). Searched `src/` for `dgram`, `createServer`, `.listen(`: the only listener is the express one at src/chromecast/receiver.ts:247.
- **GAP E: FFmpeg.** `ffmpeg` is installed by Dockerfile:5 and .github/workflows/ci.yml:29 and is named in README.md:14, 37, 207. Searched `src/` for `ffmpeg`, `spawn`, `child_process`, `exec(`: the only hit is the comment at src/audio/pipeline.ts:190. No process is spawned.
- **GAP F: classes not on the chain.** `AudioBuffer` (src/audio/pipeline.ts:254-303) has no reference outside its own definition (search for `AudioBuffer` in `src/`). `AudioPipeline` holds its own `AudioBroadcaster` (src/audio/pipeline.ts:167, 173) exposed by `getBroadcaster()` (src/audio/pipeline.ts:214-216), which has no caller (search for `getBroadcaster`); the bridge uses a separate instance (src/bridge.ts:39).
- **GAP G: `airplay.targetDevices`.** Defined and validated in src/config.ts:11, 27, 129-131 and described at README.md:136 as "List of AirPlay devices to auto-connect to". Search for `targetDevices` in `src/` gives hits in src/config.ts only. No code reads the list to connect a device.
- **GAP H: volume on the wire.** `setVolume` (src/airplay/sender.ts:82-96) calls `sendVolumeCommand` (src/airplay/sender.ts:98-107), whose body is one debug log call. Comments at src/airplay/sender.ts:105-106: "Volume command would be sent via RTSP here / For simplicity, we'll just log it".

## 4. Documentation

| Path | Kind | One-line summary |
|------|------|------------------|
| README.md (256 lines) | README | Describes "Cast2Air - Chromecast to AirPlay Bridge" (README.md:1): features (9-18), flow diagram (20-32), prerequisites (34-41), install from source and Docker (43-70), quick start (72-93), CLI commands (95-108), configuration (110-140), advanced usage (142-161), troubleshooting (163-198), architecture (200-208), limitations (210-215), acknowledgments (231-236), roadmap of six unchecked items (238-245). |
| CONTRIBUTING.md (162 lines) | Contributor guide | Bug report and pull request process (9-69), development setup (71-96), code style (98-104), project structure tree (106-122), testing section stating "Currently, testing is manual. Automated tests are welcome contributions!" (126) with a six-item unchecked manual checklist (128-134), areas for contribution by priority (136-154). |
| LICENSE (21 lines) | Licence | MIT licence text. |
| config.example.json (15 lines) | Example configuration | The same default values as src/config.ts:21-35. |

Documentation kinds with no file present (git ls-files filter for `CHANGELOG`, `NOTES`, `TODO`, `ISSUE`, `docs/`): changelog, notes file, design document, issue list.

Related material that is not a file in the tree:

- Commit message bodies carry numbered lists of changes. Commit e8801c3 lists features and ends with a note beginning "Note: This is an initial implementation with simplified RTSP/RAOP protocol handling." Commit a744781 lists FIX #1 to FIX #15 by severity. Commit 2db4cb5 lists nine numbered items. Commit e556801 lists numbered P0 and P1 items, a "Before Fixes" and "After Fixes" list and a "PRODUCTION READINESS" list.
- In-code `FIX` and `FIX #n` comments mark the lines those commits changed, for example src/index.ts:13, src/chromecast/receiver.ts:28, src/audio/pipeline.ts:265, src/airplay/sender.ts:19, src/config.ts:42.
- README.md:251-252 links to an issues page and a wiki under the placeholder path `yourusername/cast2air`. Neither is in the repo.
- Search of all tracked files except package-lock.json for `TODO`, `FIXME`, `HACK`, `XXX`: zero hits.

## 5. Dependencies

### Manifests and install scripts

| Path | Kind |
|------|------|
| package.json | npm manifest (dependencies 25-35, devDependencies 36-42, engines 43-45) |
| package-lock.json | npm lockfile, lockfileVersion 3 (package-lock.json:4) |
| Dockerfile | apt installs (Dockerfile:4-10), `npm ci --only=production` (Dockerfile:20) |
| .github/workflows/ci.yml | apt installs (ci.yml:26-29), `npm ci` (ci.yml:32, 71) |
| README.md | Manual install commands (README.md:39, 47-60, 189-198) |

There is no standalone install script (no `.sh` file; section 1).

### Bluetooth dependencies

NONE. No Bluetooth package is named in package.json, package-lock.json, Dockerfile, ci.yml or README.md. Search terms are listed under GAP A in section 3.

### Audio and media dependencies named

| Dependency | Where named | Version |
|------------|-------------|---------|
| ffmpeg (apt) | Dockerfile:5; .github/workflows/ci.yml:29; README.md:37, 191, 194 | Not pinned |
| castv2 (npm) | package.json:27 | Range `^0.1.10`; lockfile 0.1.10 (package-lock.json:495-496) |
| castv2-client (npm) | package.json:28 | Range `^1.2.0`; lockfile 1.2.0 (package-lock.json:505-506) |
| airtunes2 | README.md:233, acknowledgments only | Not in package.json or package-lock.json (lockfile search for `airtunes` returned zero hits) |

No npm package for audio decoding, encoding, resampling or sound-device output is in package.json:25-42.

### Network discovery dependencies named

| Dependency | Where named | Version |
|------------|-------------|---------|
| bonjour-service (npm) | package.json:26; imported at src/airplay/discovery.ts:1 | Range `^1.0.0`; lockfile 1.3.0 (package-lock.json:447-448) |
| multicast-dns (npm, transitive) | package-lock.json:1207-1208 | Lockfile 7.2.5 |
| node-ssdp (npm) | package.json:32 | Range `^4.0.0`; lockfile 4.0.1 (package-lock.json:1229-1230) |
| avahi-daemon, avahi-discover, libavahi-compat-libdnssd-dev (apt) | Dockerfile:6-8; README.md:39; ci.yml:29 (libavahi-compat-libdnssd-dev only) | Not pinned |
| dbus (apt) | Dockerfile:9 | Not pinned |

### Other dependencies

| Dependency | Where named | Version |
|------------|-------------|---------|
| Node.js | package.json:43-45 (`>=16.0.0`); Dockerfile:1 (`node:20-bullseye-slim`); ci.yml:15 (16.x, 18.x, 20.x) | As listed |
| commander | package.json:29 | `^11.0.0`; lockfile 11.1.0 (package-lock.json:576-577) |
| express | package.json:30 | `^4.18.0`; lockfile 4.21.2 (package-lock.json:766-767) |
| express-rate-limit | package.json:31 | `^8.2.1`; lockfile 8.2.1 (package-lock.json:812-813) |
| winston | package.json:33 | `^3.10.0`; lockfile 3.18.3 (package-lock.json:1753-1754) |
| winston-daily-rotate-file | package.json:34 | `^5.0.0`; lockfile 5.0.0 (package-lock.json:1775-1776) |
| protobufjs (transitive) | package-lock.json:1311-1312 | Lockfile 6.11.4 |
| @types/express (dev) | package.json:37 | `^4.17.25`; lockfile 4.17.25 (package-lock.json:223-224) |
| @types/express-rate-limit (dev) | package.json:38 | `^5.1.3`; lockfile 5.1.3 (package-lock.json:236-237) |
| @types/node (dev) | package.json:39 | `^20.19.25`; lockfile 20.19.25 (package-lock.json:279-280) |
| ts-node (dev) | package.json:40 | `^10.9.2`; lockfile 10.9.2 (package-lock.json:1636-1637) |
| typescript (dev) | package.json:41 | `^5.3.3`; lockfile 5.9.3 (package-lock.json:1693-1694) |

Modules imported by `src/` (all `import` lines): `commander`, `readline` (src/index.ts:3, 7); `express`, `http`, `events`, `stream`, `express-rate-limit` (src/chromecast/receiver.ts:1-6); `bonjour-service`, `events` (src/airplay/discovery.ts:1, 3); `stream`, `net`, `crypto` (src/airplay/sender.ts:3-5); `stream`, `events` (src/audio/pipeline.ts:1, 3); `fs`, `path` (src/config.ts:1-2); `winston`, `winston-daily-rotate-file` (src/utils/logger.ts:1-2).

## 6. Stage pointers

Pointers for S02, S03 and S04 list the nearest non-Bluetooth material where it exists, labelled as such, because the repo has no Bluetooth material (GAP A).

| Stage | Paths most likely to hold the answers |
|-------|----------------------------------------|
| S01 Platform and stack | README.md:17, 34-41 (platforms, prerequisites); package.json:25-45; package-lock.json; tsconfig.json:3-5; Dockerfile:1-10; .github/workflows/ci.yml:15, 26-29; commit subjects a744781 ("ARM version") and e556801 body ("x86 build successful"); branch name `awdeo_x86_kube`. S01.3 Bluetooth stack: NONE SEEN. |
| S02 Adapter and controller | NONE SEEN. Searched the terms listed under GAP A (including `bluetooth`, `bluez`, `hci`) across all tracked files: zero hits. |
| S03 Discoverability, pairing and trust | NONE SEEN for Bluetooth. Nearest non-Bluetooth material: receiver name and device description at src/chromecast/receiver.ts:23-34, 100-103, 192-208; advertisement function at src/chromecast/receiver.ts:285-292; name defaults at src/config.ts:23 and src/index.ts:39, 46-48. |
| S04 Connection and A2DP profile | NONE SEEN for A2DP. Nearest non-Bluetooth material: session commands LOAD, PLAY, PAUSE, STOP at src/chromecast/receiver.ts:105-143, 210-242; single `currentSession` field at src/chromecast/receiver.ts:20, 218-223, 240. |
| S05 Audio capture | src/chromecast/receiver.ts:145-189 (HTTP `POST /stream` entry, start, end, abort handling); src/bridge.ts:55-58, 146-176; format settings at src/config.ts:14-18, 30-34 and config.example.json:10-14; `AudioBuffer` at src/audio/pipeline.ts:254-303; server timeouts at src/chromecast/receiver.ts:258-265. |
| S06 Remote control and metadata | Transport: src/chromecast/receiver.ts:126-134, 228-242; src/bridge.ts:60-72, 178-194. Volume: src/index.ts:131-137, 238-251; src/bridge.ts:252, 269-271; src/airplay/sender.ts:23, 44, 82-107; src/config.ts:28, 132-136. Metadata and status: src/chromecast/receiver.ts:8-13, 218-223; src/bridge.ts:46-53, 273-280; src/index.ts:253-268. |
| S07 Processing | src/audio/pipeline.ts:177-212 (`transcode`), 15-162 (`AudioBroadcaster`), 254-303 (`AudioBuffer`); src/bridge.ts:155-168; README.md:14, 207, 242 (roadmap item on synchronisation); commit e8801c3 body note on transcoding. Latency figures and clock-drift handling: NONE SEEN. Search for `latency`, `drift`, `sync` (excluding `async` and `...Sync(` calls) across all tracked files except package-lock.json: hits are README.md:242 and CONTRIBUTING.md:141 only, both naming audio synchronisation as open work. |
| S08 Output | src/airplay/sender.ts:1-327 (whole file: device table, TCP connection, RTSP function, stop, disconnect); src/airplay/discovery.ts:1-202 (mDNS browse, reconnect backoff at 101-144); src/bridge.ts:146-176, 246-267; src/index.ts:107-129, 182-236; src/config.ts:26-29; README.md:12-13, 206, 210-215. |
| S09 User interface and control surfaces | CLI: src/index.ts:30-87 (commands and options), 89-268 (interactive prompt and its actions), 169-180 (help text). HTTP: src/chromecast/receiver.ts:66-143 (`/health`, `/healthz`, `/ready`, `/ssdp/device-desc.xml`, `/receiver/:sessionId`), 145-189 (`/stream`). Config file: config.example.json; src/config.ts. Docs: README.md:72-161. Startup instructions printed at src/index.ts:58-71. |
| S10 Lifecycle and operations | Install: README.md:43-70; Dockerfile; docker-compose.yml; package.json:6-13. Restart and health: docker-compose.yml:8; Dockerfile:39-40; src/bridge.ts:282-365; src/chromecast/receiver.ts:66-97. Shutdown and process handlers: src/index.ts:270-346. Recovery: src/airplay/discovery.ts:101-144. Privileges: docker-compose.yml:7, 15. Configuration: src/config.ts:21-35, 41-108, 169-186; config.example.json; .gitignore:6; README.md:110-161. Logging: src/utils/logger.ts:1-50; Dockerfile:36; docker-compose.yml:11, 14. Packaging and CI: Dockerfile; .github/workflows/ci.yml; package.json:1-5, 12. |
| S11 Testing and measured behaviour | CONTRIBUTING.md:124-134 (manual testing statement and checklist); .github/workflows/ci.yml:9-38 (build and type check only); package.json:6-13 (no `test` script); README.md:210-215 (limitations), 238-245 (roadmap), 163-198 (troubleshooting); commit message bodies of a744781 ("All fixes tested and verified with successful TypeScript compilation."), 2db4cb5 and e556801 ("Tested: x86 build successful, all TypeScript checks passing"; MTBF lines under "Before Fixes" and "After Fixes"); warning text at src/audio/pipeline.ts:194-195; comments at src/airplay/sender.ts:265-279. Test files, recorded test output, named source devices, measurement logs: NONE SEEN (git ls-files shows no test, spec, log or results file). |
| S12 Security and exposure | Listener and routes: src/chromecast/receiver.ts:51-64 (body limit, rate limit), 244-283 (listen, timeouts, connection limit), 285-292 (line 291: "NOTE: No authentication required - designed for local network use only"). Ports: Dockerfile:29; src/config.ts:24; src/index.ts:14-28, 40. Privilege: docker-compose.yml:7 (`network_mode: host`), 15 (`privileged: true`); Dockerfile has no `USER` line (Dockerfile:1-43). Input handling: src/chromecast/receiver.ts:41-49, 112-123, 213-216; src/config.ts:47-82; src/bridge.ts:129-144. Outbound socket: src/airplay/sender.ts:183-187. Secrets: no secret value was seen in any tracked file (search for `password`, `secret`, `token`, `api_key`, `private` hit only the TypeScript `private` keyword on class members and the URL-parameter stripping code at src/bridge.ts:129-138); `.env` and `config.json` are git-ignored (.gitignore:4, 6); a per-connection random session ID is generated at src/airplay/sender.ts:30. Commit a744781 body lists the security-labelled changes. |

## 7. Git references

Commands used: `git branch -a`, `git for-each-ref`, `git tag`, `git stash list`, `git rev-parse --abbrev-ref HEAD`, `git symbolic-ref refs/remotes/origin/HEAD`, `git log --all`.

- **Default branch:** `awdeo_x86_kube`. `refs/remotes/origin/HEAD` is not set (git symbolic-ref reports "ref refs/remotes/origin/HEAD is not a symbolic ref"), so the default cannot be read from the remote. The manifest records `awdeo_x86_kube` as default "because it is the only branch besides the session branch" (/home/user/audio/00-manifest.md:32). There is no `main` or `master` branch.
- **Current branch:** `claude/inspiring-galileo-a9dlia` (git rev-parse --abbrev-ref HEAD). The manifest states this branch "was created by the session tooling when the repo was cloned" (/home/user/audio/00-manifest.md:33).

| Ref | Commit | Commit date |
|-----|--------|-------------|
| refs/heads/awdeo_x86_kube | e556801cdd966f7bb86b8e7d2400718d38e8866c | 2025-11-21 08:48:35 +0000 |
| refs/heads/claude/inspiring-galileo-a9dlia | e556801cdd966f7bb86b8e7d2400718d38e8866c | 2025-11-21 08:48:35 +0000 |
| refs/remotes/origin/awdeo_x86_kube | e556801cdd966f7bb86b8e7d2400718d38e8866c | 2025-11-21 08:48:35 +0000 |
| refs/remotes/origin/claude/inspiring-galileo-a9dlia | e556801cdd966f7bb86b8e7d2400718d38e8866c | 2025-11-21 08:48:35 +0000 |

- **Branches:** 2 local, 2 remote-tracking, all at the same commit. No branch holds a commit that another lacks.
- **Tags:** 0 (git tag printed nothing).
- **Stash count:** 0 (git stash list printed nothing).
- **Commits reachable from all refs:** 4, in one linear chain, author name "Claude" on each (git log --all).

| Commit | Date | Subject | Files changed |
|--------|------|---------|---------------|
| e8801c3 | 2025-11-18 07:29:52 +0000 | Add Cast2Air: Chromecast to AirPlay bridge | 20 added (all files in the tree) |
| a744781 | 2025-11-19 06:54:55 +0000 | Fix all 16 security and reliability vulnerabilities (ARM version) | 8 modified: package-lock.json, package.json, src/airplay/sender.ts, src/audio/pipeline.ts, src/bridge.ts, src/chromecast/receiver.ts, src/config.ts, src/index.ts |
| 2db4cb5 | 2025-11-21 08:36:36 +0000 | Fix 9 critical memory leaks and race conditions | 4 modified: src/airplay/sender.ts, src/audio/pipeline.ts, src/bridge.ts, src/chromecast/receiver.ts |
| e556801 | 2025-11-21 08:48:35 +0000 | Fix 12 critical reliability issues for production readiness | 7 modified: package-lock.json, package.json, src/airplay/discovery.ts, src/bridge.ts, src/chromecast/receiver.ts, src/index.ts, src/utils/logger.ts |

- No file was deleted or renamed in any commit (git log --all --name-status shows only A and M).
- Remote: `origin` at github.com/tunlezah/awdeo (git remote -v).
