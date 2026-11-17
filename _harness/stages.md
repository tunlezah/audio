# Stage checklist

In Phase 0, copy this whole section word for word to OUT/_harness/stages.md.

The product is treated as a pipeline of twelve stages. Every repo is audited against all twelve in the same order, so that records line up across repos. A question ID joins the stage and the question number: the second question of S03 is S03.2. There are 48 stage questions and 8 history questions.

Names in brackets are examples to help recognition. Record whatever is actually in the repo.

The Bluetooth stage auditor covers S01 to S06 (24 questions). The Audio stage auditor covers S07 to S12 (24 questions). The History auditor covers H1 to H8.

## S01 Platform and stack

1. Target hardware and operating system named or assumed (boards, computers, OS versions).
2. Languages, runtimes and frameworks used.
3. Bluetooth stack and version (examples: BlueZ, BlueALSA, PipeWire, PulseAudio modules, a vendor or microcontroller stack).
4. Audio stack and version.
5. Other external dependencies, with versions where pinned.

## S02 Adapter and controller

1. How the Bluetooth adapter is found, selected, powered and configured.
2. Assumptions about adapter model, chipset, firmware or number of adapters.
3. Handling of an adapter that is missing, blocked or reset.

## S03 Discoverability, pairing and trust

1. How the device becomes discoverable and pairable, for how long, and under what name and device class.
2. Pairing method and agent behaviour: confirmation, PIN, automatic acceptance.
3. How paired devices are stored, trusted, listed and removed.
4. What happens when a second device tries to pair or connect.
5. Any control over who is allowed to pair.

## S04 Connection and A2DP profile

1. How the A2DP sink role is registered or enabled.
2. Codecs offered and how codec negotiation is handled.
3. Connection states handled: connect, disconnect, out of range, reconnect, reconnect after reboot.
4. Multiple sources: simultaneous connections, switching and priority.

## S05 Audio capture

1. The exact point where audio leaves the Bluetooth stack and enters this project's code or pipeline (API, device, node, socket or file descriptor).
2. Audio format at that point: sample rate, bit depth, channels, encoded or PCM.
3. How the start and stop of a stream are detected.
4. Buffering at capture, and handling of underruns, overruns and gaps.

## S06 Remote control and metadata

1. Transport control between this device and the source: play, pause, skip.
2. Volume: where it is applied, and whether source volume and device volume are linked.
3. Track metadata and playback status: whether fetched, stored or shown.

## S07 Processing

1. Every transformation between capture and output: decode, resample, mix, volume, equalisation, encode.
2. Latency: where it arises, any figure measured or stated, any compensation.
3. Handling of clock drift or synchronisation between source and output.

## S08 Output

1. Every output destination supported (examples: local sound device, network cast target, browser, stream, file) and how one is selected.
2. Protocol and format used for each output.
3. Behaviour when an output is unavailable, slow or changes during playback.
4. Simultaneous output to more than one destination.

## S09 User interface and control surfaces

1. Every way a person or program controls or observes the product: web page, app, command line, API, buttons, lights, display, configuration files.
2. The actions and status items each surface offers.
3. First-time setup as implemented, step by step.
4. How errors and state changes are communicated to the user.

## S10 Lifecycle and operations

1. Installation and setup steps, and which are automated or manual.
2. Start on boot, start order, restart and recovery after a crash.
3. Permissions and privileges required.
4. Configuration: where it lives, what can be set, the defaults.
5. Logging and diagnostics available.
6. Packaging and update approach.

## S11 Testing and measured behaviour

1. Tests present, what they cover, and any recorded results.
2. Source devices named as tested, with the result for each.
3. Measurements recorded: latency, dropouts, processor load, memory, range, uptime.
4. Known issues listed by the author.

## S12 Security and exposure

1. Network services and ports opened, and the authentication on each.
2. Privilege level the processes run with.
3. Secrets and keys: where they are stored and how they are protected (location only, per Rule 11).

## History questions

- H1 Timeline. First and last commit dates, commit count, and periods of activity and inactivity.
- H2 Approach changes. Each point where one approach replaced another (a library swapped, a module rewritten, a file deleted and replaced), with what replaced what and any stated reason.
- H3 Reverts and repeated fixes. Reverted commits, and areas touched by three or more fix-type commits.
- H4 Unmerged work. Each branch, stash or tag not merged into the default branch: what it attempted, how far it got, when it was last touched.
- H5 Dead and disabled code. Commented-out blocks, switched-off features and unused modules, and what each was for.
- H6 Author's markers. Every TODO, FIXME, HACK, XXX, BUG and WORKAROUND comment, and every entry in a notes, changelog or known-issues file.
- H7 Workarounds. Sleeps, retries, delays, version pins with comments, and special cases for a named device, with the problem each one works around where that is evidenced.
- H8 Final state. The subjects of the last ten commits, any uncommitted changes listed in the manifest, and whether the repo was left mid-change.
