# R1 history: /home/user/a2dplaya

History auditor output. Read-only. All paths are relative to /home/user/a2dplaya unless they start with a slash. Line numbers refer to the tree at commit 70ad071 (branch main; the checked-out branch claude/inspiring-galileo-a9dlia points at the same commit, /home/user/audio/00-manifest.md:22). Nothing was checked out. File content was read with `git show HEAD:PATH` and `git grep`.

Commands run, all inside the repo: `git log --all` (with `--date=short`, `--date=iso`, `--stat`, `--name-only`, `--diff-filter=D`, and `-i --grep` for the terms listed in R1-H3-01), `git show`, `git diff --stat`, `git branch -a -vv`, `git branch -a --no-merged`, `git branch -a --no-merged main`, `git stash list`, `git tag`, `git for-each-ref`, `git reflog --all`, `git rev-list --count`, `git rev-parse`, `git status --porcelain --ignored`, `git ls-files`, `git grep`, `git count-objects -v`, `git fsck --unreachable --no-reflogs`, `git fsck --lost-found --no-reflogs`. The last command can write to .git/lost-found when dangling objects exist; it printed nothing, and .git/lost-found does not exist after the run (`ls .git`). `git status --porcelain --ignored` is empty and `git rev-parse HEAD` returns 70ad071385a8dc79886a6b5596495f68cf9eb824 after all commands.

## H1 Timeline

- Commit count: 4 reachable from all refs, 4 on main (`git rev-list --all --count`, `git rev-list --count main`). Three are ordinary commits and one is a merge (commit 70ad071, parents 673e119 and d78a903).
- First commit: 673e119, author date 2026-02-22 09:40:08 +1100, subject "Create README.md", author name tunlezah, committer name GitHub (`git log --all --date=iso`). README.md at this commit is one byte, a newline (`git show 673e119:README.md | od -c`).
- Second commit: 47701ce, 2026-02-22 05:55:10 +0000, subject "Implement A2DPlaya: Bluetooth A2DP to Chromecast/AirPlay audio bridge", author name Claude. 38 files changed, 4976 insertions (`git log --all --stat`). All 23 .py files, index.html, install.sh, requirements.txt and setup.py enter the history in this commit.
- Third commit: d78a903, 2026-02-22 05:55:47 +0000, subject "Add .gitignore and remove cached Python bytecode files", author name Claude. 12 files changed: .gitignore added, eleven .pyc files deleted (commit d78a903).
- Last commit: 70ad071, 2026-02-22 17:33:54 +1100, subject "Merge pull request #1 from tunlezah/claude/autonomous-production-system-yqNwU", author name tunlezah, committer name GitHub. `git diff d78a903 70ad071 --stat` is empty: the merge adds no content beyond its second parent.
- Author date and committer date are identical on all four commits (`git log --all --pretty='%ad %cd'`).
- Converted to UTC: 673e119 at 2026-02-21 22:40:08; 47701ce at 2026-02-22 05:55:10; d78a903 at 2026-02-22 05:55:47; 70ad071 at 2026-02-22 06:33:54.
- Period of activity: one, of 7 h 53 min 46 s from first commit to last (computed from the four dates above).
- Gaps inside that period: 7 h 15 min 02 s between 673e119 and 47701ce; 37 s between 47701ce and d78a903; 38 min 07 s between d78a903 and 70ad071.
- Period of inactivity: no commit after 70ad071 (2026-02-22). The manifest snapshot is dated 2026-11-17 07:22 UTC (/home/user/audio/00-manifest.md:18), 268 days later.
- Files touched by more than one commit: README.md (673e119, 47701ce) and the eleven .pyc files (47701ce, d78a903). Every other file is touched by exactly one commit (`git log --all --name-only --pretty=format: | sort | uniq -c | sort -rn`).
- The body of commit 47701ce lists ten components and ends with "Comprehensive test suite (44 tests) covering config, resampler, FFT analyzer, and audio pipeline" (commit 47701ce). `git grep -c -E '^\s+def test_' -- tests/` counts 14 + 9 + 12 + 9 = 44 test functions. No test result is stated in any commit message.
- Commits 47701ce and d78a903 each end with a session link line (commit bodies; value not copied here).

## H2 Approach changes

No commit replaces one library, module or protocol with another. All product source arrived in commit 47701ce and was not modified afterwards (`git log --all --stat`). The one deletion in the history is recorded below. A difference inside commit 47701ce between text that names MP3 and code that serves WAV is recorded at R1-H5-03. The README change from one byte (673e119) to 113 lines (47701ce) is listed under H1.

### R1-H2-01  Tracked Python bytecode files deleted and an ignore file added
- Question: H2, relates to S11.1
- What was attempted: commit 47701ce added eleven .pyc files under a2dplaya/__pycache__/, a2dplaya/audio/__pycache__/ and tests/__pycache__/ (commit 47701ce, `--stat`). Commit d78a903, 37 seconds later, deleted all eleven and added .gitignore with ten patterns, including `__pycache__/`, `*.pyc` and `.pytest_cache/` (.gitignore:1-2, .gitignore:8; commit d78a903). Stated reason for the change: UNKNOWN. The message is the subject line "Add .gitignore and remove cached Python bytecode files" and a session link.
- Presence: REMOVED for the .pyc files (commit d78a903). CURRENT for .gitignore (.gitignore:1-10).
- Outcome: WORKED
- Outcome evidence: OBSERVED. `git ls-files` at 70ad071 lists 28 files and none ends in .pyc; `git status --porcelain --ignored` is empty.
- Why it turned out that way: OBSERVED. Commit d78a903 deletes the eleven files (`git log --all --diff-filter=D --name-only`) and adds the ignore patterns in the same commit.
- Depends on: the interpreter tag `cpython-311` in all eleven deleted names; the suffix `cpython-311-pytest-9.0.2.pyc` on four names under tests/__pycache__/ (commit 47701ce). pytest is not listed in requirements.txt:1-17 or setup.py:16-26.
- Open questions: Do the four `pytest-9.0.2` file names record a test run made before commit 47701ce, and what was its result? Why were bytecode files present for a2dplaya/config.py and three a2dplaya/audio/ modules and for no other package module?
- Confidence: HIGH. OBSERVED in the commit pair and the current file list.

## H3 Reverts and repeated fixes

### R1-H3-01  NOT FOUND
- Question: H3
- Searched: `git log --all -i --grep=TERM --date=short --pretty='%h %ad %s'` for each of: revert, fix, bug, broken, workaround, hotfix, oops, undo, rollback, "roll back", wip, temp, tmp, replace, switch, rewrite, refactor (no commit returned for any of these); the same command for remove (one commit, d78a903). `git log --all --name-only --pretty=format: | sort | uniq -c | sort -rn | head -30` for files touched three or more times (highest count is 2). `git log --all --date=iso` to read all four subjects and bodies.
- Nearest related material: commit d78a903, which deletes files added 37 seconds earlier by commit 47701ce (recorded at R1-H2-01). It is not a `git revert` commit and its message does not use the word fix.

## H4 Unmerged work

### R1-H4-01  NOT FOUND
- Question: H4
- Searched: `git branch -a --no-merged` and `git branch -a --no-merged main` (both print nothing); `git branch -a -vv` and `git for-each-ref` (four refs, all at 70ad071: main, claude/inspiring-galileo-a9dlia, origin/main, origin/claude/inspiring-galileo-a9dlia); `git stash list` (prints nothing); `git tag` (prints nothing); `git fsck --unreachable --no-reflogs` and `git fsck --lost-found --no-reflogs` (both print nothing); `git reflog --all` (seven entries, all at 70ad071, all dated 2026-11-17 07:19:36 +0000); `git count-objects -v` (61 loose objects, no packs).
- Nearest related material: branch claude/autonomous-production-system-yqNwU, named in the subject of commit 70ad071. It has no ref in this clone, and its two commits 47701ce and d78a903 are ancestors of main through merge 70ad071. Branch claude/inspiring-galileo-a9dlia holds no commit of its own (/home/user/audio/00-manifest.md:33).

## H5 Dead and disabled code

A search for commented-out code (`git grep -n -E '^\s*#\s*(import |from .* import|def |class |self\.|return\b|if .*:$|for .*:$|while .*:$|try:|[A-Za-z_\.]+\(.*\)\s*$|[A-Za-z_\.]+ = )' -- '*.py' '*.sh' '*.txt'`) returned no line. The two commented-out lines found by reading are in requirements.txt (R1-H5-07). Every module under a2dplaya/ is imported by another module (`git grep -n -E 'from a2dplaya'`: a2dplaya/__main__.py:12-13, a2dplaya/app.py:16-22, a2dplaya/audio/pipeline.py:26-28); no unused module was found. Unused Bluetooth reconnect settings are recorded at R1-H7-02.

### R1-H5-01  Bluetooth audio hand-off to the pipeline: callback, transport descriptor and audio thread declared without a user
- Question: H5, relates to S05.1
- What was attempted: BluetoothManager takes an `audio_callback` argument and stores it (a2dplaya/bluetooth/manager.py:69-71). It declares `_transport_fd` and `_audio_thread` as None under the comment "Audio transport" (a2dplaya/bluetooth/manager.py:88-90) and closes and joins them in stop() (a2dplaya/bluetooth/manager.py:113-122). Constants for `org.bluez.Media1` and `org.bluez.MediaTransport1` are defined (a2dplaya/bluetooth/manager.py:28-29). The docstring of start() reads "Initialize Bluetooth subsystem and register as A2DP sink." (a2dplaya/bluetooth/manager.py:93). The receiving function _bt_audio_callback exists (a2dplaya/app.py:176-180) and is passed in at a2dplaya/app.py:74. What it was for, STATED: "captures the audio and feeds it to the AudioPipeline via a callback" (a2dplaya/bluetooth/manager.py:65).
- Presence: DISABLED. Unreachable: `git grep -n '_audio_callback'` shows no call of the stored callback in a2dplaya/bluetooth/manager.py (only line 71); `_transport_fd` occurs only at lines 89, 116, 119, 122; `_audio_thread` only at lines 90, 113, 114; `BLUEZ_MEDIA_IFACE` and `BLUEZ_MEDIA_TRANSPORT_IFACE` only at lines 28-29.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. INFERRED on the code path only: no statement assigns the descriptor, starts the thread or calls the callback, so a2dplaya/app.py:176-180 has no caller on the Bluetooth side.
- Depends on: the `dbus` import at a2dplaya/bluetooth/manager.py:98; the stated requirement "PulseAudio or PipeWire with bluetooth module" (a2dplaya/bluetooth/manager.py:11); the package `pulseaudio-module-bluetooth` (install.sh:35, install.sh:44).
- Open questions: Was a transport acquisition step written and left out of commit 47701ce? Was Bluetooth audio expected to reach the pipeline by a route outside this class? Commit 47701ce describes the class as a "Bluetooth A2DP sink manager with BlueZ D-Bus API"; was audio reception ever exercised?
- Confidence: MEDIUM. INFERRED from a whole-tree search of clear code.

### R1-H5-02  Pairing agent settings and constants without a reader
- Question: H5, relates to S03.2
- What was attempted: a constant `BT_AGENT_CAPABILITY = "NoInputNoOutput"` with the comment "Auto-accept pairing" (a2dplaya/config.py:57), two BluetoothConfig fields `auto_accept_pairing: bool = True` and `agent_capability` (a2dplaya/config.py:67-68), and constants for `org.bluez.Agent1` and `org.bluez.AgentManager1` (a2dplaya/bluetooth/manager.py:30-31).
- Presence: DISABLED. Unreachable: `git grep -n -E 'auto_accept_pairing|agent_capability|BT_AGENT_CAPABILITY'` returns a2dplaya/config.py:57, 67, 68 and tests/test_config.py:34 only; `BLUEZ_AGENT_IFACE` and `BLUEZ_AGENT_MANAGER_IFACE` occur only at a2dplaya/bluetooth/manager.py:30-31.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. INFERRED on the code path only: no code registers an agent or reads the two fields, so the settings have no effect on pairing.
- Depends on: the default asserted in tests/test_config.py:34; pairing as implemented goes through a2dplaya/bluetooth/manager.py:194-198.
- Open questions: Was an agent implementation planned for these constants? Which component answers a pairing request when the process runs?
- Confidence: MEDIUM. INFERRED from a whole-tree search of clear code.

### R1-H5-03  MP3 stream route for Chromecast, with a buffer that has no writer
- Question: H5, relates to S08.2
- What was attempted: the module docstring states "Audio is served as an HTTP MP3 stream that the Chromecast fetches" and "AudioPipeline -> MP3 encoder -> HTTP stream server" (a2dplaya/cast/manager.py:5, a2dplaya/cast/manager.py:10). A route `/stream.mp3` (a2dplaya/cast/manager.py:56-57) runs _serve_mp3_stream (a2dplaya/cast/manager.py:63-86), which reads get_stream_chunk (a2dplaya/cast/manager.py:284-291) from `_stream_buffer`. An attribute `_mp3_encoder = None` sits under the comment "MP3 encoder (lazy init)" (a2dplaya/cast/manager.py:163-164). Config holds `CHROMECAST_FORMAT = "mp3"  # Most compatible format` and a `stream_format` field (a2dplaya/config.py:30, a2dplaya/config.py:89).
- Presence: DISABLED. `_stream_buffer` is created at a2dplaya/cast/manager.py:149, cleared at 260 and read at 287-290; no statement adds to it. `_mp3_encoder` occurs only at line 164. `stream_format` is read only by tests/test_config.py:40. The cast request uses the other route: `/stream.wav` (a2dplaya/cast/manager.py:224) with content type "audio/wav" (a2dplaya/cast/manager.py:410, a2dplaya/cast/manager.py:433).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No commit, comment or note says why the docstring and config name MP3 while cast_to requests WAV. Commit 47701ce describes "HTTP stream server serving WAV audio to Cast devices".
- Depends on: no MP3 encoder library in requirements.txt:1-17, setup.py:16-26 or install.sh:27-51 (`git grep -n -i -E 'lame|ffmpeg'` returns nothing); the hard-coded 44100 Hz, 2 channels, 16 bits of the WAV route (a2dplaya/cast/manager.py:100-102).
- Open questions: Was MP3 the first design and WAV a later substitution within the same session? Was the `/stream.mp3` route ever requested by a Cast device?
- Confidence: MEDIUM. INFERRED from a whole-tree search of clear code.

### R1-H5-04  Browser audio over WebSocket: client list never filled, broadcast loop that only sleeps
- Question: H5, relates to S08.1
- What was attempted: the module docstring lists "Browser-based audio playback via WebSocket audio streaming" and "Uses Flask for HTTP and flask-sock for WebSocket support." (a2dplaya/web/server.py:8, a2dplaya/web/server.py:10). WebServer holds a list `_ws_clients` (a2dplaya/web/server.py:50-52); audio_consumer walks it and calls `send_audio` on each entry (a2dplaya/web/server.py:96-112); the consumer is registered as "webui" (a2dplaya/app.py:120). A thread "EQ-Broadcast" runs _eq_broadcast_loop (a2dplaya/web/server.py:77-82), whose docstring reads "Periodically broadcast EQ data to WebSocket clients." and whose body is one sleep in a loop (a2dplaya/web/server.py:430-433). README.md:14 lists "Browser Playback - Listen directly in the browser via Web Audio API"; the diagram labels the browser output "(WebSocket)" (README.md:28-29).
- Presence: DISABLED. Unreachable: `_ws_clients` occurs only at a2dplaya/web/server.py:51, 104, 112, with no statement that adds an entry; `git grep -n -i -E 'flask_sock|flask-sock'` returns only the docstring line a2dplaya/web/server.py:10; flask-sock is absent from requirements.txt:1-17 and setup.py:16-26; no `<audio` or `AudioContext` occurs in a2dplaya/web/static/index.html.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. INFERRED on the code path only: with an empty client list the loop at a2dplaya/web/server.py:104 has nothing to iterate.
- Depends on: Flask as the only web dependency (requirements.txt:5, setup.py:17); the EQ data reaching the page by Server-Sent Events (a2dplaya/web/server.py:341-360, a2dplaya/web/static/index.html:468).
- Open questions: Was a WebSocket route written and left out of commit 47701ce? Does the README feature line describe an implemented path?
- Confidence: MEDIUM. INFERRED from a whole-tree search of clear code.

### R1-H5-05  HTTP route /api/audio/stream that sends a WAV header and no audio
- Question: H5, relates to S08.1
- What was attempted: a Flask route with the docstring "PCM audio stream for browser-based playback via Web Audio API." (a2dplaya/web/server.py:364-366). Its generator yields one WAV header (a2dplaya/web/server.py:379-386), then under the comment "Stream PCM data" loops assigning `eq_data` and sleeping 20 ms (a2dplaya/web/server.py:388-391).
- Presence: CURRENT. The route is registered (a2dplaya/web/server.py:364). No page script refers to it: `git grep -n -i 'api/audio' -- a2dplaya/web/static/index.html` returns nothing.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. INFERRED on the code path only: the generator has one `yield` (line 386) and the variable `eq_data` assigned at line 390 is not read.
- Depends on: `self._running` set by WebServer.start (a2dplaya/web/server.py:67); pipeline format properties read at a2dplaya/web/server.py:373-375.
- Open questions: Was this route an earlier attempt at browser playback than the WebSocket consumer of R1-H5-04, or a later one? Was it ever opened in a browser?
- Confidence: MEDIUM. INFERRED from clear code.

### R1-H5-06  shairport-sync route for AirPlay output
- Question: H5, relates to S08.1
- What was attempted: stream_to tries _connect_raop and then _connect_shairport (a2dplaya/airplay/manager.py:133-149). _connect_shairport runs `shairport-sync --version` (a2dplaya/airplay/manager.py:394-397), raises RuntimeError when the program is absent (398-399), and otherwise logs a message and returns False (403-404). An attribute `_external_proc` is declared under the comment "External process (shairport-sync pipe mode)" (a2dplaya/airplay/manager.py:81-82) and terminated in stop_streaming (168-174); no statement assigns a process to it. The module docstring states "this implementation uses the shairport-sync approach when available, or direct RAOP protocol implementation" (a2dplaya/airplay/manager.py:9-10).
- Presence: CURRENT. The function is called at a2dplaya/airplay/manager.py:144; every path through it returns False or raises.
- Outcome: FAILED
- Outcome evidence: STATED. Log text "shairport-sync based AirPlay not yet fully implemented" (a2dplaya/airplay/manager.py:403), followed by `return False` (a2dplaya/airplay/manager.py:404).
- Why it turned out that way: STATED. "This is a simplified approach - in practice you'd configure shairport-sync as a named pipe sink" (a2dplaya/airplay/manager.py:401-402).
- Depends on: the `shairport-sync` program, which install.sh:27-51 does not install; the RAOP route having returned False or raised first (a2dplaya/airplay/manager.py:134-140).
- Open questions: Commit 47701ce lists "AirPlay output via RAOP protocol with zeroconf discovery and shairport-sync fallback"; was the fallback ever more than this check?
- Confidence: HIGH. STATED in the code and the code agrees.

### R1-H5-07  Two dependency lines commented out in requirements.txt
- Question: H5, relates to S01.5
- What was attempted: `dbus-python>=1.2` and `pyalsaaudio>=0.9` are present as comments (requirements.txt:14, requirements.txt:17). What each was for, STATED: "Bluetooth D-Bus bindings (optional, falls back to bluetoothctl CLI)" (requirements.txt:13) and "ALSA audio capture (optional, falls back to arecord CLI)" (requirements.txt:16).
- Presence: DISABLED. Commented out (requirements.txt:14, requirements.txt:17).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: STATED. "System package recommended: apt install python3-dbus" (requirements.txt:14); "System package recommended: apt install python3-alsaaudio" (requirements.txt:17).
- Depends on: install.sh:37 and install.sh:46 install `python3-dbus` as a system package; no line of install.sh names `python3-alsaaudio` (`git grep -n alsaaudio -- install.sh` returns nothing); the virtual environment is created with `python3 -m venv "$VENV_DIR"` and no further option (install.sh:61); the service runs the venv interpreter (install.sh:123).
- Open questions: Is the system `dbus` module visible to the venv interpreter created at install.sh:61? Was the pyalsaaudio capture path (a2dplaya/audio/line_in.py:149-179) ever run?
- Confidence: MEDIUM. STATED reason; the result is not evidenced.

### R1-H5-08  --no-bluetooth switch implemented as an adapter name that matches nothing
- Question: H5, relates to S02.3
- What was attempted: a flag `--no-bluetooth` with help text "Disable Bluetooth (line-in only mode)" (a2dplaya/__main__.py:41-45). When set, main() assigns `config.bluetooth.preferred_adapter = "__disabled__"` under the comment "Disable Bluetooth by preventing its initialization" (a2dplaya/__main__.py:79-81).
- Presence: CURRENT (a2dplaya/__main__.py:79-81).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. INFERRED on the code path only: AppController.start constructs and starts BluetoothManager with no check of the flag (a2dplaya/app.py:70-76); the D-Bus setup skips every adapter whose path lacks the preferred string and raises "No Bluetooth adapter found" (a2dplaya/bluetooth/manager.py:247-256); start() catches that and calls _setup_adapter_cli (a2dplaya/bluetooth/manager.py:103-106), which does not read `preferred_adapter` and runs `bluetoothctl power on` and `system-alias` (a2dplaya/bluetooth/manager.py:286-294).
- Depends on: `preferred_adapter` being read only at a2dplaya/bluetooth/manager.py:247; the broad `except (ImportError, Exception)` at a2dplaya/bluetooth/manager.py:103.
- Open questions: Does the adapter stay unpowered when the flag is given? Was the flag ever run on a machine with an adapter?
- Confidence: LOW. INFERRED from a multi-step reading with no run evidence.

### R1-H5-09  Line-in capture switched off by default, with a web Start route that does not set the switch
- Question: H5, relates to S09.2
- What was attempted: `enable_line_in: bool = False` (a2dplaya/config.py:79). LineInCapture.start returns with the log text "Line-in capture disabled in config" when the field is unset (a2dplaya/audio/line_in.py:68-70). The field is set True by `--line-in` (a2dplaya/__main__.py:65-67) or by an "audio" entry in the config file (a2dplaya/config.py:173-177); no environment mapping names it (a2dplaya/config.py:206-216). AppController starts capture only when the field is set (a2dplaya/app.py:109-111). The route `POST /api/linein/start` sets the device name and calls start() (a2dplaya/web/server.py:319-329); it does not set the field. The page section for line-in is hidden with `style="display:none;"` until the "Line In" tab is chosen (a2dplaya/web/static/index.html:441, a2dplaya/web/static/index.html:576).
- Presence: DISABLED by default setting (a2dplaya/config.py:79); the code is in the default branch.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. INFERRED on the code path only: with the default config, the call at a2dplaya/web/server.py:328 reaches the early return at a2dplaya/audio/line_in.py:68-70, and the route still answers `{"status": "started"}` (a2dplaya/web/server.py:329).
- Depends on: the comment "Don't auto-start unless configured" (a2dplaya/app.py:109); `enable_line_in` occurring only at a2dplaya/__main__.py:67, a2dplaya/app.py:110, a2dplaya/audio/line_in.py:68 and a2dplaya/config.py:79.
- Open questions: Was the Start button on the page expected to work without `--line-in`? Was line-in capture ever run?
- Confidence: MEDIUM. INFERRED from clear code.

### R1-H5-10  Settings and constants that no code reads
- Question: H5, relates to S10.4
- What was attempted: `eq_enabled: bool = True` (a2dplaya/config.py:81); `web.debug`, set by `--debug` (a2dplaya/__main__.py:62) and by `A2DPLAYA_WEB_DEBUG` (a2dplaya/config.py:209); CastConfig `stream_sample_rate` and `stream_channels` (a2dplaya/config.py:90-91); AirPlayConfig `stream_buffer_size` (a2dplaya/config.py:100); constants `CHROMECAST_SAMPLE_WIDTH` and `AIRPLAY_SAMPLE_WIDTH` (a2dplaya/config.py:29, a2dplaya/config.py:37); `data_dir` (a2dplaya/config.py:121).
- Presence: DISABLED. Unreachable as settings: `git grep` finds `eq_enabled` only at a2dplaya/config.py:81; `stream_buffer_size` only at a2dplaya/config.py:100; `data_dir` only at a2dplaya/config.py:121, 195, 196; the two width constants only at their definitions; `stream_sample_rate` and `stream_channels` are read only by the AirPlay code (a2dplaya/airplay/manager.py:327), while the Cast WAV route uses literals (a2dplaya/cast/manager.py:100-102); Flask is started with the literal `debug=False` (a2dplaya/web/server.py:407) and `web.debug` is otherwise read only by tests/test_config.py:30.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No comment or commit says what each unread setting was for beyond its name.
- Depends on: the FFT analysis call being unconditional (a2dplaya/audio/pipeline.py:246-247); `--debug` still changing `log_level` (a2dplaya/__main__.py:61).
- Open questions: Was `eq_enabled` meant to gate the FFT step at a2dplaya/audio/pipeline.py:247? Was `data_dir` meant to hold paired-device or state files?
- Confidence: MEDIUM. INFERRED from a whole-tree search of clear code.

### R1-H5-11  Pipeline underrun and overrun counters with no reachable increment
- Question: H5, relates to S05.4
- What was attempted: a statistics dictionary with keys "underruns" and "overruns" (a2dplaya/audio/pipeline.py:83-89). "overruns" is incremented in an `except Exception` branch around `self._buffer.append(pcm_data)` (a2dplaya/audio/pipeline.py:214-218). The buffer is `deque(maxlen=100)` (a2dplaya/audio/pipeline.py:77). A local `chunk_duration` is computed at a2dplaya/audio/pipeline.py:227.
- Presence: DISABLED. Unreachable: `git grep -n underruns` finds only the declaration (a2dplaya/audio/pipeline.py:86) and a key check in tests/test_pipeline.py:113; `chunk_duration` occurs only at line 227.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. INFERRED on the code path only: a deque with `maxlen` drops an item from the other end on append and does not raise when full, so the branch at lines 216-218 is not entered by a full buffer.
- Depends on: the comment "Max ~100 chunks buffered" (a2dplaya/audio/pipeline.py:77); the empty-buffer branch that sleeps and does not count (a2dplaya/audio/pipeline.py:231-233).
- Open questions: Were the counters ever read during a run? Is silent loss of the oldest chunk the intended behaviour when the buffer is full?
- Confidence: MEDIUM. INFERRED from clear code.

## H6 Author's markers

### R1-H6-01  NOT FOUND
- Question: H6
- Searched: `\b(TODO|FIXME|HACK|XXX|BUG|WORKAROUND)\b` case-sensitive across the whole tree (no match); case-insensitive `todo|fixme|hack|xxx|workaround|work around|not yet|simplified|placeholder|stub|not implemented|NotImplemented|for now|temporary|in production|real implementation|would be|future|deprecated|legacy|known issue|limitation|fallback` across the whole tree; file names CHANGELOG*, TODO*, NOTES* and known-issues files (none among the 28 files of `git ls-files`; /home/user/audio/repos/R1/01-map.md:129); the four commit bodies (`git log --all --date=iso`); Owner notes ("none", /home/user/audio/00-manifest.md:39).
- Nearest related material: four phrases that are not in the marker list. "Implements a simplified RAOP handshake:" (a2dplaya/airplay/manager.py:292, related stage S08.2). "0, 0, 0, 0,  # Timestamp (simplified)" (a2dplaya/airplay/manager.py:375, related stage S07.3). "This is a simplified approach - in practice you'd configure shairport-sync as a named pipe sink" (a2dplaya/airplay/manager.py:401-402, related stage S08.1). "shairport-sync based AirPlay not yet fully implemented" (a2dplaya/airplay/manager.py:403, related stage S08.1). The case-insensitive search also returned the word "fallback" at a2dplaya/airplay/manager.py:106, 149, 388; a2dplaya/audio/line_in.py:7, 25, 43, 116; a2dplaya/web/server.py:415; a2dplaya/web/static/index.html:476, 493; a2dplaya/cast/manager.py:7, 423; a2dplaya/bluetooth/manager.py:104.

## H7 Workarounds

Scope notes, with citations and no record: no `==` version pin exists; the lower bounds `flask>=2.0`, `pychromecast>=13.0` and `zeroconf>=0.80` carry comments that name a purpose and no problem (requirements.txt:4-11; setup.py:17-24). No special case for a named device was found: `git grep -n -i -E 'iphone|android|samsung|pixel|sony|bose|homepod|apple tv|google home|nest|sonos|alexa'` returns only README.md:10-11, which are feature lines. Other sleep calls, used to pace a loop: a2dplaya/web/server.py:351 (30 per second, Server-Sent Events), a2dplaya/web/server.py:391 and 433 (recorded at R1-H5-05 and R1-H5-04). Time limits on subprocess and socket calls, not recorded as sleeps or retries: a2dplaya/bluetooth/manager.py:189, 276, 289, 293, 351, 405, 445, 481, 518, 555; a2dplaya/airplay/manager.py:264, 300, 396; a2dplaya/audio/line_in.py:120; a2dplaya/cast/manager.py:438.

### R1-H7-01  Test polls up to 50 times at 50 ms for the pipeline thread to deliver a chunk
- Question: H7, relates to S11.1
- What was attempted: a loop of 50 passes, each sleeping 0.05 s, that ends when the consumer has received data (tests/test_pipeline.py:67-70), followed by the assertion (tests/test_pipeline.py:72). Problem worked around, STATED: "Wait for processing - poll with retries for CI reliability" (tests/test_pipeline.py:66).
- Presence: CURRENT (tests/test_pipeline.py:66-70)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No test output or CI result is in the repo.
- Depends on: the pipeline thread's 1 ms idle sleep (a2dplaya/audio/pipeline.py:228-233); no CI configuration file in the repo (no .github/ directory and no *.yml among the 28 files of `git ls-files`).
- Open questions: Which CI system does the comment refer to? Did a shorter wait fail before this loop was written?
- Confidence: MEDIUM. STATED purpose; no result.

### R1-H7-02  Bluetooth reconnect delay and retry-count settings with no reader
- Question: H7, relates to S04.3
- What was attempted: `BT_RECONNECT_DELAY = 3  # seconds between reconnection attempts` and `BT_RECONNECT_MAX_RETRIES = 10` (a2dplaya/config.py:55-56), carried into BluetoothConfig as `reconnect_delay` and `reconnect_max_retries` (a2dplaya/config.py:64-65). Problem worked around: UNKNOWN.
- Presence: DISABLED. Unreachable: `git grep -n -E 'reconnect_delay|reconnect_max_retries|BT_RECONNECT'` returns only a2dplaya/config.py:55, 56, 64, 65. The only other occurrence of the word is the docstring "Mark a device as trusted for auto-reconnection." (a2dplaya/bluetooth/manager.py:213).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. INFERRED on the code path only: no loop or timer reads the two values, so no reconnection attempt is made by this project's code.
- Depends on: device trust set through a2dplaya/bluetooth/manager.py:212-216; `AutoEnable=true` written by install.sh:98-99.
- Open questions: Was a reconnect loop planned and not written? Is reconnection left to the Bluetooth stack's own handling of trusted devices?
- Confidence: MEDIUM. INFERRED from a whole-tree search of clear code.

### R1-H7-03  Web page reloads device lists on fixed timers after each action
- Question: H7, relates to S09.4
- What was attempted: after `POST /api/bluetooth/scan` the page calls loadBtDevices at 3 s, 8 s and 15 s (a2dplaya/web/static/index.html:588-593). After pair and connect it reloads once at 2 s (index.html:642, 651); after disconnect at 1 s (660); after cast play at 3 s and cast stop at 1 s (699, 704); after AirPlay play at 3 s and stop at 1 s (743, 748). Problem worked around: UNKNOWN; no comment sits on these lines.
- Presence: CURRENT (a2dplaya/web/static/index.html:591-593)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. INFERRED on the design only: the scan runs in a background thread for up to `discovery_timeout` seconds (a2dplaya/bluetooth/manager.py:145-150; 15 s at a2dplaya/config.py:54) and no route pushes its completion to the page.
- Depends on: the 15 s Bluetooth discovery timeout (a2dplaya/config.py:54); the scan route returning before discovery ends (a2dplaya/bluetooth/manager.py:139-151).
- Open questions: Were 3, 8 and 15 seconds chosen from observed scan behaviour? Do pair and connect finish within the 2 s reload on a real device?
- Confidence: LOW. INFERRED; purpose not stated.

### R1-H7-04  Web page falls back from the event stream to 500 ms status polling
- Question: H7, relates to S09.4
- What was attempted: on an EventSource error the page closes the stream and calls startPolling (a2dplaya/web/static/index.html:475-480), which fetches `/api/status` every 500 ms (index.html:494-497). A failed fetch sets the status text to "Disconnected - retrying..." (index.html:505-507). Init calls both startEqStream and startPolling (index.html:799-800). Problem worked around, STATED in comments: "Fallback to polling" (index.html:476), "Update EQ from status if SSE not available" (index.html:541).
- Presence: CURRENT (a2dplaya/web/static/index.html:475-497)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No result is recorded.
- Depends on: the `/api/eq/stream` route (a2dplaya/web/server.py:341-360), which exists only under Flask; the built-in server used without Flask serves static files only (a2dplaya/web/server.py:414-428).
- Open questions: After the stream is closed at index.html:477, is it ever reopened? Does polling run alongside the stream from page load, given index.html:799-800?
- Confidence: MEDIUM. STATED purpose in comments; no result.

### R1-H7-05  Line-in ALSA read errors: sleep 0.1 s and read again, with no limit
- Question: H7, relates to S05.4
- What was attempted: inside the pyalsaaudio capture loop, any exception from `inp.read()` or the callback is logged as "ALSA read error" and followed by `time.sleep(0.1)`; the loop then continues while `_running` is set (a2dplaya/audio/line_in.py:165-177). Problem worked around: UNKNOWN.
- Presence: CURRENT in code (a2dplaya/audio/line_in.py:175-177); reached only when line-in is enabled (R1-H5-09).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No result is recorded.
- Depends on: the `alsaaudio` module, which is commented out of requirements.txt:17; `PCM_NORMAL` mode (a2dplaya/audio/line_in.py:155).
- Open questions: Which ALSA error was expected here? Is there a case where the read fails on every pass?
- Confidence: LOW. INFERRED from code; purpose not stated.

### R1-H7-06  AirPlay sender waits 4 ms when fewer than 352 frames are buffered
- Question: H7, relates to S07.2
- What was attempted: _raop_stream_loop takes 352 * 4 bytes when available and otherwise sleeps and retries (a2dplaya/airplay/manager.py:358-368). Purpose, STATED: "~4ms for 352 frames at 44100Hz" (a2dplaya/airplay/manager.py:367). Packets are sent as soon as data is available, with no sleep between sends (a2dplaya/airplay/manager.py:370-379).
- Presence: CURRENT (a2dplaya/airplay/manager.py:366-368)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No result is recorded.
- Depends on: the 512 KB buffer cap (a2dplaya/airplay/manager.py:75); the timestamp field fixed at zero (a2dplaya/airplay/manager.py:375).
- Open questions: 352 frames at 44100 Hz last 7.98 ms by arithmetic and the comment says about 4 ms; which figure was intended? Was any receiver's playback timing observed?
- Confidence: MEDIUM. STATED purpose; no result.

### R1-H7-07  Pipeline thread sleeps 1 ms when its buffer is empty
- Question: H7, relates to S07.2
- What was attempted: _process_loop sleeps `min_sleep` when the deque is empty or a pop raises IndexError (a2dplaya/audio/pipeline.py:230-240). Problem worked around, STATED: "1ms minimum sleep to prevent busy-waiting" (a2dplaya/audio/pipeline.py:228).
- Presence: CURRENT (a2dplaya/audio/pipeline.py:228-240)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No measurement of processor load or latency is in the repo.
- Depends on: the polling design of the loop (a2dplaya/audio/pipeline.py:230-233); no blocking queue or condition variable in a2dplaya/audio/pipeline.py.
- Open questions: What latency does the 1 ms poll add per chunk? What is the idle processor load?
- Confidence: MEDIUM. STATED purpose; no result.

### R1-H7-08  Cast stream handlers sleep 10 ms when no chunk is ready
- Question: H7, relates to S08.3
- What was attempted: both HTTP stream handlers loop while streaming, write a chunk when one exists and otherwise sleep 0.01 s (a2dplaya/cast/manager.py:76-82 for `/stream.mp3`; a2dplaya/cast/manager.py:116-122 for `/stream.wav`). Problem worked around: UNKNOWN; no comment sits on these lines.
- Presence: CURRENT for the WAV handler (a2dplaya/cast/manager.py:116-122). The MP3 handler's buffer has no writer (R1-H5-03).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No result is recorded.
- Depends on: chunks of up to 8192 bytes (a2dplaya/cast/manager.py:298-299); the 1 MB buffer cap that discards the oldest bytes (a2dplaya/cast/manager.py:152, a2dplaya/cast/manager.py:281-282).
- Open questions: What does a Cast device do while the handler sends nothing during a gap in the source audio? Was a gap ever observed?
- Confidence: LOW. INFERRED from code; purpose not stated.

### R1-H7-09  Bluetooth discovery refreshes the device list once per second until a timeout
- Question: H7, relates to S03.3
- What was attempted: the D-Bus discovery worker loops, calling _refresh_devices_dbus and sleeping 1.0 s, until `discovery_timeout` passes or scanning is cancelled (a2dplaya/bluetooth/manager.py:317-321). The bluetoothctl variant sleeps 1.0 s and then refreshes (a2dplaya/bluetooth/manager.py:342-346). The timeout is 15 s (a2dplaya/config.py:54, a2dplaya/config.py:63). Problem worked around: UNKNOWN.
- Presence: CURRENT (a2dplaya/bluetooth/manager.py:317-321)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. INFERRED on the design only: no D-Bus signal receiver or main loop is present in the tree (/home/user/audio/repos/R1/01-map.md:79), so device changes are read by polling.
- Depends on: `GetManagedObjects`-style refresh rather than signals; the 15 s default.
- Open questions: Does a device that appears and disappears within one second get listed? Was 15 seconds chosen from observation?
- Confidence: LOW. INFERRED; purpose not stated.

### R1-H7-10  Zeroconf discovery waits a fixed number of seconds before reading results
- Question: H7, relates to S08.1
- What was attempted: Chromecast discovery starts a ServiceBrowser, calls `time.sleep(timeout)`, then cancels it (a2dplaya/cast/manager.py:388-391). AirPlay discovery does the same with two browsers (a2dplaya/airplay/manager.py:239-246). Both timeouts default to 10 s (a2dplaya/config.py:88, a2dplaya/config.py:97). Problem worked around: UNKNOWN.
- Presence: CURRENT (a2dplaya/cast/manager.py:389; a2dplaya/airplay/manager.py:243)
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No result is recorded.
- Depends on: the `zeroconf` package (requirements.txt:11); for Chromecast this route runs only when the pychromecast route is not used (a2dplaya/cast/manager.py:186-194).
- Open questions: Does the HTTP request that triggers discovery stay open for the full 10 seconds? Was any device found by this route?
- Confidence: LOW. INFERRED from code; purpose not stated.

### R1-H7-11  systemd unit restarts the process 5 seconds after a failure
- Question: H7, relates to S10.2
- What was attempted: the generated unit sets `Restart=on-failure` and `RestartSec=5` (install.sh:124-125). Problem worked around: UNKNOWN; no comment sits on these lines.
- Presence: CURRENT (install.sh:113-129). The unit is written only when the person answers the second installer prompt (/home/user/audio/repos/R1/01-map.md:44).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No service log is in the repo.
- Depends on: `After=bluetooth.target network.target sound.target` (install.sh:116); each component start being wrapped in try/except inside the process (a2dplaya/app.py:70-125).
- Open questions: Does the process ever exit with a failure status, given that component errors are caught at a2dplaya/app.py:78-80, 89-91, 99-101, 113-115 and 123-125? Was the unit ever installed and started?
- Confidence: LOW. INFERRED from code; purpose not stated.

## H8 Final state

- Subjects of the last ten commits: the history holds four, listed newest first (`git log -10 --date=iso main`).
  1. 70ad071, 2026-02-22 17:33:54 +1100, "Merge pull request #1 from tunlezah/claude/autonomous-production-system-yqNwU"
  2. d78a903, 2026-02-22 05:55:47 +0000, "Add .gitignore and remove cached Python bytecode files"
  3. 47701ce, 2026-02-22 05:55:10 +0000, "Implement A2DPlaya: Bluetooth A2DP to Chromecast/AirPlay audio bridge"
  4. 673e119, 2026-02-22 09:40:08 +1100, "Create README.md"
- Uncommitted changes listed in the manifest: none. `git status --porcelain` is recorded as empty (/home/user/audio/00-manifest.md:22). `git status --porcelain --ignored` was empty when re-run for this file.
- Stash count 0, tag count 0, no unmerged branch (/home/user/audio/00-manifest.md:22, /home/user/audio/00-manifest.md:35; R1-H4-01).
- Git state at the last commit: not mid-change. The last commit is a pull-request merge (commit 70ad071) with no content of its own; the last content commit is the bytecode removal (commit d78a903); no merge, rebase or cherry-pick is in progress (`ls .git` shows no MERGE_HEAD, rebase-merge, rebase-apply or CHERRY_PICK_HEAD).
- Code state at the last commit, STATED: one path logs "shairport-sync based AirPlay not yet fully implemented" and returns False (a2dplaya/airplay/manager.py:403-404; R1-H5-06). The RAOP docstring lists four handshake steps, of which SETUP and RECORD have no request text in the function body (a2dplaya/airplay/manager.py:292-296, a2dplaya/airplay/manager.py:298-353).
- Code state at the last commit, INFERRED from the searches cited in each record: declared parts with no user are recorded at R1-H5-01, R1-H5-02, R1-H5-03, R1-H5-04, R1-H5-05, R1-H5-10, R1-H5-11 and R1-H7-02. All of them entered in commit 47701ce and no later commit touches them.
- No commit message, note or comment states that work was stopped, paused or abandoned (the four commit bodies; search terms listed at R1-H6-01).

## Tally

- Questions answered with findings: 5 (H1, H2, H5, H7, H8)
- Questions answered NOT FOUND: 3 (H3, H4, H6)
- Total: 8
- Finding records: 23 (R1-H2-01; R1-H5-01 to R1-H5-11; R1-H7-01 to R1-H7-11)
- NOT FOUND records: 3 (R1-H3-01, R1-H4-01, R1-H6-01)
