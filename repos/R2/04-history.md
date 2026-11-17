# R2 history: AudioBurgerSonnet

Repo root: /home/user/AudioBurgerSonnet. Paths are relative to that root unless they start with `/`.
Read at commit 1d878c2 (`git rev-parse HEAD` = 1d878c2e1b15c4cdb2baf342c30fbb247d491737; `main`, `origin/main` and the session branch all point at it, `git for-each-ref`).
A bare `path:line` refers to the tree at 1d878c2. `HASH:path:line` refers to the file as it stood in that commit (read with `git show HASH:path`).
Nothing was checked out, built or run. Commands used: `git log`, `git show`, `git branch -a`, `git tag`, `git stash list`, `git status --porcelain`, `git rev-parse`, `git rev-list --count`, `git shortlog`, `git for-each-ref`, `git ls-tree`, `git grep`, and file reads.

Record forms used: H1 and H8 are lists, H6 is a table, H2, H5 and H7 are finding records, H3 and H4 are NOT FOUND records.

---

## H1 Timeline

### R2-H1-01  Timeline of the repository

- First commit: 9f9cb0b, 2026-01-20 08:21:02 +1100, "Create readme.md" (`git log --all --reverse --date=iso`).
- Last commit: 1d878c2, 2026-02-02 14:09:12 +1100, "Merge pull request #15 from tunlezah/claude/implement-ai-agent-guide-9YcaS" (`git log --all -1 --date=iso`).
- Span: 14 calendar days, first to last, with commits on 6 of them.
- Commit count: 35 reachable from all refs; 20 non-merge and 15 merge commits (`git rev-list --count --all`, with `--no-merges`, with `--merges`).
- Authors: "tunlezah" 18 commits (3 direct commits 9f9cb0b, f510135, d72817b, plus the 15 merges); "Claude" 17 commits, all non-merge (`git shortlog -sn --all`; `git log --first-parent --no-merges main`).
- All 15 merge commits have an empty combined diff (`git show --cc` on each printed 0 lines), so no content was changed inside a merge.
- Commits per day, author-local dates (`git log --all --date=short --pretty=%ad | sort | uniq -c`):
  - 2026-01-20: 12 commits (9 non-merge). Content: `readme.md` placeholder (9f9cb0b), `project-context.md` (f510135, d72817b), six research and design reports (e8f523d, 933dede, 4b3987d, f8ed837, 54cd761, 254f53b).
  - 2026-01-21: 8 commits (3 non-merge). Content: `architecture-and-build-plan.md` (e181276), the code skeleton (70fc363), the assessment `fixes.md` (d51fcef).
  - 2026-01-22: 1 commit, the merge of pull request #9 (4b859c6, 06:52 +1100).
  - 2026-01-26: 2 commits (both non-merge). Content: "v2.0 security fixes" with `patch1.md` (b61e1ec), installer and `INSTALLER.md` (70b5a5a).
  - 2026-02-01: 6 commits (2 non-merge). Content: `README.md` rewrite (deb899b), first `PROJECT-COMPLETION-ROADMAP.md` (6445123), merges #10 to #13.
  - 2026-02-02: 6 commits (4 non-merge). Content: roadmap rewrite (1491214), `AI-AGENT-IMPLEMENTATION-GUIDE.md` (cec17a7), two implementation commits (eb02d59, add5474), merges #14 and #15.
- Days without commits inside the span: 2026-01-23 to 2026-01-25 (3 days) and 2026-01-27 to 2026-01-31 (5 days).
- No commit after 2026-02-02. The manifest records the audit run as started 2026-11-17 (/home/user/audio/00-manifest.md:3), 288 days later.
- The two commits of 2026-01-26 (70b5a5a, b61e1ec) entered `main` on 2026-02-01 through merges ad70621 (20:14:40 +1100) and b1c1a12 (20:15:24 +1100) (`git log m^1..m^2` for each merge).
- Non-merge commits that changed any non-`.md` file: 70fc363, b61e1ec, 70b5a5a, eb02d59, add5474 (`git log --all --no-merges --stat`). The other 15 non-merge commits changed `.md` files only.
- Timing of the last code work, all on 2026-02-02 UTC: cec17a7 (guide) 02:51:07; eb02d59 03:03:01 (7 files, 2,161 insertions, 177 deletions); add5474 03:04:50 (3 files, 245 insertions, 3 deletions), 1 minute 49 seconds after eb02d59; merge #14 (3c78b1c) 03:08:50; merge #15 (1d878c2) 03:09:12 (`git log --all --date=iso-local`; `git log --no-merges --stat`).
- Files changed by the most commits (`git log --all --name-only --pretty=format: | sort | uniq -c | sort -rn`): `audioburger/web_ui/api_server.py` 4, `audioburger/main.py` 4; ten files at 2; every other file at 1.

---

## H2 Approach changes

### R2-H2-01  Bluetooth stub module rewritten to BlueZ D-Bus control calls
- Question: H2, relates to S04.1
- What was attempted: the stub `audioburger/bluetooth/a2dp_sink.py` added in 70fc363 (187 lines; its docstring read "CURRENT STATUS: STUB - No functionality implemented yet.", removed line shown in `git show eb02d59 -- audioburger/bluetooth/a2dp_sink.py`) was rewritten in eb02d59 to call BlueZ over the system D-Bus through `dbus-python`: adapter lookup, `Powered`, `Alias` and two ObjectManager signal receivers (a2dp_sink.py:87-122), discoverable and pairable properties (a2dp_sink.py:173-203), paired-device listing (a2dp_sink.py:205-278), `Device1.Connect` and `Device1.Disconnect` (a2dp_sink.py:280-364). Stated reason for the change: UNKNOWN. Commit eb02d59 lists the work under "PHASE 3: Bluetooth A2DP Sink Implementation" and gives no reason.
- Presence: CURRENT (a2dp_sink.py at 1d878c2). The stub bodies are REMOVED, commit eb02d59.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The only statement about the result is the general line in commit eb02d59, "All core audio routing components are now functional."; the message reports no run, test or log, and no test file or log is tracked (`git ls-files`, /home/user/audio/repos/R2/01-map.md:331).
- Why it turned out that way: UNKNOWN. Not evidenced.
- Depends on: `dbus-python>=1.3.2` (requirements.txt:16); a BlueZ daemon on bus name `org.bluez` (a2dp_sink.py:97); the adapter name from configuration, default `hci0` (config.toml:6); the code listing in the first roadmap, which the rewritten module follows (6445123:PROJECT-COMPLETION-ROADMAP.md:358-483).
- Open questions: Was the rewritten module ever run against a BlueZ daemon? Was a source device ever paired or connected through it?
- Confidence: MEDIUM. The replacement itself is read directly from the diff; the result has no evidence.

### R2-H2-02  Planned PipeWire connection in the audio pipeline replaced by sounddevice capture with numpy and scipy processing
- Question: H2, relates to S05.1
- What was attempted: the stub pipeline of 70fc363 described its future work as PipeWire calls ("Initialize PipeWire connection", 70fc363:audioburger/audio_plane/audio_pipeline.py:70; "# STUB: Future implementation will initialize PipeWire here", 70fc363:audioburger/audio_plane/audio_pipeline.py:78). Commit eb02d59 removed those lines and added a processing loop (audio_pipeline.py:157-193), line-in capture through `sd.rec` on the default input (audio_pipeline.py:203-220), an FFT to 10 bands with `scipy.fft` (audio_pipeline.py:234-276), resampling with `scipy.signal.resample_poly` (audio_pipeline.py:278-302) and two output callbacks (audio_pipeline.py:311-341). No PipeWire call was added; the Bluetooth capture function added in the same commit returns zeros (see R2-H5-01). Stated reason for the change: UNKNOWN. Commit eb02d59 says "Add line-in capture via sounddevice library" and gives no reason for the choice. The first roadmap listed three capture options and showed the sounddevice one as its example (6445123:PROJECT-COMPLETION-ROADMAP.md:316-321).
- Presence: CURRENT (audio_pipeline.py at 1d878c2). The PipeWire stub comments in `start()` and `stop()` are REMOVED, commit eb02d59. Four "Future implementation will" docstrings remain in the file (audioburger/audio_plane/audio_pipeline.py:361, :392, :424, :445); two of the four name PipeWire, "Configure PipeWire graph for selected source" (audioburger/audio_plane/audio_pipeline.py:362) and "Reconfigure PipeWire graph" (audioburger/audio_plane/audio_pipeline.py:426).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Commit eb02d59 states "All core audio routing components are now functional." without a run, test or measurement.
- Why it turned out that way: UNKNOWN. Not evidenced.
- Depends on: `sounddevice>=0.4.6`, `numpy>=1.24.0`, `scipy>=1.10.0` (requirements.txt:11-13); the default input device of the host (audio_pipeline.py:209-216); `audio.sample_rate`, `audio.buffer_size`, `audio.channels` (config.toml:12-14).
- Open questions: Was line-in capture run on any hardware? Why was the PipeWire connection named in the architecture (architecture-and-build-plan.md:141-150) not written?
- Confidence: MEDIUM. Read directly from the diff of eb02d59; result not evidenced.

### R2-H2-03  Chromecast stub module rewritten to an aiohttp stream server with pychromecast calls
- Question: H2, relates to S08.2
- What was attempted: the stub `audioburger/chromecast/chromecast_output.py` of 70fc363 (250 lines) was rewritten in eb02d59: an aiohttp server on `/stream.<format>` (chromecast_output.py:110-124), a stream handler that drains an in-memory byte buffer (chromecast_output.py:126-154), `add_audio_data` (chromecast_output.py:156-167), discovery through `pychromecast.discovery.discover_chromecasts` (chromecast_output.py:207-281), connect, playback and local-address lookup (chromecast_output.py:283-420). In the same commit the signature `connect(self, device_name: str)` (70fc363:audioburger/chromecast/chromecast_output.py:139) became `connect(self, device_uuid: str)` (chromecast_output.py:283). Stated reason for the change: UNKNOWN. Commit eb02d59 lists the work under "PHASE 4: Chromecast Output Implementation" and gives no reason, including none for the change from name to UUID.
- Presence: CURRENT (chromecast_output.py at 1d878c2). The stub bodies are REMOVED, commit eb02d59.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Same general statement in eb02d59 as in R2-H2-01; no run or test is reported.
- Why it turned out that way: UNKNOWN. Not evidenced.
- Depends on: `pychromecast>=14.0.0` (requirements.txt:19); `chromecast.http_port` 8081 and `chromecast.format` "flac" (config.toml:21-22); the code listing in the first roadmap (6445123:PROJECT-COMPLETION-ROADMAP.md:504-634).
- Open questions: Was a Chromecast device ever discovered or connected with this code? Did any device ever request the stream URL?
- Confidence: MEDIUM. Read directly from the diff; result not evidenced.

### R2-H2-04  Planned pybluez dependency dropped; dbus-python kept as the only Bluetooth library
- Question: H2, relates to S01.3
- What was attempted: the skeleton's requirements file carried two commented-out Bluetooth lines, `# pybluez>=0.23  # BlueZ D-Bus bindings` and `# dbus-python>=1.3.2  # D-Bus communication` (70fc363:requirements.txt:14-15), under the heading "# Future Dependencies (commented out until needed in later phases)" (70fc363:requirements.txt:10). Commit eb02d59 deleted the pybluez line and activated `dbus-python>=1.3.2` (requirements.txt:16). Stated reason: the commit message says only "Uncomment and add required dependencies in requirements.txt". A reason is written in the first roadmap: "`pybluez` is deprecated. Use `dbus-python` for BlueZ D-Bus API." (6445123:PROJECT-COMPLETION-ROADMAP.md:198), and its decision list reads "BlueZ via D-Bus (not pybluez)" (6445123:PROJECT-COMPLETION-ROADMAP.md:1397).
- Presence: REMOVED for the pybluez line, commit eb02d59. CURRENT for dbus-python (requirements.txt:16). pybluez was never an active requirement in any commit.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No install log or run is tracked.
- Why it turned out that way: STATED. "`pybluez` is deprecated." (6445123:PROJECT-COMPLETION-ROADMAP.md:198). The statement is absent from the roadmap at 1d878c2 (the file was rewritten in 1491214).
- Depends on: `dbus-python>=1.3.2` as a lower bound, not a pin (requirements.txt:16). Two documents at 1d878c2 still name pybluez: README.md:274 and architecture-and-build-plan.md:1909.
- Open questions: Was pybluez ever installed or tried, or was it dropped on the written statement alone?
- Confidence: MEDIUM. STATED reason in a document, and the code agrees that only `dbus` is imported (a2dp_sink.py:16-22).

### R2-H2-05  GStreamer named in the audio framework research, absent from the architecture and from the code
- Question: H2, relates to S07.1
- What was attempted: the audio framework research report (commit 4b3987d, 2026-01-20) gives as its key finding "PipeWire + GStreamer combination is recommended." (audio-framework-research-report.md:7) and assigns EQ, visualisation and format conversion to GStreamer (audio-framework-research-report.md:7). The architecture document added one day later (commit e181276) contains no occurrence of the word GStreamer (`grep -c -i gstreamer architecture-and-build-plan.md` = 0) and lists "FFmpeg/libsamplerate (resampling)" and "Custom FFT analysis" (architecture-and-build-plan.md:149-150). The code at 1d878c2 uses numpy and scipy (audio_pipeline.py:13-15). The word GStreamer occurs in one tracked file only, the research report (52 occurrences; 0 in the other thirteen `.md` files and 0 in code, per `grep -c -i`). Stated reason for the change: UNKNOWN. Commit e181276 says the document "synthesizes all research reports" and gives no reason for leaving GStreamer out.
- Presence: REMOVED as a plan between 4b3987d and e181276 (never present in code). numpy and scipy processing is CURRENT (audio_pipeline.py:234-302).
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. No document or commit states why.
- Depends on: `numpy>=1.24.0`, `scipy>=1.10.0` (requirements.txt:11-12). The first roadmap states the choice as "FFT: NumPy/SciPy (not external library)" (6445123:PROJECT-COMPLETION-ROADMAP.md:1400).
- Open questions: Was GStreamer evaluated in practice at any point? Who or what dropped it when the architecture was written?
- Confidence: MEDIUM. INFERRED from word counts and from the two documents' text; the gap between them is clear.

### R2-H2-06  Hardcoded status response replaced by a response built from live component objects
- Question: H2, relates to S09.2
- What was attempted: the skeleton returned a fixed dictionary with `"status": "stub"` for each component (70fc363:audioburger/web_ui/api_server.py:164-169). The README rewrite recorded this: "The `/api/v1/status` response is a hardcoded dictionary." (README.md:221). Commit eb02d59 gave `APIServer` references to the three components (api_server.py:38-41, :60-63; audioburger/main.py:187-192) and built the response from `is_running()` and `get_status()` (api_server.py:234-261). Stated reason: eb02d59, "Fix status endpoint to return live component data".
- Presence: CURRENT (api_server.py:222-263). The fixed dictionary is REMOVED, commit eb02d59.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No request log or test is tracked.
- Why it turned out that way: UNKNOWN. Not evidenced.
- Depends on: component references passed at construction (audioburger/main.py:187-192). README.md:220-221 still describes the earlier state, because no `.md` file was changed after eb02d59 (/home/user/audio/repos/R2/01-map.md:170).
- Open questions: Was the endpoint called after the change?
- Confidence: MEDIUM. Read directly from the diff; result not evidenced.

### R2-H2-07  WebSocket echo behaviour removed
- Question: H2, relates to S09.2
- What was attempted: the skeleton echoed each text message back to the sender ("# Echo messages for testing (stub behavior)", 70fc363:audioburger/web_ui/api_server.py:235; `send_str(f"Echo: {msg.data}")`, 70fc363:audioburger/web_ui/api_server.py:240). Commit b61e1ec removed the echo and left a branch that logs and does nothing else (api_server.py:402-405), together with a connection limit, a message size limit and a per-connection rate limit (api_server.py:33-36, :360-400). Stated reason: patch1.md:126, "Removed echo functionality (security risk for XSS)"; code comment api_server.py:405, "# For now, don't echo raw user input (security)".
- Presence: REMOVED, commit b61e1ec. README.md:216 and README.md:290 still call the endpoint an echo stub.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. patch1.md lists testing as still to be done: "Regression Testing Required" (patch1.md:537) and "Load test WebSocket connections and rate limiting" (patch1.md:541).
- Why it turned out that way: UNKNOWN. Not evidenced.
- Depends on: aiohttp `>=3.9.0` (requirements.txt:5); the limits at api_server.py:33-36.
- Open questions: Were the tests listed at patch1.md:140-164 ever run?
- Confidence: MEDIUM. STATED reason, and the code agrees that no echo call remains; the result is not evidenced.

### R2-H2-08  Hardcoded version string replaced by one version constant
- Question: H2, relates to S10.6
- What was attempted: the skeleton status response carried `"version": "0.1.0-stub"` (70fc363:audioburger/web_ui/api_server.py:164) and `audioburger/__init__.py` was empty (70fc363 stat: 0 lines). Commit b61e1ec added `__version__ = "2.0.0"` (audioburger/__init__.py:5) and imported it in `main.py` and `api_server.py` (audioburger/main.py:26; api_server.py:21). Stated reason: b61e1ec, "L-1: Centralize version management"; patch1.md:432, "Version was hardcoded as `\"0.1.0-stub\"` in multiple locations, making version management difficult."
- Presence: CURRENT (audioburger/__init__.py:5). The literal is REMOVED, commit b61e1ec.
- Outcome: PARTIAL. Known defect listed: two other version values remain at 1d878c2, `version='0.1.0'` in setup.py:31 and `"version": "0.1.0"` in audioburger/web_ui/frontend/package.json:3.
- Outcome evidence: STATED for the change (patch1.md:444-457); INFERRED for the defect, from reading setup.py:31 and package.json:3, which neither commit b61e1ec nor any later commit changed (`git log -- setup.py` shows 70fc363 only).
- Why it turned out that way: UNKNOWN. No statement covers setup.py or package.json.
- Depends on: Python import of `audioburger.__version__` (audioburger/main.py:26).
- Open questions: Is the package version in setup.py meant to differ from the application version?
- Confidence: MEDIUM. STATED evidence for the change, clear code for the remaining values.

### R2-H2-09  Unvalidated TOML loading replaced by validated loading with rollback on reload
- Question: H2, relates to S10.4
- What was attempted: the skeleton imported `tomli` directly (70fc363:audioburger/config/config_manager.py:10) and loaded values without range checks. Commit b61e1ec changed the import to `tomllib` with `tomli` as fallback (audioburger/config/config_manager.py:13-16), added per-value validation (audioburger/config/config_manager.py:202-262, :290-374) and a reload that restores the previous configuration on any error (audioburger/config/config_manager.py:377-409); the file grew by 274 changed lines (`git show --stat b61e1ec`). Stated reason: b61e1ec, "This patch addresses critical security vulnerabilities and high-priority reliability issues identified in the Phase 5 security assessment." The item is C-4 in fixes.md:245.
- Presence: CURRENT (audioburger/config/config_manager.py at 1d878c2; last changed in b61e1ec).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. patch1.md:248-269 lists test commands with expected results, not recorded results; patch1.md:539 lists "Test configuration reload with various config changes" under testing still required.
- Why it turned out that way: UNKNOWN. Not evidenced.
- Depends on: Python 3.11 `tomllib` or the `tomli` package (requirements.txt:8). patch1.md:241-246 records an alternative that was not taken: "Research recommended Pydantic for modern Python config validation." (patch1.md:242).
- Open questions: Were the test commands of patch1.md:248-269 run?
- Confidence: MEDIUM. Read directly from the diff and patch1.md; result not evidenced.

### R2-H2-10  Placeholder readme.md deleted and README.md status section rewritten
- Question: H2, relates to S11.4
- What was attempted: `readme.md` (one line, "New file", 9f9cb0b:readme.md:1) was the first file in the repo. `README.md` was added with the skeleton (70fc363) with the heading "Current Status: Phase 4, Step 1 - Foundation & Skeleton" (70fc363:README.md:7). Commit deb899b deleted `readme.md` and rewrote the status part of `README.md` (128 insertions, 67 deletions), adding "(NOT a working application)" to the heading (README.md:7) and a section "What's Implemented vs Stubbed (Source-Verified)" (README.md:187-240). Stated reason: deb899b, "The project is a skeleton, not a working application. The previous README overstated what was functional"; and "Remove empty readme.md placeholder (duplicate of README.md)".
- Presence: REMOVED for `readme.md`, commit deb899b (`git log --all --diff-filter=D --name-only`). CURRENT for the rewritten README.md. README.md:255 still names `readme.md` as a file to delete.
- Outcome: WORKED for the deletion and rewrite as a documentation change.
- Outcome evidence: OBSERVED. `git show --stat deb899b` shows `readme.md | 1 -` and `README.md | 194`; `git ls-files` at 1d878c2 lists `README.md` and no `readme.md`.
- Why it turned out that way: STATED. deb899b: "The previous README overstated what was functional (e.g. claimed the Svelte frontend was served, but no static-file route exists".
- Depends on: the code as it stood at 4b859c6, the parent of deb899b (`git log --pretty='%h <- %p'`). README.md was not changed by eb02d59 or add5474, so its status section describes the code before those two commits.
- Open questions: NONE.
- Confidence: HIGH. OBSERVED in git, and the commit message agrees.

### R2-H2-11  Completion roadmap rewritten from nine phases to eight
- Question: H2, relates to NONE
- What was attempted: `PROJECT-COMPLETION-ROADMAP.md` was added in 6445123 (2026-02-01, 1,408 lines) with nine phases: 1 Security and infrastructure, 2 Audio pipeline, 3 Bluetooth, 4 Chromecast, 5 Web output, 6 Complete Web UI, 7 Complete REST API, 8 Tests, 9 Systemd service (6445123:PROJECT-COMPLETION-ROADMAP.md:65, :202, :354, :500, :678, :758, :987, :1137, :1262). Commit 1491214 (2026-02-02) rewrote it (592 insertions, 936 deletions) into eight phases (PROJECT-COMPLETION-ROADMAP.md:144, :247, :365, :480, :608, :789, :909, :926). Sections present in the first version and absent after the rewrite: the FLAC encoder listing (6445123:PROJECT-COMPLETION-ROADMAP.md:635-676), the systemd service phase (6445123:PROJECT-COMPLETION-ROADMAP.md:1262), the capture options naming `pw-record` (6445123:PROJECT-COMPLETION-ROADMAP.md:229-230, :316-318) and "Key Technical Decisions" (6445123:PROJECT-COMPLETION-ROADMAP.md:1394-1402); `grep -n -i -E 'ffmpeg|pw-record'` on the file at 1d878c2 returns no line. Stated reason: 1491214, "Re-analyzed entire codebase to provide accurate current state".
- Presence: CURRENT for the eight-phase version (PROJECT-COMPLETION-ROADMAP.md at 1d878c2). The nine-phase version is REMOVED, commit 1491214.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. A planning document has no recorded result.
- Why it turned out that way: UNKNOWN. Not evidenced.
- Depends on: the two implementation commits use the nine-phase numbering ("PHASE 1: Security & Infrastructure Fixes" in eb02d59; "PHASE 9: Systemd Service" in add5474), and their branch started from 1285478, before the rewrite: the tree at add5474 holds the nine-phase file and no `AI-AGENT-IMPLEMENTATION-GUIDE.md` (`git show add5474:PROJECT-COMPLETION-ROADMAP.md`; `git ls-tree --name-only add5474`; parents from `git log --pretty='%h <- %p'`).
- Open questions: Why were the FLAC encoder and systemd phases left out of the rewrite? Was the eight-phase version ever used for any code?
- Confidence: HIGH for what changed (read from both versions in git). No outcome to grade.

### R2-H2-12  "Open Questions" section deleted from the requirements document and a no-authentication line added
- Question: H2, relates to S12.1
- What was attempted: `project-context.md` was created in f510135 with a section "## 12. Open Questions" listing "Preferred Linux distribution baseline", "Default Bluetooth codec priorities" and "Whether multi-user Web UI access is required" (f510135:project-context.md:256-260). Seventy-three seconds later, commit d72817b deleted the section and added one line under the web UI research task: "Single user only, so does not require authentication" (project-context.md:243). Stated reason: UNKNOWN. The commit message is "Update project-context.md" with no body.
- Presence: REMOVED for the section, commit d72817b. CURRENT for the added line (project-context.md:243).
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. Not evidenced.
- Depends on: the single-user statement. fixes.md:31 later lists "C-1: No Authentication on Web API" and patch1.md:556 lists it as not included, "(requires design decision on auth method)".
- Open questions: Were the first two open questions (distribution baseline, codec priorities) answered anywhere before they were deleted?
- Confidence: HIGH for what changed (read from `git show d72817b`). No outcome to grade.

### R2-H2-13  Web output encoding: Opus named in the plans, raw 16-bit PCM bytes written in the code
- Question: H2, relates to S08.2
- What was attempted: the architecture names Opus for the browser path ("**Recommended**: Option A (WebSocket + MSE with Opus encoding)", architecture-and-build-plan.md:1152) and the first roadmap lists "Web Audio: Opus over WebSocket with Web Audio API" (6445123:PROJECT-COMPLETION-ROADMAP.md:1399), while its code sketch for the same step carries the comment "# Encode as Opus or raw PCM" (6445123:PROJECT-COMPLETION-ROADMAP.md:342). The code added in eb02d59 converts float32 to int16 and passes the bytes on (audio_pipeline.py:320-323); add5474 sends them with `ws.send_bytes` (api_server.py:619-638). No Opus encoder call is in the code (`git grep -n -i opus -- '*.py'` returns no line). Stated reason: UNKNOWN. Neither commit message mentions Opus or the choice of PCM.
- Presence: CURRENT for raw PCM (audio_pipeline.py:311-325; api_server.py:619-638). Opus exists in documents only (architecture-and-build-plan.md:1142-1154; README.md:290-292).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Commit add5474 states "WebSocket streaming for browser audio playback" as a capability, with no run reported; no tracked frontend code opens the socket (/home/user/audio/repos/R2/01-map.md:131).
- Why it turned out that way: UNKNOWN. Not evidenced.
- Depends on: a WebSocket client able to play interleaved int16 PCM at 48 kHz; none is tracked (`git log -- audioburger/web_ui/frontend/src` shows 70fc363 only).
- Open questions: Was Opus encoding attempted and dropped, or never started? Was the binary stream ever received by a browser?
- Confidence: MEDIUM. INFERRED from clear code and document text.

### R2-H2-14  Chromecast stream encoding: FLAC through FFmpeg named in the plans, no encoder in the code
- Question: H2, relates to S08.2
- What was attempted: the architecture gives an FFmpeg command for FLAC ("# FLAC encoding via FFmpeg", architecture-and-build-plan.md:858-859) and the first roadmap gives a `FLACEncoder` class that starts an `ffmpeg` subprocess (6445123:PROJECT-COMPLETION-ROADMAP.md:635-676). The module rewritten in eb02d59 appends the pipeline's int16 bytes to a buffer and writes them to the HTTP response under the header `audio/<format>` (chromecast_output.py:131, :142-147, :163-167); no `subprocess` or `ffmpeg` appears in the code (`git grep -n -i -E 'ffmpeg|subprocess' -- '*.py'` returns no line). The module docstring still lists "FLAC/AAC encoding for audio streams" (chromecast_output.py:9). The roadmap rewrite 1491214 dropped the encoder section (see R2-H2-11). Stated reason: UNKNOWN. Commit eb02d59 lists "Implement HTTP streaming server with aiohttp" and does not mention encoding.
- Presence: CURRENT for the unencoded stream (chromecast_output.py:126-167). The FLAC encoder exists in documents only (architecture-and-build-plan.md:858-859) and in the superseded roadmap version, REMOVED by commit 1491214.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. Not evidenced.
- Depends on: `chromecast.format` "flac" (config.toml:22), used for the URL path and the header only (chromecast_output.py:113, :131); install.sh installs no ffmpeg package (/home/user/audio/repos/R2/01-map.md:209).
- Open questions: Was the encoder left out on purpose? Did any Chromecast device ever fetch this stream?
- Confidence: MEDIUM. INFERRED from clear code and from the two document versions.

### R2-H2-15  Second systemd unit definition added beside the installer's generated unit
- Question: H2, relates to S10.2
- What was attempted: the installer (70b5a5a, 2026-01-26) writes a unit with `Type=simple`, `Restart=always`, `RestartSec=5` and `After=network.target sound.target` (install.sh:968, :972, :976-977). Commit add5474 (2026-02-02) added a tracked `audioburger.service` with `Type=notify`, `WatchdogSec=60s`, `Restart=on-failure`, `RestartSec=10s` and `After=network.target bluetooth.target` (audioburger.service:3, :7, :14, :17-18). The tracked file matches the unit text of phase 9 in the first roadmap except for the PipeWire lines, which the roadmap had as `After=network.target bluetooth.target pipewire.service` and `Wants=pipewire.service` (6445123:PROJECT-COMPLETION-ROADMAP.md:1262-1300). Neither definition replaced the other: install.sh was not changed after 70b5a5a (`git log -- install.sh`) and does not copy the tracked file (/home/user/audio/repos/R2/01-map.md:66). Stated reason: add5474, "Create audioburger.service file with proper systemd integration"; no reason is given for keeping both.
- Presence: CURRENT for both (install.sh:965-993; audioburger.service:1-32).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No journal output or service status is tracked. patch1.md:540 lists "Test systemd integration in actual systemd environment" as still required.
- Why it turned out that way: UNKNOWN. Not evidenced.
- Depends on: the `sdnotify` package for `Type=notify` readiness (audioburger/main.py:29-33, :358-361); the paths `/opt/audioburger` and user `audioburger` named in the tracked unit (audioburger.service:9-10), which install.sh creates only in part (service user at install.sh:935-954; no `/opt/audioburger` in install.sh per `grep -n '/opt' install.sh`, which returns no line).
- Open questions: Which of the two units was meant to be installed? Was either ever started under systemd?
- Confidence: MEDIUM. INFERRED from two clear files and their commit dates.

---

## H3 Reverts and repeated fixes

### R2-H3-01  NOT FOUND
- Question: H3
- Searched: `git log --all -i --grep='revert'` (no commit); `git log --all -i --grep='fix'` (5 commits: eb02d59, b1c1a12, 6a9bbb0, b61e1ec, d51fcef); `git show --stat` on each of those five to list the files touched; `git show --cc` on all 15 merges (all empty); `git log --all --diff-filter=D --name-only` (one deletion, `readme.md` in deb899b); per-file commit counts from `git log --all --name-only --pretty=format: | sort | uniq -c | sort -rn`. Result: no commit reverts another, and no file or directory is touched by three or more commits whose message contains "fix".
- Nearest related material: two fix-type commits with file changes. b61e1ec ("Implement v2.0 security fixes and systemd watchdog integration") changed `audioburger/__init__.py`, `audioburger/config/config_manager.py`, `audioburger/main.py`, `audioburger/web_ui/api_server.py` and added `patch1.md`. eb02d59 (body: "PHASE 1: Security & Infrastructure Fixes" and "Fix status endpoint to return live component data") changed `audioburger/main.py` and `audioburger/web_ui/api_server.py` among seven files. The two files touched by both are `audioburger/main.py` and `audioburger/web_ui/api_server.py`, at two fix-type commits each. The other three matches carry no code change: d51fcef added `fixes.md` only ("No code changes made - this is assessment only per Phase 5 requirements."), and b1c1a12 and 6a9bbb0 are merges that match on their branch names.

---

## H4 Unmerged work

### R2-H4-01  NOT FOUND
- Question: H4
- Searched: `git branch -a --no-merged` (empty); `git branch -a --no-merged main` (empty); `git stash list` (empty); `git tag` (empty); `git for-each-ref` (four refs, all at 1d878c2: `refs/heads/main`, `refs/heads/claude/inspiring-galileo-a9dlia`, `refs/remotes/origin/main`, `refs/remotes/origin/claude/inspiring-galileo-a9dlia`); `git rev-list --count --all` against `git rev-list --count main` (35 and 35, per /home/user/audio/repos/R2/01-map.md:358).
- Nearest related material: the 15 merge subjects name 14 distinct pull-request source branches, for example `claude/security-fixes-v2.0-zv5Li` (b1c1a12) and `claude/implement-ai-agent-guide-9YcaS` (1d878c2); none exists as a ref in this clone and every commit they brought is reachable from `main` (`git log m^1..m^2` for each merge). The session branch `claude/inspiring-galileo-a9dlia` holds no work of its own (/home/user/audio/00-manifest.md:33). Whether the remote holds other branches was not readable from this clone (no fetch was run, Rule 1).

---

## H5 Dead and disabled code

### R2-H5-01  Bluetooth audio capture function is a placeholder that returns silence
- Question: H5, relates to S05.1
- What was attempted: `_capture_bluetooth()` was added in eb02d59 as the capture path for the Bluetooth source (audio_pipeline.py:222-232). Its body is one statement, `return np.zeros((self.config.buffer_size, self.config.channels), dtype=np.float32)` (audio_pipeline.py:232). Its docstring reads "For now, this is a placeholder that returns silence." (audio_pipeline.py:227) and its comment reads "# TODO: Implement Bluetooth audio capture via PipeWire" (audio_pipeline.py:229). The function is reached when the source is set to Bluetooth (audio_pipeline.py:199-200). The first roadmap had the same function with a body of `pass` (6445123:PROJECT-COMPLETION-ROADMAP.md:491-495); the rewritten roadmap shows it returning zeros with the comment "# This requires PipeWire setup" (PROJECT-COMPLETION-ROADMAP.md:356-359).
- Presence: DISABLED. Present and reachable, with the capture switched off by the placeholder body (audio_pipeline.py:229-232).
- Outcome: FAILED
- Outcome evidence: STATED. audio_pipeline.py:227, "For now, this is a placeholder that returns silence."
- Why it turned out that way: STATED that it was not written (audio_pipeline.py:229, TODO). The reason it was not written is UNKNOWN.
- Depends on: PipeWire exposing a Bluetooth input node, as the comment at audio_pipeline.py:230 assumes; no PipeWire dependency is in any manifest (/home/user/audio/repos/R2/01-map.md:206).
- Open questions: Why do the messages of eb02d59 ("Capture audio from line-in or Bluetooth A2DP sources") and add5474 ("All audio routing is now fully functional end-to-end") describe this path as working?
- Confidence: HIGH. STATED in the code and the code agrees (`return np.zeros`).

### R2-H5-02  Async get_paired_devices is shadowed by a synchronous method of the same name
- Question: H5, relates to S03.3
- What was attempted: commit eb02d59 added two methods named `get_paired_devices` to class `A2DPSink`: `async def` at a2dp_sink.py:205-242 and a plain `def` at a2dp_sink.py:244-278 whose docstring is "Synchronous version of get_paired_devices." (a2dp_sink.py:246). The two bodies query the same ObjectManager data; the async one adds two debug log calls (a2dp_sink.py:213, :237).
- Presence: DISABLED. INFERRED: in a Python class body a later definition of the same name replaces the earlier one, so the async version at :205 is unreachable. All three callers use the name without `await` (a2dp_sink.py:298, :340; api_server.py:501).
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. Not evidenced.
- Depends on: Python name binding in class bodies.
- Open questions: Were both versions meant to be kept under different names?
- Confidence: MEDIUM. INFERRED from clear code.

### R2-H5-03  Async discover_devices is shadowed by a synchronous method of the same name
- Question: H5, relates to S08.1
- What was attempted: commit eb02d59 added two methods named `discover_devices` to class `ChromecastOutput`: `async def` at chromecast_output.py:207-247 and a plain `def` at chromecast_output.py:249-281 whose docstring is "Synchronous version of discover_devices for compatibility." (chromecast_output.py:251). The async version stores the browser in `_discovery_browser` (chromecast_output.py:225, :241); the synchronous one does not.
- Presence: DISABLED. INFERRED: the later definition replaces the earlier one, so the async version at :207 is unreachable. The single caller uses no `await` (api_server.py:532).
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. The docstring says "for compatibility" without saying with what.
- Depends on: Python name binding in class bodies; `pychromecast.discovery.discover_chromecasts` (chromecast_output.py:263).
- Open questions: Compatibility with which caller was meant?
- Confidence: MEDIUM. INFERRED from clear code.

### R2-H5-04  Chromecast start_playback has no caller
- Question: H5, relates to S08.2
- What was attempted: `start_playback()` builds the stream URL and calls `mc.play_media(...)` (chromecast_output.py:361-389). It was added in eb02d59. `git grep -n -w start_playback -- '*.py'` returns the definition only. The HTTP route for Chromecast connection calls `connect()` and nothing further (api_server.py:537-551).
- Presence: DISABLED. Defined and unreachable from any tracked caller.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. Not evidenced.
- Depends on: an active cast object from `connect()` (chromecast_output.py:368-370).
- Open questions: Which component was meant to call it?
- Confidence: MEDIUM. INFERRED from a repository-wide search.

### R2-H5-05  Bluetooth disable_discoverable has no caller
- Question: H5, relates to S03.1
- What was attempted: `disable_discoverable()` sets the adapter's `Discoverable` property to false (a2dp_sink.py:193-203). `git grep -n -w disable_discoverable -- '*.py'` returns the definition only. The method existed as a stub in 70fc363 and received its body in eb02d59.
- Presence: DISABLED. Defined and unreachable from any tracked caller.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. Not evidenced.
- Depends on: an initialised adapter properties interface (a2dp_sink.py:195-197).
- Open questions: NONE.
- Confidence: MEDIUM. INFERRED from a repository-wide search.

### R2-H5-06  Unused class and attributes in the Bluetooth module
- Question: H5, relates to S03.3
- What was attempted: class `BluetoothDevice` with `address`, `name`, `trusted`, `connected` (a2dp_sink.py:25-32) and the attributes `_paired_devices` (a2dp_sink.py:53) and `_active_device` (a2dp_sink.py:54) date from the skeleton 70fc363. Commit eb02d59 added `_dbus_loop` (a2dp_sink.py:57), which is never assigned again, and `_adapter` (a2dp_sink.py:59), which is assigned at a2dp_sink.py:98 and never read. `git grep` for each name returns only these lines (the class name appears only in the two type annotations at :53-54).
- Presence: DISABLED. Present, with no reader.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. Not evidenced.
- Depends on: NONE recorded.
- Open questions: Was `BluetoothDevice.trusted` meant to back a trust list (the module docstring names "Device pairing and trust management", a2dp_sink.py:5)?
- Confidence: MEDIUM. INFERRED from a repository-wide search.

### R2-H5-07  Unused buffers and stream teardown in the audio pipeline
- Question: H5, relates to S05.4
- What was attempted: commit eb02d59 added `_audio_buffer` (audio_pipeline.py:66) and `_fft_buffer` (audio_pipeline.py:67), which are never read or written after construction, and `_sd_stream = None` (audio_pipeline.py:79) with teardown code for it in `stop()` (audio_pipeline.py:145-149). Nothing assigns a stream object to `_sd_stream`; capture uses the blocking call `sd.rec` (audio_pipeline.py:210-216). The docstring line "Buffer management and underrun recovery" remains in the module header (audio_pipeline.py:9).
- Presence: DISABLED. Present, with no reader (buffers) and an unreachable branch (teardown).
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. Not evidenced.
- Depends on: NONE recorded.
- Open questions: Was a callback-driven input stream planned (the guide document shows one, AI-AGENT-IMPLEMENTATION-GUIDE.md:281-305)?
- Confidence: MEDIUM. INFERRED from a repository-wide search.

### R2-H5-08  Unused discovered-device list in the Chromecast module
- Question: H5, relates to S08.1
- What was attempted: `_discovered_devices: List[ChromecastDevice] = []` (chromecast_output.py:56) dates from the skeleton 70fc363. Discovery results are returned as dictionaries and not stored (chromecast_output.py:265-277). `git grep -n _discovered_devices` returns line 56 only.
- Presence: DISABLED. Present, with no reader or writer.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. Not evidenced.
- Depends on: NONE recorded.
- Open questions: NONE.
- Confidence: MEDIUM. INFERRED from a repository-wide search.

### R2-H5-09  Configuration keys that are defined and have no reader, seven of the nine range-checked
- Question: H5, relates to S10.4
- What was attempted: the following nine keys are declared (config.toml and `config_manager.py` dataclasses) and read by no code outside `config_manager.py` (`git grep` for each name over `*.py`): `bluetooth.discoverable_timeout` (config.toml:7), `bluetooth.reconnect_attempts` (config.toml:8), `bluetooth.reconnect_backoff_base` (config.toml:9), `audio.eq_update_rate` (config.toml:16), `chromecast.discovery_timeout` (config.toml:20), `chromecast.reconnect_attempts` (config.toml:23), `web_ui.static_path` (config.toml:30), `logging.buffer_size` (config.toml:34), `logging.systemd_integration` (config.toml:35). Seven of the nine are range-checked since b61e1ec, once when the file is loaded and once in `_validate_config`: `bluetooth.discoverable_timeout` (audioburger/config/config_manager.py:293-295, :217-218), `bluetooth.reconnect_attempts` (audioburger/config/config_manager.py:296-298, :214-215), `bluetooth.reconnect_backoff_base` (audioburger/config/config_manager.py:299-301, :220-221), `audio.eq_update_rate` (audioburger/config/config_manager.py:322-324, :236-237), `chromecast.discovery_timeout` (audioburger/config/config_manager.py:336-338, :240-241), `chromecast.reconnect_attempts` (audioburger/config/config_manager.py:343-345, :249-250), `logging.buffer_size` (audioburger/config/config_manager.py:371-373, :261-262); `git blame -s` attributes each of those lines to b61e1ec. The other two have no check: `web_ui.static_path` and `logging.systemd_integration` are taken from the file with a plain `.get()` (audioburger/config/config_manager.py:358, :374), on lines that `git blame -s` attributes to the skeleton 70fc363, and neither name occurs in `_validate_config` (audioburger/config/config_manager.py:202-262). `audio.eq_bands` is read once, for a log entry (audio_pipeline.py:92). Fixed values stand where three of them would apply: the pairing handler returns the literal 120 (api_server.py:510) and `enable_discoverable` defaults to 120 (a2dp_sink.py:173); the log ring buffer is `deque(maxlen=1000)` (audioburger/logging/logger.py:39); discovery defaults to 5 seconds (chromecast_output.py:249).
- Presence: DISABLED. Present in configuration, with no effect on behaviour.
- Outcome: UNVERIFIED
- Outcome evidence: NONE for behaviour. The absence of a reader for the two Bluetooth reconnect keys is also STATED: "Use `reconnect_attempts` and `reconnect_backoff_base` from config (defined but unused)." (README.md:273), written at deb899b; the search above shows the same at 1d878c2.
- Why it turned out that way: UNKNOWN. Not evidenced.
- Depends on: the features the keys name. Reconnection is listed in the module docstrings ("Device reconnection with exponential backoff", a2dp_sink.py:7; "Connection health monitoring and auto-reconnect", chromecast_output.py:8) and no reconnection code exists (`git grep -n -i -E 'retry|backoff' -- '*.py'` returns config and docstring lines only).
- Open questions: NONE.
- Confidence: HIGH for the two reconnect keys (STATED and the code agrees). MEDIUM for the others (INFERRED from a repository-wide search).

### R2-H5-10  Empty packages control_plane and tests
- Question: H5, relates to S11.1
- What was attempted: `audioburger/control_plane/__init__.py` and `audioburger/tests/__init__.py` were added empty in 70fc363 and never changed (`git log -- audioburger/tests audioburger/control_plane` shows 70fc363 only; both are 0 lines). No module imports `control_plane` (`git grep -n control_plane -- '*.py'` returns one docstring example, audioburger/logging/logger.py:47). Test packages are active requirements since eb02d59 (requirements.txt:25-27).
- Presence: DISABLED. Present and empty.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The emptiness of the tests package is also STATED: "`audioburger/tests/` contains only `__init__.py`. No unit, integration, or E2E tests exist." (README.md:239) and "zero tests" (PROJECT-COMPLETION-ROADMAP.md:138).
- Why it turned out that way: UNKNOWN. Tests are phase 8 in both roadmap versions (6445123:PROJECT-COMPLETION-ROADMAP.md:1137; PROJECT-COMPLETION-ROADMAP.md:926); no commit names that phase.
- Depends on: NONE recorded.
- Open questions: What was `control_plane` meant to hold, given that the control code lives in `web_ui/api_server.py`?
- Confidence: HIGH. STATED, and the tree agrees.

### R2-H5-11  Inbound WebSocket text messages are accepted and dropped
- Question: H5, relates to S09.2
- What was attempted: the WebSocket handler's text branch logs "WebSocket message received" and does nothing else (api_server.py:402-405). The comments read "# Future: validate and parse as JSON command" and "# For now, don't echo raw user input (security)" (api_server.py:404-405). The branch took this form in b61e1ec, when the echo was removed (see R2-H2-07). The handler docstring still lists "Provide bidirectional control messaging" under "Future implementation will:" (api_server.py:353-356).
- Presence: DISABLED. The command channel is present and switched off.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. Not evidenced.
- Depends on: NONE recorded.
- Open questions: NONE.
- Confidence: MEDIUM. INFERRED from clear code and its comments.

### R2-H5-12  EQ data callback is called without await on a coroutine function
- Question: H5, relates to S09.2
- What was attempted: the processing loop calls `self._eq_callback(eq_bands)` as a plain call (audio_pipeline.py:176-178). Commit add5474 registered `self.api_server.broadcast_eq_data` as that callback (audioburger/main.py:198), and that method is declared `async def` (api_server.py:591). Both roadmap and guide documents show a different wiring, a wrapper that calls `asyncio.create_task(self.api_server.broadcast_eq_data(bands))` (PROJECT-COMPLETION-ROADMAP.md:919; AI-AGENT-IMPLEMENTATION-GUIDE.md:1399).
- Presence: DISABLED. INFERRED: calling a coroutine function without `await` or task creation returns a coroutine object and does not run its body, so the EQ broadcast at api_server.py:600-617 is unreachable by this path.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Commit add5474 states "Connect EQ visualization callback for real-time frequency band updates" with no run reported.
- Why it turned out that way: UNKNOWN. Not evidenced.
- Depends on: Python coroutine semantics; the tracked frontend has no EQ display and opens no WebSocket (/home/user/audio/repos/R2/01-map.md:131).
- Open questions: Was the EQ broadcast ever observed arriving at a client?
- Confidence: MEDIUM. INFERRED from clear code.

### R2-H5-13  Commented-out development tool requirements
- Question: H5, relates to S01.5
- What was attempted: three requirement lines are commented out under "# Development Tools (optional)": `# black>=23.0.0`, `# flake8>=6.0.0`, `# mypy>=1.0.0` (requirements.txt:29-32). They were commented out in the skeleton (70fc363) and stayed so when eb02d59 activated the other commented lines and changed the heading from "# Development Tools" to "# Development Tools (optional)" (`git show eb02d59 -- requirements.txt`).
- Presence: DISABLED. Commented out (requirements.txt:30-32).
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN beyond the word "optional" in the heading.
- Depends on: NONE recorded.
- Open questions: NONE.
- Confidence: HIGH for presence (read directly). No outcome to grade.

### R2-H5-14  Commented-out capability line in the installer's unit text
- Question: H5, relates to S10.3
- What was attempted: the unit text written by the installer contains "# Allow binding to privileged ports if needed" followed by `#AmbientCapabilities=CAP_NET_BIND_SERVICE` (install.sh:988-989). Present since 70b5a5a, the only commit to touch install.sh.
- Presence: DISABLED. Commented out (install.sh:989).
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. Configuration validation rejects ports below 1024 for both servers (patch1.md:212, :217), so the capability has no use with validated settings; that link is INFERRED, since no text connects the two.
- Depends on: `web_ui.http_port` and `chromecast.http_port` limits (audioburger/config/config_manager.py:243-254).
- Open questions: NONE.
- Confidence: HIGH for presence (read directly). No outcome to grade.

### R2-H5-15  Helper functions and imports with no user
- Question: H5, relates to S10.5
- What was attempted: defined and never called or used (`git grep -n -w` for each name): `StructuredLogger.clear_buffer` (audioburger/logging/logger.py:87-90); `get_log_level` (audioburger/logging/logger.py:211-213); `get_config`, defined at audioburger/config/config_manager.py:466 and imported at audioburger/main.py:21 without a call; `LogLevel`, imported at audioburger/main.py:20 without a use in that file; `get_real_user_home` in the installer (install.sh:208-211; `grep -c -w get_real_user_home install.sh` = 1).
- Presence: DISABLED. Present, with no caller.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. Not evidenced.
- Depends on: NONE recorded.
- Open questions: NONE.
- Confidence: MEDIUM. INFERRED from a repository-wide search.

### R2-H5-16  Server-side control routes and WebSocket broadcast have no tracked client
- Question: H5, relates to S09.1
- What was attempted: commit add5474 added eleven REST routes for inputs, outputs, Bluetooth, Chromecast and stream control (api_server.py:183-200) and `broadcast_audio` (api_server.py:619-638). The tracked frontend is the skeleton's status page, labelled "Main Application Component (STUB)" (audioburger/web_ui/frontend/src/App.svelte:3), with one request, `fetch('/api/v1/status')` (audioburger/web_ui/frontend/src/App.svelte:19). `git log -- audioburger/web_ui/frontend/src` shows 70fc363 only, so no frontend source was changed by eb02d59 or add5474. The frontend work is phase 6 in the first roadmap (6445123:PROJECT-COMPLETION-ROADMAP.md:758) and phase 5 in the rewrite (PROJECT-COMPLETION-ROADMAP.md:608); no commit names it.
- Presence: CURRENT for the server routes; unused by any tracked client (INFERRED from the search of `frontend/src` recorded at /home/user/audio/repos/R2/01-map.md:119).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Commit add5474 states "All endpoints properly handle errors and return JSON responses" with no request log.
- Why it turned out that way: UNKNOWN. Not evidenced.
- Depends on: an HTTP client outside the repo for every action other than viewing status.
- Open questions: Were the routes ever exercised with a command-line client?
- Confidence: MEDIUM. INFERRED from clear code and the single-commit history of the frontend source.

---

## H6 Author's markers

### R2-H6-01  Markers and notes, changelog and known-issues entries

Search used for the named marker set: `git grep -n -E '\b(TODO|FIXME|HACK|XXX|BUG|WORKAROUND)\b'` over all tracked files except `package-lock.json`, then the same case-insensitively. One code comment from the named set exists. Rows are grouped: (a) named-set markers, (b) other author markers in code, (c) `fixes.md` entries, (d) `patch1.md` entries, (e) status entries in `README.md`, (f) status entries in `PROJECT-COMPLETION-ROADMAP.md`, (g) known-issue and risk entries in research and design documents.

| location | marker | text (at most 25 words) | related stage |
|---|---|---|---|
| **(a) named-set markers** | | | |
| audioburger/audio_plane/audio_pipeline.py:229 | TODO | "Implement Bluetooth audio capture via PipeWire" | S05.1 |
| AI-AGENT-IMPLEMENTATION-GUIDE.md:531 | TODO (word named, not a marker) | "File must NOT contain \"STUB\", \"stub\", \"Future implementation\", or \"TODO\"" | S11.1 |
| AI-AGENT-IMPLEMENTATION-GUIDE.md:1449-1450 | TODO, FIXME (words named in a grep command) | "grep -r \"TODO\" audioburger/ --include=\"*.py\"" | S11.1 |
| AI-AGENT-IMPLEMENTATION-GUIDE.md:1547 | TODO, FIXME (checklist item) | "No \"TODO\" or \"FIXME\" comments" | S11.1 |
| **(b) other author markers in code (outside the named set)** | | | |
| audioburger/audio_plane/audio_pipeline.py:227 | "For now" | "For now, this is a placeholder that returns silence." | S05.1 |
| audioburger/audio_plane/audio_pipeline.py:231 | "For now" | "# For now, return silence" | S05.1 |
| audioburger/audio_plane/audio_pipeline.py:361-365 | "Future implementation" | "Configure PipeWire graph for selected source" (first of four listed items) | S05.1 |
| audioburger/audio_plane/audio_pipeline.py:392-395 | "Future implementation" | "Stop audio capture", "Teardown processing pipeline", "Flush buffers" | S05.3 |
| audioburger/audio_plane/audio_pipeline.py:407 | STUB | "# STUB: Future implementation will stop audio graph here" | S05.3 |
| audioburger/audio_plane/audio_pipeline.py:424-428 | "Future implementation" | "Reconfigure PipeWire graph" (second of four listed items) | S05.1 |
| audioburger/audio_plane/audio_pipeline.py:445-449 | "Future implementation" | "Reconfigure output handler" (second of four listed items) | S08.3 |
| audioburger/web_ui/api_server.py:2 | STUB | "Web UI and API Server stub module." | S09.1 |
| audioburger/web_ui/api_server.py:10 | STUB | "CURRENT STATUS: STUB - Basic HTTP server with minimal endpoints." | S09.1 |
| audioburger/web_ui/api_server.py:172-176 | "Future implementation" | "Full REST API for configuration" (first of four listed items) | S09.2 |
| audioburger/web_ui/api_server.py:353-356 | "Future implementation" | "Provide bidirectional control messaging" (third of three listed items) | S09.2 |
| audioburger/web_ui/api_server.py:404 | "Future" | "# Future: validate and parse as JSON command" | S09.2 |
| audioburger/web_ui/api_server.py:405 | "For now" | "# For now, don't echo raw user input (security)" | S12.1 |
| audioburger/web_ui/frontend/src/App.svelte:3-6 | STUB | "This is a minimal stub UI that displays system status and demonstrates API connectivity." | S09.1 |
| audioburger/main.py:284-285 | assumption note | "every 10 seconds (assuming WatchdogSec=30s in systemd service file)" | S10.2 |
| install.sh:17 | Note | "# Note: We don't use 'set -e' because we want explicit error handling" | S10.1 |
| **(c) fixes.md, "Fixes Required Before Release" (added d51fcef; status column text from patch1.md)** | | | |
| fixes.md:31 | C-1 | "No Authentication on Web API". patch1.md:556: not included, "(requires design decision on auth method)" | S12.1 |
| fixes.md:85 | C-2 | "Missing Input Validation on API Endpoints". Addressed per patch1.md:21 | S12.1 |
| fixes.md:169 | C-3 | "WebSocket Denial of Service". Addressed per patch1.md:87 | S12.1 |
| fixes.md:245 | C-4 | "Unchecked Configuration File Parsing". Addressed per patch1.md:168 | S10.4 |
| fixes.md:337 | C-5 | "Race Condition in Shutdown Handling". Addressed per patch1.md:273 | S10.2 |
| fixes.md:430 | C-6 | "Incomplete WebSocket Client Cleanup". Marked "(Partial)" at patch1.md:471 | S09.4 |
| fixes.md:520 | H-1 | "No Resource Limits". patch1.md:557: not included, "(requires monitoring implementation)" | S10.2 |
| fixes.md:570 | H-2 | "No Error Recovery for Component Failures". patch1.md:558: not included, "(requires retry logic design)" | S10.2 |
| fixes.md:638 | H-3 | "Missing Timeout Handling". patch1.md:559: not included | S10.2 |
| fixes.md:676 | H-4 | "Log Injection Vulnerability". patch1.md:560: not included | S10.5 |
| fixes.md:756 | H-5 | "Missing Configuration Reload Validation". Marked "(H-5 Partial)" at patch1.md:507 | S10.4 |
| fixes.md:852 | H-6 | "Frontend XSS Vulnerability (Future Risk)". patch1.md:561: not included | S12.1 |
| fixes.md:936 | H-7 | "No HTTPS/TLS Support". patch1.md:562: not included, "(requires cert management design)" | S12.1 |
| fixes.md:1018 | H-8 | "No Systemd Watchdog Integration". Addressed per patch1.md:328 | S10.2 |
| fixes.md:1090 | M-1 | "Missing Component Health Checks". patch1.md:563: not included | S10.5 |
| fixes.md:1102 | M-2 | "No Metrics/Observability". patch1.md:563: not included | S10.5 |
| fixes.md:1117 | M-3 | "Logging Ring Buffer Race Condition". patch1.md:563: not included | S10.5 |
| fixes.md:1139 | M-4 | "No Log Rotation for Large Logs". patch1.md:563: not included | S10.5 |
| fixes.md:1159 | M-5 | "Frontend Auto-Refresh Creates Unnecessary Load". patch1.md:563: not included | S09.2 |
| fixes.md:1169 | M-6 | "Missing CORS Configuration". patch1.md:563: not included | S12.1 |
| fixes.md:1203 | M-7 | "No Request ID Tracing". patch1.md:563: not included | S10.5 |
| fixes.md:1232 | M-8 | "Signal Handlers Not Removed on Shutdown". patch1.md:563: not included | S10.2 |
| fixes.md:1257 | L-1 | "Hardcoded Version String". Addressed per patch1.md:429 | S10.6 |
| fixes.md:1267 | L-2 | "Missing Type Hints in Some Functions". patch1.md:564: not included | NONE |
| fixes.md:1277 | L-3 | "No Graceful Degradation for Missing Static Files". patch1.md:564: not included | S09.4 |
| fixes.md:1289 | L-4 | "Magic Numbers Throughout Codebase". patch1.md:564: not included | NONE |
| fixes.md:1302 | L-5 | "No User-Friendly Error Messages". patch1.md:564: not included | S09.4 |
| fixes.md:1323 | L-6 | "Inconsistent Logging Styles". patch1.md:564: not included | S10.5 |
| fixes.md:1338 | L-7 | "No Signal Handling for Audio Components (Future)". patch1.md:564: not included | S10.2 |
| fixes.md:1348 | L-8 | "No Automated Dependency Updates". patch1.md:564: not included | S10.6 |
| fixes.md:1360 | D-1 | "Add Structured Logging to systemd Journal Fields". Not mentioned in patch1.md | S10.5 |
| fixes.md:1370 | D-2 | "Add Configuration Schema Validation with JSON Schema". Not mentioned in patch1.md | S10.4 |
| fixes.md:1380 | D-3 | "Implement Graceful WebSocket Reconnection in Frontend". Not mentioned in patch1.md | S09.4 |
| fixes.md:1390 | D-4 | "Add HTTP/2 Support". Not mentioned in patch1.md | S12.1 |
| fixes.md:1402 | D-5 | "Add Compression for API Responses". Not mentioned in patch1.md | S09.1 |
| fixes.md:1417 | D-6 | "Add OpenAPI/Swagger Documentation". Not mentioned in patch1.md | S09.1 |
| fixes.md:1427 | D-7 | "Add Integration Tests". Not mentioned in patch1.md | S11.1 |
| fixes.md:1439 | D-8 | "Add Performance Benchmarks". Not mentioned in patch1.md | S11.3 |
| fixes.md:1449-1587 | appendices A to D | "Testing Recommendations", "Security Checklist", "Reliability Checklist", "Implementation Priority" (headings) | S11.1 |
| **(d) patch1.md, "Fixes Implemented v2.0" (added b61e1ec)** | | | |
| patch1.md:3-5 | release header | "Release Date: 2026-01-26", "Version: 2.0.0", "Previous Version: 0.1.0-stub" | S10.6 |
| patch1.md:21 | C-2 | request size limit, JSON error handling, type check and allowlist on `/api/v1/log-level` (patch1.md:37-55) | S12.1 |
| patch1.md:87 | C-3 | connection limit 10, message limit 64 KB, 100 messages per second, lock on client list (patch1.md:103-123) | S12.1 |
| patch1.md:126 | C-3, item 5 | "Removed echo functionality (security risk for XSS)" | S09.2 |
| patch1.md:136-138 | C-3, open items | "Implement global rate limiting across all connections (not just per-connection)" | S12.1 |
| patch1.md:168 | C-4 | TOML error handling, value validation, reload with rollback (patch1.md:184-233) | S10.4 |
| patch1.md:241-246 | C-4, alternative | "Research recommended Pydantic for modern Python config validation." | S10.4 |
| patch1.md:273 | C-5 | shutdown lock and flag, SIGABRT handler, duplicate stop prevention (patch1.md:289-305) | S10.2 |
| patch1.md:328 | H-8 | sdnotify, READY, WATCHDOG heartbeat every 10 seconds, health check, STOPPING (patch1.md:343-374) | S10.2 |
| patch1.md:386-393 | H-8, required unit settings | "Type=notify", "WatchdogSec=30s", "Restart=on-failure", "RestartSec=5s" | S10.2 |
| patch1.md:429 | L-1 | "Version was hardcoded as `\"0.1.0-stub\"` in multiple locations, making version management difficult." | S10.6 |
| patch1.md:471 | C-6 (Partial) | lock on client list, guarded removal, grace period at stop (patch1.md:481-498) | S09.4 |
| patch1.md:507 | H-5 (Partial) | "SIGHUP triggers config reload with validation and component restart if needed." | S10.4 |
| patch1.md:537-542 | testing still required | "Regression Testing Required" with five items, including "Test systemd integration in actual systemd environment" | S11.1 |
| patch1.md:544-548 | testing still required | "Security Testing Required" with four items | S11.1 |
| patch1.md:552-566 | deferred issues | "The following issues from fixes.md are **NOT** included in this patch" (C-1, H-1 to H-4, H-6, H-7, M-1 to M-8, L-2 to L-8) | S11.4 |
| patch1.md:570-583 | dependency change | "`sdnotify` - Systemd notification library (optional, graceful degradation if not available)" | S01.5 |
| patch1.md:589-592 | upgrade notes | "If using systemd, update service file with `Type=notify` and `WatchdogSec=30s`" | S10.2 |
| **(e) README.md status section (written deb899b, before eb02d59 and add5474)** | | | |
| README.md:7 | status heading | "Current Status: Phase 4, Step 1 - Foundation & Skeleton (NOT a working application)" | S11.4 |
| README.md:220-221 | incomplete | "The `/api/v1/status` response is a hardcoded dictionary." | S09.2 |
| README.md:223-224 | incomplete | "The component is written and will work — but it **cannot be served**." | S09.1 |
| README.md:228-229 | stub | "All methods are stubs. `start()` sets `_running = True` and logs. No BlueZ, no D-Bus, no pairing, no audio reception." | S04.1 |
| README.md:231-232 | stub | "All methods are stubs. Enums and state variables are defined. No PipeWire, no resampling, no FFT, no EQ, no audio routing." | S05.1 |
| README.md:234-235 | stub | "All methods are stubs. No mDNS discovery, no HTTP streaming server, no PyChromecast, no encoding." | S08.2 |
| README.md:239 | not started | "`audioburger/tests/` contains only `__init__.py`. No unit, integration, or E2E tests exist." | S11.1 |
| README.md:240 | not started | "`requirements.txt` has all audio/Bluetooth/Chromecast/test dependencies commented out." | S01.5 |
| README.md:244-320 | to-do tables | eight sprint tables of items still to be written; for example README.md:273, reconnect settings "(defined but unused)" | S11.4 |
| **(f) PROJECT-COMPLETION-ROADMAP.md status section (written 1491214, before eb02d59 and add5474)** | | | |
| PROJECT-COMPLETION-ROADMAP.md:80 | not working | "Audio Pipeline": "All methods are stubs" (PROJECT-COMPLETION-ROADMAP.md:82) | S05.1 |
| PROJECT-COMPLETION-ROADMAP.md:95 | not working | "Bluetooth A2DP Sink": "All methods are stubs" (PROJECT-COMPLETION-ROADMAP.md:97) | S04.1 |
| PROJECT-COMPLETION-ROADMAP.md:111 | not working | "Chromecast Output": "All methods are stubs" (PROJECT-COMPLETION-ROADMAP.md:113) | S08.2 |
| PROJECT-COMPLETION-ROADMAP.md:126-134 | issues | "**NOT SERVED** - API server has no static file route", "No EQ visualization", "No input/output controls", "No device management UI" | S09.1 |
| PROJECT-COMPLETION-ROADMAP.md:136-138 | not working | "Directory contains only `__init__.py` - **zero tests**." | S11.1 |
| PROJECT-COMPLETION-ROADMAP.md:1031-1045 | checklist | eleven unchecked items, for example "Bluetooth devices can be paired and connected" | S11.1 |
| **(g) known-issue and risk entries in research and design documents (about outside software and about the design, written before any code)** | | | |
| bluetooth-a2dp-sink-research.md:278 | known bug 1 | "Profile Unavailability After Reconnection (BlueZ Kernel Bug)" | S04.3 |
| bluetooth-a2dp-sink-research.md:286 | known bug 2 | "Auto-Connection Audio Failures" | S04.3 |
| bluetooth-a2dp-sink-research.md:292 | known bug 3 | "Aggressive Auto-Reconnect Breaks Device Switching" | S04.4 |
| bluetooth-a2dp-sink-research.md:298 | known bug 4 | "Suspend/Resume Reconnection Failures" | S04.3 |
| bluetooth-a2dp-sink-research.md:307 | known bug 5 | "BlueZ 5.83+ Regression (Recent)" | S01.3 |
| bluetooth-a2dp-sink-research.md:316 | stability issue 1 | "Multiple Instance Crashes" (WirePlumber) | S10.2 |
| bluetooth-a2dp-sink-research.md:322 | stability issue 2 | "Cache Corruption" (WirePlumber) | S10.2 |
| research-chromecast-output.md:448 | known issue 1 | "Live Streaming Buffer Delays (PyChromecast)" | S07.2 |
| research-chromecast-output.md:458 | known issue 2 | "Version Compatibility Issues (mkchromecast + PyChromecast)" | S01.5 |
| research-chromecast-output.md:467 | known issue 3 | "Router Multicast UDP Filtering" | S08.3 |
| research-chromecast-output.md:477 | known issue 4 | "Codec-Specific Delay (MP3 + lame)" | S07.2 |
| research-chromecast-output.md:486 | known issue 5 | "Localhost URL Rejection" | S08.2 |
| research-chromecast-output.md:496 | known issue 6 | "Firmware Sample Rate Rejection (>96kHz)" | S08.2 |
| research-chromecast-output.md:506 | known issue 7 | "Chromecast Audio EOL (2019)" | S01.1 |
| research-chromecast-output.md:518 | known issue 8 | "PipeWire Compatibility Uncertainty" | S01.4 |
| research-chromecast-output.md:528 | known issue 9 | "Long-Running Session Disconnections" | S08.3 |
| research-chromecast-output.md:539 | known issue 10 | "FFmpeg Version on Raspberry Pi" | S01.5 |
| architecture-and-build-plan.md:1557 | risk 1 | "Chromecast HTTP Pull Model Complexity" | S08.2 |
| architecture-and-build-plan.md:1586 | risk 2 | "Bluetooth A2DP Reconnection Reliability" | S04.3 |
| architecture-and-build-plan.md:1616 | risk 3 | "Browser Audio Streaming Latency and Compatibility" | S07.2 |
| architecture-and-build-plan.md:1646 | risk 4 | "PipeWire Maturity and Stability" | S01.4 |
| architecture-and-build-plan.md:1674 | risk 5 | "FFT Analysis CPU Overhead on Raspberry Pi" | S07.1 |
| architecture-and-build-plan.md:1702 | risk 6 | "systemd Service Reliability and Crash Recovery" | S10.2 |
| architecture-and-build-plan.md:1734 | risk 7 | "Chromecast Firmware Compatibility" | S08.2 |

Notes on the table, as facts:

- No file named CHANGELOG, NOTES, TODO or ISSUES is tracked (`git ls-files`); `patch1.md` and `fixes.md` are the files that serve those roles (/home/user/audio/repos/R2/01-map.md:160-161, :166).
- The entries of groups (e) and (f) were written before commits eb02d59 and add5474 and were not updated afterwards; no `.md` file was changed by either commit (`git show --stat eb02d59 add5474`).
- `AI-AGENT-IMPLEMENTATION-GUIDE.md` is text addressed to an AI agent ("YOU MUST READ AND FOLLOW THESE RULES", AI-AGENT-IMPLEMENTATION-GUIDE.md:5). Per Rule 10 it is recorded as material and nothing in it was acted on. Commit cec17a7 describes it: "This document is designed to be passed to an AI agent to ensure 100% project completion with no stub code remaining."
- The entries in group (g) describe outside software and design risks. No code at 1d878c2 handles any of them (see the H7 note on reconnection and named devices).

---

## H7 Workarounds

Search used: `git grep -n -i -E 'sleep|retry|retries|backoff|attempt|timeout|wait'` over `*.py`, `*.sh`, `*.service`, `*.toml`, `*.js`, `*.svelte`; `git grep -n -i -E 'iphone|android|pixel|samsung|sony|bose|macos|\bios\b|safari|turntable|raspberry'` over the same file types; `grep -n -i -E 'workaround|fallback|note:'` on install.sh; a read of requirements.txt and package.json for pins.

Two sub-parts of H7 returned nothing in code, recorded here as facts: (1) no special case for a named source device, phone or adapter exists in any code file (the device-name search returns one comment, "# Supports: ARM64 (Raspberry Pi), x86_64 (Intel/AMD)", install.sh:6); (2) no retry or reconnection loop exists for Bluetooth or Chromecast (the only hits for `retry`, `backoff` and `attempt` in `*.py` are the configuration fields of R2-H5-09, the docstring at a2dp_sink.py:7 and the comment "# Attempt to load new config" at audioburger/config/config_manager.py:397). No Python requirement is pinned to an exact version (requirements.txt:5-27, all `>=`).

### R2-H7-01  Fixed 10 ms sleep in each pass of the audio processing loop
- Question: H7, relates to S05.4
- What was attempted: the loop ends each pass with `await asyncio.sleep(0.01)  # ~100Hz processing rate` (audio_pipeline.py:185), whether or not audio was captured. Added in eb02d59. The line-in capture in the same pass is a blocking `sd.rec(..., blocking=True)` of `buffer_size` frames (audio_pipeline.py:210-216), 1,024 frames at 48,000 Hz by default (config.toml:12-13).
- Presence: CURRENT (audio_pipeline.py:185).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No timing, underrun or load measurement is tracked.
- Why it turned out that way: UNKNOWN. The problem the sleep addresses is not stated beyond the rate comment.
- Depends on: `audio.buffer_size` and `audio.sample_rate` (config.toml:12-13); the asyncio event loop shared with both HTTP servers (audioburger/main.py:206-232).
- Open questions: What gap between captured buffers does the added 10 ms produce? Was continuous audio ever heard through this loop?
- Confidence: MEDIUM. Presence read directly; effect not evidenced.

### R2-H7-02  100 ms delay after an exception in the audio processing loop
- Question: H7, relates to S05.4
- What was attempted: on any exception other than cancellation, the loop logs "Error in audio processing loop" and sleeps 0.1 s before the next pass (audio_pipeline.py:189-191). Added in eb02d59. There is no counter and no limit on repeats.
- Presence: CURRENT (audio_pipeline.py:189-191).
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. Not evidenced.
- Depends on: errors raised inside the loop body; errors inside the capture, FFT, resample and output helpers are caught in those helpers first (audio_pipeline.py:218-220, :273-276, :300-302, :324-325, :340-341).
- Open questions: NONE.
- Confidence: MEDIUM. Presence read directly; effect not evidenced.

### R2-H7-03  50 ms polling sleep in the Chromecast stream handler
- Question: H7, relates to S08.3
- What was attempted: the HTTP stream handler loops forever; when the shared buffer is empty it waits `await asyncio.sleep(0.05)  # 50ms polling` (chromecast_output.py:140-149). Added in eb02d59. The loop has no exit condition other than an exception from the write (chromecast_output.py:151-152).
- Presence: CURRENT (chromecast_output.py:149).
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. Not evidenced.
- Depends on: `add_audio_data` filling the buffer (chromecast_output.py:156-167); one buffer shared by all connected stream clients (chromecast_output.py:142-144).
- Open questions: What happens when two clients request the stream at once?
- Confidence: MEDIUM. Presence read directly; effect not evidenced.

### R2-H7-04  1 MB cap on the Chromecast stream buffer
- Question: H7, relates to S05.4
- What was attempted: `add_audio_data` appends to a bytes buffer and, above 1,048,576 bytes, keeps the last 1,048,576 bytes: "# Limit buffer size to prevent memory issues" and "# 1MB limit" (chromecast_output.py:165-167). Added in eb02d59. The buffer grows whenever the destination is Chromecast, whether or not any client is reading (audio_pipeline.py:327-341).
- Presence: CURRENT (chromecast_output.py:165-167).
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: STATED for the purpose: "Limit buffer size to prevent memory issues" (chromecast_output.py:165). No observed memory problem is recorded.
- Depends on: int16 stereo at 48 kHz, 192,000 bytes per second, so the cap holds about 5.5 seconds (INFERRED by arithmetic from audio_pipeline.py:337-338 and config.toml:12-14).
- Open questions: Was the cut at an arbitrary byte offset ever heard as a fault?
- Confidence: MEDIUM. STATED purpose alone.

### R2-H7-05  One-second grace sleep when the API server stops
- Question: H7, relates to S10.2
- What was attempted: `APIServer.stop()` closes all WebSockets, then waits `await asyncio.sleep(1.0)` under the comment "# Wait for handlers to finish (grace period)" before clearing the client list (api_server.py:134-143). Added in b61e1ec as part of C-6; patch1.md:493 lists "Grace period for handlers to finish (line 128)".
- Presence: CURRENT (api_server.py:139-140).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. patch1.md:542 lists "Verify graceful shutdown under all signal types" as still required.
- Why it turned out that way: STATED for the problem: "WebSocket client list cleanup had race condition and missing error handling." (patch1.md:474). The item is marked "(Partial)" (patch1.md:471).
- Depends on: handlers finishing inside one second.
- Open questions: What remains of C-6 after the partial change?
- Confidence: MEDIUM. STATED evidence alone.

### R2-H7-06  Watchdog heartbeat every 10 seconds, with three different watchdog settings in the repo
- Question: H7, relates to S10.2
- What was attempted: commit b61e1ec added a heartbeat task that sends `WATCHDOG=1` after a health check and sleeps 10 seconds (audioburger/main.py:280-306). The problem it addresses is stated: "If application hangs (deadlock, infinite loop), systemd would never know and wouldn't restart the service." (patch1.md:331). The interval was written for a 30-second watchdog: "assuming WatchdogSec=30s in systemd service file" (audioburger/main.py:284-285) and "update service file with `Type=notify` and `WatchdogSec=30s`" (patch1.md:590). The tracked unit added later sets `WatchdogSec=60s` (audioburger.service:14). The unit the installer writes is `Type=simple` with no `WatchdogSec` line (install.sh:965-993).
- Presence: CURRENT (audioburger/main.py:280-306).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. patch1.md:540 lists systemd testing as still required.
- Why it turned out that way: UNKNOWN. Not evidenced.
- Depends on: the `sdnotify` package, without which the task is never started (audioburger/main.py:84, :367-368); a unit with `Type=notify` and a watchdog interval; which of the two units is installed (see R2-H2-15).
- Open questions: Was the service ever run under either unit? With the installer's unit, is the heartbeat sent to anything?
- Confidence: MEDIUM. STATED purpose and clear code; result not evidenced.

### R2-H7-07  Two-second sleep before the installer's HTTP check
- Question: H7, relates to S10.1
- What was attempted: the installer's verification step runs `sleep 2  # Give service time to start` and then requests `/api/v1/status` with curl; on failure it prints a warning, "Web UI not responding (may need more time to start)", and does not count an error (install.sh:1080-1086). Present since 70b5a5a.
- Presence: CURRENT (install.sh:1080).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No installer log is tracked (`.gitignore:46` ignores `*.log`).
- Why it turned out that way: STATED for the purpose in the comment (install.sh:1080). No observed start time is recorded.
- Depends on: the service being started by the installer (install.sh:1002-1009) and reaching its listening state within two seconds.
- Open questions: Was the installer ever run to this step on any machine?
- Confidence: MEDIUM. STATED purpose alone.

### R2-H7-08  Installer port search across up to 100 successive ports, with a /dev/tcp fallback check
- Question: H7, relates to S10.1
- What was attempted: `find_available_port` tries the start port and up to 99 following ports (install.sh:481-494; `max_attempts` default 100 at install.sh:483). The availability check falls back to opening `/dev/tcp/127.0.0.1/<port>` when no port-listing tool is found ("# Fallback: try to bind to the port", install.sh:476-477). Present since 70b5a5a, whose message lists "Detects port conflicts (8080 for Web UI, 8081 for Chromecast)".
- Presence: CURRENT (install.sh:476-494).
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. No observed port conflict is recorded.
- Depends on: default ports 8080 and 8081 (install.sh:39-40); bash `/dev/tcp` support for the fallback.
- Open questions: NONE.
- Confidence: MEDIUM. Presence read directly; result not evidenced.

### R2-H7-09  tomllib import with tomli fallback, and a requirement comment explaining the tomli line
- Question: H7, relates to S01.5
- What was attempted: `try: import tomllib  # Python 3.11+ standard library` with `except ImportError: import tomli as tomllib  # Fallback for older Python` (audioburger/config/config_manager.py:13-16), added in b61e1ec in place of the direct `import tomli` of the skeleton (70fc363:audioburger/config/config_manager.py:10). The requirement line carries a comment: "Python 3.11+ has tomllib built-in for reading, but tomli is more flexible" (requirements.txt:8). The package requires Python 3.11 or later (setup.py:36).
- Presence: CURRENT (audioburger/config/config_manager.py:13-16; requirements.txt:8).
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. With `python_requires='>=3.11'` the fallback branch is not reached on a supported interpreter (INFERRED from setup.py:36); no text says why it was kept.
- Depends on: Python version; `tomli>=2.0.1` as a lower bound (requirements.txt:8).
- Open questions: NONE.
- Confidence: MEDIUM. Presence read directly; result not evidenced.

### R2-H7-10  Node.js 20 sources used for a stated minimum of Node.js 18
- Question: H7, relates to S10.1
- What was attempted: the installer sets `MIN_NODE_VERSION="18"` (install.sh:27) and, on apt systems without a new enough Node.js, pipes the NodeSource `setup_20.x` script into bash under the comment "# Try to use NodeSource repository for latest Node.js" (install.sh:417-421); on dnf it runs `dnf module enable nodejs:20` (install.sh:429). A failure of the NodeSource step prints a warning and continues to the distribution package (install.sh:421-423). Present since 70b5a5a.
- Presence: CURRENT (install.sh:417-431).
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. No text states which distribution's Node.js version was too old.
- Depends on: network access to the NodeSource host; `curl` (install.sh:419); Vite 5 and Svelte 4 in the frontend manifest (audioburger/web_ui/frontend/package.json:12-14).
- Open questions: NONE.
- Confidence: MEDIUM. Presence read directly; result not evidenced.

---

## H8 Final state

### R2-H8-01  Final state of the repository

Subjects of the last ten commits, newest first (`git log --all --date=short --pretty='%h %ad %s'`):

1. 1d878c2 2026-02-02 "Merge pull request #15 from tunlezah/claude/implement-ai-agent-guide-9YcaS"
2. 3c78b1c 2026-02-02 "Merge pull request #14 from tunlezah/claude/project-analysis-roadmap-9Ycyz"
3. add5474 2026-02-02 "Implement Phases 5, 7, and 9: Web Output, REST API, and systemd service"
4. eb02d59 2026-02-02 "Implement Phases 1-4: Core audio routing functionality"
5. cec17a7 2026-02-02 "Add comprehensive AI Agent Implementation Guide for project completion"
6. 1491214 2026-02-02 "Update project completion roadmap with fresh analysis"
7. 1285478 2026-02-01 "Merge pull request #13 from tunlezah/claude/project-analysis-roadmap-9Ycyz"
8. 6445123 2026-02-01 "Add comprehensive project completion roadmap for AI implementation"
9. b1c1a12 2026-02-01 "Merge pull request #12 from tunlezah/claude/security-fixes-v2.0-zv5Li"
10. ad70621 2026-02-01 "Merge pull request #11 from tunlezah/claude/universal-linux-installer-Dtnjq"

Uncommitted changes:

- The manifest records `git status --porcelain` as empty and a stash count of 0 for R2 (/home/user/audio/00-manifest.md:23). The same commands returned nothing during this audit, and HEAD was still 1d878c2e1b15c4cdb2baf342c30fbb247d491737.
- No unmerged branch, stash or tag exists (see R2-H4-01).

Whether the repo was left mid-change. In git terms nothing is pending: the tree is clean and every commit is merged. In content terms the last work stops partway through its own plan. Grade: INFERRED, from the facts below taken together; each one is cited.

- The last two code commits claim completion: "All core audio routing components are now functional." (eb02d59) and "All audio routing is now fully functional end-to-end" (add5474). The Bluetooth capture path added by the first of them is marked "For now, this is a placeholder that returns silence." (audio_pipeline.py:227) with a TODO (audio_pipeline.py:229). See R2-H5-01.
- The two commits name phases 1 to 4, then 5, 7 and 9, of the nine-phase roadmap (6445123:PROJECT-COMPLETION-ROADMAP.md:65-1262). Phase 6 "Complete Web UI" (6445123:PROJECT-COMPLETION-ROADMAP.md:758) and phase 8 "Tests" (6445123:PROJECT-COMPLETION-ROADMAP.md:1137) are named in no commit. `audioburger/web_ui/frontend/src` and `audioburger/tests` were last changed in 70fc363 (`git log -- audioburger/web_ui/frontend/src audioburger/tests`).
- Within the phases that are named, parts listed in that roadmap are absent from the code: the FLAC encoder of phase 4.2 (see R2-H2-14) and the browser playback code of phase 5.2 (6445123:PROJECT-COMPLETION-ROADMAP.md:707; no `AudioContext` or `WebSocket` in `frontend/src`, /home/user/audio/repos/R2/01-map.md:131).
- The guide document was merged 22 seconds before the implementation pull request (3c78b1c 14:08:50 +1100; 1d878c2 14:09:12 +1100). The implementation branch started from 1285478 and did not contain the guide or the rewritten roadmap (`git ls-tree --name-only add5474`; `git log --pretty='%h <- %p'`). At 1d878c2 the guide's own completion list has items the tree does not meet: "No \"TODO\" or \"FIXME\" comments" (AI-AGENT-IMPLEMENTATION-GUIDE.md:1547) against audio_pipeline.py:229; "No \"STUB\" or \"stub\" in any Python file" (AI-AGENT-IMPLEMENTATION-GUIDE.md:1545) against api_server.py:2, :10 and audio_pipeline.py:407; "All tests pass" (AI-AGENT-IMPLEMENTATION-GUIDE.md:1565) against an empty tests package (R2-H5-10).
- No document was updated after the code changed: README.md:7 still reads "Current Status: Phase 4, Step 1 - Foundation & Skeleton (NOT a working application)", and api_server.py:10 still reads "CURRENT STATUS: STUB - Basic HTTP server with minimal endpoints."
- Two systemd unit definitions with different settings stand side by side (R2-H2-15), and three version values differ: 2.0.0 (audioburger/__init__.py:5), 0.1.0 (setup.py:31), 0.1.0 (audioburger/web_ui/frontend/package.json:3).
- The last non-merge commit was made 1 minute 49 seconds after the one before it (R2-H1-01), and no commit follows the merge of pull request #15.

---

## Tally

- Questions answered with findings: 6 (H1, H2, H5, H6, H7, H8)
- Questions answered NOT FOUND: 2 (H3, H4)
- Total: 8

Record count: 44 finding records (1 list for H1, 15 for H2, 16 for H5, 1 table for H6, 10 for H7, 1 list for H8) and 2 NOT FOUND records (R2-H3-01, R2-H4-01).
