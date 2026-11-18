# R4 verification (Aurilis)

- Repo path: /home/user/Aurilis
- HEAD when verified: d740bc2b39836a0af00d8ad35144d31511bc9060. `git status --porcelain` printed nothing at the start and at the end.
- Verified 2026-11-17. Nothing was built, installed, run, checked out or fetched.
- Records checked: 02-stages-bluetooth.md (file time 07:53 UTC), 03-stages-audio.md (18:29 UTC) and 04-history.md (07:54 UTC) in /home/user/audio/repos/R4/. The verifier did not write them.
- This file reports on the records. It adds no finding about the repo and changes no record.
- A file of this name already existed (file time 18:28 UTC, one minute older than 03-stages-audio.md). It was opened only after the checks below were complete, and this file replaces it. Its single FAIL quoted a sentence for R4-S07-03, "Gain is clamped to ±12 dB", that the present 03-stages-audio.md does not contain. The verdict for R4-S07-03 below is on the present text.

## Method

- Read in full, with line numbers: all 18 non-test `.go` files, README.md, scripts/install.sh, systemd/aurilis.service, go.mod, .gitignore, web/static/index.html, web/static/js/app.js. Read in part: go.sum (first 60 characters of each of its 6 lines), test/integration_test.go (lines 1-30, 44-56, 140-233), internal/pipeline/ringbuffer_test.go (77-82, 105-124), internal/dsp/analyzer_test.go (96-124), internal/dsp/volume_test.go (27-62, 96-118). The other test files and web/static/css/style.css were searched, not read.
- Every path:line citation in every record was compared with those readings. Every "no hits", "only" or "no caller" search claim in a finding record was re-run with `grep -rnIiE` over the tree (`.git` and go.sum left out).
- /home/user/audio/00-manifest.md and /home/user/audio/repos/R4/01-map.md were read in full, because records cite them. The manifest holds rows for other repos; nothing from those rows is used here.
- git commands that Rule 1 names: `ls-files`, `status --porcelain`, `log` (with `--all`, `--format`, `--grep`, `--diff-filter`, `--name-only`, `--name-status`), `show --stat`, `show REF:PATH`, `diff --name-status`, `diff --stat`, `branch -a -v`, `branch -a --contains`, `tag`, `stash list`, `rev-parse HEAD`, `rev-parse --is-shallow-repository`.
- Disclosure. Six read-only git commands that Rule 1 does not name were run, each to open a citation in 04-history.md that rests on the same command: `rev-list --all --count`, `for-each-ref`, `reflog --all`, `config --get-all remote.origin.fetch`, `notes list`, `cat-file -t 006d973151bd00505c9ea0747c429291a1546230`. None writes to the repo. The file .git/FETCH_HEAD and the listings of `.git` and `.git/refs` were read as files.
- Not run: `git ls-remote`, and any other command that contacts the remote. See the BLOCKED line.

How each check was applied:

- A. A record fails when a cited location is absent, or when the location does not support the claim made from it. A line range that is off by a few lines, a search summary that leaves out a hit with no bearing on the claim, and a wording slip that the cited lines themselves make plain are listed under "Slips noted in PASS records" and are not failed.
- B. The grade on each outcome and each cause was compared with Rule 3. Each statement labelled INFERRED was checked for a sentence of reasoning.
- C. WORKED, PARTIAL or FAILED was accepted only with OBSERVED or STATED evidence of the result. UNVERIFIED was accepted where the repo holds no evidence of the result for the thing recorded.
- D. The three files were searched for the Rule 6 word list, for other proposing or ranking wording, and for the names and IDs of other repos. Each hit was read in context.
- E. One search per NOT FOUND record, with terms the record did not use. Terms and hits are in "Check E searches".

## Verdicts

### File 02 (43 finding records, 2 NOT FOUND records)

- R4-S01-01 PASS
- R4-S01-02 PASS
- R4-S01-03 PASS
- R4-S01-04 PASS
- R4-S01-05 PASS
- R4-S01-06 PASS
- R4-S02-01 PASS
- R4-S02-02 PASS
- R4-S02-03 PASS
- R4-S02-04 PASS
- R4-S02-05 PASS
- R4-S02-06 PASS
- R4-S02-07 PASS
- R4-S02-08 PASS
- R4-S02-09 PASS
- R4-S03-01 PASS
- R4-S03-02 PASS
- R4-S03-03 PASS
- R4-S03-04 PASS
- R4-S03-05 PASS
- R4-S03-06 PASS
- R4-S03-07 PASS
- R4-S03-08 PASS
- R4-S03-09 PASS
- R4-S03-10 PASS
- R4-S04-01 PASS
- R4-S04-02 PASS
- R4-S04-03 PASS
- R4-S04-04 PASS
- R4-S04-05 PASS
- R4-S04-06 PASS
- R4-S04-07 PASS
- R4-S04-08 PASS
- R4-S04-09 PASS
- R4-S05-01 PASS
- R4-S05-02 PASS
- R4-S05-03 PASS
- R4-S05-04 PASS
- R4-S05-05 PASS
- R4-S05-06 PASS
- R4-S05-07 PASS
- R4-S05-08 PASS
- R4-S06-01 PASS (NOT FOUND; check E found nothing)
- R4-S06-02 PASS
- R4-S06-03 PASS (NOT FOUND; check E found nothing)

### File 03 (58 finding records, 3 NOT FOUND records)

- R4-S07-01 PASS
- R4-S07-02 PASS
- R4-S07-03 PASS
- R4-S07-04 PASS
- R4-S07-05 PASS
- R4-S07-06 PASS
- R4-S07-07 PASS
- R4-S07-08 PASS
- R4-S07-09 PASS
- R4-S07-10 PASS (NOT FOUND; check E found nothing)
- R4-S08-01 PASS
- R4-S08-02 PASS
- R4-S08-03 PASS
- R4-S08-04 PASS
- R4-S08-05 PASS
- R4-S08-06 PASS
- R4-S08-07 PASS
- R4-S08-08 PASS
- R4-S08-09 PASS
- R4-S08-10 PASS
- R4-S08-11 PASS
- R4-S08-12 PASS
- R4-S09-01 PASS
- R4-S09-02 PASS
- R4-S09-03 PASS
- R4-S09-04 PASS
- R4-S09-05 PASS
- R4-S09-06 PASS
- R4-S09-07 PASS
- R4-S09-08 PASS
- R4-S09-09 PASS
- R4-S09-10 PASS
- R4-S09-11 PASS
- R4-S09-12 PASS
- R4-S09-13 PASS
- R4-S09-14 PASS
- R4-S09-15 PASS
- R4-S10-01 PASS
- R4-S10-02 PASS
- R4-S10-03 PASS
- R4-S10-04 PASS
- R4-S10-05 PASS
- R4-S10-06 PASS
- R4-S10-07 PASS
- R4-S10-08 PASS
- R4-S10-09 PASS
- R4-S10-10 PASS
- R4-S10-11 PASS
- R4-S11-01 PASS
- R4-S11-02 PASS
- R4-S11-03 PASS (NOT FOUND; check E found nothing that answers S11.2)
- R4-S11-04 PASS (NOT FOUND; check E found nothing that answers S11.3)
- R4-S11-05 PASS
- R4-S12-01 PASS
- R4-S12-02 PASS
- R4-S12-03 PASS
- R4-S12-04 PASS
- R4-S12-05 PASS
- R4-S12-06 PASS
- R4-S12-07 PASS
- R4-S12-08 PASS

### File 04 (29 finding records, 3 NOT FOUND records, 2 factual lists)

The two factual lists (H1, H8) are neither finding records nor NOT FOUND records. Their facts were compared with the repo (check A, and check D for wording) and each gets a verdict line.

- R4-H1-01 PASS (list; the three hashes, dates, authors, parents, the two gaps, the 22 h 13 min span, the 267-day interval, the `git show --stat` figures and the one-empty-line first README all match)
- R4-H2-01 PASS (NOT FOUND; check E found nothing)
- R4-H3-01 PASS (NOT FOUND; check E found nothing)
- R4-H4-01 no verdict, BLOCKED on check A: the record rests on the output of `git ls-remote --heads --tags origin`, which cannot be opened from the clone. The clone-side statements were opened and match: `git cat-file -t 006d973151bd00505c9ea0747c429291a1546230` fails with "could not get object info"; `git reflog --all` holds "fetch --no-progress --depth 50 origin main: storing head"; the refspec is as quoted; all four refs are at d740bc2. Checks B, C and D: no failure.
- R4-H5-01 PASS
- R4-H5-02 PASS
- R4-H5-03 PASS
- R4-H5-04 PASS
- R4-H5-05 PASS
- R4-H5-06 PASS
- R4-H5-07 PASS
- R4-H5-08 PASS
- R4-H5-09 PASS
- R4-H5-10 PASS
- R4-H5-11 PASS
- R4-H5-12 PASS
- R4-H5-13 PASS
- R4-H5-14 PASS
- R4-H6-01 PASS (NOT FOUND; check E found nothing)
- R4-H7-01 PASS
- R4-H7-02 PASS
- R4-H7-03 PASS
- R4-H7-04 PASS
- R4-H7-05 PASS
- R4-H7-06 PASS
- R4-H7-07 PASS
- R4-H7-08 PASS
- R4-H7-09 PASS
- R4-H7-10 PASS
- R4-H7-11 PASS
- R4-H7-12 PASS
- R4-H7-13 PASS
- R4-H7-14 PASS
- R4-H8-01 PASS (list; the three subjects, the message bodies, the clean tree, the stash count at 00-manifest.md:25 and the no-hit search for `phase` all match)

## Check E searches

Each tree search was case-insensitive over the tracked files with go.sum left out, and with web/static/css/style.css left out except where stated. Commit-message searches name their git command.

| Record | Question | Verifier's terms | Result |
|--------|----------|------------------|--------|
| R4-S06-01 | S06.1 | `play`, `pause`, `skip`, `next`, `prev`, `rewind`, `forward`, `resume`, `remote.?control`, `keypress`, `passthrough`, `media.?key`, `Control1` | 69 hits, none on transport control toward the source: the browser playback code and its labels (web/static/index.html:121-126; web/static/js/app.js:11, 349-416), "playback" in README.md:13, internal/stream/http_stream.go:16, 18 and internal/pipeline/interfaces.go:17, "display" in README.md:14 and internal/bluetooth/pairing_agent.go:67-81, "skip" in byte, sample and transient comments (app.js:59; internal/audio/bluetooth_input.go:205; internal/pipeline/ringbuffer.go:95; scripts/install.sh:185; three test files), `aplay` (scripts/install.sh:192), "previously" (internal/bluetooth/bluez.go:478, 519), `nextID`, `NextWriter` and `forwardBluetoothEvents` identifiers, and the constant at internal/bluetooth/bluez.go:18 that the record already names. Nothing found. |
| R4-S06-03 | S06.3 | `now.?playing`, `song`, `genre`, `cover`, `artwork`, `\bid3\b`, `player`, `"Status"`, `playback.?status`, `Position`, `Duration` | 44 hits, none on track metadata: `cover` inside "discover", "Discovering", "StartDiscovery", "DeviceDiscovery"; `Duration` in `NewRingBufferDuration`, `durationMs` and `time.Duration`; "position" in ring-buffer comments (internal/pipeline/ringbuffer.go:57, 135; test/integration_test.go:87); the `status` message-type constant (internal/websocket/hub.go:28); the constant at internal/bluetooth/bluez.go:18 that the record already names. Nothing found. |
| R4-S07-10 | S07.3 | `skew`, `ppm`, `adaptive`, `rate.?match`, `timestamp`, `\bpts\b`, `monotonic`, `time\.Since`, `time\.Now`, `sample.?count`, `catch.?up`, `speed`, `stretch`, `compensat`, `synchron` | 4 hits, all `time.Now()` for WebSocket deadlines (internal/websocket/hub.go:510, 512, 541, 565). Nothing found. |
| R4-S11-03 | S11.2 | `ipad`, `tablet`, `galaxy`, `oneplus`, `xiaomi`, `huawei`, `sony`, `bose`, `spotify`, `chrome`, `firefox`, `safari`, `\bios\b`, `macos`, `compatib`, `verified`, `confirmed`, `works (on|with)`, `tried`, `headset`, `speaker` | 8 hits: README.md:9, already in the record; web/static/js/app.js:365, a comment naming "Safari/iOS" as a browser playback platform, with no test and no result; "compatible" and "compatibility" about ALSA and browsers (README.md:80, 244; internal/stream/http_stream.go:18, 58); two test log strings (test/integration_test.go:138-139). No source device named as tested was found. |
| R4-S11-04 | S11.3 | `func Benchmark`, `b\.N`, `elapsed`, `profil`, `\bperf`, `throughput`, `glitch`, `stutter`, `cpu`, `\bram\b`, `\bms\b`, `millisec`, `fps`, `percent`, `usage`, `stats`, `statistics`, `counter` | 8 hits, none a recorded measurement: "profile" and `Profile1` (README.md:277; scripts/install.sh:409); a parameter comment (internal/pipeline/ringbuffer.go:32); `rb.nextID` (ringbuffer.go:63, 64); ticker constants (internal/websocket/hub.go:274; internal/stream/http_stream.go:75, 126). Nothing found. |
| R4-H2-01 | H2 | `git log --all -i -E --grep` with `instead`, `originally`, `initially`, `moved`, `was using`, `v2`, `new approach`, `rework`, `redesign`, `overhaul`, `supersede`, `change`; `git log --all --diff-filter=RDC -M --name-status`; tree search for `instead`, `originally`, `initially`, `in favou?r`, `rather than`, `changed (to|from)`, `now uses`, `earlier`, `v1\b`, `v2\b`, `supersed`, `redesign`, `rework`, `overhaul`, `ported`, `converted` | No commit matched. No rename, deletion or copy in any commit. Tree: 14 hits, none describing one approach replacing another: README.md:301 (hardware advice, quoted in R4-H7-14); "supported" and "unsupported" strings (scripts/install.sh:44, 55, 56, 78; internal/stream/http_stream.go:49, 113); version strings (go.mod:7; README.md:3; scripts/install.sh:4; web/static/js/app.js:2); a test variable `v2` (internal/dsp/volume_test.go:21-23). Nothing found. |
| R4-H3-01 | H3 | `git log --all -i -E --grep` with `undo`, `rollback`, `roll back`, `back out`, `oops`, `typo`, `again`, `retry`, `still`, `another`, `address`, `resolve`, `issue`, `problem`, `error`, `wrong`, `crash`, `fail`, `cleanup`, `tweak`, `adjust`; per-path commit count | No commit matched. README.md is in 2 commits and every other path in 1. Nothing found. |
| R4-H6-01 | H6 | Case-sensitive: `NOTE`, `NB`, `WARNING`, `CAUTION`, `IMPORTANT`, `DEPRECATED`, `KLUDGE`, `OPTIMIZE`, `REVIEW`, `TBD`, `WIP`, `UNDONE`, `REFACTOR`, `TEMP` as whole words; `@todo`, `not yet`, `unfinished`, `incomplete`, `unimplemented`, `to be (done|added|implemented)`, `needs? (work|to be)`; `git ls-files` filtered for `readme`, `license`, `contrib`, `authors`, `release`, `version`, `bugs`, `faq`, `.txt`, `.md`, `.rst`, `.org` | Tree: no hit. Files: README.md and internal/version/version.go only. Nothing found. |

## Slips noted in PASS records

These did not change a verdict. In each case the claim that the record builds on holds at the lines named here.

| Record | Detail |
|--------|--------|
| R4-S02-02 | The list of readers of the manager's active-adapter field leaves out the status broadcast, which reads it through `GetActiveAdapter` (internal/websocket/hub.go:310). |
| R4-S02-06 | The search `time\.Sleep|attempt` in `*.go` is summarised as "bluez.go:486-494 only". A further hit is the comment at internal/bluetooth/bluez.go:478, on the same function. |
| R4-S03-10 | The `allow` hit list leaves out web/static/css/style.css:246 (`not-allowed`). The `auth|password|token|login` summary leaves out "nologin" at scripts/install.sh:342 and README.md:161. |
| R4-S04-05 | "`range` occurs only as the Go loop keyword": the word also occurs as an HTML input type (web/static/index.html:50, 132; web/static/js/app.js:233) and in two CSS selectors (web/static/css/style.css:323, 368). None concerns radio range. |
| R4-S07-03 | The shelf functions are said to be called from internal/dsp/biquad_test.go:121, 130. Those lines are the test declarations; the calls are at lines 122 and 131 (as R4-H5-09 has them). |
| R4-S07-04 | The volume tests are described as using "constant-value buffers (internal/dsp/volume_test.go:8-118)". Lines 31, 45 and 62 use four-value sequences with differing values; lines 102-105 use a constant buffer. |
| R4-S07-05 | The analyser tests are described as using "generated sine waves (internal/dsp/analyzer_test.go:29-63, 104-124)". Lines 104-124 (`TestFFT_KnownValues`) use the vector 1, 1, 1, 1 (line 106). |
| R4-S07-09 | "no other reader of the field exists per `git grep BufferSizeMs`": test/integration_test.go:21 also reads it (R4-S07-08 cites that line). |
| R4-S09-10 | "four prompts": a fifth, conditional prompt follows a port found in use (scripts/install.sh:238). |
| R4-S09-15 | The glyph change on the `error` event is cited to web/static/js/app.js:375-383, 406-416. The `error` listener is at app.js:369-373; lines 375-383 hold `playing` and `ended`. |
| R4-S10-04 | "A warning-only start exits with no error": after the warnings the process goes on to wait for a signal (cmd/aurilis/main.go:203-205) and does not exit. |
| R4-S10-06 | "clamping volume and EQ gain (config.go:118-149)": the EQ gain clamp at internal/config/config.go:142-147 is inside `SetEQBand`, which R4-S07-03 and R4-H5-10 list with test callers only; the hub writes the bands directly (internal/websocket/hub.go:404-410). The volume clamp is on the run path (hub.go:445). |
| R4-S10-07 | "declared, defaulted, documented and prompted for" is said of both fields. The prompt at scripts/install.sh:249-251 is for the data directory only; `auto_reconnect` is written as a fixed value at install.sh:331. |
| R4-S11-04 | The hit list for `metric` leaves out internal/dsp/equalizer_test.go:143 ("Parametric" again). |
| R4-H2-01 | go.mod:10 is listed as a hit of the tree search. No term in the record's list matched that line when the search was re-run; the other five listed hits matched. |
| R4-H7-04 | "otherwise it logs and exits unless the error matches": at internal/audio/bluetooth_input.go:194-197 the loop returns in both cases; a closed-file error suppresses the log line only (R4-S05-06 words the same lines that way). |

## Observations outside checks A to E

No verdict is attached to these. They are about the three files, not about the repo.

- R4-S07-03, present text. Every citation in it was opened: the ±12 clamp at internal/dsp/equalizer.go:81-86 is inside `SetBandGain`; its eight call sites are the test lines the record lists; `SetAllBands` (equalizer.go:94-106), the constructor (equalizer.go:21-38) and the hub copy (internal/websocket/hub.go:404-410) hold no clamp; the slider bound is at web/static/js/app.js:233.
- UNVERIFIED records that quote the test statement. Nine records quote "43 passing tests (unit + integration)" (commit c98d8d3) and state that it covers the unit in isolation: R4-S05-08, R4-S06-02, R4-S07-03, R4-S07-04, R4-S07-05, R4-S08-07, R4-S08-12, R4-S09-05, R4-S10-06. Under check C each was accepted as UNVERIFIED. In seven of them (the 03 records) the Outcome evidence field reads "NONE for ..." with added text, where the template gives "NONE".
- The two WORKED records (R4-S11-01, R4-S11-02) rest on that same statement, graded STATED. The statement counts 43; the tree holds 54 `^func Test` lines (50 in six unit-test files, 4 in test/integration_test.go) and no `t.Run`. Both records state the difference.
- Confidence against the Rule 5 wording (Rule 5 is not among checks A to D). R4-S02-05 and R4-H7-14 give LOW for STATED material alone, where Rule 5 names MEDIUM. R4-S01-04, R4-S03-04, R4-S03-10 and R4-S04-06 give MEDIUM for STATED material that the code agrees with, where Rule 5 names HIGH; each of the four has outcome UNVERIFIED and says so in its reason.
- Rule 8. R4-H5-08, R4-H5-10, R4-H5-11 and R4-H5-13 each list several separate unused items in one record.
- Rule 11. No record copies the fixed PIN or passkey value at internal/bluetooth/pairing_agent.go:62-77. No device address appears in the three files.
- Rule 6 word list. The only hits in the three files are "instead" inside quotations of README.md:301 (02 line 130; 03 lines 104 and 588; 04 line 409). "must" occurs inside quotations of internal/pipeline/interfaces.go:17 (03 line 157; 04 line 97). No name or ID of another repo occurs.
- Text outside records. The header of 02 (line 8) reports no hits for `iphone|android|pixel|samsung|macbook|windows`; a case-insensitive run hits `devicePixelRatio` at web/static/js/app.js:278, 279, 281 (R4-S11-03 lists those three). The S09 stage note in 03 (line 258) leaves out README.md:14 from its `display` hits.
- Tallies. 138 `### R4-` headings plus the two list IDs; no ID is repeated. Each file's closing tally matches the verifier's count: 02 has 43 and 2; 03 has 58 and 3; 04 has 29, 3 and 2 lists. Every finding record carries all nine template fields and every NOT FOUND record all three.
- 04-history.md discloses one command outside the Rule 1 list (`git fsck --lost-found --no-reflogs`). The `.git` listing it gives matches the present listing, and `.git/lost-found` is absent. Its Method section also lists `git ls-remote`, `git for-each-ref`, `git reflog`, `git notes list`, `git cat-file`, `git rev-list` and `git config --get-all`, which Rule 1 does not name.

## Coverage

"Records" lists the records whose Question field names the question. "Related history records" lists history finding records whose Question field says "relates to" that question; they are not counted as answering it.

| Question | Records | Related history records |
|----------|---------|-------------------------|
| S01.1 | R4-S01-01, R4-S01-02 | |
| S01.2 | R4-S01-03 | |
| S01.3 | R4-S01-04 | |
| S01.4 | R4-S01-05 | |
| S01.5 | R4-S01-06 | |
| S02.1 | R4-S02-01, R4-S02-02, R4-S02-03 | |
| S02.2 | R4-S02-04, R4-S02-05 | R4-H7-14 |
| S02.3 | R4-S02-06, R4-S02-07, R4-S02-08, R4-S02-09 | |
| S03.1 | R4-S03-01, R4-S03-02, R4-S03-03 | R4-H5-04 |
| S03.2 | R4-S03-04, R4-S03-05 | |
| S03.3 | R4-S03-06, R4-S03-07, R4-S03-08 | R4-H5-05 |
| S03.4 | R4-S03-09 | |
| S03.5 | R4-S03-10 | |
| S04.1 | R4-S04-01 | |
| S04.2 | R4-S04-02, R4-S04-03 | |
| S04.3 | R4-S04-04, R4-S04-05, R4-S04-06, R4-S04-07, R4-S04-08 | R4-H5-06, R4-H7-01 |
| S04.4 | R4-S04-09 | |
| S05.1 | R4-S05-01, R4-S05-02 | R4-H7-02 |
| S05.2 | R4-S05-03, R4-S05-04 | R4-H5-11, R4-H7-03 |
| S05.3 | R4-S05-05, R4-S05-06 | |
| S05.4 | R4-S05-07, R4-S05-08 | R4-H7-04, R4-H7-05 |
| S06.1 | R4-S06-01 (NOT FOUND) | R4-H5-03 |
| S06.2 | R4-S06-02 | |
| S06.3 | R4-S06-03 (NOT FOUND) | |
| S07.1 | R4-S07-01, R4-S07-02, R4-S07-03, R4-S07-04, R4-S07-05, R4-S07-06, R4-S07-07 | R4-H5-09 |
| S07.2 | R4-S07-08, R4-S07-09 | R4-H7-08 |
| S07.3 | R4-S07-10 (NOT FOUND) | |
| S08.1 | R4-S08-01, R4-S08-02, R4-S08-03, R4-S08-04 | R4-H5-01 |
| S08.2 | R4-S08-05, R4-S08-06 | R4-H5-12, R4-H7-07 |
| S08.3 | R4-S08-07, R4-S08-08, R4-S08-09, R4-S08-10, R4-S08-11 | |
| S08.4 | R4-S08-12 | |
| S09.1 | R4-S09-01, R4-S09-02, R4-S09-03, R4-S09-04, R4-S09-05 | |
| S09.2 | R4-S09-06, R4-S09-07, R4-S09-08, R4-S09-09 | R4-H5-08, R4-H7-11 |
| S09.3 | R4-S09-10, R4-S09-11, R4-S09-12 | |
| S09.4 | R4-S09-13, R4-S09-14, R4-S09-15 | R4-H7-09, R4-H7-10 |
| S10.1 | R4-S10-01, R4-S10-02 | R4-H5-13, R4-H5-14, R4-H7-13 |
| S10.2 | R4-S10-03, R4-S10-04 | R4-H7-12 |
| S10.3 | R4-S10-05 | |
| S10.4 | R4-S10-06, R4-S10-07 | R4-H5-07 |
| S10.5 | R4-S10-08, R4-S10-09 | |
| S10.6 | R4-S10-10, R4-S10-11 | |
| S11.1 | R4-S11-01, R4-S11-02 | |
| S11.2 | R4-S11-03 (NOT FOUND) | |
| S11.3 | R4-S11-04 (NOT FOUND) | |
| S11.4 | R4-S11-05 | |
| S12.1 | R4-S12-01, R4-S12-02, R4-S12-03, R4-S12-04 | |
| S12.2 | R4-S12-05, R4-S12-06 | |
| S12.3 | R4-S12-07, R4-S12-08 | |
| H1 | R4-H1-01 (list) | |
| H2 | R4-H2-01 (NOT FOUND) | |
| H3 | R4-H3-01 (NOT FOUND) | |
| H4 | R4-H4-01 (check A not completed, see BLOCKED) | |
| H5 | R4-H5-01 to R4-H5-14 (14 records) | |
| H6 | R4-H6-01 (NOT FOUND) | |
| H7 | R4-H7-01 to R4-H7-14 (14 records) | |
| H8 | R4-H8-01 (list) | |

History finding records that relate to no stage question: R4-H4-01, R4-H5-02, R4-H5-10, R4-H7-06.

Questions checked: 56 (S01.1 to S12.3: 48; H1 to H8: 8). Questions with at least one record: 56. MISSING: none.

## BLOCKED

BLOCKED: check A for R4-H4-01 was not completed. The record's evidence is the output of `git ls-remote --heads --tags origin`: a branch `claude/go-cross-platform-action-VZiQD` on the remote at commit 006d973151bd00505c9ea0747c429291a1546230. Neither the branch name nor the commit is in the clone (refs, .git/FETCH_HEAD, reflog and object store were checked), `git ls-remote` is not among the commands Rule 1 names, and Rule 1 does not permit a fetch. The existence of the remote branch, its tip hash, and the remote head list in the "Refs checked" notes of 04-history.md are therefore unconfirmed by the verifier. The BLOCKED line at 04-history.md:442 concerns the same branch.

## Totals

| | PASS | FAIL | Not completed (BLOCKED) | Total |
|---|------|------|-------------------------|-------|
| Finding records | 129 | 0 | 1 | 130 |
| NOT FOUND records | 8 | 0 | 0 | 8 |
| Factual lists (H1, H8) | 2 | 0 | 0 | 2 |
| All verdict lines | 139 | 0 | 1 | 140 |

- PASS: 139
- FAIL: 0
- MISSING: 0
- Not completed: 1 (R4-H4-01, check A, see BLOCKED)
