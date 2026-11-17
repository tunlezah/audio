# R6 history — /home/user/SonicFlow

Audited read-only on 2026-11-17 at commit 3046b9e (all four refs point at it; 01-map.md:323). All paths are relative to /home/user/SonicFlow. Citations of the form "commit HASH, path" refer to the content of that path in that commit, read with `git show`. Nothing was checked out.

Commands run (all read-only): `git log --all --date=short --pretty='%h %ad %s'`; `git log --all --date=iso --pretty='%h %ad | %cd | %an'`; `git log --all --reverse --stat` with full messages; `git log --all -i --grep='revert'`; `git log --all -i --grep='fix'`; `git log --all --diff-filter=D --name-only`; `git branch -a --no-merged`; `git branch -a --no-merged main`; `git stash list`; `git tag`; `git for-each-ref`; `git rev-list --all --count`; `git log --all --merges`; `git log --all --name-only --pretty=format: | sort | uniq -c | sort -rn`; `git diff-tree --no-commit-id --name-only -r HASH`; `git show HASH -- PATH`; `git show HASH:PATH`; `git log --all -S'<term>'`; `git grep -n -I`; `git status --short`; `git shortlog -sn --all`.

Rule 10 note: the two documents with text addressed to an AI agent (01-map.md:5) were read as material only.

Rule 11 note: a Bluetooth-address-shaped test fixture was added by commit 0ad9f4e in src/bluetooth/manager.rs (test `pw_link_port_names_parse`, src/bluetooth/manager.rs:891-902; kind: device address used as unit-test input). Value not copied. `git shortlog -sne --all` prints author e-mail addresses; values not copied.

---

## H1 Timeline

- First commit: d8b17a3, 2026-08-29 20:58:42 +0000, "Phase 0: toolchain, scaffold, first green build" (`git log --all --reverse --date=iso`).
- Last commit: 3046b9e, 2026-08-30 09:32:00 +0000, "v0.2.1: art_online config knob; installer template + README document it" (same command).
- Span from first to last commit: 12 hours 33 minutes (difference of the two timestamps above).
- Commit count: 22 reachable from all refs (`git rev-list --all --count`); 21 non-merge commits and 1 merge commit, 128e29f (`git log --all --merges --oneline`).
- Two root commits: d8b17a3 (no parent) and b306b25 "Create readme.md" (no parent, timestamp 2026-08-30 16:17:43 +1000, which is 06:17:43 UTC). The merge 128e29f "Merge GitHub placeholder repo history" has parents beead5a and b306b25 (`git log --all --pretty='%h %p'`).
- Author and commit dates are identical on every commit (`git log --all --date=iso --pretty='%h %ad | %cd'`).
- Two author identities: one with 21 commits, one with 1 commit (b306b25) (`git shortlog -sn --all`). 19 of 22 commit messages carry a "Co-Authored-By: Claude Fable 5" trailer; b306b25, 128e29f and 5eaf177 do not (`git log --all --pretty='%h %(trailers:key=Co-Authored-By)'`).
- Activity periods, UTC (`git log --all --date=iso`):
  - 2026-08-29 20:58 to 21:45 — 8 commits: d8b17a3, bb2c661, 4505247, 174ec2f, 3d4afd4, b19435c, 9d619ed, 1e20b41.
  - Gap of 2 h 13 min.
  - 2026-08-29 23:58 — 1 commit: 8d2e000.
  - Gap of 1 h 3 min.
  - 2026-08-30 01:01 to 01:56 — 4 commits: 0ad9f4e, 2641e55, 3629c21, 26a79c2.
  - Gap of 4 h 21 min.
  - 2026-08-30 06:17 to 06:30 — 5 commits: b306b25, beead5a, 128e29f, 5eaf177, 7448b91.
  - 2026-08-30 07:36 — 07b63e5. 08:11 — 3624d19. 09:17 — 738f12c. 09:32 — 3046b9e.
- Inactivity: no commit on any ref after 2026-08-30 09:32 UTC. The manifest snapshot is dated 2026-11-17 (00-manifest.md:18), 79 days later.
- The first commit adds 46 files and 16,871 lines (`git show --stat d8b17a3`). It contains every Rust source file present today except src/bluetooth/manager.rs (added by 4505247) and src/art.rs (added by 738f12c) (`git log --all --diff-filter=A --name-only`, compared with `git ls-files src`).
- Commit 174ec2f "Phase 3: EQ verified live" changes no files (`git diff-tree --no-commit-id --name-only -r 174ec2f` prints nothing).
- Version labels in commit subjects: v0.1.1 (3624d19), v0.2.0 (738f12c), v0.2.1 (3046b9e). No tags exist (`git tag` prints nothing).
- Files changed in the most commits (`git log --all --name-only`): install.sh 7; forinstall.md 6; src/main.rs 5; src/bluetooth/avrcp.rs 5; README.md 5.

---

## H2 Approach changes

Convention for this section: each record describes one point where one approach replaced another. "Presence" and "Outcome" describe the replacing approach. The fate of the replaced approach and the commit that removed it are given under "What was attempted". Where the commit message gives no reason, the reason is written as UNKNOWN; a reason found only in a code comment or notes file is quoted and labelled as such.

Scope note: DECISIONS.md:1-105 and BUILD-PLAN.md:1-100 list departures from the predecessor specification that were settled before the first commit. Three of them name a swapped library or tool and have records below (R6-H2-17 to R6-H2-19). The rest are design decisions with no before-and-after in this repo's history and are located by 01-map.md section 6.

### R6-H2-01  Seat-monitoring disable moved from a planned plain entry to a WirePlumber profile feature
- Question: H2, relates to S04.1
- What was attempted: at the first commit the notes file carried a pending WirePlumber fragment containing `monitor.bluez.seat-monitoring = disabled` with no profile section (commit d8b17a3, forinstall.md, lines 48-52 of that version). Commit bb2c661 replaced that entry with a done entry that places the key under `wireplumber.profiles.main` in a user-level fragment (forinstall.md:94-104). Stated reason, commit bb2c661: "monitor.bluez.seat-monitoring is a WirePlumber profile FEATURE; disabled it in wireplumber.profiles.main via user conf.d fragment."
- Presence: CURRENT. Fragment text at install.sh:192-197; diagnostic check at deploy/doctor.sh:20-24.
- Outcome: WORKED
- Outcome evidence: STATED. Commit bb2c661: "Audio Sink 110b now advertised with sbc/sbc_xq/aptx/aptx_hd/ldac endpoints." forinstall.md:163-164: "Verified: `bluetoothctl show` now lists Audio Sink 0000110b".
- Why it turned out that way: STATED. forinstall.md:101-103: "every session is SSH (no active seat), so the bluez monitor logged "Seat state changed: closing" and unregistered its endpoints immediately".
- Depends on: a headless machine whose sessions are all SSH (forinstall.md:101-102); WirePlumber 0.5.13 and PipeWire 1.6.2 on the development machine (forinstall.md:61-62); the WirePlumber 0.5 conf format (install.sh:86-91).
- Open questions: Was the key tried in any other location before bb2c661? Does the result hold on a machine that has an active seat?
- Confidence: HIGH. STATED in the commit and the notes file; the fragment in install.sh agrees.

### R6-H2-02  Development user-level WirePlumber fragment superseded by a system-wide copy with migration
- Question: H2, relates to S10.1
- What was attempted: the fragment was first written by hand at user level during development (forinstall.md:94-96, 161-163). The installer writes the same content to `/etc/wireplumber/wireplumber.conf.d/` (first in commit 1e20b41, install.sh; now install.sh:185-212). Commit 26a79c2 added removal of the user-level copy (install.sh:213-219). Reason in the commit message: UNKNOWN (26a79c2 lists "WirePlumber fragment migration from the dev user copy" without a reason). Code comment, install.sh:213: "Migrate the dev-session user-level copy so it can't shadow/diverge."
- Presence: CURRENT. install.sh:185-219.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. forinstall.md:40-41 describes the user-level copy as still present at the 2026-08-30 audit; no file states that the installer's migration step was run.
- Why it turned out that way: UNKNOWN.
- Depends on: the user-level path named at install.sh:214; the service user chosen at install.sh:40-53.
- Open questions: Was install.sh v2 run on the development machine after 26a79c2? Which copy was in effect when later live checks (0ad9f4e, 2641e55) were made?
- Confidence: MEDIUM. INFERRED from clear script text. No result evidence.

### R6-H2-03  Placeholder Bluetooth command drain replaced by the bluer-based manager
- Question: H2, relates to S02.1
- What was attempted: the first commit spawned `drain_bluetooth_commands`, which logged and dropped every Bluetooth command (commit d8b17a3, src/main.rs, lines 66-67 and 121-132 of that version). Commit 4505247 deleted it and added src/bluetooth/manager.rs (849 lines) spawned from src/main.rs:79. Reason in the commit message: UNKNOWN (the message lists the new content only). Removed code comment, commit d8b17a3, src/main.rs line 66: "Phase 2 replaces this drain with the bluer-based manager."
- Presence: CURRENT. src/bluetooth/manager.rs:67-122; src/main.rs:79.
- Outcome: PARTIAL
- Outcome evidence: STATED. Commit 4505247: "Verified live: scan found 19 devices, names resolved, alias=SonicFlow, discoverable/pairable on, scan start/stop clean". Known defects, both STATED in commit 0ad9f4e: the phone's stream node was "invisible to 'pactl list short sources'" so audio-active was not detected; and "rapid clicks let bluer's async session teardown kill the new discovery". Both were changed in 0ad9f4e (R6-H2-08, R6-H7-03).
- Why it turned out that way: STATED for the two defects, in the 0ad9f4e text quoted above. UNKNOWN otherwise.
- Depends on: `bluer` 0.17 (Cargo.toml:14); the development machine's adapter set (forinstall.md:131-132); the 500 ms poll (src/bluetooth/constants.rs:3).
- Open questions: Which source device, if any, was connected during the 4505247 live check? The message names scan results only.
- Confidence: MEDIUM. STATED evidence alone.

### R6-H2-04  Per-device 250 ms name fast-poll removed; global poll and hcitool retained
- Question: H2, relates to S03.3
- What was attempted: the first commit defined `NAME_RESOLUTION_POLL` (250 ms) and `NAME_RESOLUTION_MAX_ATTEMPTS` (24) (commit d8b17a3, src/bluetooth/constants.rs, lines 8-9 of that version). Commit 4505247 deleted both constants. Reason in the commit message: UNKNOWN. Code comment, src/bluetooth/constants.rs:7-8: "the 500 ms global property poll already re-reads every alias, so the fast poll only halved worst-case latency for double the D-Bus traffic".
- Presence: CURRENT. Global poll at src/bluetooth/manager.rs:615-655; hcitool path at src/bluetooth/manager.rs:544-609. The fast-poll constants are REMOVED (commit 4505247).
- Outcome: WORKED
- Outcome evidence: STATED. Commit 4505247: "Verified live: scan found 19 devices, names resolved".
- Why it turned out that way: UNKNOWN. The message does not say which mechanism resolved the names.
- Depends on: the 500 ms poll interval (src/bluetooth/constants.rs:3); `hcitool` being installed (src/bluetooth/manager.rs:580).
- Open questions: Was the fast poll ever exercised in this repo? The constants were under a module-level `allow(dead_code)` in d8b17a3.
- Confidence: MEDIUM. STATED evidence alone.

### R6-H2-05  Scaffold spectrum-bars page replaced by the full web UI
- Question: H2, relates to S09.1
- What was attempted: the first commit's webui/src/main.tsx (112 lines) was a header plus live spectrum bars (commit d8b17a3, webui/src/main.tsx). Commit b19435c reduced main.tsx and added 13 new files under webui/src (`git show --stat b19435c`). Reason in the commit message: UNKNOWN. Removed code comment, commit d8b17a3, webui/src/main.tsx lines 1-2: "The full SonicFlow design-spec UI replaces this in Phase 5."
- Presence: CURRENT. webui/src/main.tsx:26; webui/src/app.tsx:49.
- Outcome: WORKED
- Outcome evidence: STATED. Commit b19435c: "Verified live in headless Chromium: 440Hz tone through the EQ sink lights the correct bars end-to-end".
- Why it turned out that way: UNKNOWN.
- Depends on: headless Chromium as the only browser named in b19435c; a generated 440 Hz tone, not a Bluetooth source.
- Open questions: Which UI functions other than the visualizer were exercised at b19435c?
- Confidence: MEDIUM. STATED evidence alone.

### R6-H2-06  Pairing agent's fixed auto-pair flag replaced by a flag shared with the manager
- Question: H2, relates to S03.2
- What was attempted: the agent held `auto_pair: bool`, read once at registration (commit 4505247, src/bluetooth/agent.rs). Commit 9d619ed changed it to `Arc<AtomicBool>` shared with the manager and added the `SetAutoPair` command (src/bluetooth/agent.rs:11-19; src/bluetooth/manager.rs:270-279). Commit message, 9d619ed: "New: auto-pair toggle end to end (AtomicBool shared with the live agent, persisted via runtime state". Code comment, src/bluetooth/agent.rs:11-12: "Shared with the manager so the Settings toggle flips the LIVE agent without re-registering it (decision D3)."
- Presence: CURRENT. src/bluetooth/agent.rs:11-19, 111-126; src/web/routes.rs:73.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The verification statements in 9d619ed cover Phases 6, 7 and 10 and discovery; none names the toggle.
- Why it turned out that way: UNKNOWN.
- Depends on: the agent staying registered for the life of the D-Bus connection (src/bluetooth/manager.rs:128-141).
- Open questions: Was a pairing attempted with the toggle off?
- Confidence: MEDIUM. INFERRED from clear code. No result evidence.

### R6-H2-07  Guessed AVRCP player path replaced by ObjectManager discovery with a cached path
- Question: H2, relates to S06.3
- What was attempted: the MediaPlayer1 path was built as `<device>/player0` (commit d8b17a3, src/bluetooth/avrcp.rs line 123; reshaped in 4505247). Commit 0ad9f4e replaced it with `find_player_path`, which enumerates org.bluez objects and caches the result (src/bluetooth/avrcp.rs:328-330). Stated reason, commit 0ad9f4e: "BlueZ 5.8x moved the player to dev_XX/avrcp/player0 — discover the MediaPlayer1 path via ObjectManager and cache it instead of guessing."
- Presence: CURRENT. src/bluetooth/avrcp.rs:328-353.
- Outcome: WORKED
- Outcome evidence: STATED. Commit 0ad9f4e: "Live-verified: track title/artist/album/position from a playing iPhone."
- Why it turned out that way: STATED. Same commit, quoted above; BlueZ 5.85 on the development machine (forinstall.md:146-147).
- Depends on: BlueZ 5.85 (forinstall.md:147); one iPhone as the only source named.
- Open questions: Was any non-Apple source checked? Which iPhone model and OS version?
- Confidence: HIGH. STATED in the commit; the discovery code is present.

### R6-H2-08  Audio-active detection widened from the pactl source list to pactl plus pw-link port names
- Question: H2, relates to S05.3
- What was attempted: detection read `pactl list short sources` only and took the second column of each line (commit 4505247, src/bluetooth/manager.rs). Commit 0ad9f4e added `pw-link -o` and made the parser scan every token (src/bluetooth/manager.rs:820-866). Stated reason, commit 0ad9f4e: "PipeWire >=1.x creates the phone's A2DP node as a STREAM (role=playback), invisible to 'pactl list short sources'; also parse pw-link -o port names."
- Presence: CURRENT. src/bluetooth/manager.rs:826-843.
- Outcome: WORKED
- Outcome evidence: STATED. Commit 0ad9f4e: "iPhone now transitions to audio_active."
- Why it turned out that way: STATED. src/bluetooth/manager.rs:821-824 gives the same cause as the commit.
- Depends on: PipeWire 1.6.2 (forinstall.md:61); node names beginning `bluez_input.` or `bluez_source.` (src/bluetooth/manager.rs:851-856); one iPhone.
- Open questions: deploy/doctor.sh:49 still counts sources with `pactl list short sources` only; what does it report for a stream-role node?
- Confidence: HIGH. STATED in the commit; code and unit test (src/bluetooth/manager.rs:891-902) agree.

### R6-H2-09  One chip per Cast or AirPlay device replaced by one dropdown chip per output type
- Question: H2, relates to S09.2
- What was attempted: the casting row rendered one chip for each discovered device (commit 2641e55^, webui/src/components/Casting.tsx, lines 71 and 99 of that version). Commit 2641e55 grouped them into one dropdown chip each. Stated reason, commit 2641e55: "22+19 devices no longer flood the row".
- Presence: CURRENT. webui/src/components/Casting.tsx (commit 2641e55); README.md:173-175.
- Outcome: PARTIAL
- Outcome evidence: STATED. Defect given by the following commit, 3629c21: "Downward lists overflowed the viewport and added a scrollbar." Changed in 3629c21 (R6-H2-10).
- Why it turned out that way: STATED. Commit 3629c21 subject: "the row sits at the page bottom".
- Depends on: a network with 22 Cast and 19 AirPlay entries (commit 9d619ed).
- Open questions: NONE.
- Confidence: MEDIUM. STATED evidence alone.

### R6-H2-10  Casting dropdowns changed from opening downward to opening upward
- Question: H2, relates to S09.2
- What was attempted: commit 3629c21 changed 5 lines of webui/src/style.css so the lists open upward. Stated reason, commit 3629c21: "Downward lists overflowed the viewport and added a scrollbar."
- Presence: CURRENT. webui/src/style.css (commit 3629c21).
- Outcome: WORKED
- Outcome evidence: STATED. Commit 3629c21: "Verified: vertical overflow now 0px with the AirPlay list open."
- Why it turned out that way: STATED. Same commit.
- Depends on: the casting row being at the bottom of the page layout (commit 3629c21 subject).
- Open questions: On which of the three layouts (desktop, tablet, phone) was the overflow measured?
- Confidence: MEDIUM. STATED evidence alone.

### R6-H2-11  Implicit rustls crypto-provider selection replaced by an explicit ring provider at startup
- Question: H2, relates to S08.3
- What was attempted: before 2641e55 no provider was installed. Commit 2641e55 added `rustls::crypto::ring::default_provider().install_default()` at the top of `main` (src/main.rs:33-42). Stated reason, commit 2641e55: "rustls had no default CryptoProvider (ring AND aws-lc in the graph) so the cast session thread PANICKED on TLS connect".
- Presence: CURRENT. src/main.rs:33-42; `rustls` with feature `ring` at Cargo.toml:34; both `ring` and `aws-lc-rs` in Cargo.lock:2264 and Cargo.lock:278.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The verification sentence in 2641e55 covers WebRTC in two browsers; no file states that a Cast session completed after the change.
- Why it turned out that way: UNKNOWN for the replacement. For the replaced state, STATED: src/main.rs:35-36 "observed: cast-session thread died in rustls crypto/mod.rs".
- Depends on: `rust_cast` 0.21 and `webrtc` 0.12 both being linked (Cargo.toml:24, 31).
- Open questions: Did a Cast session play audio after 2641e55? README.md:283-285 makes a general statement about "real Chromecasts" without naming a session.
- Confidence: MEDIUM. The earlier panic is STATED; the result of the replacement is not.

### R6-H2-12  Browser autoplay handling changed from mute-play-unmute to a held muted state with a user unmute
- Question: H2, relates to S08.3
- What was attempted: on a blocked `play()` the client muted, played, then unmuted in code (commit b19435c, webui/src/webrtc.ts). Commit 2641e55 replaced this with `tryPlay`, which stays muted and reports it, and `unmute`, called from a click (webui/src/webrtc.ts:42-62, 92-100). Stated reason, commit 2641e55: "stay MUTED when autoplay blocks the second play() and turn the chip into a tap-to-unmute affordance (Safari-safe)".
- Presence: CURRENT. webui/src/webrtc.ts:42-62, 92-100; webui/src/state.tsx:60-63.
- Outcome: WORKED
- Outcome evidence: STATED. Commit 2641e55: "Verified over LAN in headless Chromium and real Firefox 154: WebRTC connected, unmuted, signal present in the received track".
- Why it turned out that way: STATED. webui/src/webrtc.ts:8-9: "programmatic unmute outside a gesture is exactly what Safari blocks, so unmute() must be called from a fresh user click."
- Depends on: headless Chromium and Firefox 154 as the browsers named.
- Open questions: Was Safari or iOS tested? BUILD-PLAN.md:68 lists "Safari/iOS" in the Phase 6 check; no commit or notes entry reports a Safari result.
- Confidence: MEDIUM. STATED evidence alone.

### R6-H2-13  Linear installer replaced by a 12-step check-apply-verify installer, with a new uninstaller
- Question: H2, relates to S10.1
- What was attempted: installer v1 was a 238-line linear script under `set -euo pipefail` (commit 8d2e000, install.sh). Commit 26a79c2 rewrote it (414 lines changed) into 12 steps that print ok, fixed or FAIL, record changes under /var/lib/sonicflow, and delegate user-session work (install.sh:8-17, 26-35, 56-64); it also added uninstall.sh (184 lines). Reason in the commit message: UNKNOWN. The message lists properties only: "12 check/apply/verify steps (ok/fixed/FAIL), first-install vs repair via /var/lib/sonicflow, root entry with user-session delegation".
- Presence: CURRENT. install.sh:1-409; uninstall.sh:1-186.
- Outcome: UNVERIFIED
- Outcome evidence: NONE for a run of install.sh v2. For one part of uninstall.sh, STATED in commit 26a79c2: "main.conf keys restored byte-identically — sandbox-tested roundtrip".
- Why it turned out that way: UNKNOWN.
- Depends on: apt, systemd, ufw and `ss` being present (install.sh:94-128, 276, 320); Ubuntu 26.04 as the reference system (install.sh:3).
- Open questions: Was v2 run end to end on the development machine? BUILD-PLAN.md:100 sets the check "clean install path replayed on the box; reboot → everything works untouched"; no file reports it.
- Confidence: MEDIUM. Script text is clear; no result evidence for the installer.

### R6-H2-14  Placeholder readme.md merged from a second history and deleted in favour of README.md
- Question: H2, relates to NONE
- What was attempted: b306b25 "Create readme.md" is a separate root commit holding a one-line file. beead5a rewrote README.md (246 insertions, 82 deletions; the previous README.md from 1e20b41 had 73 lines). 128e29f merged the two histories. 5eaf177 deleted readme.md. Stated reason, commit 5eaf177: "Remove placeholder readme.md (superseded by README.md)".
- Presence: CURRENT for README.md (README.md:1-285). readme.md is REMOVED (commit 5eaf177).
- Outcome: WORKED
- Outcome evidence: OBSERVED. `git log --all --diff-filter=D --name-only` lists readme.md under 5eaf177 only; `git ls-files` lists README.md and no readme.md (01-map.md:22).
- Why it turned out that way: STATED. Commit 128e29f subject: "Merge GitHub placeholder repo history".
- Depends on: NONE found.
- Open questions: NONE.
- Confidence: HIGH. OBSERVED in git output.

### R6-H2-15  Gradient artwork placeholder replaced by a bundled NoAlbumArt image
- Question: H2, relates to S06.3
- What was attempted: `ArtworkFallback` drew a gradient from the palette's EQ colours (commit b19435c, webui/src/components/NowPlaying.tsx). Commit 3624d19 replaced it with an `<img>` of `/no-album-art.png` and dropped the palette arguments. Stated reason, commit 3624d19: "User-provided NoAlbumArt.png (1254px source kept at repo root, 512px web copy served) now shows whenever the playing source provides no album art".
- Presence: CURRENT. webui/src/components/NowPlaying.tsx:13-28 (extended by 738f12c with the idle image).
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: webui/public/no-album-art.png and webui/public/nothing-playing.png being served (commits 3624d19, 738f12c).
- Open questions: webui/src/components/NowPlaying.tsx:3 still reads "Artwork falls back to an EQ-gradient placeholder."; was the header comment left unchanged on purpose?
- Confidence: MEDIUM. INFERRED from clear code. No result evidence.

### R6-H2-16  Reading an image file path from the AVRCP track replaced by a three-tier art cascade
- Question: H2, relates to S06.3
- What was attempted: `try_extract_artwork` read the file named by the track's `Image` key (commit d8b17a3, src/bluetooth/avrcp.rs, lines 279-292 of that version; its comment: "Best-effort: many BlueZ builds expose nothing here; degrade gracefully."). Commit 738f12c deleted it and added src/art.rs: Bluetooth cover art over OBEX, then iTunes, Deezer and MusicBrainz lookups, then a bundled image (src/art.rs:1-15). Reason in the commit message: "cascade per user decisions" (738f12c); no technical reason is given.
- Presence: CURRENT. src/art.rs:1-486; spawn point in src/bluetooth/avrcp.rs (commit 738f12c).
- Outcome: PARTIAL
- Outcome evidence: STATED. Commit 738f12c: "Live-tested online cascade (iTunes 600px art downloaded; Deezer fielded-query quirk fixed; MB release found)". Condition list: the online tier has a stated live test; the Bluetooth cover-art tier has no stated result (the same commit says only "gracefully skipped otherwise").
- Why it turned out that way: UNKNOWN.
- Depends on: `curl` (src/art.rs:263); `Experimental = true` and BlueZ >= 5.79 for the Bluetooth tier (forinstall.md:144-147); `bluez-obexd` (install.sh:97); `art_online` (src/state/config.rs:28-31).
- Open questions: Did the Bluetooth cover-art tier return an image from any phone? Was `Experimental = true` set on the development machine (forinstall.md:144 marks it "[PENDING → install.sh]")?
- Confidence: MEDIUM. STATED evidence alone.

### R6-H2-17  PipeWire development libraries installed during development, then dropped from the installer
- Question: H2, relates to S01.4
- What was attempted: `libpipewire-0.3-dev` and `libspa-0.2-dev` were installed for the project on 2026-08-29 (forinstall.md:27-33) and are still listed as pending build dependencies (forinstall.md:74-75). No installer version installs them (commit 8d2e000, install.sh, lines 62-70; install.sh:96-104). The `pipewire` crate is not linked (Cargo.toml:19-20). Stated reason, forinstall.md:33-35 (added by commit 26a79c2): "turned out unnecessary (subprocess architecture; ffmpeg does MP3) — installer no longer installs them."
- Presence: CURRENT for the subprocess approach (Cargo.toml:19-20; 01-map.md:78-87). The two packages are absent from install.sh:96-104.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No build log or CI result is stored (01-map.md:301).
- Why it turned out that way: UNKNOWN.
- Depends on: the CLI tools named at Cargo.toml:20 being installed.
- Open questions: Was a version of the code that linked libpipewire ever written? No commit contains one (`git log --all -S'pipewire = ' -- Cargo.toml` shows only the comment in d8b17a3).
- Confidence: MEDIUM. STATED reason; no result evidence.

### R6-H2-18  In-process MP3 encoder crate dropped; MP3 produced by an ffmpeg subprocess
- Question: H2, relates to S07.1
- What was attempted: BUILD-PLAN.md:10-11 lists the dependency set as "deps per SS§5.2, updated: rust_cast 0.21, drop mp3lame-encoder". `libmp3lame-dev` was installed during development (forinstall.md:31-32) and is not in the installer (install.sh:96-104). MP3 comes from `ffmpeg -c:a libmp3lame` (src/audio/cast_stream.rs:31-33). Stated reason, forinstall.md:75-76: "ffmpeg path means libmp3lame-dev is NOT needed at build time."
- Presence: CURRENT. src/audio/cast_stream.rs:31-39. No `mp3lame` crate in Cargo.toml (`git log --all -S'mp3lame' -- Cargo.toml Cargo.lock` prints nothing).
- Outcome: UNVERIFIED
- Outcome evidence: NONE for MP3 output. Commit 3d4afd4 states "AAC stream headers + icy-name verified", which covers the AAC route only.
- Why it turned out that way: UNKNOWN.
- Depends on: an `ffmpeg` build that includes libmp3lame (src/audio/cast_stream.rs:32).
- Open questions: Was the MP3 stream played by any client?
- Confidence: MEDIUM. STATED reason; no result evidence.

### R6-H2-19  hciconfig not used; Class of Device set by main.conf with a btmgmt runtime fallback
- Question: H2, relates to S02.1
- What was attempted: DECISIONS.md:70-72 sets Class of Device through `/etc/bluetooth/main.conf` with a `btmgmt` fallback and says "Do not depend on deprecated `hciconfig`." The code never called hciconfig (`git log --all -S'hciconfig' -- src` lists d8b17a3 and 4505247, both comment text only). Commit 4505247 changed the constant from a hex string to btmgmt's numeric major and minor (src/bluetooth/constants.rs:15-20).
- Presence: CURRENT. install.sh:170; src/bluetooth/manager.rs:215-246.
- Outcome: UNVERIFIED
- Outcome evidence: NONE for the Class value on air after install. forinstall.md:164 records a change of service bits "0x000104 → 0x0c0104" after the WirePlumber fragment, which predates any main.conf edit (forinstall.md:84 is marked [PENDING]).
- Why it turned out that way: UNKNOWN.
- Depends on: BlueZ >= 5.66 (src/bluetooth/constants.rs:16); root access for main.conf (install.sh:37); CAP_NET_ADMIN for btmgmt (src/bluetooth/manager.rs:216-217).
- Open questions: Was `Class = 0x000414` applied on the development machine, and what Class did `bluetoothctl show` report afterwards?
- Confidence: MEDIUM. INFERRED from clear code and STATED design; no result evidence.

---

## H3 Reverts and repeated fixes

### R6-H3-01  NOT FOUND
- Question: H3 (reverted commits)
- Searched: `git log --all -i --grep='revert'` (two hits, 738f12c and 26a79c2; in both the word refers to the uninstaller undoing system changes, not to a git revert); `git log --all --pretty='%s' | grep -i '^Revert'` (nothing); `git log --all --diff-filter=D --name-only` (one deletion, readme.md in 5eaf177, recorded as R6-H2-14); all 22 full commit messages read.
- Nearest related material: commit 26a79c2 ("recorded reverts", meaning uninstall.sh:119-135).

Method for the records below: a fix-type commit is one whose message matches `git log --all -i --grep='fix'`. Eight commits match: d8b17a3, bb2c661, b19435c, 8d2e000, 0ad9f4e, 2641e55, 26a79c2, 738f12c. Counting the files each one touches (`git log --all -i --grep='fix' --name-only --pretty=format: | sort | uniq -c`), six files reach three or more: src/bluetooth/avrcp.rs 4, forinstall.md 4, install.sh 3, src/main.rs 3, webui/src/style.css 3, webui/src/components/Settings.tsx 3. Each record says how many of those commits changed the file for a fix that the message names. Four commits have "fix" in the subject line: bb2c661, 8d2e000, 0ad9f4e, 2641e55.

### R6-H3-02  AVRCP monitor (src/bluetooth/avrcp.rs) touched by four fix-type commits
- Question: H3, relates to S06.3
- What was attempted: d8b17a3 created the file. 8d2e000 stopped counting a missing player object toward the stale-connection threshold (src/bluetooth/avrcp.rs:194-203). 0ad9f4e replaced the guessed player path with discovery (R6-H2-07). 738f12c replaced artwork extraction with the art cascade (R6-H2-16). Two of the four (8d2e000, 0ad9f4e) name an AVRCP fix in the subject. A fifth commit, 4505247, also changed the file and is not fix-type.
- Presence: CURRENT. src/bluetooth/avrcp.rs:1-369.
- Outcome: WORKED
- Outcome evidence: STATED. Commit 0ad9f4e: "Live-verified: track title/artist/album/position from a playing iPhone." No result is stated for the 8d2e000 change.
- Why it turned out that way: STATED. Commit 8d2e000: "A missing MediaPlayer1 (idle phone, turntable) was counted as D-Bus staleness and forced a reconnect every ~1.25s." Commit 0ad9f4e: "BlueZ 5.8x moved the player to dev_XX/avrcp/player0".
- Depends on: BlueZ 5.85 (forinstall.md:147); one iPhone; error text containing "UnknownObject", "Unknown object" or "UnknownInterface" (src/bluetooth/avrcp.rs:197-199).
- Open questions: Was the reconnect loop observed to stop after 8d2e000? Was a source with no player (the turntable case in the message) connected after the change?
- Confidence: MEDIUM. STATED evidence; the 8d2e000 result is not stated.

### R6-H3-03  Installer (install.sh) touched by three fix-type commits
- Question: H3, relates to S10.1
- What was attempted: 8d2e000 added a step that stops a development instance before the unit starts (now install.sh:348-351). 26a79c2 rewrote the script (R6-H2-13). 738f12c added `bluez-obexd` and `Experimental = true` (install.sh:97, 173-175). One of the three (8d2e000) names an installer fix in its subject. Four other commits also changed the file: 1e20b41 (created), 07b63e5 (toolchain checks skipped under `--no-build`, install.sh:117-129), 3624d19 and 3046b9e (version string; 3046b9e also the config template, install.sh:337-340).
- Presence: CURRENT. install.sh:1-409.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No file reports the result of an installer run.
- Why it turned out that way: UNKNOWN. Stated reason for the 8d2e000 step: "install.sh stops a running sonicflow-dev/dev binary before claiming the port."
- Depends on: the reference system named at install.sh:3.
- Open questions: Did the 8d2e000 step follow an installer run that met a port conflict? The commit does not say.
- Confidence: MEDIUM. Script text is clear; no result evidence.

### R6-H3-04  Notes file (forinstall.md) touched by four fix-type commits
- Question: H3, relates to S10.1
- What was attempted: d8b17a3 created the file. bb2c661 recorded the A2DP sink registration cause and the linger change (R6-H2-01). 26a79c2 added the machine provenance audit and the note that three packages were unnecessary (forinstall.md:18-47). 738f12c added the album-art entries (forinstall.md:143-153). One of the four (bb2c661) records a fix in this file. Two other commits changed it: 1e20b41 (status header) and 7448b91 (GitHub CLI entry, forinstall.md:49-52).
- Presence: CURRENT. forinstall.md:1-168.
- Outcome: WORKED
- Outcome evidence: STATED, for the one fix the file records: forinstall.md:163-164, quoted in R6-H2-01.
- Why it turned out that way: STATED. forinstall.md:97-104; see R6-H2-01.
- Depends on: see R6-H2-01.
- Open questions: 17 entries remain marked pending (forinstall.md:55-127, 144, 148); which of them were carried out on the development machine by running install.sh?
- Confidence: MEDIUM. The count is mechanical; three of the four commits are not fixes to this file's subject.

### R6-H3-05  Entry point (src/main.rs) touched by three fix-type commits
- Question: H3, relates to S10.2
- What was attempted: d8b17a3 created the file. 2641e55 pinned the rustls provider and added a startup discovery pass for Cast and AirPlay (src/main.rs:33-42, 177-181). 738f12c added `mod art;` (src/main.rs:1). One of the three (2641e55) changed the file for a fix its message names. Two other commits changed it: 4505247 and 1e20b41.
- Presence: CURRENT. src/main.rs:1-240.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. See R6-H2-11.
- Why it turned out that way: UNKNOWN.
- Depends on: see R6-H2-11.
- Open questions: NONE beyond R6-H2-11.
- Confidence: MEDIUM. The count is mechanical; one real fix.

### R6-H3-06  Stylesheet (webui/src/style.css) touched by three fix-type commits
- Question: H3, relates to S09.1
- What was attempted: d8b17a3 created the file (70 lines). b19435c rewrote it for the full UI (960 lines changed). 2641e55 added dropdown styles (101 lines). None of the three messages names a stylesheet fix. A fourth commit, 3629c21, changed the dropdown direction (R6-H2-10); its message does not contain the word "fix".
- Presence: CURRENT. webui/src/style.css:1-1056.
- Outcome: WORKED
- Outcome evidence: STATED, for the 3629c21 change only: "Verified: vertical overflow now 0px with the AirPlay list open."
- Why it turned out that way: STATED. See R6-H2-10.
- Depends on: see R6-H2-10.
- Open questions: NONE.
- Confidence: MEDIUM. The count is mechanical; the one stated correction sits in a commit outside the fix-type set.

### R6-H3-07  Settings component (webui/src/components/Settings.tsx) touched by three fix-type commits
- Question: H3, relates to S09.2
- What was attempted: b19435c created the file. 0ad9f4e changed the scan copy to state that the device is always visible and that Scan is outbound-only (webui/src/components/Settings.tsx:594-603). 2641e55 made the device list collapsible. The 0ad9f4e change accompanies the scan fix in src/bluetooth/manager.rs (R6-H7-03). A fourth commit, 9d619ed, added the auto-pair control.
- Presence: CURRENT. webui/src/components/Settings.tsx:1-704.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. Commit 0ad9f4e gives the copy change without a reason: "Settings copy now states SonicFlow is always visible to phones and Scan is outbound-only."
- Depends on: NONE found.
- Open questions: What prompted the copy change?
- Confidence: MEDIUM. The count is mechanical; no fix to this file is named.

---

## H4 Unmerged work

### R6-H4-01  NOT FOUND
- Question: H4
- Searched: `git branch -a --no-merged` and `git branch -a --no-merged main` (both print nothing); `git stash list` (prints nothing); `git tag` (prints nothing); `git for-each-ref` (four refs, all at 3046b9e8baea2b9150963706525ca95d76f983bc); 00-manifest.md:27 and notes 3-5 (branch count 4, stash count 0, tag count 0; the session branch holds no work of its own); `git rev-list --all --count` (22, equal to the count reachable from main).
- Nearest related material: the second root commit b306b25, merged into main by 128e29f (R6-H2-14). The transient unit `sonicflow-dev` named at forinstall.md:43-44 and install.sh:349 has no definition in any commit (`git log --all -S'systemd-run'` prints nothing).

