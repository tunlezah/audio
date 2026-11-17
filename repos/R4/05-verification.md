# R4 verification (Aurilis)

- Repo path: /home/user/Aurilis
- HEAD when verified: d740bc2b39836a0af00d8ad35144d31511bc9060. `git status --porcelain` printed nothing before and after.
- Verified 2026-11-17. Nothing was built, installed, run, checked out or fetched.
- Records checked: /home/user/audio/repos/R4/02-stages-bluetooth.md, 03-stages-audio.md, 04-history.md. The verifier did not write them.
- This file reports on the records. It adds no finding about the repo and changes no record.

## Method

- Files read in full, with line numbers: every non-test `.go` file (18), test/integration_test.go, scripts/install.sh, systemd/aurilis.service, README.md, go.mod, go.sum, .gitignore, web/embed.go, web/static/index.html, web/static/js/app.js. The six unit-test files under internal/ were read at the cited line ranges and searched. web/static/css/style.css was searched, not read.
- Every path:line citation in every record was compared with those readings. Every "search: no hits" or "only at" claim in a finding record was re-run with `grep -rnE` over the tree (`.git` and go.sum left out).
- git commands run in the repo: `log`, `show --stat`, `show REF:PATH`, `diff --name-status`, `diff --stat`, `branch -a -v`, `tag`, `stash list`, `rev-parse`, `status`. One `git grep` was run as a file search for `^func Test`. The files .git/config and .git/logs/* were read as files.
- /home/user/audio/00-manifest.md was opened at the lines the records cite (3, 25, 31, 35, 37-39). The snapshot table printed with them holds rows for other repos; nothing from those rows is used here.
- Not run: `git ls-remote` and any other command that contacts the remote. See the BLOCKED line.

How each check was applied:

- A. A record fails when a cited location is absent, or when the location does not support the claim made from it. A range that is off by a few lines, or a search summary that omits a hit with no bearing on the claim, is listed under "Citation slips" and is not failed.
- B. The grade given to each outcome and each cause was compared with Rule 3. Each INFERRED statement was checked for a reasoning sentence.
- C. WORKED, PARTIAL or FAILED was accepted only with OBSERVED or STATED evidence of the result. UNVERIFIED was accepted where no evidence of the result for the thing recorded exists.
- D. All three files were searched for the Rule 6 word list and for evaluative or proposing wording, and for references to another repo. Each hit was read in context.
- E. One search with terms the record did not use, per NOT FOUND record. Terms and hits are in the section "Check E searches".

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
- R4-S07-03 FAIL A: "Gain is clamped to ±12 dB" is cited to internal/dsp/equalizer.go:81-86, which lies inside `SetBandGain`; that method has test callers only (internal/dsp/equalizer_test.go:64; test/integration_test.go:167), and the setter the hub calls, `SetAllBands` (equalizer.go:94-106, called at internal/websocket/hub.go:403), holds no clamp.
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

The two factual lists are neither finding records nor NOT FOUND records. Their facts were compared with the repo (check A only) and each gets a verdict line.

- R4-H1-01 PASS (list; dates, gaps, counts, `git show --stat` figures, first README content and the 267-day interval all match)
- R4-H2-01 PASS (NOT FOUND; check E found nothing)
- R4-H3-01 PASS (NOT FOUND; check E found nothing)
- R4-H4-01 BLOCKED on check A, not counted as PASS or FAIL: the record rests on a remote ref list (`git ls-remote`) that cannot be opened from the clone. Clone-side claims were opened and match: commit 006d973151bd00505c9ea0747c429291a1546230 is absent (`git rev-parse --verify` fails); .git/logs/refs/remotes/origin/main holds "fetch --no-progress --depth 50 origin main: storing head"; .git/config:10 holds the refspec quoted. Checks B, C and D: no failure.
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
- R4-H8-01 PASS (list; three commit subjects, message bodies, clean tree, stash count and the no-hit search for `phase` all match)

## Check E searches

Each search was case-insensitive over the tracked tree with go.sum left out, unless a git command is named.

| Record | Question | Verifier's terms | Result |
|--------|----------|------------------|--------|
| R4-S06-01 | S06.1 | `skip`, `rewind`, `fast.?forward`, `remote.?control`, `key.?press`, `uinput`, `110e`, `110c`, `110f`, `Control1`, `\bplayer\b`, `\bnext\b`, `previous` | 10 hits, none on transport control: comments on skipping bytes, samples or transients (web/static/js/app.js:59; internal/audio/bluetooth_input.go:205; internal/pipeline/ringbuffer.go:95; test files), scripts/install.sh:185, and "previously" at internal/bluetooth/bluez.go:478, 519. Nothing found. |
| R4-S06-03 | S06.3 | `now.?playing`, `song`, `genre`, `playback.?status`, `PlaybackStatus`, `GetAll`, `artwork`, `cover`, `"Status"`, `subtitle`, `lyrics` | 28 hits, all from `cover` inside "discover", "Discovering", "StartDiscovery", "DeviceDiscovery". Nothing found. |
| R4-S07-10 | S07.3 | `timestamp`, `skew`, `ppm`, `adaptive`, `time\.Now`, `monoton`, `catch.?up`, `playbackRate`, `rate.?match`, `slew`, `\bpll\b`, `fell behind`, `falls behind`, `pace` | 15 hits: CSS `space-between`, `white-space`, `monospace`; `time.Now()` for WebSocket deadlines (internal/websocket/hub.go:510, 512, 541, 565); internal/pipeline/ringbuffer.go:95, which the record already gives as nearest material. Nothing found. |
| R4-S11-03 | S11.2 | `galaxy`, `ipad`, `mac ?os`, `\bios\b`, `oneplus`, `xiaomi`, `huawei`, `chromebook`, `thinkpad`, `works with`, `verified (on|with)`, `confirmed`, `safari`, `chrome`, `firefox`, `tablet` | 2 hits: README.md:9, already in the record; web/static/js/app.js:365, a comment naming "Safari/iOS" as a browser playback platform, with no test and no result (the comment is recorded at R4-S08-03 and R4-H5-12). No source device named as tested was found. |
| R4-S11-04 | S11.3 | `func Benchmark`, `testing\.B`, `time\.Since`, `elapsed`, `load average`, `\brss\b`, `profil`, sizes in MB, KB or GB, percentages, `range of`, `metres`, `meters`, `hours`, `days`; a second pass for numbers followed by `ms`, `msec` or `fps` outside test files | First pass: 18 hits, all CSS percentages and web/static/index.html:51. Second pass: 3 hits, all design constants in comments (internal/websocket/hub.go:274; internal/stream/http_stream.go:73; internal/dsp/volume.go:23). No recorded measurement was found. |
| R4-H2-01 | H2 | `instead of`, `rather than`, `originally`, `initially`, `alternative`, `moved to`, `changed to`, `now uses`, `was using`, `supersede`, `\.bak`, `backup`, `_old`, `old_`, `v2\b`, `shell wrapper`, `exec\.Command`; `git log --all -S` for `exec.Command`, `bluetoothctl`, `pulseaudio`, `bluealsa`, `libsbc`, `pipewire`; `git log --all --diff-filter=R`; `git log --all --shortstat` | Tree: 5 hits. README.md:10 is already in the record as nearest material; README.md:301 is hardware advice; three are a test variable named `v2` (internal/dsp/volume_test.go:21-23). `git log -S`: 0 commits for each term. Renames: 0. Deletions: 0 in all three commits. Nothing found. |
| R4-H3-01 | H3 | `git log --all -i -E --grep` with `undo`, `rollback`, `roll back`, `back ?out`, `oops`, `typo`, `again`, `still`, `resolve`, `address`, `tweak`, `adjust`, `clean ?up`, `issue`, `problem`, `error`, `fail`, `crash`, `amend`, `redo`; paths touched by three or more commits | No commit matched. No path is touched by three commits. Nothing found. |
| R4-H6-01 | H6 | `\bNOTE\b`, `N\.B\.`, `caveat`, `kludge`, `\bTBD\b`, `\bWIP\b`, `\?\?\?`, `!!!`, `not supported`, `unsupported`, `experimental`, `untested`, `unfinished`, `incomplete`, `quirk`, `gotcha`, `beware` | 6 hits, none an author's marker: installer error strings (scripts/install.sh:44, 55, 78), a log string (internal/bluetooth/a2dp_sink.go:95), an HTTP error string (internal/stream/http_stream.go:49, 113). Nothing found. |

## Citation slips noted in PASS records

These did not change a verdict. Each is a wording or range detail where the claim holds at the lines named here.

| Record | Detail |
|--------|--------|
| R4-S02-06 | The search `time\.Sleep|attempt` in `*.go` is summarised as "bluez.go:486-494 only". A fourth hit is the comment at internal/bluetooth/bluez.go:478. |
| R4-S03-10 | The `allow` hit list omits web/static/css/style.css:246 (`not-allowed`). The `auth|password|token|login` summary omits "nologin" at scripts/install.sh:342 and README.md:161. |
| R4-S07-05 | The analyser tests are described as using "generated sine waves (analyzer_test.go:29-63, 104-124)". Lines 29-63 use a sine wave. Lines 104-124 (`TestFFT_KnownValues`) use the constant vector 1, 1, 1, 1. |
| R4-S09-15 | The play-button glyph change on the `error` event is cited to web/static/js/app.js:375-383, 406-416. The `error` listener is at app.js:369-373; lines 375-383 hold `playing` and `ended`. |
| R4-S10-06 | "clamping volume and EQ gain (config.go:118-149)": the EQ gain clamp at internal/config/config.go:142-147 sits in `SetEQBand`, which R4-H5-10 lists with test callers only; the hub writes `cfg.EQBands` directly (internal/websocket/hub.go:404-410). The volume clamp is on the run path (hub.go:445). Same subject as the R4-S07-03 failure. |
| R4-S10-07 | "declared, defaulted, documented and prompted for" is said of both fields. The prompt at scripts/install.sh:249-251 is for the data directory only. |
| R4-H7-04 | "otherwise it logs and exits unless the error matches": at internal/audio/bluetooth_input.go:194-197 the loop returns in both cases; the match with a closed-file error suppresses the log line only. R4-S05-06 words the same code that way. |

## Observations outside checks A to E

No verdict is attached to these. They are about the records, not about the repo.

- Outcome with a stated test claim attached. Nine UNVERIFIED records quote the commit statement "43 passing tests (unit + integration)" (commit c98d8d3) and say that it covers the unit in isolation and not the thing recorded: R4-S05-08, R4-S06-02, R4-S07-03, R4-S07-04, R4-S07-05, R4-S08-07, R4-S08-12, R4-S09-05, R4-S10-06. Under check C each was accepted as UNVERIFIED. In seven of them the Outcome evidence field reads "NONE for ..." with added text where the template gives "NONE".
- The two WORKED records (R4-S11-01, R4-S11-02) rest on that same statement, graded STATED. The statement counts 43 tests; the tree holds 54 `^func Test` lines (50 in six unit-test files, 4 in test/integration_test.go), which the verifier's count confirms. Both records state the difference.
- Confidence against the Rule 5 wording. R4-S02-05 and R4-H7-14 give LOW for STATED evidence alone, where Rule 5 names MEDIUM. R4-S01-04, R4-S03-04, R4-S03-10 and R4-S04-06 give MEDIUM for STATED evidence that the code agrees with, where Rule 5 names HIGH; each of the four has outcome UNVERIFIED and says so in its reason.
- Rule 8. R4-H5-08, R4-H5-10, R4-H5-11 and R4-H5-13 each list several separate unused items in one record.
- Rule 11. No record copies the fixed PIN or passkey value at internal/bluetooth/pairing_agent.go:62-77, and no device address appears in the three files.
- Rule 6 word list. The only hits in the three files are "instead" inside quotations of README.md:301 (02 line 130; 03 lines 104 and 588; 04 line 409) and "must" inside quotations of internal/pipeline/interfaces.go:17. No reference to another repo was found.
- Tallies. Record IDs are unique (138 `### R4-` headings plus the two list IDs). Each file's closing tally matches the verifier's count: 02 has 43 and 2; 03 has 58 and 3; 04 has 29, 3 and 2 lists. Every finding record carries all nine template fields and every NOT FOUND record all three.
- 04-history.md discloses one command outside the Rule 1 list (`git fsck --lost-found --no-reflogs`). The `.git` listing it gives matches the present listing and `.git/lost-found` is absent. The Method section of that file also lists `git ls-remote`, `git for-each-ref`, `git reflog`, `git notes list`, `git cat-file`, `git rev-list` and `git config --get-all`, which Rule 1 does not name.

## Coverage

"Records" lists the records whose Question field names the question. "Related history records" lists history finding records whose Question field says "relates to" that question.

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

BLOCKED: check A for R4-H4-01 was not completed. The record's evidence is the output of `git ls-remote --heads --tags origin`: a branch `claude/go-cross-platform-action-VZiQD` on the remote at commit 006d973151bd00505c9ea0747c429291a1546230. Neither the branch nor the commit is in the clone, `git ls-remote` is not among the commands Rule 1 names, and Rule 1 does not permit a fetch. The existence of the remote branch, its tip hash and the remote head list in the "Refs checked" notes of 04-history.md are therefore unconfirmed by the verifier. The BLOCKED line at 04-history.md:442 concerns the same branch.

## Totals

| | PASS | FAIL | Not completed (BLOCKED) | Total |
|---|------|------|-------------------------|-------|
| Finding records | 128 | 1 | 1 | 130 |
| NOT FOUND records | 8 | 0 | 0 | 8 |
| Factual lists (H1, H8) | 2 | 0 | 0 | 2 |
| All verdict lines | 138 | 1 | 1 | 140 |

- PASS: 138
- FAIL: 1 (R4-S07-03, check A)
- MISSING: 0
- Not completed: 1 (R4-H4-01, check A, see BLOCKED)
