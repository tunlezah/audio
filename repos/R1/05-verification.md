# R1 verification

Verifier output for repo R1 at /home/user/a2dplaya. Read-only. Files checked: 02-stages-bluetooth.md, 03-stages-audio.md and 04-history.md in /home/user/audio/repos/R1/. Repo paths below are relative to /home/user/a2dplaya. Line numbers refer to the tree at commit 70ad071.

Records checked: 104 finding records (34 in file 02, 47 in file 03, 23 in file 04) and 12 NOT FOUND records (6, 3 and 3). No record ID occurs twice.

State of the audit folder at the start of this pass: a file already existed at this path, last written 2026-11-17 18:29 UTC. Files 02 and 03 were last written after it (18:30 and 18:32 UTC); file 04 was last written at 07:35 UTC. This pass checked the three files as they stand now, made its own checks, and replaces the earlier file. None of the three files changed during this pass (`ls -la --time-style=full-iso` before and after).

## Method

- All 28 tracked files were read in full, apart from the style block of a2dplaya/web/static/index.html (lines 7-343), which was searched and read only at the lines that records cite.
- Check A: every path:line citation in every finding record was compared with the file text. Every search that a record names with its terms was run again with `git grep` on the tracked files, under the convention the record's file states (02-stages-bluetooth.md:9 for file 02; the quoted command for files 03 and 04). Commit citations were checked with `git log --all`, `git show`, `git ls-tree` and `git diff --stat`. Citations of /home/user/audio/00-manifest.md and /home/user/audio/repos/R1/01-map.md were opened. Arithmetic in records was recomputed.
- Check B: each grade was compared with Rule 3 (/home/user/audio/_harness/rules.md:9-13).
- Check C: each outcome was compared with Rule 4 (rules.md:15). Outcome counts: 100 UNVERIFIED with evidence NONE, 3 FAILED with STATED evidence (R1-S08-03, R1-S11-04, R1-H5-06), 1 WORKED with OBSERVED evidence (R1-H2-01).
- Check D: the three files were searched for the words listed in Rule 6 (rules.md:28), for the names and IDs of the other repos, and for comparison wording; every record was also read for proposals. The only hits are inside quotations of requirements.txt:14 and requirements.txt:17 (02-stages-bluetooth.md:39, 04-history.md:128).
- Check E: one search with terms that the record did not use, for each NOT FOUND record. Terms and results are in the next section.
- A FAIL line names the one statement that did not check out. Every other citation in that record was opened and matched.
- Git commands run in the repo, all of them reading only: log, show, diff --stat, status, branch -a, tag, stash list, ls-files, rev-parse, grep, ls-tree, for-each-ref, rev-list --count, reflog, count-objects. After the last command `git status --porcelain --ignored` prints nothing and `git rev-parse HEAD` returns 70ad071385a8dc79886a6b5596495f68cf9eb824.

## Check E searches

All searches are `git grep -n -i -E` over all tracked files unless stated. Terms are listed with commas; they were joined with the alternation bar when run.

| Record | Question | Terms used by the verifier | Result |
|---|---|---|---|
| R1-S03-10 | S03.4 | `len\(self\._devices`, other device, new connection, incoming, reject, refus, busy, one at a time, only one, kick, evict, existing, current device, in use, max_conn, concurrent, another | 2 hits: a2dplaya/audio/pipeline.py:43 and 228, both about threads. Nothing on a second device. |
| R1-S03-11 | S03.5 | pairable, bond, approv, confirm, permit, restrict, deny, denied, JustWorks, NoInputNoOutput, trusted, guest, owner, secure, unknown device | 11 hits: a2dplaya/bluetooth/manager.py:180 (covered by R1-S03-01); 46, 55, 213, 388, 542-544, 559 (covered by R1-S03-08 and R1-S03-09); a2dplaya/config.py:57 (covered by R1-S03-07); setup.py:35 (licence line). Nothing limits who may pair. |
| R1-S04-03 | S04.2 | bitpool, bitrate, kbps, a2dp_codec, vendor, opus, vorbis, flac, mpeg, subband, quality, negotiat, endpoint, MediaCodec, encoding | 12 hits: README.md:5, 87, 89; a2dplaya/__init__.py:7; a2dplaya/audio/resampler.py:6; a2dplaya/cast/__init__.py:5; a2dplaya/cast/manager.py:66, 430; a2dplaya/config.py:24; a2dplaya/web/server.py:6, 40, 339. None concerns a Bluetooth codec. |
| R1-S04-08 | S04.4 | two devices, both, other source, active source, last connected, most recent, first device, handover, hand over, `select.*source`, `source.*select`, sources, for dev in | 16 hits: the word "sources" or "resources" in docstrings, three `for dev in` loops (a2dplaya/bluetooth/manager.py:134, already in the record; a2dplaya/cast/manager.py:256; a2dplaya/airplay/manager.py:183), and the page's source tabs (a2dplaya/web/static/index.html:280, 570, 572), which the record cites. Nothing on more than one Bluetooth source. |
| R1-S06-01 | S06.1 | resume, rewind, `fast.?forward`, playpause, play/pause, toggle, remote control, media key, `key ?press`, uinput, mpris, player, passthrough | 13 hits: "record players" and "tape players" text (README.md:5, 12; a2dplaya/__init__.py:5-6; a2dplaya/audio/line_in.py:4-5), the page's section toggle (a2dplaya/web/static/index.html:401, 417, 429, 581, 582, 584) and a test name (tests/test_resampler.py:19). Nothing is sent to the source device. |
| R1-S06-03 | S06.3 | song, duration, position, elapsed, genre, cover art, artwork, id3, icy, `"Status"`, paused, media info, GetAll, `props\.Get\(` | 19 hits: adapter and device property reads (a2dplaya/bluetooth/manager.py:252, 375, 379, 386-388, 391), status words of outputs and routes, `chunk_duration` (a2dplaya/audio/pipeline.py:227), a fixed `icy-name` header on the /stream.mp3 route (a2dplaya/cast/manager.py:69) and two style rules. No track metadata or playback status of the source. |
| R1-S07-07 | S07.3 | skew, ppm, adaptive, `rate.?match`, resync, `catch.?up`, fill level, buffer level, monotonic, perf_counter, `time\.time`, pacing, throttl, slew, compensat, underflow, `real.?time` | 10 hits: a2dplaya/audio/fft_analyzer.py:4, 19, 79; a2dplaya/audio/pipeline.py:270; a2dplaya/bluetooth/manager.py:318-319, 343-344; a2dplaya/web/server.py:6; tests/test_config.py:45. None concerns drift or synchronisation. |
| R1-S11-02 | S11.2 | phone, tablet, laptop, turntable, headphone, compatib, supported devices, confirmed, tried, known to work, huawei, xiaomi, oneplus, windows, macos, ipad, echo dot, firestick | 6 hits: README.md:5; a2dplaya/__init__.py:5; a2dplaya/airplay/manager.py:5; a2dplaya/audio/__init__.py:5; a2dplaya/config.py:23, 30. No device is named as tested. |
| R1-S11-03 | S11.3 | profil, timeit, elapsed, "took ", throughput, bitrate, `\bRAM\b`, load average, stress, soak, hours, metres, meters, percent, usage, stabil, glitch, stutter, `\bfps\b` | 4 hits: README.md:55, 58; a2dplaya/__main__.py:4; a2dplaya/audio/fft_analyzer.py:35. No recorded measurement. |
| R1-H3-01 | H3 | `git log --all -i --grep=TERM` for restore, back out, again, correct, repair, patch, adjust, retry, redo, amend, regress, fail, error, wrong, issue, problem, cleanup, clean up, delete, drop, disable, re-add, typo, attempt; `git log --all --diff-filter=M --name-only`; `git log --all --diff-filter=R --name-only`; `git log --all --oneline -- PATH` for each package directory, tests and install.sh | No commit for any term. The only modified file in the history is README.md in commit 47701ce. No rename. No path has more than 2 commits. |
| R1-H4-01 | H4 | `git log --all --not main --oneline`; `git branch -a --contains d78a903`; file listing of .git/refs; test for .git/packed-refs; `git rev-parse --verify -q refs/stash`; `git rev-parse --is-shallow-repository`; `git log --all --merges --oneline` | 0 commits outside main; all four branches contain d78a903; .git/refs holds four ref files and no stash, tag or pull ref; no .git/packed-refs; the stash ref does not resolve; the clone is not shallow; the one merge is 70ad071. |
| R1-H6-01 | H6 | `\bnote\b`, `\bnb:`, kludge, caveat, quick and dirty, ugly, `\btbd\b`, @todo, revisit, `\blater\b`, for simplicity, needs to, does not work, `doesn.t work`, unsupported, experimental, incomplete, unfinished, broken, `\bbug\b`, `optimi[sz]`, refactor, caution, gotcha, beware, basic fallback, should be; `git ls-files` filtered for change, history, news, notes, issue, todo, known, doc, roadmap, plan, bugs, .rst, .txt, .md | 15 hits, none a TODO, FIXME, HACK, XXX, BUG or WORKAROUND comment: "For simplicity," (a2dplaya/airplay/manager.py:8), two "Unsupported" messages (a2dplaya/audio/resampler.py:177; install.sh:22), `BrokenPipeError` (a2dplaya/cast/manager.py:83, 123), "basic fallback" (a2dplaya/cast/manager.py:423) and test docstrings. File names matched: README.md and requirements.txt only. |

## Verdicts: 02-stages-bluetooth.md

- R1-S01-01 PASS
- R1-S01-02 PASS
- R1-S01-03 PASS
- R1-S01-04 PASS
- R1-S01-05 PASS
- R1-S01-06 PASS
- R1-S01-07 PASS
- R1-S02-01 PASS
- R1-S02-02 PASS
- R1-S02-03 FAIL A: the record says grep `restart|reload` in install.sh returns only install.sh:131; run case-insensitive, as 02-stages-bluetooth.md:9 defines "grep", it also returns install.sh:124 and 125.
- R1-S02-04 PASS
- R1-S02-05 PASS
- R1-S02-06 PASS
- R1-S03-01 PASS
- R1-S03-02 PASS
- R1-S03-03 PASS
- R1-S03-04 PASS
- R1-S03-05 PASS
- R1-S03-06 PASS
- R1-S03-07 PASS
- R1-S03-08 PASS
- R1-S03-09 PASS
- R1-S03-10 PASS
- R1-S03-11 PASS
- R1-S04-01 PASS
- R1-S04-02 PASS
- R1-S04-03 PASS
- R1-S04-04 PASS
- R1-S04-05 PASS
- R1-S04-06 PASS
- R1-S04-07 PASS
- R1-S04-08 PASS
- R1-S05-01 PASS
- R1-S05-02 PASS
- R1-S05-03 PASS
- R1-S05-04 PASS
- R1-S05-05 PASS
- R1-S06-01 PASS
- R1-S06-02 PASS
- R1-S06-03 PASS

## Verdicts: 03-stages-audio.md

- R1-S07-01 PASS
- R1-S07-02 PASS
- R1-S07-03 PASS
- R1-S07-04 PASS
- R1-S07-05 PASS
- R1-S07-06 PASS
- R1-S07-07 PASS
- R1-S08-01 PASS
- R1-S08-02 PASS
- R1-S08-03 PASS
- R1-S08-04 PASS
- R1-S08-05 PASS
- R1-S08-06 PASS
- R1-S08-07 PASS
- R1-S08-08 PASS
- R1-S08-09 PASS
- R1-S08-10 PASS
- R1-S08-11 PASS
- R1-S08-12 PASS
- R1-S08-13 PASS
- R1-S08-14 PASS
- R1-S08-15 PASS
- R1-S09-01 PASS
- R1-S09-02 PASS
- R1-S09-03 PASS
- R1-S09-04 PASS
- R1-S09-05 PASS
- R1-S09-06 PASS
- R1-S09-07 PASS
- R1-S09-08 PASS
- R1-S09-09 PASS
- R1-S09-10 PASS
- R1-S09-11 PASS
- R1-S10-01 PASS
- R1-S10-02 PASS
- R1-S10-03 PASS
- R1-S10-04 PASS
- R1-S10-05 PASS
- R1-S10-06 PASS
- R1-S10-07 PASS
- R1-S10-08 PASS
- R1-S11-01 PASS
- R1-S11-02 PASS
- R1-S11-03 PASS
- R1-S11-04 PASS
- R1-S11-05 PASS
- R1-S12-01 PASS
- R1-S12-02 PASS
- R1-S12-03 PASS
- R1-S12-04 PASS

## Verdicts: 04-history.md

- R1-H2-01 PASS
- R1-H3-01 PASS
- R1-H4-01 PASS
- R1-H5-01 PASS
- R1-H5-02 PASS
- R1-H5-03 PASS
- R1-H5-04 PASS
- R1-H5-05 PASS
- R1-H5-06 PASS
- R1-H5-07 PASS
- R1-H5-08 PASS
- R1-H5-09 PASS
- R1-H5-10 PASS
- R1-H5-11 PASS
- R1-H6-01 PASS
- R1-H7-01 PASS
- R1-H7-02 PASS
- R1-H7-03 PASS
- R1-H7-04 PASS
- R1-H7-05 PASS
- R1-H7-06 PASS
- R1-H7-07 PASS
- R1-H7-08 PASS
- R1-H7-09 PASS
- R1-H7-10 PASS
- R1-H7-11 PASS

## Lists without record IDs

H1 and H8 are lists and carry no record ID (/home/user/audio/_harness/templates.md:48). They are not counted in the totals. Their items were checked under check A.

- H1 list (04-history.md:7-21): every item matches. Commit count 4 and 4; hashes, dates, subjects, author and committer names as `git log --all --date=iso` prints them; 38 files and 4976 insertions in 47701ce; 12 files in d78a903; empty `git diff d78a903 70ad071 --stat`; README.md at 673e119 is one newline byte; the UTC conversions, the 7 h 53 min 46 s span and the three gaps; 268 days to the manifest date (/home/user/audio/00-manifest.md:18); 44 test functions; a session link line at the end of the bodies of 47701ce and d78a903.
- H8 list (04-history.md:309-321): every item matches. Four subjects and dates; empty status (/home/user/audio/00-manifest.md:22); tag count 0 (/home/user/audio/00-manifest.md:35); no MERGE_HEAD, rebase or cherry-pick file in .git; a2dplaya/airplay/manager.py:292-296, 298-353 and 403-404 as described.

## Notes outside the verdicts

These did not change a verdict. They are recorded so the reader has them.

1. R1-S03-09: the record gives no pattern for its grep. `trust_device(` returns a2dplaya/bluetooth/manager.py:212 alone, as the record says. The bare word also returns lines 215, 216, 529 and 550, which are the names `_trust_device_dbus` and `_trust_device_cli`.
2. R1-S01-02, Open questions: "holds bytecode for ... only" lists the six files under a2dplaya/. Commit 47701ce also holds five files under tests/__pycache__/, one of which the same record names in its "What was attempted" field.
3. R1-S08-05: the Confidence line says the generator body is seven statements. The generator at a2dplaya/web/server.py:371-391 holds eight top-level statements. The line carries no citation.
4. R1-S10-06: the record says each name was checked with `git grep -n <name>` and has no reader outside a2dplaya/config.py and tests. For `stream_sample_rate` and `stream_channels` that grep also returns a2dplaya/airplay/manager.py:327, which reads the AirPlayConfig fields of the same names (a2dplaya/config.py:98-99), not the CastConfig fields the record lists (a2dplaya/config.py:90-91).
5. R1-S07-07: the Searched field does not list 16 lines in a2dplaya/web/static/index.html (499 to 783) where its term `sync` matches the word `async`.
6. R1-H6-01: the template gives H6 as a table (/home/user/audio/_harness/templates.md:48). The file gives a NOT FOUND record. The phrase "For simplicity," at a2dplaya/airplay/manager.py:8 is not in the record's list of nearest phrases; it is not one of the six markers. The record's case-insensitive search also matches tests/test_config.py:54 and 77 (`NamedTemporaryFile`, term `temporary`), which the record does not list.
7. H7 scope notes (04-history.md:186, no record ID): the named-device grep is reported as returning only README.md:10-11. It also returns a2dplaya/config.py:166, where the term `nest` matches the word "nested".
8. H1 list, second item: "All 23 .py files ... and setup.py". setup.py is one of the 23.
9. R1-S08-03 gives Presence DISABLED and R1-H5-06 gives Presence CURRENT for the same function (a2dplaya/airplay/manager.py:386-404).
10. R1-S08-05 gives Presence CURRENT for the route /api/audio/stream and R1-S08-10 gives DISABLED for the audio format that route declares (a2dplaya/web/server.py:364-397).
11. R1-S02-06 gives Confidence MEDIUM and R1-H5-08 gives Confidence LOW for the same reading of a2dplaya/__main__.py:79-81.
12. R1-S01-01 gives Confidence MEDIUM with the reason "STATED platform support ... that the installer code agrees with". Rule 5 assigns that wording to HIGH (/home/user/audio/_harness/rules.md:21).
13. The Presence field cites no location of its own in R1-S07-06, R1-S08-07, R1-S08-11, R1-S08-12, R1-S08-14, R1-S08-15, R1-S09-11, R1-S10-06 and R1-S10-07. The template line reads "with citation" (/home/user/audio/_harness/templates.md:13).
14. Eleven records in file 04 open "Why it turned out that way" with UNKNOWN and follow it with an INFERRED sentence about the code path (R1-H5-01, R1-H5-02, R1-H5-04, R1-H5-05, R1-H5-08, R1-H5-09, R1-H5-11, R1-H7-02, R1-H7-03, R1-H7-09 and, without the word INFERRED, R1-H5-10). Each INFERRED sentence carries its reasoning and citations, and those citations matched.

## Coverage

| Question | Records |
|---|---|
| S01.1 | R1-S01-01 |
| S01.2 | R1-S01-02 |
| S01.3 | R1-S01-03, R1-S01-04 |
| S01.4 | R1-S01-05, R1-S01-06 |
| S01.5 | R1-S01-07 |
| S02.1 | R1-S02-01, R1-S02-02, R1-S02-03 |
| S02.2 | R1-S02-04 |
| S02.3 | R1-S02-05, R1-S02-06 |
| S03.1 | R1-S03-01, R1-S03-02, R1-S03-03, R1-S03-04 |
| S03.2 | R1-S03-05, R1-S03-06, R1-S03-07 |
| S03.3 | R1-S03-08, R1-S03-09 |
| S03.4 | R1-S03-10 (NOT FOUND) |
| S03.5 | R1-S03-11 (NOT FOUND) |
| S04.1 | R1-S04-01, R1-S04-02 |
| S04.2 | R1-S04-03 (NOT FOUND) |
| S04.3 | R1-S04-04, R1-S04-05, R1-S04-06, R1-S04-07 |
| S04.4 | R1-S04-08 (NOT FOUND) |
| S05.1 | R1-S05-01, R1-S05-02 |
| S05.2 | R1-S05-03 |
| S05.3 | R1-S05-04 |
| S05.4 | R1-S05-05 |
| S06.1 | R1-S06-01 (NOT FOUND) |
| S06.2 | R1-S06-02 |
| S06.3 | R1-S06-03 (NOT FOUND) |
| S07.1 | R1-S07-01, R1-S07-02, R1-S07-03, R1-S07-04, R1-S07-05 |
| S07.2 | R1-S07-06 |
| S07.3 | R1-S07-07 (NOT FOUND) |
| S08.1 | R1-S08-01, R1-S08-02, R1-S08-03, R1-S08-04, R1-S08-05 |
| S08.2 | R1-S08-06, R1-S08-07, R1-S08-08, R1-S08-09, R1-S08-10 |
| S08.3 | R1-S08-11, R1-S08-12, R1-S08-13 |
| S08.4 | R1-S08-14, R1-S08-15 |
| S09.1 | R1-S09-01, R1-S09-02, R1-S09-03, R1-S09-04, R1-S09-05 |
| S09.2 | R1-S09-06, R1-S09-07, R1-S09-08 |
| S09.3 | R1-S09-09 |
| S09.4 | R1-S09-10, R1-S09-11 |
| S10.1 | R1-S10-01 |
| S10.2 | R1-S10-02, R1-S10-03 |
| S10.3 | R1-S10-04 |
| S10.4 | R1-S10-05, R1-S10-06 |
| S10.5 | R1-S10-07 |
| S10.6 | R1-S10-08 |
| S11.1 | R1-S11-01 |
| S11.2 | R1-S11-02 (NOT FOUND) |
| S11.3 | R1-S11-03 (NOT FOUND) |
| S11.4 | R1-S11-04, R1-S11-05 |
| S12.1 | R1-S12-01, R1-S12-02 |
| S12.2 | R1-S12-03 |
| S12.3 | R1-S12-04 |
| H1 | No record ID. Answered by the H1 list, 04-history.md:7-21, the form set by templates.md:48 |
| H2 | R1-H2-01 |
| H3 | R1-H3-01 (NOT FOUND) |
| H4 | R1-H4-01 (NOT FOUND) |
| H5 | R1-H5-01, R1-H5-02, R1-H5-03, R1-H5-04, R1-H5-05, R1-H5-06, R1-H5-07, R1-H5-08, R1-H5-09, R1-H5-10, R1-H5-11 |
| H6 | R1-H6-01 (NOT FOUND) |
| H7 | R1-H7-01, R1-H7-02, R1-H7-03, R1-H7-04, R1-H7-05, R1-H7-06, R1-H7-07, R1-H7-08, R1-H7-09, R1-H7-10, R1-H7-11 |
| H8 | No record ID. Answered by the H8 list, 04-history.md:309-321, the form set by templates.md:48 |

No question is MISSING. All 48 stage questions and H2 to H7 have at least one record. H1 and H8 have a list and no record, as the template sets out; they are not marked MISSING for that reason.

## Totals

- Records checked: 116 (104 finding records, 12 NOT FOUND records)
- PASS: 115 (103 finding records, 12 NOT FOUND records)
- FAIL: 1 (R1-S02-03, check A)
- MISSING: 0
