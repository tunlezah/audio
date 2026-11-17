# R5 stage audit: Bluetooth stages S01 to S06

- Repo: /home/user/awdeo, audited at commit e556801 (git rev-parse HEAD). The branch `awdeo_x86_kube` and the session branch point at the same commit (git branch -a; git log --all). The working tree is clean (git status --short printed nothing). There are 4 commits, 0 tags and 0 stashes (git log --all; git tag; git stash list).
- Method: every tracked file except package-lock.json and LICENSE was read in full; those two were covered by search. All 4 commit messages were read in full (git log --all --format=%B).
- Owner notes for this repo: none ("(none given)" at /home/user/audio/00-manifest.md:15; "none" under "Owner notes" at /home/user/audio/00-manifest.md:37-39).

## Scope note

The repo names itself "Cast2Air - Chromecast to AirPlay Bridge" (README.md:1; package.json:2-4). No tracked file and no commit holds Bluetooth material. The searches behind that statement are listed once here as **search set B** and referred to by that name in the records:

- B1: `git grep -n -i -E 'bluetooth|bluez|a2dp|bluealsa|blue-alsa|pipewire|wireplumber|pulseaudio|pulse|alsa|avrcp|hci|sbc|aac|aptx|ldac|rfkill|btmgmt|bluetoothctl|pair|discoverable|mpris|org\.bluez|btusb|adapter|codec'` over all tracked files except package-lock.json. Two hits, both the word "codec": README.md:243 and CONTRIBUTING.md:145.
- B2: `git grep -n -i -E 'bluetooth|bluez|a2dp|alsa|pulse|noble|dbus|speaker|bleno|hci|avrcp|mpris'` over package-lock.json. Zero hits.
- B3: `git log --all -i -E --grep='bluetooth|a2dp|bluez|pair|adapter|codec'`. Zero commits.
- B4: `git log --all -i -G<term>` for each of `bluetooth`, `a2dp`, `bluez`, `avrcp`, `hci`. Zero commits for each.
- B5: `git log --all --name-status`: only A and M entries, so no file was deleted or renamed in any commit.

How the questions are answered in this file:

- S01.3 and every question of S02, S03 and S04 ask about the Bluetooth link itself. Each has a NOT FOUND record that cites the nearest non-Bluetooth material.
- S05 and S06 ask where audio and control enter the project's code. The repo holds a non-Bluetooth input path for these, so each has finding records. Every such record says in its title or first line that no Bluetooth stack is involved.

---

## S01 Platform and stack

### R5-S01-01  Platform and operating system statements in the README, Dockerfile and CI workflow
- Question: S01.1
- What was attempted: The README lists the product as cross-platform: "Works on Linux, macOS, and Windows" (README.md:17), with per-OS prerequisite lines (README.md:39-41, 190-197). Commit e8801c3 body repeats "Cross-platform support (Linux, macOS, Windows)". A container image is defined on `node:20-bullseye-slim` (Dockerfile:1) with one compose service (docker-compose.yml:3-17). The CI workflow runs on `ubuntu-latest` (.github/workflows/ci.yml:11, 41, 59). No board or computer model is named, and no OS version other than the image tag: `git grep -n -i -E 'raspberry|\bpi\b|\barm|aarch64|x86|amd64|kube|k8s|systemd|debian|ubuntu|bullseye|windows|macos|linux'` over tracked files except package-lock.json hit only the lines cited in this record.
- Presence: CURRENT (README.md:17 and Dockerfile:1 at commit e556801)
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No log, test output, CI result or author statement shows the program running on any named system.
- Why it turned out that way: UNKNOWN.
- Depends on: Node.js `>=16.0.0` (package.json:43-45); image tag `node:20-bullseye-slim` (Dockerfile:1); host networking and privileged mode in compose (docker-compose.yml:7, 15); an mDNS service per OS (README.md:38-41).
- Open questions: Was the program run on Linux, macOS or Windows? Does the image build, given that `npm ci --only=production` (Dockerfile:20) comes before `npm run build` (Dockerfile:26), which runs `tsc` (package.json:7), and `typescript` is declared under devDependencies (package.json:41)? Did the CI workflow ever run, given that it triggers on branches `main` and `develop` (.github/workflows/ci.yml:3-7) and the repo's branches are `awdeo_x86_kube` and the session branch (git branch -a)?
- Confidence: MEDIUM. The statements are read directly from the files; no result evidence exists.

### R5-S01-02  TypeScript build on x86, and an "ARM version" label on an earlier commit
- Question: S01.1
- What was attempted: Commit e556801 body ends "Tested: x86 build successful, all TypeScript checks passing". The default branch is named `awdeo_x86_kube` (git branch -a; /home/user/audio/00-manifest.md:32). The subject of commit a744781 ends "(ARM version)". No tracked file names an architecture, and no Kubernetes file is tracked (search in R5-S01-01: zero hits for `arm`, `aarch64`, `x86`, `amd64`, `kube`, `k8s`; git ls-files lists 20 files, none a manifest of that kind).
- Presence: CURRENT. Commit e556801 is the tip of `awdeo_x86_kube` (git log --all).
- Outcome: WORKED. The scope of this outcome is compilation on x86 only.
- Outcome evidence: STATED. Commit e556801: "Tested: x86 build successful, all TypeScript checks passing". Commit a744781: "All fixes tested and verified with successful TypeScript compilation."
- Why it turned out that way: UNKNOWN. No cause is recorded.
- Depends on: TypeScript range `^5.3.3` (package.json:41), locked at 5.9.3 (package-lock.json:1693-1694); the `tsc` build script (package.json:7); an x86 machine that no file identifies.
- Open questions: What ARM hardware does "(ARM version)" refer to? Was the program run, as distinct from compiled, on either architecture? What does "kube" in the branch name refer to?
- Confidence: MEDIUM. STATED evidence alone. `dist/` is git-ignored (.gitignore:2) and no build output or CI log is in the repo.

### R5-S01-03  TypeScript on Node.js, with express for HTTP and commander for the CLI
- Question: S01.2
- What was attempted: All source is TypeScript: 8 `.ts` files under `src/` (git ls-files). Compiler settings: target ES2020, module commonjs, strict (tsconfig.json:3-4, 8). TypeScript range `^5.3.3` (package.json:41), locked 5.9.3 (package-lock.json:1693-1694); the README badge says 5.3 (README.md:5). Runtime Node.js `>=16.0.0` (package.json:43-45); Node 20 in the image (Dockerfile:1); Node 16.x, 18.x and 20.x in the CI matrix (.github/workflows/ci.yml:15). Frameworks and libraries imported: express (src/chromecast/receiver.ts:1, 25), express-rate-limit (src/chromecast/receiver.ts:6), commander (src/index.ts:3, 9), bonjour-service (src/airplay/discovery.ts:1), winston and winston-daily-rotate-file (src/utils/logger.ts:1-2). Node core modules imported: `readline` (src/index.ts:7), `http` for a type (src/chromecast/receiver.ts:2), `events`, `stream` (src/chromecast/receiver.ts:4-5; src/audio/pipeline.ts:1, 3), `net`, `crypto` (src/airplay/sender.ts:4-5), `fs`, `path` (src/config.ts:1-2). `ts-node` runs the source directly in the `dev` script (package.json:9, 40).
- Presence: CURRENT (package.json:25-45; tsconfig.json:1-20)
- Outcome: UNVERIFIED
- Outcome evidence: NONE for the program running. Compilation alone is STATED as successful; that is recorded in R5-S01-02.
- Why it turned out that way: UNKNOWN.
- Depends on: the versions listed above; the `tsc` build producing `dist/index.js` (package.json:5, 7-8; tsconfig.json:6-7).
- Open questions: Was the compiled program started on any of the three Node versions in the CI matrix?
- Confidence: MEDIUM. INFERRED from clear manifest and import lines; no result evidence.

### R5-S01-04  NOT FOUND
- Question: S01.3
- Searched: search set B (B1 to B5 in the scope note). Also read package.json:25-42 (no Bluetooth package), Dockerfile:4-10 (apt list), .github/workflows/ci.yml:26-29 (apt list), README.md:34-41 (prerequisites) and all `import` lines in `src/` (`git grep -n -E "^import|require\(" -- src`).
- Nearest related material: NONE for a Bluetooth stack. The apt package `dbus` is installed in the image (Dockerfile:9); no file states its purpose and no D-Bus client library is in package.json:25-42 or package-lock.json (B2).

### R5-S01-05  Audio handled as raw byte streams inside the Node.js process; no operating-system audio stack
- Question: S01.4
- What was attempted: Audio moves through Node.js stream objects only: a `Readable` built from an HTTP request body (src/chromecast/receiver.ts:153-155, 166-168), a `PassThrough` in the pipeline (src/audio/pipeline.ts:185, 197), one `PassThrough` per output device (src/audio/pipeline.ts:98-114) and a TCP socket (src/airplay/sender.ts:183-187, 210). No sound-server, sound-device or codec library is declared (package.json:25-42) or named anywhere (B1 hit only the word "codec" in two roadmap lines; B2 zero hits).
- Presence: CURRENT (src/audio/pipeline.ts:177-212 at commit e556801)
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No artefact or statement shows audio passing through the chain.
- Why it turned out that way: UNKNOWN.
- Depends on: Node.js stream defaults; the design choice stated at src/audio/pipeline.ts:188-189: "For the initial version, we'll just pass through the audio without transcoding."
- Open questions: Was any audio ever sent through the chain? What did an output device do with the bytes it received?
- Confidence: MEDIUM. INFERRED from clear code.

### R5-S01-06  FFmpeg named and installed as the transcoding component; no code invokes it
- Question: S01.4
- What was attempted: FFmpeg is installed by apt with no version pin (Dockerfile:5; .github/workflows/ci.yml:29) and listed as a prerequisite "for audio transcoding" (README.md:37; CONTRIBUTING.md:75). The README describes "Real-time Transcoding: Automatic audio format conversion using FFmpeg" (README.md:14) and "AudioPipeline: FFmpeg-based audio transcoding and buffering" (README.md:207). In `src/` the only mention is a comment (src/audio/pipeline.ts:190). No process is started: `git grep -n -E 'spawn|child_process'` over `src/` gives zero hits.
- Presence: CURRENT for the install lines and the documentation (Dockerfile:5; README.md:14). No call site exists in any of the 4 commits (`git grep -i ffmpeg <commit> -- src package.json` shows the one comment line in each).
- Outcome: FAILED
- Outcome evidence: STATED. Log text in code: "Note: Audio transcoding not implemented. Audio may not play correctly on some devices." (src/audio/pipeline.ts:195). Commit e8801c3: "Full protocol implementation and audio transcoding (using FFmpeg) can be added in future iterations."
- Why it turned out that way: STATED. "For the initial version, we'll just pass through the audio without transcoding." (src/audio/pipeline.ts:188-189). The three later commits did not change this (the comment is present in each; see Presence).
- Depends on: an unpinned apt `ffmpeg` package on Debian bullseye (Dockerfile:1, 5); a transcoding call that the repo does not contain.
- Open questions: NONE.
- Confidence: HIGH. STATED evidence that the code agrees with.

### R5-S01-07  npm dependencies declared with ranges and locked in package-lock.json
- Question: S01.5
- What was attempted: Runtime dependencies imported by the source, with range and locked version: bonjour-service `^1.0.0`, locked 1.3.0 (package.json:26; package-lock.json:447-448); commander `^11.0.0`, locked 11.1.0 (package.json:29; package-lock.json:576-577); express `^4.18.0`, locked 4.21.2 (package.json:30; package-lock.json:766-767); express-rate-limit `^8.2.1`, locked 8.2.1 (package.json:31; package-lock.json:812-813); winston `^3.10.0`, locked 3.18.3 (package.json:33; package-lock.json:1753-1754); winston-daily-rotate-file `^5.0.0`, locked 5.0.0 (package.json:34; package-lock.json:1775-1776). Development dependencies: @types/express `^4.17.25`, @types/express-rate-limit `^5.1.3`, @types/node `^20.19.25`, ts-node `^10.9.2`, typescript `^5.3.3` (package.json:36-42). bonjour-service brings multicast-dns `^7.2.5` (package-lock.json:452-455), locked 7.2.5 (package-lock.json:1207-1208). The lockfile is lockfileVersion 3 (package-lock.json:4). Commit e556801 body lists "winston-daily-rotate-file@^4.x" under "Dependencies Added", while package.json:34 reads `^5.0.0`.
- Presence: CURRENT (package.json:25-42)
- Outcome: UNVERIFIED
- Outcome evidence: NONE for the dependencies at run time. Compilation against them is STATED as successful; that is recorded in R5-S01-02.
- Why it turned out that way: UNKNOWN.
- Depends on: the npm registry versions locked in package-lock.json; Node.js `>=16.0.0` (package.json:43-45).
- Open questions: Why does the commit body name a `^4.x` range for winston-daily-rotate-file when the manifest holds `^5.0.0`?
- Confidence: MEDIUM. INFERRED from clear manifest lines.

### R5-S01-08  Chromecast and SSDP libraries declared but never imported
- Question: S01.5
- What was attempted: Three packages are declared and locked: castv2 `^0.1.10`, locked 0.1.10 (package.json:27; package-lock.json:495-496); castv2-client `^1.2.0`, locked 1.2.0 (package.json:28; package-lock.json:505-506); node-ssdp `^4.0.0`, locked 4.0.1 (package.json:32; package-lock.json:1229-1230). None appears in an `import` or `require(` line in `src/` (`git grep -n -E "^import|require\(" -- src`). node-ssdp is named in a comment only: "For production, consider using node-ssdp or similar" (src/chromecast/receiver.ts:287). The README acknowledgments name castv2 (README.md:234) and also airtunes2 (README.md:233), which is in neither package.json nor package-lock.json (`git grep -i airtunes` at each commit over `src` and package.json: zero hits).
- Presence: DISABLED. Declared in the manifest in all 4 commits and unreachable from the code, since no source file imports them (`git grep -n -i -E 'castv2|node-ssdp' <commit> -- src package.json` for e8801c3, a744781, 2db4cb5, e556801 shows the package.json lines and the one comment).
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. The comment at src/chromecast/receiver.ts:286 reads "Note: Full SSDP/DIAL implementation would require additional libraries".
- Depends on: nothing in the code calls these packages.
- Open questions: Were these packages ever wired in outside the 4 commits in the repo? Was airtunes2 used at any point?
- Confidence: MEDIUM. INFERRED from clear import lines across all commits.

### R5-S01-09  System packages installed by apt without version pins
- Question: S01.5
- What was attempted: The image installs `ffmpeg`, `avahi-daemon`, `avahi-discover`, `libavahi-compat-libdnssd-dev` and `dbus` (Dockerfile:4-10). CI installs `ffmpeg` and `libavahi-compat-libdnssd-dev` (.github/workflows/ci.yml:26-29). The README lists the three avahi packages for Linux (README.md:39). No version is pinned in any of these lines. The image start command is `CMD ["npm", "start", "start"]` (Dockerfile:43); no line in the Dockerfile or compose file starts `avahi-daemon` or `dbus` (Dockerfile:1-43; docker-compose.yml:1-17). Device discovery in the code goes through bonjour-service (src/airplay/discovery.ts:1, 56, 61), which depends on multicast-dns (package-lock.json:452-455).
- Presence: CURRENT (Dockerfile:4-10)
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: Debian bullseye package versions at image build time (Dockerfile:1); `network_mode: host` (docker-compose.yml:7), described as "host network mode for device discovery" (README.md:68).
- Open questions: Is `avahi-daemon` or `dbus` used by anything at run time in the container? What purpose was `dbus` installed for?
- Confidence: MEDIUM. INFERRED from clear Dockerfile and import lines.

---

## S02 Adapter and controller

### R5-S02-01  NOT FOUND
- Question: S02.1
- Searched: search set B (B1 to B5). B1 includes `hci`, `adapter`, `rfkill`, `btmgmt`, `bluetoothctl`, `btusb`, `org\.bluez`: zero hits for each. Read Dockerfile:1-43, docker-compose.yml:1-17, src/config.ts:1-187 and config.example.json:1-15 in full for any device or adapter setting: none.
- Nearest related material: NONE for a Bluetooth adapter. The compose service runs with `privileged: true` and `network_mode: host` (docker-compose.yml:7, 15); the only stated purpose is "host network mode for device discovery" (README.md:68), which refers to network discovery of AirPlay devices.

### R5-S02-02  NOT FOUND
- Question: S02.2
- Searched: search set B (B1 to B5). Additional `git grep -n -i -E` searches over tracked files except package-lock.json: `raspberry|\bpi\b|\barm|aarch64|x86|amd64` (zero hits) and `chipset|firmware|dongle|\busb\b|controller` (zero hits). Read README.md:34-41 (prerequisites) and README.md:210-215 (limitations).
- Nearest related material: NONE. The prerequisites list names Node.js, FFmpeg and Avahi/Bonjour only (README.md:36-41).

### R5-S02-03  NOT FOUND
- Question: S02.3
- Searched: search set B (B1 to B5). Read src/index.ts:270-346 (shutdown and process handlers), src/bridge.ts:196-244 (start and stop) and src/airplay/discovery.ts:101-144 for any hardware recovery path.
- Nearest related material: Non-Bluetooth only. Recovery of the mDNS browser for AirPlay output devices, with up to 10 attempts and a delay doubling from 5000 ms to a 60000 ms cap (src/airplay/discovery.ts:19-20, 101-144). Container restart policy `restart: unless-stopped` (docker-compose.yml:8).

---

## S03 Discoverability, pairing and trust

### R5-S03-01  NOT FOUND
- Question: S03.1
- Searched: search set B (B1 to B5); B1 includes `discoverable` and `pair`: zero hits. `git grep -n -i -E "friendlyName|deviceType|modelName|chromecast\.name|--name"` over tracked files except package-lock.json. Read src/chromecast/receiver.ts:192-208, 285-292.
- Nearest related material: Non-Bluetooth only. A Chromecast-style receiver name, default "Cast2Air Bridge" (src/config.ts:23), settable by `--name` (src/index.ts:39, 46-48) or `chromecast.name` (README.md:134). A device description served at `/ssdp/device-desc.xml` with device type `urn:dial-multiscreen-org:device:dial:1` (src/chromecast/receiver.ts:100-103, 192-208). The function `startSSDPAdvertisement()` holds three log calls and no network code (src/chromecast/receiver.ts:285-292); the README lists "Full DIAL/SSDP support may require additional work" (README.md:212). No time limit on visibility appears in any file.

### R5-S03-02  NOT FOUND
- Question: S03.2
- Searched: search set B (B1 to B5); also `git grep -n -i -E 'agent|\bpin\b|passkey|confirm|accept|trust'` over tracked files except package-lock.json: two hits, both comments about accepting network traffic (src/chromecast/receiver.ts:87; src/index.ts:291).
- Nearest related material: Non-Bluetooth only. The HTTP receiver accepts requests with no pairing step; the log line reads "NOTE: No authentication required - designed for local network use only" (src/chromecast/receiver.ts:291), added as "FIX #13: Document authentication expectations for local network use" (commit a744781).

### R5-S03-03  NOT FOUND
- Question: S03.3
- Searched: search set B (B1 to B5); B1 includes `pair`: zero hits. `git grep -n targetDevices -- src` (hits in src/config.ts only). Read src/config.ts:1-187 for any stored device list.
- Nearest related material: Non-Bluetooth only, and on the output side. AirPlay devices seen on the network are held in an in-memory Map (src/airplay/discovery.ts:15, 72, 82) and devices chosen by the operator in another in-memory Map (src/airplay/sender.ts:17, 43, 75); the CLI lists, adds and removes them (src/index.ts:107-129). The config key `airplay.targetDevices` is described as "List of AirPlay devices to auto-connect to" (README.md:136) and is validated at src/config.ts:129-131; no code reads it to connect a device. On the source side, one `currentSession` field is held in memory (src/chromecast/receiver.ts:20).

### R5-S03-04  NOT FOUND
- Question: S03.4
- Searched: search set B (B1 to B5). Read src/chromecast/receiver.ts:106-189, 210-242 and src/audio/pipeline.ts:40-96 for handling of a second sender.
- Nearest related material: Non-Bluetooth only. A second LOAD command overwrites the single `currentSession` field (src/chromecast/receiver.ts:20, 218-223). A second `POST /stream` emits another `audioStream` event (src/chromecast/receiver.ts:188); `setSource` cleans up the earlier source before taking the new one (src/audio/pipeline.ts:50-54) and ignores a call made while one is in progress (src/audio/pipeline.ts:42-45).

### R5-S03-05  NOT FOUND
- Question: S03.5
- Searched: search set B (B1 to B5); also read src/chromecast/receiver.ts:51-64, 244-292 for any allow-list, token or address check (none seen). `git grep -n -i -E 'password|secret|token|auth'` over `src/`: hits are the log-sanitising code at src/bridge.ts:129-138 and the comment and log line at src/chromecast/receiver.ts:288, 291.
- Nearest related material: Non-Bluetooth only. A rate limit of 100 requests per minute per IP (src/chromecast/receiver.ts:56-64), a limit of 100 connections (src/chromecast/receiver.ts:265), and the statement "No authentication required - designed for local network use only" (src/chromecast/receiver.ts:291).

---

## S04 Connection and A2DP profile

### R5-S04-01  NOT FOUND
- Question: S04.1
- Searched: search set B (B1 to B5); B1 includes `a2dp`, `bluez`, `bluealsa`, `pipewire`, `pulse`: zero hits. Read src/chromecast/receiver.ts:1-310 in full.
- Nearest related material: Non-Bluetooth only. The receiving role in this repo is an express HTTP server described as a "Chromecast Receiver" (README.md:11; src/chromecast/receiver.ts:15-34, 244-256). The README calls it "a simplified Chromecast receiver" (README.md:212). The declared Cast protocol libraries are not imported (R5-S01-08).

### R5-S04-02  NOT FOUND
- Question: S04.2
- Searched: search set B (B1 to B5); B1 includes `sbc`, `aac`, `aptx`, `ldac`, `codec`: the only hits are README.md:243 and CONTRIBUTING.md:145. Read src/audio/pipeline.ts:177-212.
- Nearest related material: Non-Bluetooth only. No codec is offered or negotiated. The request's Content-Type header is carried as a string and logged (src/chromecast/receiver.ts:150; src/audio/pipeline.ts:182), and the bytes pass through unchanged (src/audio/pipeline.ts:194-197). The roadmap holds an unchecked item "Support for more audio codecs" (README.md:243) and CONTRIBUTING lists "Additional audio codec support" (CONTRIBUTING.md:145).

### R5-S04-03  NOT FOUND
- Question: S04.3
- Searched: search set B (B1 to B5). `git grep -n -i -E 'reconnect|out of range|reboot'` over tracked files except package-lock.json: every hit is in src/airplay/discovery.ts (lines 17-184). Read src/chromecast/receiver.ts:146-189, 258-278.
- Nearest related material: Non-Bluetooth only. On the input side, the HTTP request events `end`, `error` and `close` (src/chromecast/receiver.ts:170-186) and server timeouts (src/chromecast/receiver.ts:258-265); these are recorded in R5-S05-05. No code re-establishes an input connection. On the output side, the mDNS browser has reconnect logic (src/airplay/discovery.ts:101-144). After a restart nothing is restored from disk except `config.json` values (src/config.ts:84-108); the compose restart policy is `unless-stopped` (docker-compose.yml:8).

### R5-S04-04  NOT FOUND
- Question: S04.4
- Searched: search set B (B1 to B5). Read src/chromecast/receiver.ts:20, 106-189 and src/audio/pipeline.ts:40-96 for priority or switching between senders (no such code seen).
- Nearest related material: Non-Bluetooth only. One `currentSession` field (src/chromecast/receiver.ts:20, 218-223) and one broadcaster source at a time (src/audio/pipeline.ts:16, 50-54); the same material as R5-S03-04. Multiple devices are handled on the output side only: "Stream to multiple AirPlay devices simultaneously" (README.md:13; src/airplay/sender.ts:121-134).

---

## S05 Audio capture

### R5-S05-01  Audio enters as the body of an HTTP POST to /stream; no Bluetooth stack is involved
- Question: S05.1
- What was attempted: The express route `POST /stream` (src/chromecast/receiver.ts:146-189) wraps the request body in a `Readable` (153-155), pushes each body chunk into it (166-168) and emits an `audioStream` event carrying the stream and the content type (188). The bridge handler for that event (src/bridge.ts:55-58, registered at src/bridge.ts:87) calls `handleAudioStream` (src/bridge.ts:146-176). The listener is opened by `this.app.listen(this.port, ...)` (src/chromecast/receiver.ts:247) on default port 8009 (src/config.ts:24; src/index.ts:40). No code in the repo sends a request to `/stream`: `git grep -n -E "fetch\(|http\.request|https\.request|axios|http\.get|/stream|/receiver"` over tracked files except package-lock.json hits only the two route definitions (src/chromecast/receiver.ts:106, 146) and an import line (src/bridge.ts:1). The README and CONTRIBUTING do not mention the route (same search); the README names the source as "any Chromecast-compatible app" (README.md:30).
- Presence: CURRENT (src/chromecast/receiver.ts:146-189 at commit e556801; present since commit e8801c3, where the route is at line 76 of the same file)
- Outcome: UNVERIFIED
- Outcome evidence: NONE. "Currently, testing is manual." (CONTRIBUTING.md:126) and the checklist item "Audio streams correctly" is unchecked (CONTRIBUTING.md:131).
- Why it turned out that way: UNKNOWN.
- Depends on: an HTTP client outside the repo that posts audio to `/stream`; express `^4.18.0` (package.json:30); the rate limiter of 100 requests per minute per IP registered before the route (src/chromecast/receiver.ts:56-64); the server socket timeout of 30000 ms (src/chromecast/receiver.ts:259); at least one AirPlay device added through the CLI before the request arrives, since the handler otherwise logs "No AirPlay devices connected, cannot stream audio" and returns (src/bridge.ts:148-151); no authentication on the route (src/chromecast/receiver.ts:291).
- Open questions: What client was meant to post to `/stream`? Does any Chromecast sender app issue such a request? Was the route ever exercised?
- Confidence: MEDIUM. INFERRED from clear code; no result evidence.

### R5-S05-02  LOAD command stores a media URL; no code retrieves audio from it; no Bluetooth stack is involved
- Question: S05.1
- What was attempted: `POST /receiver/:sessionId` with JSON type `LOAD` (src/chromecast/receiver.ts:106-125) calls `handleLoad` (210-226), which stores `media.contentId` as `mediaUrl` in the session (218-223) and emits `mediaLoad` (225). The bridge handler logs the title and a sanitised URL and does nothing else (src/bridge.ts:46-53). `mediaUrl` is referenced at src/chromecast/receiver.ts:10, 220 and src/bridge.ts:48 only (`git grep -n mediaUrl -- src`). No HTTP client call exists in `src/` (search in R5-S05-01).
- Presence: CURRENT. The route is active; the stored URL is logged only (src/bridge.ts:46-53).
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: a client that sends JSON of the form `{type: 'LOAD', media: {contentId, ...}}` to the route; the 1 MB JSON body limit (src/chromecast/receiver.ts:53).
- Open questions: Was fetching the URL intended as an audio entry point? Does any client speak this JSON-over-HTTP form, given that the declared `castv2` libraries are not imported (R5-S01-08)?
- Confidence: MEDIUM. INFERRED from clear code.

### R5-S05-03  Format at the entry point is whatever the client sends, labelled only by the Content-Type header
- Question: S05.2
- What was attempted: No Bluetooth stack is involved; the entry point is the route in R5-S05-01. The content type is read from the request header with the default `application/octet-stream` (src/chromecast/receiver.ts:150) and carried along as a string (src/chromecast/receiver.ts:188; src/bridge.ts:55-58, 156-160). Its only uses are log fields (src/bridge.ts:56; src/audio/pipeline.ts:182). The bytes are not parsed, decoded or inspected (src/audio/pipeline.ts:188-197). A comment in the bridge reads "Transcode the audio to PCM format required by AirPlay" (src/bridge.ts:155), while the function it calls logs "Audio pipeline: Pass-through mode (no transcoding)" (src/audio/pipeline.ts:194). Sample rate, bit depth, channel count and encoded-or-PCM at this point are therefore not fixed or checked by any code.
- Presence: CURRENT (src/chromecast/receiver.ts:150; src/audio/pipeline.ts:177-212)
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The author's log text "Audio may not play correctly on some devices." (src/audio/pipeline.ts:195) is a forecast, not a recorded result.
- Why it turned out that way: UNKNOWN for any playback result. The design choice is STATED: "For the initial version, we'll just pass through the audio without transcoding." (src/audio/pipeline.ts:188-189).
- Depends on: the format chosen by the client; the Content-Type header the client sets.
- Open questions: What format did the author expect to arrive at `/stream`? What format do the AirPlay targets accept on the raw TCP connection opened at src/airplay/sender.ts:183-187?
- Confidence: MEDIUM. INFERRED from clear code.

### R5-S05-04  Configured audio format (44100 Hz, 16 bit, 2 channels) is loaded and logged but does not act on the stream
- Question: S05.2
- What was attempted: Defaults `sampleRate: 44100`, `bitDepth: 16`, `channels: 2` (src/config.ts:30-34; config.example.json:10-14; README.md:124-128). Values from `config.json` are validated: sample rate above 0, bit depth one of 8, 16, 24, 32, channels above 0 (src/config.ts:141-156); the README says "Bit depth for audio (16 or 24)" (README.md:139). The values are passed to `transcode` as `outputFormat` (src/bridge.ts:156-160). INFERRED: they have no effect on the audio, because `outputFormat` appears only in the parameter list and one log call (src/audio/pipeline.ts:180, 182), and `git grep -n -E 'sampleRate|bitDepth|channels' -- src` outside src/config.ts hits only the interface definition (src/audio/pipeline.ts:6-8).
- Presence: CURRENT. Loaded, validated and logged (src/config.ts:141-156; src/audio/pipeline.ts:182).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The README troubleshooting entry "Adjust audio settings in `config.json`" (README.md:180) records no result.
- Why it turned out that way: UNKNOWN. Related STATED fact: "Audio transcoding not implemented." (src/audio/pipeline.ts:195).
- Depends on: a transcoding step that the repo does not contain (R5-S01-06).
- Open questions: Were these three values meant to describe the input, the output, or both?
- Confidence: MEDIUM. INFERRED from clear code.

### R5-S05-05  Stream start and stop taken from the HTTP request lifecycle
- Question: S05.3
- What was attempted: No Bluetooth stack is involved. Start: the arrival of a `POST /stream` request; `audioStream` is emitted inside the route handler before any body chunk has been read (src/chromecast/receiver.ts:146-188). Stop: request `end` pushes `null` and answers `{status: 'ok'}` (170-174); request `error` destroys the stream with the error (176-179); request `close` before `end` destroys the stream (158-164, 182-186). The `close` handling was added in commit 2db4cb5 (item 6, "ChromecastReceiver Stream Orphaning"). Downstream, the broadcaster's `end` listener ends every consumer, clears its active flag and emits `ended` (src/audio/pipeline.ts:67-76); no listener for `ended` exists (`git grep -n "'ended'" -- src` hits src/audio/pipeline.ts:75 only). The bridge flag `isStreaming` is set true at src/bridge.ts:170 and set false only in `pauseStreaming` and `stopStreaming` (src/bridge.ts:181, 191), which run on PAUSE and STOP commands and on bridge stop (src/bridge.ts:64-72, 230). INFERRED: after a request ends without a PAUSE or STOP command, `isStreaming` stays true, because no path from the `end` event reaches src/bridge.ts:181 or 191.
- Presence: CURRENT (src/chromecast/receiver.ts:146-189 at commit e556801)
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Commit 2db4cb5 gives the purpose of the abort handling ("Fix: Prevents orphaned streams on network interruptions") without a test result.
- Why it turned out that way: UNKNOWN.
- Depends on: Node.js HTTP request events; a client that keeps one request open for the length of a stream; the server socket timeout of 30000 ms, commented "30 second timeout for requests" (src/chromecast/receiver.ts:259).
- Open questions: After an aborted request, what ends the per-device consumer streams and sockets? Does the `status` command report "Streaming: Yes" after a stream has ended? What happens to a long-running stream request when the 30000 ms timeout applies?
- Confidence: MEDIUM. INFERRED from clear code.

### R5-S05-06  Chunks forwarded as they arrive, with no buffer size set and no check of push or write results
- Question: S05.4
- What was attempted: No Bluetooth stack is involved. The `Readable` has an empty `read()` and each request chunk is pushed without use of the return value (src/chromecast/receiver.ts:153-155, 166-168). `transcode` pipes it into a `PassThrough` (src/audio/pipeline.ts:185, 197). The broadcaster writes each chunk to every consumer without use of the return value of `write` (src/audio/pipeline.ts:58-65). Each consumer is piped to a TCP socket (src/airplay/sender.ts:210). No buffer size or duration is set anywhere on this path. If no AirPlay device has been added, the handler returns without consuming or destroying the stream (src/bridge.ts:148-151) while the route goes on pushing chunks (src/chromecast/receiver.ts:166-168). INFERRED: data waits in process memory with no set bound wherever a downstream reader is slower than the sender, because no step on the path acts on a push or write result. No handling of underruns, overruns or gaps exists: `git grep -n -i -E 'underrun|overrun|xrun|\bgap|silence|highWaterMark|backpressure|drain|latency|jitter'` over tracked files except package-lock.json hits only the unused class in R5-S05-07 (src/audio/pipeline.ts:271, 275) and a shutdown comment (src/index.ts:270).
- Presence: CURRENT (src/chromecast/receiver.ts:153-168; src/audio/pipeline.ts:58-65)
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: Node.js stream defaults; the relative speed of the sending client and each AirPlay socket; the number of consumers, one per added device (src/airplay/sender.ts:122-124).
- Open questions: How much memory is held when an output socket stalls? What reaches the output during a pause in the incoming request body?
- Confidence: MEDIUM. INFERRED from clear code.

### R5-S05-07  AudioBuffer class: 8192-byte regrouping with a 1 MB cap, defined but never used
- Question: S05.4
- What was attempted: `AudioBuffer extends Transform` (src/audio/pipeline.ts:254-303) regroups input into chunks of 8192 bytes by default (260, 283-292) and flushes the remainder at the end (297-302). Since commit a744781 ("FIX #11") it returns an error through the callback when the held bytes plus the new chunk exceed 1048576 (src/audio/pipeline.ts:260, 265, 269-278). The log text there says "Buffer size limit reached, applying backpressure" (271) and the code path is `callback(new Error('Buffer overflow'))` (276). The class has no reference outside its own definition.
- Presence: DISABLED. Defined and exported, never instantiated: `git grep -n AudioBuffer -- src` hits src/audio/pipeline.ts:254 only, and the same search at commits e8801c3, a744781, 2db4cb5 and e556801 shows the definition line alone.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Commit a744781 lists "FIX #11: Add bounded buffer (1MB limit) to prevent unbounded memory growth" as a change made, not as a result seen.
- Why it turned out that way: UNKNOWN.
- Depends on: nothing in the code calls the class.
- Open questions: Where in the chain was the class meant to sit? Why was it changed in commit a744781 while unused?
- Confidence: MEDIUM. INFERRED from clear code across all 4 commits.

---

## S06 Remote control and metadata

### R5-S06-01  PLAY, PAUSE and STOP commands received over HTTP; no skip; nothing sent back to the source
- Question: S06.1
- What was attempted: No Bluetooth or AVRCP is involved. `POST /receiver/:sessionId` accepts a JSON `type` of LOAD, PLAY, PAUSE or STOP (src/chromecast/receiver.ts:106-134); any other type gets HTTP 400 (135-136). PLAY emits `play` (228-231) and the bridge handler makes one log call (src/bridge.ts:60-62). PAUSE emits `pause` (src/chromecast/receiver.ts:233-236); the bridge calls `pauseStreaming` (src/bridge.ts:64-67, 178-184), which calls `airplaySender.stopStreaming()`, destroying each device stream and ending each socket (src/airplay/sender.ts:284-312). STOP clears `currentSession` and emits `stop` (src/chromecast/receiver.ts:238-242); the bridge also stops the pipeline and cleans the broadcaster (src/bridge.ts:69-72, 186-194). The `sessionId` path parameter is passed to the LOAD handler only (src/chromecast/receiver.ts:124, 127, 130, 133). INFERRED: PLAY after PAUSE does not resume output for the same stream, because the PLAY handler holds a log call only and the per-device streams were destroyed at src/airplay/sender.ts:295-298. No skip, seek, previous or queue command exists: `git grep -n -i -E 'skip|\bnext\b|previous|seek|queue'` over `src/`, README.md, CONTRIBUTING.md and config.example.json gives zero hits. Control runs from the source to this program only; no request toward a source exists in the code (search in R5-S05-01).
- Presence: CURRENT (src/chromecast/receiver.ts:106-143, 228-242 at commit e556801)
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The manual checklist is unchecked throughout (CONTRIBUTING.md:128-134).
- Why it turned out that way: UNKNOWN.
- Depends on: a client that uses this JSON-over-HTTP command form; the statement "This implementation uses a simplified Chromecast receiver." (README.md:212).
- Open questions: Does any sender app issue commands in this form? After PAUSE and then PLAY, does audio resume?
- Confidence: MEDIUM. INFERRED from clear code.

### R5-S06-02  Volume set from the CLI is stored and logged; no command is sent and no samples are scaled
- Question: S06.2
- What was attempted: The CLI command `volume <0-100>` (src/index.ts:131-137, 238-251; README.md:105) calls `bridge.setVolume` (src/bridge.ts:269-271), which calls `AirPlaySender.setVolume`. That method clamps the value to 0-100, stores it in one field shared by all devices (src/airplay/sender.ts:18, 82-85) and calls `sendVolumeCommand` for each connection that is connected and has a socket (88-92). `sendVolumeCommand` maps the value to a range of -30 to 0 (99-100) and makes one debug log call (101-104). The starting value is `airplay.volume`, default 50 (src/config.ts:28, 132-136; README.md:137), passed in when a device is added (src/bridge.ts:252; src/airplay/sender.ts:44). No code alters audio samples (src/audio/pipeline.ts:188-197). On the source side the receiver has no volume command type (src/chromecast/receiver.ts:118-137). INFERRED from that switch statement: source volume and this value are not linked.
- Presence: CURRENT (src/airplay/sender.ts:82-107 at commit e556801)
- Outcome: FAILED
- Outcome evidence: STATED. Comments in the function: "Volume command would be sent via RTSP here" and "For simplicity, we'll just log it" (src/airplay/sender.ts:105-106). The code agrees: the function body ends after the log call (src/airplay/sender.ts:98-107). The checklist item "Volume control works" is unchecked (CONTRIBUTING.md:132).
- Why it turned out that way: STATED. The RTSP session the command would travel on is a placeholder: "For this proof of concept, we'll just log the initialization" (src/airplay/sender.ts:278) and "A full implementation would require proper RTSP/RTP/RAOP protocol handling" (src/airplay/sender.ts:279).
- Depends on: an RTSP implementation that the repo does not contain (src/airplay/sender.ts:264-282).
- Open questions: Was the value meant to follow the volume set in the casting app?
- Confidence: HIGH. STATED evidence that the code agrees with.

### R5-S06-03  Title and session kept in memory from the LOAD command; a streaming flag shown in the CLI and health output
- Question: S06.3
- What was attempted: No Bluetooth or AVRCP is involved. On LOAD the receiver stores `sessionId`, `mediaUrl`, `contentType` and an optional `title` taken from `media.metadata?.title` (src/chromecast/receiver.ts:8-13, 218-223). The bridge logs the title and a sanitised URL once (src/bridge.ts:46-53). The CLI `status` command prints "Current media:" with the title or "Unknown" (src/index.ts:253-268, line 265) and "Streaming: Yes/No" from the `isStreaming` flag (src/index.ts:261; src/bridge.ts:273-280). The `/health` response carries `hasSession` and `streaming` (src/bridge.ts:289, 299-301; src/chromecast/receiver.ts:67-80). The session is cleared on STOP (src/chromecast/receiver.ts:240) and is not written to disk (src/config.ts:169-176 saves configuration only). No artist, album, artwork, position or duration field exists: `git grep -n -i -E 'artist|album|artwork|position|duration'` over `src/`, README.md, CONTRIBUTING.md and config.example.json gives zero hits. Nothing is passed to the output devices (src/airplay/sender.ts:1-327 holds no title or metadata code). Replies to the source are `{status: 'ok'}` or an error object (src/chromecast/receiver.ts:115, 122, 136, 138, 141).
- Presence: CURRENT (src/chromecast/receiver.ts:218-223; src/index.ts:253-268 at commit e556801)
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: a client that sends a LOAD command with a `metadata.title` field; the accuracy of the `isStreaming` flag (R5-S05-05).
- Open questions: Does any sender deliver a LOAD command in this form? Is the title shown correct once a second stream starts without a new LOAD?
- Confidence: MEDIUM. INFERRED from clear code.

---

## Tally

| Stage | Questions | Answered with findings | Answered NOT FOUND | Finding records | NOT FOUND records |
|-------|-----------|------------------------|--------------------|-----------------|-------------------|
| S01 | 5 | 4 (S01.1, S01.2, S01.4, S01.5) | 1 (S01.3) | 8 | 1 |
| S02 | 3 | 0 | 3 | 0 | 3 |
| S03 | 5 | 0 | 5 | 0 | 5 |
| S04 | 4 | 0 | 4 | 0 | 4 |
| S05 | 4 | 4 | 0 | 7 | 0 |
| S06 | 3 | 3 | 0 | 3 | 0 |
| Total | 24 | 11 | 13 | 18 | 13 |

- Questions answered with findings: 11
- Questions answered NOT FOUND: 13
- Total: 24
