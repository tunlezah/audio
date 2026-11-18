# R6 verification — /home/user/SonicFlow

Verifier pass over 02-stages-bluetooth.md, 03-stages-audio.md and 04-history.md. Repo read at commit 3046b9e (branch claude/inspiring-galileo-a9dlia, same commit as main); read-only commands only. Nothing in the three record files was changed.

Checks: A = each citation exists and says what the record claims. B = evidence grades correct under Rule 3. C = outcome follows from the evidence under Rule 4. D = no field makes a proposal, gives advice, ranks, or compares with another repo (Rules 6 and 9). E = NOT FOUND records only: one independent search with different terms.

Record count checked: 259 finding records (70 in file 02, 94 in file 03, 95 in file 04) and 2 NOT FOUND records (both in file 04). Files 02 and 03 contain no NOT FOUND record. The H1 list, H6 table and H8 list in file 04 are not records under the templates and carry no verdict line; they are counted under coverage only.

## Method

- Every `path:line` citation in the three files (1,901 in all) was resolved by script against the working tree; every path and line range exists. The cited lines were printed next to each record and read against the claim. For ranges longer than 6 to 14 lines the first and last lines were printed; where a record states a figure, flag or string inside a long range, that line was opened separately.
- Every quoted string of ten characters or more (506) was matched by script against the cited lines, the commit messages, the tracked files and the full patch history. Two of the 506 were parsing artefacts (quotation marks inside a `grep` command in R6-S12-01 and R6-S12-06). Of the 504 quotations, 502 were found; quotations of removed code were found in the history at the commit named. The two not found word for word are listed under "Notes on PASS verdicts" (R6-S09-12, R6-H7-37).
- Citations of the form "commit HASH, path" were read with `git show HASH:PATH`; diff claims with `git show HASH -- PATH`; search claims (`grep`, `git log -S`) were re-run where the record rests on them.
- docs/screenshot.png was opened for the two records that grade it OBSERVED.
- Checks B and C were read from each record's Outcome, Outcome evidence and Why fields against Rules 3 and 4. Check D was run as a word search for the Rule 6 list (every hit sits inside a quotation from the repo) and by reading each field.
- A verdict of FAIL is given where a stated fact, quotation, grade or outcome is not supported by the cited material. A cited range that is a few lines short of the detail described, with the detail itself true, is listed under "Notes on PASS verdicts" and does not change the verdict.

## Verdicts

### File 02, S01 Platform and stack

- R6-S01-01 PASS
- R6-S01-02 PASS
- R6-S01-03 PASS
- R6-S01-04 PASS
- R6-S01-05 PASS
- R6-S01-06 PASS
- R6-S01-07 PASS
- R6-S01-08 PASS
- R6-S01-09 PASS
- R6-S01-10 PASS
- R6-S01-11 PASS
- R6-S01-12 PASS
- R6-S01-13 PASS
- R6-S01-14 PASS

### File 02, S02 Adapter and controller

- R6-S02-01 PASS
- R6-S02-02 PASS
- R6-S02-03 PASS
- R6-S02-04 PASS
- R6-S02-05 PASS
- R6-S02-06 PASS
- R6-S02-07 PASS
- R6-S02-08 PASS
- R6-S02-09 PASS
- R6-S02-10 PASS

### File 02, S03 Discoverability, pairing and trust

- R6-S03-01 PASS
- R6-S03-02 PASS
- R6-S03-03 PASS
- R6-S03-04 PASS
- R6-S03-05 PASS
- R6-S03-06 PASS
- R6-S03-07 PASS
- R6-S03-08 PASS
- R6-S03-09 PASS
- R6-S03-10 PASS
- R6-S03-11 PASS
- R6-S03-12 PASS

### File 02, S04 Connection and A2DP profile

- R6-S04-01 PASS
- R6-S04-02 PASS
- R6-S04-03 PASS
- R6-S04-04 PASS
- R6-S04-05 PASS
- R6-S04-06 PASS
- R6-S04-07 PASS
- R6-S04-08 PASS
- R6-S04-09 PASS

### File 02, S05 Audio capture

- R6-S05-01 PASS
- R6-S05-02 PASS
- R6-S05-03 PASS
- R6-S05-04 PASS
- R6-S05-05 PASS
- R6-S05-06 PASS
- R6-S05-07 PASS
- R6-S05-08 PASS
- R6-S05-09 PASS
- R6-S05-10 PASS
- R6-S05-11 PASS
- R6-S05-12 PASS
- R6-S05-13 PASS
- R6-S05-14 PASS

### File 02, S06 Remote control and metadata

- R6-S06-01 PASS
- R6-S06-02 PASS
- R6-S06-03 PASS
- R6-S06-04 PASS
- R6-S06-05 PASS
- R6-S06-06 PASS
- R6-S06-07 PASS
- R6-S06-08 PASS
- R6-S06-09 PASS
- R6-S06-10 PASS
- R6-S06-11 PASS

### File 03, S07 Processing

- R6-S07-01 PASS
- R6-S07-02 PASS
- R6-S07-03 PASS
- R6-S07-04 PASS
- R6-S07-05 PASS
- R6-S07-06 PASS
- R6-S07-07 PASS
- R6-S07-08 PASS
- R6-S07-09 PASS
- R6-S07-10 PASS
- R6-S07-11 PASS
- R6-S07-12 PASS
- R6-S07-13 PASS
- R6-S07-14 PASS
- R6-S07-15 PASS

### File 03, S08 Output

- R6-S08-01 PASS
- R6-S08-02 PASS
- R6-S08-03 PASS
- R6-S08-04 PASS
- R6-S08-05 PASS
- R6-S08-06 PASS
- R6-S08-07 PASS
- R6-S08-08 PASS
- R6-S08-09 PASS
- R6-S08-10 PASS
- R6-S08-11 PASS
- R6-S08-12 PASS
- R6-S08-13 PASS
- R6-S08-14 PASS
- R6-S08-15 PASS
- R6-S08-16 PASS
- R6-S08-17 PASS
- R6-S08-18 PASS
- R6-S08-19 PASS
- R6-S08-20 PASS
- R6-S08-21 PASS
- R6-S08-22 PASS

### File 03, S09 User interface and control surfaces

- R6-S09-01 PASS
- R6-S09-02 PASS
- R6-S09-03 PASS
- R6-S09-04 PASS
- R6-S09-05 PASS
- R6-S09-06 PASS
- R6-S09-07 PASS
- R6-S09-08 PASS
- R6-S09-09 PASS
- R6-S09-10 PASS
- R6-S09-11 PASS
- R6-S09-12 PASS

### File 03, S10 Lifecycle and operations

- R6-S10-01 PASS
- R6-S10-02 PASS
- R6-S10-03 PASS
- R6-S10-04 PASS
- R6-S10-05 PASS
- R6-S10-06 PASS
- R6-S10-07 PASS
- R6-S10-08 PASS
- R6-S10-09 PASS
- R6-S10-10 PASS
- R6-S10-11 PASS
- R6-S10-12 PASS
- R6-S10-13 PASS
- R6-S10-14 PASS
- R6-S10-15 PASS
- R6-S10-16 PASS
- R6-S10-17 PASS
- R6-S10-18 PASS

### File 03, S11 Testing and measured behaviour

- R6-S11-01 PASS
- R6-S11-02 PASS
- R6-S11-03 PASS
- R6-S11-04 PASS
- R6-S11-05 PASS
- R6-S11-06 PASS
- R6-S11-07 PASS
- R6-S11-08 PASS
- R6-S11-09 PASS
- R6-S11-10 PASS
- R6-S11-11 PASS
- R6-S11-12 PASS
- R6-S11-13 PASS
- R6-S11-14 PASS
- R6-S11-15 PASS
- R6-S11-16 PASS
- R6-S11-17 PASS

### File 03, S12 Security and exposure

- R6-S12-01 PASS
- R6-S12-02 PASS
- R6-S12-03 PASS
- R6-S12-04 PASS
- R6-S12-05 PASS
- R6-S12-06 PASS
- R6-S12-07 PASS
- R6-S12-08 PASS
- R6-S12-09 PASS
- R6-S12-10 PASS

### File 04, H2 Approach changes

- R6-H2-01 PASS
- R6-H2-02 PASS
- R6-H2-03 PASS
- R6-H2-04 PASS
- R6-H2-05 PASS
- R6-H2-06 PASS
- R6-H2-07 PASS
- R6-H2-08 PASS
- R6-H2-09 PASS
- R6-H2-10 PASS
- R6-H2-11 PASS
- R6-H2-12 PASS
- R6-H2-13 PASS
- R6-H2-14 FAIL A: the record says the README.md that beead5a rewrote had 73 lines; 73 is the count at 1e20b41, and the file had 107 lines at 26a79c2, the commit before beead5a (`git show 26a79c2:README.md | wc -l`)
- R6-H2-15 PASS
- R6-H2-16 PASS
- R6-H2-17 PASS
- R6-H2-18 PASS
- R6-H2-19 PASS

### File 04, H3 Reverts and repeated fixes

- R6-H3-01 PASS (check E: `git log --all --format='%h %s %b' | grep -niE 'back ?out|undo|roll(ed)? ?back|reinstat|restor|this reverts|put back|re-?add'` gave one hit, the 26a79c2 line about uninstall.sh restoring main.conf keys; no reverted commit found)
- R6-H3-02 PASS
- R6-H3-03 PASS
- R6-H3-04 PASS
- R6-H3-05 PASS
- R6-H3-06 PASS
- R6-H3-07 PASS

### File 04, H4 Unmerged work

- R6-H4-01 PASS (check E: `git log --branches --remotes --tags --not main --oneline` printed nothing; `git branch -a -vv` shows four refs at 3046b9e; `.git/refs/tags` is empty; no `.git/packed-refs`; `git rev-parse ORIG_HEAD` is 3046b9e; nothing unmerged found)

### File 04, H5 Dead and disabled code

- R6-H5-01 PASS
- R6-H5-02 PASS
- R6-H5-03 PASS
- R6-H5-04 PASS
- R6-H5-05 PASS
- R6-H5-06 PASS
- R6-H5-07 FAIL C: outcome WORKED is given to the ignored test function, but the cited statement (commit 738f12c) reports a live test of the online cascade and does not say this function was run; the record itself notes that gap, so no evidence of this thing's result is cited
- R6-H5-08 PASS
- R6-H5-09 PASS
- R6-H5-10 PASS
- R6-H5-11 PASS
- R6-H5-12 PASS
- R6-H5-13 PASS
- R6-H5-14 PASS

### File 04, H7 Workarounds

- R6-H7-01 PASS
- R6-H7-02 PASS
- R6-H7-03 PASS
- R6-H7-04 PASS
- R6-H7-05 PASS
- R6-H7-06 PASS
- R6-H7-07 PASS
- R6-H7-08 PASS
- R6-H7-09 PASS
- R6-H7-10 PASS
- R6-H7-11 PASS
- R6-H7-12 PASS
- R6-H7-13 PASS
- R6-H7-14 PASS
- R6-H7-15 PASS
- R6-H7-16 PASS
- R6-H7-17 PASS
- R6-H7-18 PASS
- R6-H7-19 PASS
- R6-H7-20 PASS
- R6-H7-21 PASS
- R6-H7-22 PASS
- R6-H7-23 PASS
- R6-H7-24 PASS
- R6-H7-25 PASS
- R6-H7-26 PASS
- R6-H7-27 PASS
- R6-H7-28 PASS
- R6-H7-29 PASS
- R6-H7-30 PASS
- R6-H7-31 PASS
- R6-H7-32 PASS
- R6-H7-33 PASS
- R6-H7-34 PASS
- R6-H7-35 PASS
- R6-H7-36 PASS
- R6-H7-37 PASS
- R6-H7-38 PASS
- R6-H7-39 PASS
- R6-H7-40 PASS
- R6-H7-41 PASS
- R6-H7-42 PASS
- R6-H7-43 PASS
- R6-H7-44 PASS
- R6-H7-45 PASS
- R6-H7-46 PASS
- R6-H7-47 PASS
- R6-H7-48 PASS
- R6-H7-49 PASS
- R6-H7-50 PASS
- R6-H7-51 PASS
- R6-H7-52 PASS
- R6-H7-53 PASS
- R6-H7-54 PASS
- R6-H7-55 PASS
- R6-H7-56 PASS

## Notes on PASS verdicts

These points were seen during the checks and did not change a verdict. They are about the records, not about the repo.

- R6-S01-02: one citation is written `build.yml:48-54` without its directory; the full path `.github/workflows/build.yml` is given earlier in the same field.
- R6-S05-09: the "second column" parse sits at lines 825-826 of src/bluetooth/manager.rs at commit 8d2e000, three lines past the cited range 810-823. The cited range shows the `pactl`-only reader.
- R6-S09-12: "finished with N problem(s)" is in quotation marks; install.sh:407 reads `finished with $FAILURES problem(s)`.
- R6-S11-09: the heading quoted as "What it is not" is at README.md:42; the citation given, README.md:46-47, is the bullet under it.
- R6-S10-11: the record places "the six config tests" at src/state/config.rs:245-299. That range holds four of them; the other two begin at lines 301 and 318. Six tests exist in the file at HEAD and at d8b17a3, as the record says. R6-S11-01 gives the range as 245-326.
- R6-H7-33: Presence cites src/audio/chromecast.rs:395-441 for the mdns-sd function, which begins at line 392.
- R6-H7-37: "artist title" is in quotation marks and describes the format string at src/art.rs:301; it is not text from the file.
- Cross-referenced evidence: R6-S08-08, R6-S08-09, R6-S08-11, R6-S08-12, R6-S09-05, R6-S09-06, R6-S09-07, R6-H3-04 and R6-H3-05 give the evidence grade and point to another record for the quotation. Each target record carries the citation and was checked.
- OBSERVED is used in four records. R6-S09-01 and R6-S09-08 rest on docs/screenshot.png, which shows what they describe (version tag v0.1.0, visualiser, Now Playing card, two chips, both prompt texts). R6-S10-17 rests on the four tracked version strings, all 0.2.1. R6-H2-14 rests on `git log --diff-filter=D` and `git ls-files` output. Each was accepted under Rule 3 as an artefact that shows the stated result directly.
- Seven records cite harness files as a source: R6-H2-14 (01-map.md:22), R6-H2-17 (01-map.md:78-87, 301), R6-H7-07 (01-map.md:108), R6-H7-22 (01-map.md:301), R6-H7-28 (01-map.md:140), R6-H7-53 (01-map.md:59), R6-H4-01 (00-manifest.md:27). Those lines exist and say what the records claim.
- R6-S07-09, R6-S09-11, R6-H2-04, R6-H5-08 and R6-H5-12 give a compound Presence (for example DISABLED in the web UI, CURRENT on the server). Each part carries its own citation and was checked.
- Outcomes for the same material differ between files in two places, each with its own stated basis: the `btmgmt` call is FAILED in R6-S03-03 and its timeout is UNVERIFIED in R6-H7-01; the Local Player fallback chain is UNVERIFIED in R6-S08-17 and the autoplay change is WORKED in R6-H2-12 on the commit 2641e55 sentence "WebRTC connected, unmuted, signal present in the received track".

## Coverage

Each question is listed with the record IDs whose Question field names it. History records are listed under their H question only; their "relates to" stage question is not counted as an answer to that stage question.

- S01.1: R6-S01-01, R6-S01-02
- S01.2: R6-S01-03 to R6-S01-05
- S01.3: R6-S01-06 to R6-S01-08
- S01.4: R6-S01-09
- S01.5: R6-S01-10 to R6-S01-14
- S02.1: R6-S02-01 to R6-S02-03
- S02.2: R6-S02-04 to R6-S02-06
- S02.3: R6-S02-07 to R6-S02-10
- S03.1: R6-S03-01 to R6-S03-03
- S03.2: R6-S03-04, R6-S03-05
- S03.3: R6-S03-06 to R6-S03-08
- S03.4: R6-S03-09
- S03.5: R6-S03-10 to R6-S03-12
- S04.1: R6-S04-01 to R6-S04-03
- S04.2: R6-S04-04, R6-S04-05
- S04.3: R6-S04-06 to R6-S04-08
- S04.4: R6-S04-09
- S05.1: R6-S05-01 to R6-S05-05
- S05.2: R6-S05-06, R6-S05-07
- S05.3: R6-S05-08 to R6-S05-10
- S05.4: R6-S05-11 to R6-S05-14
- S06.1: R6-S06-01, R6-S06-02
- S06.2: R6-S06-03 to R6-S06-05
- S06.3: R6-S06-06 to R6-S06-11
- S07.1: R6-S07-01 to R6-S07-10
- S07.2: R6-S07-11 to R6-S07-13
- S07.3: R6-S07-14, R6-S07-15
- S08.1: R6-S08-01 to R6-S08-07
- S08.2: R6-S08-08 to R6-S08-12
- S08.3: R6-S08-13 to R6-S08-20
- S08.4: R6-S08-21, R6-S08-22
- S09.1: R6-S09-01 to R6-S09-04
- S09.2: R6-S09-05 to R6-S09-07
- S09.3: R6-S09-08
- S09.4: R6-S09-09 to R6-S09-12
- S10.1: R6-S10-01 to R6-S10-03
- S10.2: R6-S10-04 to R6-S10-08
- S10.3: R6-S10-09, R6-S10-10
- S10.4: R6-S10-11 to R6-S10-13
- S10.5: R6-S10-14, R6-S10-15
- S10.6: R6-S10-16 to R6-S10-18
- S11.1: R6-S11-01 to R6-S11-03
- S11.2: R6-S11-04 to R6-S11-06
- S11.3: R6-S11-07, R6-S11-08
- S11.4: R6-S11-09 to R6-S11-17
- S12.1: R6-S12-01 to R6-S12-05
- S12.2: R6-S12-06, R6-S12-07
- S12.3: R6-S12-08 to R6-S12-10
- H1: no record ID. Answered by the H1 list (04-history.md:13-37), the form templates.md sets for H1. Not marked MISSING.
- H2: R6-H2-01 to R6-H2-19
- H3: R6-H3-01 (NOT FOUND, reverted commits), R6-H3-02 to R6-H3-07
- H4: R6-H4-01 (NOT FOUND)
- H5: R6-H5-01 to R6-H5-14
- H6: no record ID. Answered by the H6 table (04-history.md:502-589, 80 rows), the form templates.md sets for H6. Not marked MISSING.
- H7: R6-H7-01 to R6-H7-56
- H8: no record ID. Answered by the H8 list (04-history.md:1232-1278), the form templates.md sets for H8. Not marked MISSING.

Questions with at least one record: 53 of 56 (48 stage questions, H2, H3, H4, H5, H7). Questions answered by a list or table with no record ID, as templates.md requires: 3 (H1, H6, H8). Questions with no answer of either kind: 0.

The H1 list, H6 table and H8 list were read for coverage. They carry no verdict line because they are not records. Spot checks of H1 against `git log` (commit count 22, first and last commit, the three gaps, the files changed in the most commits) and of the H6 row count (80) agreed with the file.

## Totals

- Records checked: 261 (259 finding records, 2 NOT FOUND records)
- PASS: 259
- FAIL: 2 (R6-H2-14 on check A; R6-H5-07 on check C)
- MISSING: 0
