# R5 history: /home/user/awdeo

- Repo: /home/user/awdeo (remote tunlezah/awdeo, /home/user/audio/00-manifest.md:15)
- HEAD during this audit: e556801cdd966f7bb86b8e7d2400718d38e8866c (git rev-parse HEAD), the same value as the manifest (/home/user/audio/00-manifest.md:26)
- Default branch as recorded in the manifest: `awdeo_x86_kube` (/home/user/audio/00-manifest.md:26, 32)
- Read-only commands used: `git log --all` (with `--date`, `--pretty`, `--stat`, `--name-status`, `--name-only`, `--grep`, `--diff-filter=D`, `-g`), `git log awdeo_x86_kube -L<range>:<path>`, `git show <commit>`, `git show <commit>:<path>`, `git grep` (working tree and named commits), `git branch -a`, `git branch -a -v`, `git branch -a --no-merged`, `git stash list`, `git tag`, `git status --porcelain`, `git rev-parse`, `git shortlog -sn --all`, `git count-objects -v`, `git rev-list --all --objects`, `git ls-files`, `git remote -v`. Nothing was checked out, built or run.
- Subject of the repo: the project names itself "Cast2Air" and describes a Chromecast to AirPlay bridge (README.md:1, package.json:2-4). No commit in the history contains a Bluetooth term (`git grep -i -l -E 'bluetooth|bluez|a2dp|bluealsa|pipewire|pulseaudio|avrcp'` over commits e8801c3, a744781, 2db4cb5, e556801: zero hits; `git log --all -i -E --grep='bluetooth|a2dp|bluez'`: zero hits).

Grading used in this file:

- A commit-message line that reports a named check ("TypeScript compilation", "x86 build successful") is recorded as STATED evidence of that check only.
- Commit-message lines under headings such as "IMPACT" or "PRODUCTION READINESS", and lines of the form "Fix: Prevents ...", are quoted as claims. No test, log or measurement accompanies them in the repo (git ls-files shows no test, log or results file), so they are not graded as evidence of a runtime result. Outcomes resting only on such lines are UNVERIFIED and the claim is quoted in the record.
- An outcome of FAILED is used only where the author's own comment or log text states that the function does not perform its named action and the code agrees.

---

## H1 Timeline

- First commit: e8801c3, 2025-11-18 07:29:52 +0000, "Add Cast2Air: Chromecast to AirPlay bridge" (git log --all --date=iso). It has no parent (git log --all --pretty='%h %p').
- Last commit: e556801, 2025-11-21 08:48:35 +0000, "Fix 12 critical reliability issues for production readiness" (git log --all --date=iso).
- Commit count: 4, reachable from all refs, in one linear chain e8801c3 → a744781 → 2db4cb5 → e556801 (git log --all --pretty='%h %p'). The count agrees with /home/user/audio/00-manifest.md:35.
- Author name on all four commits: "Claude" (git shortlog -sn --all prints `4 Claude`).
- Commits per day (git log --all --date=short):

| Date | Commits | Hashes |
|------|---------|--------|
| 2025-11-18 | 1 | e8801c3 |
| 2025-11-19 | 1 | a744781 |
| 2025-11-20 | 0 | none |
| 2025-11-21 | 2 | 2db4cb5 (08:36:36), e556801 (08:48:35) |

- Intervals between consecutive commits (computed from the author dates above): e8801c3 to a744781, 23 h 25 min 3 s; a744781 to 2db4cb5, 2 days 1 h 41 min 41 s; 2db4cb5 to e556801, 11 min 59 s.
- Whole span from first to last commit: 3 days 1 h 18 min 43 s.
- Inactivity after the last commit: no commit between 2025-11-21 08:48:35 +0000 and the manifest snapshot at 2026-11-17 07:22 UTC (/home/user/audio/00-manifest.md:18), which is 360 days 22 h 33 min.
- Commit kinds by subject: 1 beginning "Add", 3 beginning "Fix" (git log --all --pretty='%h %s'; `git log --all -i --grep='fix'` lists a744781, 2db4cb5, e556801).
- Files: 20 distinct paths appear in the history, all added in e8801c3; later commits only modify (git log --all --name-status -M shows A for 20 paths in e8801c3 and M elsewhere). `git log --all --diff-filter=D --name-only` prints nothing.
- Size of each commit (git log --all --stat): e8801c3, 20 files, 3591 insertions; a744781, 8 files, 517 insertions and 65 deletions; 2db4cb5, 4 files, 240 insertions and 66 deletions; e556801, 7 files, 409 insertions and 27 deletions.
- Files changed most often (git log --all --name-only, counted): src/chromecast/receiver.ts 4; src/bridge.ts 4; src/index.ts 3; src/audio/pipeline.ts 3; src/airplay/sender.ts 3; package.json 3; package-lock.json 3; src/utils/logger.ts 2; src/config.ts 2; src/airplay/discovery.ts 2; the other ten paths 1 each.
- Clone-side reflog: every entry is dated 2026-11-17 07:19:44 +0000 and records the clone itself, including `fetch --no-progress --depth 50 origin awdeo_x86_kube` (git log -g --all). `git rev-parse --is-shallow-repository` prints `false`.

---

## H2 Approach changes

No library was removed and no file was deleted or replaced in any commit (git log --all --name-status; package.json at e8801c3:25-33 against package.json:25-35 shows additions only). The records below are the points where existing code was taken out and a different mechanism put in its place. Three further changes of mechanism are recorded under H7 because they are delays or retries: R5-H7-01 (startup wait), R5-H7-02 (discovery reconnect), R5-H7-07 (shutdown sequence).

### R5-H2-01  Per-device consumer streams replaced one shared source stream
- Question: H2, relates to S08.4
- What was attempted: in e8801c3 the bridge handed one `Readable` to `startStreaming`, which stored it as `currentStream` and attached one `data` listener per device socket to that same stream (e8801c3:src/bridge.ts:90-91; e8801c3:src/airplay/sender.ts:16, 93-105, 144-149). Commit a744781 added the class `AudioBroadcaster`, which writes each source chunk to one `PassThrough` per consumer, and changed `startStreaming` to take a factory returning one consumer per device (src/audio/pipeline.ts:15-162; src/airplay/sender.ts:109-124; src/bridge.ts:162-168). The same commit removed the `end` and `error` handlers on the shared stream that had called `stopStreaming()` (e8801c3:src/airplay/sender.ts:107-115; commit a744781, src/airplay/sender.ts). Stated reason: "FIX #1: Implement AudioBroadcaster pattern to prevent stream reuse across multiple AirPlay devices" (commit a744781). The next commit states a defect in this version: "Fix: Prevents 3 leaked listeners per streaming session" (commit 2db4cb5), recorded in R5-H2-04.
- Presence: CURRENT (src/audio/pipeline.ts:15-162; src/bridge.ts:39, 163-168). The replaced approach is REMOVED by commit a744781 (src/airplay/sender.ts, src/bridge.ts).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The only check the commit states is compilation: "All fixes tested and verified with successful TypeScript compilation." (commit a744781).
- Why it turned out that way: UNKNOWN. No run result is recorded.
- Depends on: Node.js `stream` and `events` modules (src/audio/pipeline.ts:1, 3); more than one device in the sender's connection table (src/airplay/sender.ts:122-134); the pass-through pipeline output being used as the broadcaster source (src/bridge.ts:156-163).
- Open questions: After a744781, which code path closes the device sockets or sets `isStreaming` to false when the source stream ends without a STOP command? The broadcaster emits `ended` (src/audio/pipeline.ts:75) and a search of src for `.on('ended'` gives zero hits. Was output to two or more devices ever run?
- Confidence: MEDIUM. INFERRED from clear code and diffs; the reason is STATED in the commit message.

### R5-H2-02  `pipe()` to the device socket replaced a per-chunk `socket.write` listener
- Question: H2, relates to S08.2
- What was attempted: in e8801c3 each device socket received audio through a `data` listener that called `socket.write(chunk)` (e8801c3:src/airplay/sender.ts:144-149). Commit a744781 replaced the listener with `audioStream.pipe(socket, { end: false })` and stored the per-device stream on the connection entry (src/airplay/sender.ts:12, 124, 209-210). In-code comment: "FIX #5: Pipe stream properly with cleanup" (src/airplay/sender.ts:209). Stated reason: "FIX #5: Implement proper stream cleanup and resource management" (commit a744781).
- Presence: CURRENT (src/airplay/sender.ts:209-210). The replaced listener is REMOVED by commit a744781 (src/airplay/sender.ts).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Compilation is the only check stated (commit a744781).
- Why it turned out that way: UNKNOWN.
- Depends on: a TCP socket to the device host and port, default port 5000 (src/airplay/sender.ts:183-187); the RTSP step before the pipe being a placeholder (R5-H5-05); the bytes piped being the unmodified request body (R5-H5-04).
- Open questions: What does a RAOP device do with these bytes, given that no RTSP exchange precedes the pipe (src/airplay/sender.ts:264-282)?
- Confidence: MEDIUM. INFERRED from clear code and the diff of commit a744781.

### R5-H2-03  Parallel device connection with removal of failed devices replaced sequential connection
- Question: H2, relates to S08.3
- What was attempted: in e8801c3 `startStreaming` awaited `connectAndStream` for each device in turn, and a rejection went to the method's `catch` and was rethrown (e8801c3:src/airplay/sender.ts:102-105, 117-120). Commit a744781 starts all connections, converts each rejection to a `{ success: false }` result, waits with `Promise.all`, removes each failed device from the connection table, and throws only when every device failed (src/airplay/sender.ts:118-159). Stated reason: "FIX #6: Add graceful error handling for device connection failures" (commit a744781).
- Presence: CURRENT (src/airplay/sender.ts:118-159). The sequential loop is REMOVED by commit a744781 (src/airplay/sender.ts).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Compilation is the only check stated (commit a744781).
- Why it turned out that way: UNKNOWN.
- Depends on: the 10-second connection timeout added in the same commit (R5-H7-03); `removeDevice` deleting the entry (src/airplay/sender.ts:53-80).
- Open questions: After a failed device is removed at src/airplay/sender.ts:146-149, what re-adds it? The only caller chain of `addDevice` starts at the `connect` prompt command (src/index.ts:111-117, 200-208; src/bridge.ts:246-254).
- Confidence: MEDIUM. INFERRED from clear code and the diff of commit a744781.

### R5-H2-04  Stored listener references with explicit removal replaced inline anonymous listeners
- Question: H2, relates to S10.2
- What was attempted: listeners were first registered as inline anonymous functions (e8801c3:src/bridge.ts:33-72; a744781:src/audio/pipeline.ts:35-64, 71-76; e8801c3:src/airplay/discovery.ts:31-48). Commit 2db4cb5 changed three files to keep each listener in a field and remove it during cleanup: src/audio/pipeline.ts:21-24, 57-92, 130-148, 168, 199-205, 220-224; src/bridge.ts:18-25, 44-127, 232-233; src/airplay/sender.ts:13, 38, 59-63, 212-220, 242-251, 288-292. Commit e556801 did the same for discovery (src/airplay/discovery.ts:22-24, 63-87, 146-176). Stated reasons: "Fix: Prevents 3 leaked listeners per streaming session" and "Fix: Prevents handler accumulation on restart cycles" (commit 2db4cb5); "Fix: Prevents listener accumulation on restarts" (commit e556801).
- Presence: CURRENT (paths and lines above). The inline registrations are REMOVED by commits 2db4cb5 and e556801.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Claim on record, with no test described: "Memory leak rate reduced from ~50-100MB/day to near zero" (commit 2db4cb5, under its heading "IMPACT:"). Commit e556801 lists "No memory leaks from event listeners" under its heading "PRODUCTION READINESS" and states as tested only: "Tested: x86 build successful, all TypeScript checks passing".
- Why it turned out that way: UNKNOWN.
- Depends on: one bridge object per process, constructed at src/index.ts:55, with `setupEventHandlers()` called only from its constructor (src/bridge.ts:41); the listeners in `AudioBroadcaster` having been added two days earlier by commit a744781 (a744781:src/audio/pipeline.ts:35-64).
- Open questions: How were the figures "~50-100MB/day" obtained? No measurement file is tracked (git ls-files) and the first commit is dated three days before commit 2db4cb5. Under which run sequence do the "restart cycles" named in commit 2db4cb5 occur, given one bridge per process?
- Confidence: MEDIUM. INFERRED from clear code and diffs; the result claim is STATED without a described test.

### R5-H2-05  Daily-rotating log file transport replaced a single log file
- Question: H2, relates to S10.5
- What was attempted: e8801c3 logged to the console and to one file `cast2air.log` through `winston.transports.File` (e8801c3:src/utils/logger.ts:16-19). Commit e556801 replaced the file transport with `winston-daily-rotate-file`: filename `cast2air-%DATE%.log`, 20 MB maximum size, 14 days kept, zipped archives (src/utils/logger.ts:1-17, 46). The dependency was added at package.json:34 as `^5.0.0`, lockfile 5.0.0 (package-lock.json:1775-1776); the commit message names "winston-daily-rotate-file@^4.x" (commit e556801). The same commit moved `colorize()` from the logger-level format to the console transport (e8801c3:src/utils/logger.ts:7; src/utils/logger.ts:33-45); the comment above that transport reads "Console without colors for better log aggregation" (src/utils/logger.ts:32) and the transport's format includes `winston.format.colorize()` (src/utils/logger.ts:36). Stated reason: "Fix: Prevents guaranteed crash after 30 days in production" (commit e556801).
- Presence: CURRENT (src/utils/logger.ts:1-17, 31-47). The single-file transport is REMOVED by commit e556801 (src/utils/logger.ts).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Stated check: "Tested: x86 build successful, all TypeScript checks passing" (commit e556801). Claims on record in the same message: "MTBF: 3-7 days (disk full crash guaranteed)" before and "MTBF: Indefinite (log rotation prevents crash)" after.
- Why it turned out that way: UNKNOWN.
- Depends on: `LOG_LEVEL` (src/utils/logger.ts:11, 20; Dockerfile:36); the process working directory, because the filename is relative (src/utils/logger.ts:6; Dockerfile:13); the `./logs:/app/logs` volume (docker-compose.yml:11); `*.log` being git-ignored (.gitignore:3).
- Open questions: Which of the two figures in commit e556801 applies, "30 days" or "3-7 days"? Both exceed the 3 days between the first and last commit, so on what run were they based? Into which directory does the relative filename resolve under `WORKDIR /app` (Dockerfile:13), given that the mounted volume is `/app/logs`? Why does the message name `^4.x` while package.json:34 has `^5.0.0`?
- Confidence: MEDIUM. INFERRED from clear code and the diff of commit e556801.

### R5-H2-06  Bridge-backed health endpoints replaced a static `/health` response
- Question: H2, relates to S10.5
- What was attempted: e8801c3 answered `GET /health` with a fixed `{ status: 'ok', name }` (e8801c3:src/chromecast/receiver.ts:35-38). Commit e556801 made `/health` call a callback registered by the bridge and return 503 when the result's `healthy` field is false, and added `/healthz` and `/ready` (src/chromecast/receiver.ts:21, 36-39, 66-97; src/bridge.ts:200-201, 282-365). In `getHealthStatus()` the `healthy` flag is set false only inside four `catch` blocks; zero devices or an unhealthy discovery object set `status` to `degraded` and leave `healthy` true (src/bridge.ts:293-356). Stated reason: "Fix: Allows load balancers to detect failures" (commit e556801).
- Presence: CURRENT (src/chromecast/receiver.ts:66-97; src/bridge.ts:282-365). The static response is REMOVED by commit e556801 (src/chromecast/receiver.ts).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Stated check is the build only (commit e556801).
- Why it turned out that way: UNKNOWN.
- Depends on: the container health check, unchanged since e8801c3, which requests `/health` on port 8009 and treats status 200 as passing (Dockerfile:39-40; `git log --all -- Dockerfile` lists e8801c3 only); a load balancer, which is named in the commit message and in a comment (src/chromecast/receiver.ts:260) and is not defined in any tracked file (git ls-files).
- Open questions: Under which conditions does any of the four `try` blocks at src/bridge.ts:298-352 throw? Each wraps getter calls (src/chromecast/receiver.ts:307-309; src/airplay/discovery.ts:191-197; src/airplay/sender.ts:314-316; src/audio/pipeline.ts:125-127, 249-251).
- Confidence: MEDIUM. INFERRED from clear code and the diff of commit e556801.

---

## H3 Reverts and repeated fixes

Reverts: `git log --all -i --grep='revert'` prints nothing. No commit removes the whole of an earlier commit's change (read of `git show` for a744781, 2db4cb5 and e556801). Three of the four commits are fix-type commits by subject (a744781, 2db4cb5, e556801). Two files were modified by all three.

### R5-H3-01  src/bridge.ts modified by all three fix-type commits
- Question: H3, relates to S10.2
- What was attempted: commit a744781 (36 lines in `git show --stat`) added URL masking for a log line and wired in the broadcaster (src/bridge.ts:47-48, 129-144, 162-168, 190). Commit 2db4cb5 (87 lines) moved the seven event handlers into fields and added `cleanupEventHandlers()` (src/bridge.ts:18-25, 44-127, 232-233). Commit e556801 (100 lines) registered the health callback, changed the startup wait from 3000 ms to 2000 ms and added `getHealthStatus()` (src/bridge.ts:200-201, 209-219, 282-365). Each commit names a separate review as its source: "security and reliability vulnerabilities" (commit a744781 subject), "comprehensive analysis" (commit 2db4cb5), "comprehensive reliability engineering analysis" (commit e556801).
- Presence: CURRENT (src/bridge.ts, 366 lines at HEAD).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Checks stated are compilation (commit a744781) and build (commit e556801); commit 2db4cb5 states no check.
- Why it turned out that way: UNKNOWN.
- Depends on: the three reviews named in the messages; no review document is tracked (git ls-files shows no notes, report or docs file).
- Open questions: Where are the three analyses the commit messages refer to? Was the file exercised at runtime between the commits?
- Confidence: HIGH. The three modifications are read directly from `git show --stat` and the diffs.

### R5-H3-02  src/chromecast/receiver.ts modified by all three fix-type commits
- Question: H3, relates to S12.1
- What was attempted: commit a744781 (76 lines) added XML escaping of the receiver name, a 1 MB JSON body limit, a rate limit of 100 requests per minute, request validation, a default content type and a log line about authentication (src/chromecast/receiver.ts:28-29, 41-49, 52-64, 113-123, 135-136, 149-150, 213-216, 288, 291). Commit 2db4cb5 (17 lines) added a `close` handler to the `/stream` route that destroys the stream when the request closes before its `end` event; stated reason "Fix: Prevents orphaned streams on network interruptions" (src/chromecast/receiver.ts:157-164, 173, 181-186). Commit e556801 (56 lines) added the health callback, three health routes, server timeouts and limits, and a `clientError` handler (src/chromecast/receiver.ts:21, 31, 36-39, 66-97, 258-265, 272-278). Within the file, the `/stream` handler (src/chromecast/receiver.ts:146-189) was changed by a744781 and 2db4cb5, and `start()` (src/chromecast/receiver.ts:244-283) by a744781 and e556801.
- Presence: CURRENT (src/chromecast/receiver.ts, 310 lines at HEAD).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Checks stated are compilation (commit a744781) and build (commit e556801).
- Why it turned out that way: UNKNOWN.
- Depends on: express 4.21.2 and express-rate-limit 8.2.1 in the lockfile (package-lock.json:766-767, 812-813); a client that posts audio to `/stream`, which is not in the repo (/home/user/audio/repos/R5/01-map.md:135).
- Open questions: Which client was used to send requests to this server while the three batches were written?
- Confidence: HIGH. The three modifications are read directly from `git show --stat` and the diffs.

### R5-H3-03  Every commit after the first is a batch of fixes, and each batch describes defects in the state left by the commit before it
- Question: H3, relates to S11.4
- What was attempted: the three subjects are "Fix all 16 security and reliability vulnerabilities (ARM version)" (a744781), "Fix 9 critical memory leaks and race conditions" (2db4cb5) and "Fix 12 critical reliability issues for production readiness" (e556801). The body of a744781 lists FIX #1 to FIX #15, fifteen items; no "FIX #16" exists in any message or in src (`git grep -n -E 'FIX #1[0-9]' -- src`; `git log --all --pretty=%B`). The body of 2db4cb5 lists items 1 to 9. The body of e556801 lists items 1 to 7. Commit 2db4cb5 fixes listener leaks in `AudioBroadcaster`, a class that commit a744781 had added as its FIX #1 (a744781:src/audio/pipeline.ts:15-108). Commit 2db4cb5 ends its impact list with "Production-ready for 24/7 operation"; commit e556801, made 11 min 59 s later, opens with "P0 CRITICAL FIXES (Production Blockers)" and lists "MTBF: 3-7 days (disk full crash guaranteed)" as the state before it.
- Presence: CURRENT. All three commits are ancestors of the default branch head (git log --all --pretty='%h %p').
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Stated checks: "All fixes tested and verified with successful TypeScript compilation." (commit a744781) and "Tested: x86 build successful, all TypeScript checks passing" (commit e556801).
- Why it turned out that way: UNKNOWN.
- Depends on: the placeholder functions on the audio path, whose bodies are the same at HEAD as in the first commit (`git log awdeo_x86_kube -L` for src/airplay/sender.ts:98-107 and 264-282 and src/audio/pipeline.ts:188-197 lists e8801c3 only); see R5-H5-04, R5-H5-05, R5-H5-06.
- Open questions: Which of the listed items were exercised at runtime? What does "(ARM version)" in the subject of a744781 refer to, given that `git grep -n -i -E '\barm|x86|aarch|amd64|raspberry|kube'` over tracked files other than package-lock.json gives zero hits and commit e556801 reports an x86 build? Why do the counts in two subjects (16 and 12) differ from the numbered items in the bodies (15 and 7)?
- Confidence: HIGH. Read directly from commit messages and diffs.

Areas changed by two fix-type commits, below the threshold in H3:

- Stream cleanup in the sender: added by a744781 as FIX #5 (a744781:src/airplay/sender.ts:222-225) and rewritten by 2db4cb5 (src/airplay/sender.ts:242-251).
- Signal handling and shutdown: added by a744781 as FIX #12 (a744781:src/index.ts:270-314) and reworked by e556801 (src/index.ts:270-346).
- `AudioBroadcaster`: added by a744781 (a744781:src/audio/pipeline.ts:15-108) and reworked by 2db4cb5 (src/audio/pipeline.ts:15-162).
- package.json and package-lock.json: a744781 and e556801 (git log --all --name-only).

---

## H4 Unmerged work

### R5-H4-01  NOT FOUND
- Question: H4
- Searched: `git branch -a --no-merged` (prints nothing); `git branch -a -v` (four refs, all at e556801: `awdeo_x86_kube`, `claude/inspiring-galileo-a9dlia` and their two `remotes/origin` counterparts); `git stash list` (prints nothing); `git tag` (prints nothing); `git count-objects -v` (66 loose objects, none packed) against `git rev-list --all --objects | wc -l` (66), so no object exists outside the four reachable commits; `git log -g --all` (clone entries only).
- Nearest related material: branch `claude/inspiring-galileo-a9dlia`, at the same commit as the default branch and holding no work of its own (/home/user/audio/00-manifest.md:33). The words "(ARM version)" in the subject of commit a744781 and the branch name `awdeo_x86_kube` name two variants; one branch with one line of history is present. The reflog records a fetch of the single named branch `awdeo_x86_kube` (git log -g --all), so branches on the remote that were not fetched are not visible in this clone.

---

## H5 Dead and disabled code

Commented-out code: none found. `git grep -n -E '^\s*//\s*(const|let|var|if|for|while|return|await|this\.|import|export|function|[a-zA-Z_.]+\(.*\);?\s*$)' -- src` gives zero hits; the only block comment in src is the JSDoc at src/audio/pipeline.ts:11-14; every `#` line in Dockerfile is a section label (Dockerfile:3, 12, 15, 19, 22, 25, 28, 31, 34, 38, 42); docker-compose.yml and .github/workflows/ci.yml have no comment lines. The diffs of a744781, 2db4cb5 and e556801, read in full with `git show`, add or remove no commented-out block.

### R5-H5-01  Unreferenced `AudioBuffer` class
- Question: H5, relates to S05.4
- What was attempted: a `Transform` that collects chunks and emits blocks of 8192 bytes, with a 1 MB cap that returns an error from `_transform` (src/audio/pipeline.ts:254-303; cap at 265, 269-278). The class has no reference outside its own definition (`git grep -n AudioBuffer -- src` gives src/audio/pipeline.ts:254 only). It was already unreferenced in the first commit (`git grep -n AudioBuffer e8801c3 -- src` gives e8801c3:src/audio/pipeline.ts:76 only). Commit a744781 added the cap and listed it under "CRITICAL Fixes": "FIX #11: Add bounded buffer (1MB limit) to prevent unbounded memory growth".
- Presence: DISABLED. Unreachable: no code constructs it (src/audio/pipeline.ts:254).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: nothing on the run path; the audio path goes from the request stream through `transcode` to the broadcaster without it (src/bridge.ts:156-168).
- Open questions: Between which two stages was the buffer meant to sit? No comment or document says.
- Confidence: MEDIUM. INFERRED from clear code: one search hit, at the definition.

### R5-H5-02  Second `AudioBroadcaster` inside `AudioPipeline`, never used for audio
- Question: H5, relates to S07.1
- What was attempted: commit a744781 gave `AudioPipeline` its own broadcaster field and a getter (src/audio/pipeline.ts:167, 173, 214-216). `getBroadcaster()` has no caller (`git grep -n getBroadcaster -- src` gives src/audio/pipeline.ts:214 only). The only use of the field is `this.broadcaster.cleanup()` in `stop()` (src/audio/pipeline.ts:241). The bridge constructs a separate instance and uses that one for the audio path (src/bridge.ts:39, 163, 167, 190).
- Presence: DISABLED. The instance is constructed and never given a source or a consumer (src/audio/pipeline.ts:173, 214-216).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: the bridge-level instance at src/bridge.ts:39 carrying the audio.
- Open questions: Was the getter meant to supply the instance the bridge now creates for itself?
- Confidence: MEDIUM. INFERRED from clear code.

### R5-H5-03  Declared npm dependencies that no source file imports
- Question: H5, relates to S01.5
- What was attempted: `castv2` `^0.1.10`, `castv2-client` `^1.2.0` and `node-ssdp` `^4.0.0` are declared (package.json:27-28, 32) and were declared in the first commit (e8801c3:package.json:27-28, 31). No `import` or `require(` line in src names any of them (`git grep -n -E "^import|require\(" -- src`, 34 lines read). `node-ssdp` is named in a comment: "For production, consider using node-ssdp or similar" (src/chromecast/receiver.ts:287). README.md:234 credits castv2.
- Presence: DISABLED. Declared and installed, not imported (package.json:27-28, 32).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: the receiver being an express HTTP server (src/chromecast/receiver.ts:1, 25, 247), which uses none of the three.
- Open questions: Was a version of the receiver built on castv2 ever written? None is in the history (20 paths, all from e8801c3).
- Confidence: MEDIUM. INFERRED from clear code: the import list is complete and short.

### R5-H5-04  Transcoding step is a pass-through; FFmpeg is installed and never invoked
- Question: H5, relates to S07.1
- What was attempted: `AudioPipeline.transcode()` pipes its input into a `PassThrough` and returns it (src/audio/pipeline.ts:177-212, pipe at 197). Comments list what a transcoding version would do (src/audio/pipeline.ts:188-192). `ffmpeg` is installed by Dockerfile:5 and .github/workflows/ci.yml:29; src contains no `spawn`, `child_process` or `exec(` (`git grep -n -E 'spawn|child_process|exec\(' -- src`: zero hits). README.md:14 lists "Real-time Transcoding: Automatic audio format conversion using FFmpeg". The lines are unchanged since the first commit (`git log awdeo_x86_kube -L188,197:src/audio/pipeline.ts` lists e8801c3 only).
- Presence: CURRENT. The pass-through is on the run path (src/bridge.ts:156-160).
- Outcome: FAILED. No transcoding takes place.
- Outcome evidence: STATED. Log text: "Note: Audio transcoding not implemented. Audio may not play correctly on some devices." (src/audio/pipeline.ts:195). Commit e8801c3: "Full protocol implementation and audio transcoding (using FFmpeg) can be added in future iterations." The code agrees (src/audio/pipeline.ts:197).
- Why it turned out that way: STATED. Left out of the first version: "For the initial version, we'll just pass through the audio" (src/audio/pipeline.ts:188). No later commit changed it.
- Depends on: the content type and bytes sent by whatever client posts to `/stream` (src/chromecast/receiver.ts:146-189); the `audio` settings being passed in and only logged (src/bridge.ts:159; src/audio/pipeline.ts:182).
- Open questions: NONE
- Confidence: HIGH. STATED evidence that the code agrees with.

### R5-H5-05  RTSP session set-up is a placeholder that logs and returns
- Question: H5, relates to S08.2
- What was attempted: `initializeRTSPSession()` is called after the TCP connection opens (src/airplay/sender.ts:207). Its body is one debug log call and `return Promise.resolve()` (src/airplay/sender.ts:264-282). Comments list five steps of "a full implementation" (src/airplay/sender.ts:266-271). The random `sessionId` made at src/airplay/sender.ts:30 is used only in that log call (src/airplay/sender.ts:275). The comment "Send RTSP TEARDOWN command" is followed by `socket.end()` alone (src/airplay/sender.ts:301-302). The function is unchanged since the first commit (`git log awdeo_x86_kube -L264,282:src/airplay/sender.ts` lists e8801c3 only; e8801c3:src/airplay/sender.ts:181-199). The same first commit lists among its features "AirPlay sender with pure Node.js RTSP implementation" (commit e8801c3).
- Presence: CURRENT. Called on the run path (src/airplay/sender.ts:207).
- Outcome: FAILED. No RTSP request is sent.
- Outcome evidence: STATED. "For this proof of concept, we'll just log the initialization" (src/airplay/sender.ts:278). Commit e8801c3: "Note: This is an initial implementation with simplified RTSP/RAOP protocol handling." The code agrees (src/airplay/sender.ts:273-281).
- Why it turned out that way: STATED. Not implemented: "A full implementation would require proper RTSP/RTP/RAOP protocol handling" (src/airplay/sender.ts:279).
- Depends on: the raw pipe that follows it (R5-H2-02).
- Open questions: NONE
- Confidence: HIGH. STATED evidence that the code agrees with.

### R5-H5-06  Volume command to the device is a placeholder that logs
- Question: H5, relates to S06.2
- What was attempted: `setVolume()` clamps the value to 0-100 and calls `sendVolumeCommand()` for each entry with `connected` true and a socket (src/airplay/sender.ts:82-96). `sendVolumeCommand()` converts the value to a figure between -30 and 0 and writes one debug log line (src/airplay/sender.ts:98-107). Unchanged since the first commit (`git log awdeo_x86_kube -L98,107:src/airplay/sender.ts` lists e8801c3 only; e8801c3:src/airplay/sender.ts:82-91).
- Presence: CURRENT. Called from the `volume` prompt command (src/index.ts:131-137, 238-251; src/bridge.ts:269-271).
- Outcome: FAILED. Nothing is sent to the device.
- Outcome evidence: STATED. "Volume command would be sent via RTSP here" and "For simplicity, we'll just log it" (src/airplay/sender.ts:105-106). The code agrees (src/airplay/sender.ts:99-104).
- Why it turned out that way: STATED. It depends on RTSP, which is not implemented (src/airplay/sender.ts:105; R5-H5-05).
- Depends on: R5-H5-05.
- Open questions: NONE
- Confidence: HIGH. STATED evidence that the code agrees with.

### R5-H5-07  SSDP advertisement function contains log calls only
- Question: H5, relates to S03.1
- What was attempted: `startSSDPAdvertisement()` is called from the listen callback (src/chromecast/receiver.ts:252). Its body is three log calls, the first reading "SSDP advertisement started (simplified mode)" (src/chromecast/receiver.ts:285-292). Comment: "Note: Full SSDP/DIAL implementation would require additional libraries" (src/chromecast/receiver.ts:286). INFERRED: no advertisement is sent by this code, because the function opens no socket and src has no UDP or second listener (`git grep -n -E "dgram|createSocket|createServer|\.listen\(" -- src` gives src/chromecast/receiver.ts:247 only). A device-description route exists (src/chromecast/receiver.ts:100-103, 192-208). Commit a744781 added the third log line (`git log awdeo_x86_kube -L285,292:src/chromecast/receiver.ts` lists a744781 and e8801c3).
- Presence: CURRENT. Called on the run path (src/chromecast/receiver.ts:252).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: `node-ssdp`, declared and not imported (R5-H5-03); README.md:212, "Full DIAL/SSDP support may require additional work".
- Open questions: Does any casting app list the receiver without an advertisement? The repo names none as tested (CONTRIBUTING.md:126-134).
- Confidence: MEDIUM. INFERRED from clear code.

### R5-H5-08  `airplay.targetDevices` setting is defined and never read for connection
- Question: H5, relates to S10.4
- What was attempted: the key is in the config type, the defaults, the validator and the example file (src/config.ts:11, 27, 129-131; config.example.json:7). README.md:136 describes it as "List of AirPlay devices to auto-connect to". No file other than src/config.ts mentions it (`git grep -n targetDevices -- src`). It was present, and equally unread, in the first commit (e8801c3:src/config.ts:11, 27).
- Presence: DISABLED. The setting exists and no code acts on it (src/config.ts:129-131).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: devices being added only by the `connect` prompt command (src/index.ts:111-117).
- Open questions: Was automatic connection at start ever written? It is not in any commit.
- Confidence: MEDIUM. INFERRED from clear code.

### R5-H5-09  Config save and update methods with no caller
- Question: H5, relates to S10.4
- What was attempted: `ConfigManager.saveConfig()` writes the configuration to the config path and `updateConfig()` validates, merges and saves (src/config.ts:169-176, 182-186). Neither is called from outside the class (`git grep -n -E 'updateConfig|saveConfig' -- src` gives src/config.ts:169, 182, 185 only). Both were present and uncalled in the first commit (e8801c3:src/config.ts:60, 73, 75). README.md:16 lists "Configuration: Persistent configuration with JSON file support".
- Presence: DISABLED. Unreachable (src/config.ts:169, 182).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: `--name` and `--port` overrides being applied to the in-memory object only (src/index.ts:46-53).
- Open questions: Which command was meant to save changes? The prompt has none (src/index.ts:102-155).
- Confidence: MEDIUM. INFERRED from clear code.

### R5-H5-10  Events emitted with no registered listener
- Question: H5, relates to S08.3
- What was attempted: discovery emits `discoveryFailed` after ten failed reconnection attempts (src/airplay/discovery.ts:117-121, added by commit e556801). `AudioBroadcaster` emits `ended` and `error` (src/audio/pipeline.ts:75, 86, added by commit a744781). No listener is registered for any of the three: a search of src for `.on('discoveryFailed'` and `.on('ended'` gives zero hits, and the bridge's uses of its broadcaster are `setSource`, `createConsumer`, `cleanup` and `getConsumerCount` only (src/bridge.ts:163, 167, 190, 347-348).
- Presence: CURRENT. The `emit` calls are reachable (src/airplay/discovery.ts:120; src/audio/pipeline.ts:75, 86).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: Node.js `EventEmitter` behaviour for an event with no listener.
- Open questions: What does Node.js do when `error` is emitted at src/audio/pipeline.ts:86 with no listener on the broadcaster? Apart from the log line at src/airplay/discovery.ts:118, what tells the operator that discovery has stopped?
- Confidence: MEDIUM. INFERRED from clear code.

### R5-H5-11  Leftover imports and an uncalled method from commit a744781
- Question: H5, relates to NONE
- What was attempted: commit a744781 added `pipeline` to the `stream` import (src/audio/pipeline.ts:1); src has no `pipeline(` call (`git grep -n -E 'pipeline\(' -- src`: zero hits). The same commit changed the sender's stream parameters from `Readable` to `PassThrough`, leaving `Readable` imported and unused (src/airplay/sender.ts:3; `git grep -n Readable -- src/airplay/sender.ts` gives line 3 only). `AudioBroadcaster.removeConsumer()` (src/audio/pipeline.ts:116-123) has no caller; the hits at src/audio/pipeline.ts:103, 110, 111 are a local function of the same name inside `createConsumer()`.
- Presence: DISABLED. Unreferenced (src/audio/pipeline.ts:1, 116-123; src/airplay/sender.ts:3).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: nothing on the run path.
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from clear code.

### R5-H5-12  PLAY handler logs only; PAUSE closes the device sockets
- Question: H5, relates to S06.1
- What was attempted: the bridge's handler for `play` is one log call (src/bridge.ts:60-62). The handler for `pause` calls `pauseStreaming()`, which calls `airplaySender.stopStreaming()` and sets `isStreaming` false (src/bridge.ts:64-67, 178-184); `stopStreaming()` destroys each device stream and ends each socket (src/airplay/sender.ts:284-312). Streaming starts only from the `audioStream` event (src/bridge.ts:55-58, 146-176). The behaviour is the same as in the first commit; commit 2db4cb5 changed only how the handlers are registered (e8801c3:src/bridge.ts:47-54, 100-106; `git log awdeo_x86_kube -L178,184:src/bridge.ts` lists e8801c3 only).
- Presence: CURRENT (src/bridge.ts:60-67, 88-89).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: a client sending PLAY and PAUSE to `POST /receiver/:sessionId` (src/chromecast/receiver.ts:126-131).
- Open questions: After PAUSE and then PLAY, what restarts output to the devices?
- Confidence: MEDIUM. INFERRED from clear code.

### R5-H5-13  LOAD command stores a media URL that nothing fetches
- Question: H5, relates to S05.1
- What was attempted: `handleLoad()` stores `media.contentId` as `mediaUrl` and emits `mediaLoad` (src/chromecast/receiver.ts:210-226). The bridge's handler is one log call (src/bridge.ts:46-53). No code retrieves the URL (/home/user/audio/repos/R5/01-map.md:136). The handler was a log call in the first commit as well (e8801c3:src/bridge.ts:35-40). Commit a744781 added masking of the URL in that log line (src/bridge.ts:47-48, 129-144).
- Presence: CURRENT (src/bridge.ts:46-53, 86).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: audio arriving by `POST /stream` and not by the LOAD URL (src/chromecast/receiver.ts:146-189).
- Open questions: Was the LOAD URL meant to be the audio source? No comment or commit says.
- Confidence: MEDIUM. INFERRED from clear code.

### R5-H5-14  CI formatting check gated on a configuration file that is not tracked
- Question: H5, relates to S11.1
- What was attempted: the `lint` job runs prettier only if `.prettierrc` or `.prettierrc.json` exists, prints "Prettier not configured, skipping" otherwise, and has `continue-on-error: true` (.github/workflows/ci.yml:58-80). No prettier file is tracked (`git ls-files | grep -i prettier`: zero hits) and prettier is not in package.json:25-42. CONTRIBUTING.md:151 lists "Code formatting with Prettier" among open items. The workflow file is unchanged since the first commit (`git log --all -- .github/workflows/ci.yml` lists e8801c3 only).
- Presence: DISABLED. Switched off by the condition at .github/workflows/ci.yml:75.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: the absence of a prettier configuration file.
- Open questions: NONE
- Confidence: MEDIUM. INFERRED from clear code.

### R5-H5-15  CI workflow triggers name branches that are not in the clone
- Question: H5, relates to S11.1
- What was attempted: the workflow runs on push and pull request to `main` and `develop` (.github/workflows/ci.yml:3-7). The branches in the clone are `awdeo_x86_kube` and `claude/inspiring-galileo-a9dlia` (git branch -a). INFERRED: a push to `awdeo_x86_kube` matches neither name, so the push trigger as written does not fire for the branch that holds the four commits. No CI result is tracked (git ls-files). Commit e556801 lists "All builds passing" under "PRODUCTION READINESS" and states "Tested: x86 build successful, all TypeScript checks passing".
- Presence: DISABLED for the branches in this clone (.github/workflows/ci.yml:5, 7).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: the branch names on the remote, of which only one was fetched (git log -g --all).
- Open questions: Does a `main` or `develop` branch exist on the remote? Did the workflow ever run? Where was the build named in commit e556801 run?
- Confidence: LOW. INFERRED from partial material: the remote's branch list and workflow history are not visible from the clone.

---

## H6 Author's markers

### R5-H6-01  NOT FOUND
- Question: H6
- Searched: `git grep -n -w -E 'TODO|FIXME|HACK|XXX|BUG|WORKAROUND'` over tracked files other than package-lock.json (zero hits); `git grep -n -i -E 'todo|fixme|hack|xxx|workaround|work around|work-around'` over the same files (zero hits); the same case-insensitive search at each of e8801c3, a744781, 2db4cb5 and e556801 (zero hits); `git grep -n -i -w -E 'bug|bugs'` (four hits, all prose in CONTRIBUTING.md:11, 13, 15, 62, none a code comment); `git ls-files | grep -i -E 'change|note|todo|issue|known|history|docs/'` (zero hits: no notes, changelog or known-issues file); `git log --all --name-only` (20 paths ever, none of those kinds).
- Nearest related material: three groups, tabulated below. (a) 73 comments in src beginning `FIX`, written by the three fix-type commits. (b) 12 placeholder comments and log notes in src. (c) Sections of README.md and CONTRIBUTING.md that list limits and open work.

Nearest related material (a): `FIX` comments. `git grep -n -E 'FIX' -- src` gives 73 lines. The 34 numbered ones (`FIX #n`) come from commit a744781, one of them reworded by e556801 (src/index.ts:311). Of the 39 unnumbered ones, 23 come from commit 2db4cb5 (in sender.ts, pipeline.ts, bridge.ts:18, 45, 95, 232 and receiver.ts:157, 181) and 16 from commit e556801 (counts from `git show <commit> -- src | grep '^+.*FIX'`).

| location | marker | text | related stage |
|----------|--------|------|---------------|
| src/airplay/discovery.ts:22 | FIX | "Track event handlers for cleanup" | S10.2 |
| src/airplay/discovery.ts:48 | FIX | "Schedule reconnection attempt" | S08.3 |
| src/airplay/discovery.ts:63 | FIX | "Create tracked event handlers" | S10.2 |
| src/airplay/discovery.ts:89 | FIX | "Monitor browser for errors and handle reconnection" | S08.3 |
| src/airplay/discovery.ts:101 | FIX | "Auto-recovery from discovery failures" | S08.3 |
| src/airplay/discovery.ts:147 | FIX | "Remove event listeners before destroying" | S10.2 |
| src/airplay/sender.ts:12 | FIX #5 | "Track stream for cleanup" | S08.3 |
| src/airplay/sender.ts:13 | FIX | "Track error listener" | S08.3 |
| src/airplay/sender.ts:19 | FIX #7 | "10 second timeout" | S08.3 |
| src/airplay/sender.ts:38 | FIX | "Initialize error handler tracker" | S08.3 |
| src/airplay/sender.ts:59 | FIX | "Remove error listener before destroying stream" | S08.3 |
| src/airplay/sender.ts:65 | FIX #5 | "Properly cleanup stream and socket" | S08.3 |
| src/airplay/sender.ts:109 | FIX #1 & #5 | "Use individual streams from broadcaster" | S08.4 |
| src/airplay/sender.ts:118 | FIX #6 | "Track failed connections" | S08.3 |
| src/airplay/sender.ts:136 | FIX #6 | "Wait for all connections, handle failures gracefully" | S08.3 |
| src/airplay/sender.ts:172 | FIX | "Prevent multiple promise settlements (race condition)" | S08.3 |
| src/airplay/sender.ts:182 | FIX #7 | "Add connection timeout" | S08.3 |
| src/airplay/sender.ts:189 | FIX #7 | "Handle timeout" | S08.3 |
| src/airplay/sender.ts:209 | FIX #5 | "Pipe stream properly with cleanup" | S08.2 |
| src/airplay/sender.ts:212 | FIX | "Track error handler for cleanup" | S08.3 |
| src/airplay/sender.ts:242 | FIX | "Remove error listener and cleanup stream reference" | S08.3 |
| src/airplay/sender.ts:288 | FIX | "Remove error listener before cleanup" | S08.3 |
| src/airplay/sender.ts:294 | FIX #5 | "Properly cleanup streams" | S08.3 |
| src/audio/pipeline.ts:21 | FIX | "Track bound listener functions for proper cleanup" | S07.1 |
| src/audio/pipeline.ts:36 | FIX | "Increase max listeners to prevent warnings" | S08.4 |
| src/audio/pipeline.ts:41 | FIX | "Guard against rapid successive calls" | S07.1 |
| src/audio/pipeline.ts:57 | FIX | "Create bound listener functions that can be removed" | S07.1 |
| src/audio/pipeline.ts:89 | FIX | "Attach tracked listeners" | S07.1 |
| src/audio/pipeline.ts:102 | FIX | "Handle both 'close' and 'error' events to ensure cleanup" | S08.4 |
| src/audio/pipeline.ts:130 | FIX | "Remove all event listeners from source stream" | S07.1 |
| src/audio/pipeline.ts:168 | FIX | "Track listener" | S07.1 |
| src/audio/pipeline.ts:199 | FIX | "Track the error listener for proper cleanup" | S07.1 |
| src/audio/pipeline.ts:220 | FIX | "Remove error listener before cleanup" | S07.1 |
| src/audio/pipeline.ts:265 | FIX #11 | "Limit buffer growth" | S05.4 |
| src/audio/pipeline.ts:269 | FIX #11 | "Prevent unbounded buffer growth" | S05.4 |
| src/bridge.ts:18 | FIX | "Track event handlers for proper cleanup" | S10.2 |
| src/bridge.ts:45 | FIX | "Store handlers for cleanup" | S10.2 |
| src/bridge.ts:47 | FIX #14 | "Sanitize URL in logs" | S10.5 |
| src/bridge.ts:95 | FIX | "Add method to cleanup event handlers" | S10.2 |
| src/bridge.ts:129 | FIX #14 | "Sanitize URLs to remove auth tokens from logs" | S10.5 |
| src/bridge.ts:162 | FIX #1 | "Use broadcaster to send to multiple devices" | S08.4 |
| src/bridge.ts:200 | FIX | "Register health check callback before starting" | S10.5 |
| src/bridge.ts:209 | FIX | "Use event-driven discovery instead of fixed timeout" | S10.2 |
| src/bridge.ts:232 | FIX | "Clean up event handlers before stopping components" | S10.2 |
| src/bridge.ts:282 | FIX | "Deep health check for proper monitoring" | S10.5 |
| src/chromecast/receiver.ts:21 | FIX | "Bridge health check callback" | S10.5 |
| src/chromecast/receiver.ts:28 | FIX #3 | "Sanitize name to prevent XML injection" | S12.1 |
| src/chromecast/receiver.ts:36 | FIX | "Allow bridge to provide health status" | S10.5 |
| src/chromecast/receiver.ts:41 | FIX #3 | "XML sanitization to prevent injection" | S12.1 |
| src/chromecast/receiver.ts:52 | FIX #2 | "Limit JSON body size to prevent DoS" | S12.1 |
| src/chromecast/receiver.ts:55 | FIX #10 | "Add rate limiting" | S12.1 |
| src/chromecast/receiver.ts:66 | FIX | "Deep health check endpoint for proper monitoring" | S10.5 |
| src/chromecast/receiver.ts:113 | FIX #9 | "Validate request structure" | S12.1 |
| src/chromecast/receiver.ts:120 | FIX #9 | "Validate media object exists" | S12.1 |
| src/chromecast/receiver.ts:149 | FIX #15 | "Validate and default content-type" | S05.2 |
| src/chromecast/receiver.ts:157 | FIX | "Handle request abort/close to prevent stream orphaning" | S05.3 |
| src/chromecast/receiver.ts:181 | FIX | "Handle connection abort" | S05.3 |
| src/chromecast/receiver.ts:213 | FIX #9 | "Validate media properties" | S12.1 |
| src/chromecast/receiver.ts:258 | FIX | "Configure server timeouts for production" | S12.1 |
| src/chromecast/receiver.ts:272 | FIX | "Monitor connection drops" | S12.1 |
| src/chromecast/receiver.ts:288 | FIX #13 | "Document authentication expectations" | S12.1 |
| src/config.ts:42 | FIX #4 | "Prevent path traversal attacks" | S10.4 |
| src/config.ts:47 | FIX #4 | "Validate config path to prevent directory traversal" | S10.4 |
| src/config.ts:89 | FIX #9 | "Validate JSON structure" | S10.4 |
| src/config.ts:110 | FIX #9 | "Validate configuration structure" | S10.4 |
| src/config.ts:161 | FIX #8 | "Validate port number" | S10.4 |
| src/index.ts:13 | FIX #8 | "Port validation helper" | S10.4 |
| src/index.ts:51 | FIX #8 | "Validate port before using" | S10.4 |
| src/index.ts:270 | FIX | "Graceful shutdown with draining period" | S10.2 |
| src/index.ts:311 | FIX #12 | "Handle process termination with proper error handling and coordination" | S10.2 |
| src/index.ts:334 | FIX #12 | "Handle unhandled promise rejections" | S10.2 |
| src/index.ts:340 | FIX #12 | "Handle uncaught exceptions" | S10.2 |
| src/utils/logger.ts:4 | FIX | "Add log rotation to prevent disk full crashes" | S10.5 |

Nearest related material (b): placeholder comments and notes in src (`git grep -n -i -E 'for now|simplif|proof of concept|initial version|in production|full implementation|would be|not implemented|Note:|NOTE' -- src`, 12 lines).

| location | marker | text | related stage |
|----------|--------|------|---------------|
| src/airplay/sender.ts:41 | "For now" | "For now, we'll mark it as ready to connect" | S08.1 |
| src/airplay/sender.ts:105 | none | "Volume command would be sent via RTSP here" | S06.2 |
| src/airplay/sender.ts:265 | "Simplified" | "Simplified RTSP initialization" | S08.2 |
| src/airplay/sender.ts:266 | none | "In a full implementation, this would:" | S08.2 |
| src/airplay/sender.ts:278 | "proof of concept" | "For this proof of concept, we'll just log the initialization" | S08.2 |
| src/airplay/sender.ts:279 | none | "A full implementation would require proper RTSP/RTP/RAOP protocol handling" | S08.2 |
| src/audio/pipeline.ts:188 | "initial version" | "For the initial version, we'll just pass through the audio" | S07.1 |
| src/audio/pipeline.ts:189 | none | "without transcoding. In production, you would:" | S07.1 |
| src/audio/pipeline.ts:195 | "Note:" (log text) | "Note: Audio transcoding not implemented. Audio may not play correctly on some devices." | S07.1 |
| src/chromecast/receiver.ts:286 | "Note:" | "Note: Full SSDP/DIAL implementation would require additional libraries" | S03.1 |
| src/chromecast/receiver.ts:289 | none (log text) | "SSDP advertisement started (simplified mode)" | S03.1 |
| src/chromecast/receiver.ts:291 | "NOTE:" (log text) | "NOTE: No authentication required - designed for local network use only" | S12.1 |

Nearest related material (c): sections of the two documentation files. Both files were last changed in the first commit (`git log --all -- README.md CONTRIBUTING.md` lists e8801c3 only).

| location | marker | text | related stage |
|----------|--------|------|---------------|
| README.md:212 | Limitations | "Full Chromecast Protocol: This implementation uses a simplified Chromecast receiver. Full DIAL/SSDP support may require additional work" | S11.4 |
| README.md:213 | Limitations | "Video Not Supported: Only audio streaming is currently supported" | S11.4 |
| README.md:214 | Limitations | "DRM Content: Protected content may not stream due to DRM restrictions" | S11.4 |
| README.md:215 | Limitations | "Network Requirements: All devices must be on the same local network" | S11.4 |
| README.md:240 | Roadmap, unchecked | "Full DIAL/SSDP implementation for better Chromecast compatibility" | S03.1 |
| README.md:241 | Roadmap, unchecked | "Web UI for easier device management" | S09.1 |
| README.md:242 | Roadmap, unchecked | "Audio synchronization across multiple devices" | S07.3 |
| README.md:243 | Roadmap, unchecked | "Support for more audio codecs" | S07.1 |
| README.md:244 | Roadmap, unchecked | "Plugin system for custom audio processing" | S07.1 |
| README.md:245 | Roadmap, unchecked | "Mobile app for remote control" | S09.1 |
| CONTRIBUTING.md:126 | Testing | "Currently, testing is manual. Automated tests are welcome contributions!" | S11.1 |
| CONTRIBUTING.md:129-134 | Manual checklist, six unchecked items | "Device discovery works", "Can connect to AirPlay devices", "Audio streams correctly", "Volume control works", "CLI commands respond correctly", "Graceful shutdown" | S11.1 |
| CONTRIBUTING.md:139-142 | High Priority | "Automated testing suite", "Full DIAL/SSDP implementation", "Audio synchronization", "Web UI" | S11.4 |
| CONTRIBUTING.md:145-148 | Medium Priority | "Additional audio codec support", "Performance optimizations", "Better error handling", "Documentation improvements" | S11.4 |
| CONTRIBUTING.md:151-154 | Good First Issues | "Code formatting with Prettier", "Additional logging", "Configuration validation", "Help text improvements" | S11.4 |

---

## H7 Workarounds

Searched: `git grep -n -E 'setTimeout|setInterval|sleep|retry|retries|RECONNECT|TIMEOUT|timeout|delay|backoff' -- src Dockerfile docker-compose.yml .github` (27 lines, all covered by the records below).

Version pins with comments: none. package.json is plain JSON with caret ranges and one `>=` range (package.json:25-45); the base image tag `node:20-bullseye-slim` has no comment (Dockerfile:1); the CI Node matrix has no comment (.github/workflows/ci.yml:15).

Special cases for a named device: none. `git grep -n -i -E 'airport|homepod|apple ?tv|iphone|ipad|android|pixel|samsung|sonos|spotify' -- src Dockerfile docker-compose.yml config.example.json` gives zero hits. Device types are named only as examples in README.md:7, 87, 91.

### R5-H7-01  Fixed wait after starting discovery: 3000 ms, later 2000 ms
- Question: H7, relates to S10.2
- What was attempted: `bridge.start()` starts mDNS discovery, waits a fixed time, then logs the device count. The wait was 3000 ms under the comment "Wait a bit for devices to be discovered" (e8801c3:src/bridge.ts:127-128). Commit e556801 changed it to 2000 ms and added a warning when the count is zero (src/bridge.ts:209-219). The comments now read "FIX: Use event-driven discovery instead of fixed timeout" and "Still wait briefly for initial discovery, but don't block" (src/bridge.ts:209-210); the next line awaits a fixed `setTimeout` of 2000 ms before `start()` returns (src/bridge.ts:211). The commit message says "Reduce hard-coded wait from 3s to 2s" and "Make startup event-driven" (commit e556801). Problem worked around, STATED in the first comment: discovery results arrive some time after discovery starts.
- Presence: CURRENT at 2000 ms (src/bridge.ts:211). The 3000 ms value is REMOVED by commit e556801 (src/bridge.ts).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Stated check is the build only (commit e556801).
- Why it turned out that way: UNKNOWN.
- Depends on: bonjour-service 1.3.0 (package-lock.json:447-448); devices answering mDNS within the wait; devices found later being added by the `up` handler (src/airplay/discovery.ts:64-78).
- Open questions: Which part of the startup is event-driven after commit e556801? The device events were already handled in the first commit (e8801c3:src/airplay/discovery.ts:31-42). How long did discovery take on the author's network? No figure is recorded.
- Confidence: MEDIUM. INFERRED from clear code and the diff; the reason is STATED in comments.

### R5-H7-02  Discovery reconnection with exponential backoff, ten attempts
- Question: H7, relates to S08.3
- What was attempted: commit e556801 added recovery to mDNS discovery. A browser `error` event, or an exception while starting, tears the browser down and schedules a restart (src/airplay/discovery.ts:36-52, 89-93, 101-110). The delay is 5000 ms doubled per attempt and capped at 60000 ms, for at most 10 attempts (src/airplay/discovery.ts:19-20, 112-144). The counter resets when any device is discovered (src/airplay/discovery.ts:76-77). After the tenth attempt the object stops and emits `discoveryFailed` (src/airplay/discovery.ts:117-121). The first commit started discovery once with no recovery (e8801c3:src/airplay/discovery.ts:22-54). Stated problem: "Fix: Prevents permanent discovery failure on network glitches" (commit e556801).
- Presence: CURRENT (src/airplay/discovery.ts:16-20, 101-144).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Stated check is the build only (commit e556801). The message lists "Auto-recovery: Discovery reconnects automatically" under "After Fixes" with no test described.
- Why it turned out that way: UNKNOWN.
- Depends on: the bonjour-service browser object emitting an `error` event (src/airplay/discovery.ts:90); bonjour-service 1.3.0 (package-lock.json:447-448).
- Open questions: Does the bonjour-service browser emit `error`? Nothing in the repo shows it. When `start()` fails, `isRunning` is set false at src/airplay/discovery.ts:47 before `scheduleReconnect()` at line 49, and the timer callback acts only when `isRunning` is true (line 134); which path retries in that case? What consumes `discoveryFailed` (R5-H5-10)?
- Confidence: MEDIUM. INFERRED from clear code and the diff of commit e556801.

### R5-H7-03  Ten-second timeout on the TCP connection to each device
- Question: H7, relates to S08.3
- What was attempted: commit a744781 added `timeout: 10000` to `net.createConnection` and a `timeout` handler that destroys the socket and rejects (src/airplay/sender.ts:19, 182-197). The first commit set no timeout (e8801c3:src/airplay/sender.ts:129-132). Stated: "FIX #7: Add 10-second connection timeout for AirPlay devices" (commit a744781).
- Presence: CURRENT (src/airplay/sender.ts:19, 186, 190-197).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Compilation is the only check stated (commit a744781).
- Why it turned out that way: UNKNOWN.
- Depends on: Node.js `net` socket timeout semantics; device host and port from mDNS, default port 5000 (src/airplay/sender.ts:184-185).
- Open questions: Does the timeout set at src/airplay/sender.ts:186 go on applying as an idle timeout after the connection opens, for instance during silence or a pause? Which device failed to answer, if any? None is named.
- Confidence: MEDIUM. INFERRED from clear code and the diff of commit a744781.

### R5-H7-04  `settleOnce` guard on the connection promise
- Question: H7, relates to S08.3
- What was attempted: commit 2db4cb5 wrapped every `resolve` and `reject` in `connectAndStream()` in a guard that lets only the first one through (src/airplay/sender.ts:172-179, 196, 222, 224, 233, 259). Stated problem: "Prevent multiple promise settlements when both timeout and error fire" (commit 2db4cb5).
- Presence: CURRENT (src/airplay/sender.ts:172-179).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Commit 2db4cb5 states no check.
- Why it turned out that way: UNKNOWN.
- Depends on: the timeout handler added by a744781 (R5-H7-03), which made two rejection paths possible for one socket (src/airplay/sender.ts:190-197, 227-234).
- Open questions: Was the double settlement seen in a run, or found by reading the code? The message says "identified in comprehensive analysis" and gives no log.
- Confidence: MEDIUM. INFERRED from clear code; the reason is STATED in the commit message.

### R5-H7-05  `isSettingSource` flag on `AudioBroadcaster.setSource()`
- Question: H7, relates to S07.1
- What was attempted: commit 2db4cb5 added a boolean that is set on entry to `setSource()`, cleared in a `finally` block, and causes a call made while it is set to return with a warning (src/audio/pipeline.ts:19, 31, 41-47, 93-95). Stated problem: "Reject rapid successive calls to prevent stream mixing" (commit 2db4cb5).
- Presence: CURRENT (src/audio/pipeline.ts:41-47, 93-95).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Commit 2db4cb5 states no check.
- Why it turned out that way: UNKNOWN.
- Depends on: `setSource()` being a synchronous method with no `await` between setting and clearing the flag (src/audio/pipeline.ts:40-96); its single caller in the bridge (src/bridge.ts:163).
- Open questions: Under what call sequence is the flag true on entry, given that it is set and cleared within one synchronous call? When a second `POST /stream` arrives during playback, what happens to the first stream's consumers (src/audio/pipeline.ts:50-52, 129-161)?
- Confidence: MEDIUM. INFERRED from clear code; the reason is STATED in the commit message.

### R5-H7-06  Listener limit raised to 50 on `AudioBroadcaster`
- Question: H7, relates to S08.4
- What was attempted: commit 2db4cb5 added `this.setMaxListeners(50)` to the constructor under the comment "FIX: Increase max listeners to prevent warnings" (src/audio/pipeline.ts:36-37). Stated problem: "Fix: Prevents warnings with many concurrent consumers" (commit 2db4cb5).
- Presence: CURRENT (src/audio/pipeline.ts:37).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Commit 2db4cb5 states no check.
- Why it turned out that way: UNKNOWN.
- Depends on: the Node.js warning for more than the default number of listeners on one emitter.
- Open questions: Which listeners are registered on the broadcaster object itself? A search finds none (R5-H5-10); consumers are held in a `Set` (src/audio/pipeline.ts:17, 100). Was the warning seen in a log? None is tracked.
- Confidence: MEDIUM. INFERRED from clear code; the reason is STATED in the commit message.

### R5-H7-07  Shutdown with a 2-second wait and a 30-second forced exit
- Question: H7, relates to S10.2
- What was attempted: the first commit's shutdown awaited `bridge.stop()` and exited (e8801c3:src/index.ts:252-268). Commit a744781 wrapped it in `try`/`catch` and added handlers for unhandled rejections and uncaught exceptions (a744781:src/index.ts:270-314). Commit e556801 added a flag against a second shutdown, a timer that calls `process.exit(1)` after 30000 ms, and a 2000 ms wait before `bridge.stop()` (src/index.ts:270-309). The comment "Stop accepting new connections" precedes a log call (src/index.ts:291-292); the HTTP server is closed later, inside `bridge.stop()` (src/bridge.ts:235; src/chromecast/receiver.ts:294-305). Stated: "Add 2-second draining period for in-flight requests" and "Fix: Ensures clean shutdown on deployments" (commit e556801).
- Presence: CURRENT (src/index.ts:270-346). The two earlier forms are REMOVED by commits a744781 and e556801 (src/index.ts).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Stated check is the build only (commit e556801). "Graceful shutdown" is an unchecked item in the manual checklist (CONTRIBUTING.md:134).
- Why it turned out that way: UNKNOWN.
- Depends on: SIGINT and SIGTERM delivery to the Node process, which the Dockerfile starts through npm (Dockerfile:43); the `restart: unless-stopped` policy (docker-compose.yml:8).
- Open questions: What is drained during the 2000 ms at src/index.ts:295 while the server is still listening? What data loss does the commit's phrase "Shutdown: Abrupt termination, data loss" refer to? The process keeps no stored state other than log files (src/config.ts:169-176 is uncalled, R5-H5-09).
- Confidence: MEDIUM. INFERRED from clear code and diffs; reasons STATED in the commit message.

### R5-H7-08  HTTP server timeouts and limits, one sized against a load balancer
- Question: H7, relates to S12.1
- What was attempted: commit e556801 set a 30000 ms socket timeout, `keepAliveTimeout` 65000 with the comment "65 seconds (longer than load balancer)", `headersTimeout` 66000, at most 100 headers and at most 100 connections, and added a `clientError` handler that answers 400 (src/chromecast/receiver.ts:258-265, 272-278). Stated problem: "Fix: Protects against slow loris and resource exhaustion" (commit e556801).
- Presence: CURRENT (src/chromecast/receiver.ts:258-278).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Stated check is the build only (commit e556801).
- Why it turned out that way: UNKNOWN.
- Depends on: a load balancer in front of the server, named only in the comment and the commit message; none is defined in a tracked file (git ls-files). The compose file uses host networking with no proxy (docker-compose.yml:7).
- Open questions: Does the 30000 ms timeout at src/chromecast/receiver.ts:259 apply to a long `POST /stream` upload (src/chromecast/receiver.ts:146-189), and what happens to the audio when it fires?
- Confidence: MEDIUM. INFERRED from clear code; the reason is STATED in the commit message.

### R5-H7-09  Container health check with three retries, and a restart policy
- Question: H7, relates to S10.2
- What was attempted: the image declares a health check every 30 s, timeout 10 s, start period 5 s, 3 retries, requesting `/health` on port 8009 (Dockerfile:39-40). The compose service has `restart: unless-stopped` (docker-compose.yml:8). Both date from the first commit and were not changed (`git log --all -- Dockerfile docker-compose.yml` lists e8801c3 only). No comment or commit message gives a problem they work around. The port in the check is fixed while the receiver port is configurable (Dockerfile:40; src/index.ts:40; src/config.ts:24).
- Presence: CURRENT (Dockerfile:39-40; docker-compose.yml:8).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: Docker; the `/health` route returning 200, which after commit e556801 depends on the bridge health result (R5-H2-06).
- Open questions: Was the image ever built and run? Commit e556801 says "x86 build successful" without saying whether that was the image or the TypeScript build.
- Confidence: MEDIUM. INFERRED from clear code.

---

## H8 Final state

- Subjects of the last ten commits: the history holds four commits, listed newest first (git log --all --date=short --pretty='%h %ad %s'):
  1. e556801, 2025-11-21, "Fix 12 critical reliability issues for production readiness"
  2. 2db4cb5, 2025-11-21, "Fix 9 critical memory leaks and race conditions"
  3. a744781, 2025-11-19, "Fix all 16 security and reliability vulnerabilities (ARM version)"
  4. e8801c3, 2025-11-18, "Add Cast2Air: Chromecast to AirPlay bridge"
- Uncommitted changes listed in the manifest: none; the `git status --porcelain` column for R5 is "empty" (/home/user/audio/00-manifest.md:26). Re-run during this audit: `git status --porcelain` printed zero lines and HEAD was e556801cdd966f7bb86b8e7d2400718d38e8866c.
- Git state at the end: no stash (git stash list), no tag (git tag), no branch ahead of or apart from the default branch (git branch -a --no-merged prints nothing; R5-H4-01).
- How the last commit describes the state: its message ends with a list headed "PRODUCTION READINESS" of seven ticked lines and the line "Tested: x86 build successful, all TypeScript checks passing" (commit e556801). No commit message contains "WIP", "work in progress", "partial", "incomplete" or "not yet" (`git log --all --pretty=%B | grep -n -i -E 'wip|work in progress|temp|tmp|partial|incomplete|not yet|todo'` matches only the word "attempts" at two lines).
- Whether the repo was left mid-change:
  - INFERRED: no edit was interrupted, because the working tree is clean, nothing is stashed and the last commit message describes a finished batch.
  - STATED: functionality on the audio path is unfinished at HEAD. Transcoding: "Audio transcoding not implemented" (src/audio/pipeline.ts:195; R5-H5-04). RTSP: "For this proof of concept, we'll just log the initialization" (src/airplay/sender.ts:278; R5-H5-05). Volume: "For simplicity, we'll just log it" (src/airplay/sender.ts:106; R5-H5-06). These three bodies are unchanged since the first commit (R5-H3-03).
  - README.md and CONTRIBUTING.md were last changed in the first commit (`git log --all -- README.md CONTRIBUTING.md` lists e8801c3 only). The README roadmap has six unchecked items (README.md:240-245) and the manual testing checklist has six unchecked items (CONTRIBUTING.md:129-134). Neither file mentions the routes added by the last commit (`git grep -n -E 'healthz|/ready|/health' -- README.md CONTRIBUTING.md`: zero hits).
  - One item of the last commit differs between message and code: the message says "Make startup event-driven" and the code awaits a fixed 2000 ms (src/bridge.ts:211; R5-H7-01).
  - The last three commits changed no file outside `src/`, package.json and package-lock.json (git log --all --name-status).
- Bluetooth content at the end of the history: none in any commit (searches listed at the top of this file).

---

## Tally

| Question | Answered with | Record IDs |
|----------|---------------|------------|
| H1 | Findings (factual list) | none (list) |
| H2 | Findings | R5-H2-01 to R5-H2-06 |
| H3 | Findings | R5-H3-01 to R5-H3-03 |
| H4 | NOT FOUND | R5-H4-01 |
| H5 | Findings | R5-H5-01 to R5-H5-15 |
| H6 | NOT FOUND | R5-H6-01 |
| H7 | Findings | R5-H7-01 to R5-H7-09 |
| H8 | Findings (factual list) | none (list) |

- Questions answered with findings: 6 (H1, H2, H3, H5, H7, H8)
- Questions answered NOT FOUND: 2 (H4, H6)
- Total: 8
- Finding records: 33 (H2: 6, H3: 3, H5: 15, H7: 9). NOT FOUND records: 2.
