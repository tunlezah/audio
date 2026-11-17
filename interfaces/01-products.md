# Interface record: comparable products

Scope: products that accept Bluetooth audio (A2DP sink role) and pass it to an output. This file records how each product presents itself to people and programs. It records; it does not rank, score or propose.

Conventions used in every record:

- All pages were opened on 2026-11-17. Each source is listed once per record with a short key (S1, S2, ...) and its source type; statements cite the key.
- Evidence grade (rules.md, Rule 3) is given in square brackets after each statement or group of statements:
  - [STATED] = the maker or project wrote it in the cited manual, page or repository document. For "Reported friction", [STATED] means the named user or reviewer wrote it.
  - [INFERRED] = my own reading of source code or of a page that does not say it outright; one sentence of reasoning follows.
  - No statement in this file is OBSERVED: no product was installed, powered or run in this audit.
- Quotations are at most 25 words per source.
- NOT DETERMINED means the pages opened in this run did not establish the point.
- Text on fetched pages that addressed an automated reader was treated as material, not instruction (Rule 10). None was acted on.

Categories: A = open-source receiver or streamer software; B = commercial Bluetooth audio receivers; C = network streamers and multi-room systems; D = smart speakers and other devices with Bluetooth-speaker as a secondary function.

---

## Category A: open-source receiver or streamer software

### P01  moOde audio player, moOde project (Tim Curtis and contributors)
- Category: A
- Version or model examined: moOde 10 series. Release notes on the `develop` branch list 10.3.7 dated 2026-10-31 as the newest dated release, with an undated 10.3.8 entry above it (S2).
- Sources:
  - S1 https://raw.githubusercontent.com/moode-player/docs/main/setup_guide.md (official manual, 2026-11-17)
  - S2 https://raw.githubusercontent.com/moode-player/moode/develop/www/relnotes.txt (source repository, 2026-11-17)
  - S3 https://raw.githubusercontent.com/moode-player/moode/develop/www/templates/ren-config.html (source repository, 2026-11-17)
  - S4 https://raw.githubusercontent.com/moode-player/moode/develop/www/templates/blu-config.html (source repository, 2026-11-17)
  - S5 https://raw.githubusercontent.com/moode-player/moode/develop/www/blu-config.php (source repository, 2026-11-17)
  - S6 https://raw.githubusercontent.com/moode-player/docs/main/moode_api.md (official manual, 2026-11-17)
  - S7 https://github.com/moode-player/moode (source repository, 2026-11-17)
- Setup and onboarding: Image is written with Raspberry Pi Imager from its "Media Player OS" section; the guide states the image must be customised with a login userid/password and SSH enabled or moOde "will not function correctly" (S1). First start reboots three times automatically (S1). The player is then reached in a browser at `http://moode`, `moode.local` or the IP address; audio output device is chosen under Menu > Configure > Audio (S1). If the configured Wi-Fi gives no address, a hotspot with SSID "Moode" starts; its password can only be set in the WebUI Network Config screen, so a first connection by Ethernet or pre-configured Wi-Fi is needed (S1). [STATED]
- Pairing and connection: Bluetooth is one of several "renderers" on the Renderer Config screen with a Controller ON/OFF switch; the screen text says a Bluetooth adapter must be present and enabled or Bluetooth will not start (S3). A "Pairing confirmation" switch: On shows a 6-digit code that must be confirmed on both devices; Off lets devices pair without confirmation (S3). A separate "Bluetooth Control" screen offers a command list (SCAN (20 seconds), LIST paired, LIST connected, LIST trusted, DISCONNECT all, REMOVE all, HELP) and per-device Connect, Forget, Disconnect and Pair buttons (S4, S5). An "Auto-disconnect" interval disconnects all clients when nothing is playing (S3). [STATED] More than one client can be connected at once [INFERRED: the auto-disconnect help text and the "DISCONNECT all" command are worded in the plural (S3, S5); no page opened states a limit].
- Controls: Browser WebUI; REST API; SSH command-line utilities (`moodeutl`, `mpc`, `vol.sh`, `restart-renderer.php`, `set-btaudio.php`) (S1). For Bluetooth, the API's toggle/next/previous commands are forwarded to the renderer; the documented response set includes an alert reading "Playback cannot be controlled from moOde for Bluetooth" (S6). During a renderer session the DAC and DSP are held at a session maximum and only the renderer's own volume moves (S6). [STATED]
- Status feedback: A "Bluetooth Active" overlay appears in the WebUI while a client is the active source; 10.3.7 added a settings (gear) button to it (S2). 10.3.6 added Bluetooth metadata and cover art "for clients that provide it" (S2). Audio Info shows the Bluetooth codec (S2). WebUI notifications follow setting changes, for example a reconnect notice after changing the PCM buffer (S5). [STATED for S2 items; INFERRED for the notification: read from the `$_SESSION['notify']` assignments in S5.]
- Settings exposed: Renderer Config: Controller on/off, Name, Pairing confirmation, ALSA maxvol (%), CDSP maxvol (dB), Auto-disconnect, Resume MPD after the client disconnects, Restart controller (S3). Bluetooth Control: Audio routing, PCM buffer (list from 20 ms to 500 ms, 500 ms labelled Default), SBC quality (Low, Medium, High, XQ, XQ+), Controller mode (Dual labelled Default, BR/EDR, LE) (S4, S5). Defaults of the on/off switches and of the device name: NOT DETERMINED. [STATED]
- Output selection: `Output device` under Menu > Configure > Audio selects the USB or I2S device (S1). Bluetooth Control "Audio routing" switches between "Local audio" and "Bluetooth speaker" (moOde can also act as a Bluetooth source) (S4, S5). The same switch is scriptable with `set-btaudio.php` (S1). Release notes record Bluetooth support for the built-in multiroom sender (S2). [STATED]
- Error communication: WebUI alert "Pair or connect a device first." when Bluetooth speaker routing is chosen with no device (S5) [INFERRED: read from the notify message in the PHP source]. A message under the Controller switch when Bluetooth is turned off in System Config (S2) [STATED]. REST API returns JSON `alert` objects (S6) [STATED]. Logs are printed with `moodeutl` (S1) [STATED].
- Programmatic surfaces: REST API at `http://moode/command/?cmd=COMMAND`: `get_volume` reports `source` as one of `mpd`, `input`, `bluetooth`, and others; `restart_renderer --bluetooth`; `renderer_onoff`; WebUI controller endpoints `get_btmeta` and `bt_pair_response` (answers a pairing request with id, accepted, code) (S6). CLI utilities listed under Controls (S1). Preferences are stored on the local boot media (S7). [STATED]
- Reported friction: NONE FOUND in a page opened in this run. A forum thread on the 6-digit pairing code (https://moodeaudio.org/forum/showthread.php?pid=56378) appeared in search results but returned HTTP 503 when opened on 2026-11-17, so it is not used.
- Not determined: defaults of on/off settings and device name; LED or audible feedback (none is described in the pages opened); stated limit on simultaneous clients.

### P02  IoTSound (formerly balenaSound), balena / community
- Category: A
- Version or model examined: `balena.yml` on `master` gives version 3.11.0 (S5). The GitHub page for the former repository name shows the README notice that the project "is no longer in active development" (S1).
- Sources:
  - S1 https://github.com/balena-io-experimental/balena-sound (source repository, 2026-11-17)
  - S2 https://raw.githubusercontent.com/iotsound/iotsound/master/docs/01-getting-started.md (official documentation, 2026-11-17)
  - S3 https://raw.githubusercontent.com/iotsound/iotsound/master/docs/02-usage.md (official documentation, 2026-11-17)
  - S4 https://raw.githubusercontent.com/iotsound/iotsound/master/docs/03-customization.md (official documentation, 2026-11-17)
  - S5 https://raw.githubusercontent.com/iotsound/iotsound/master/balena.yml (source repository, 2026-11-17)
  - S6 https://raw.githubusercontent.com/balenablocks/bluetooth/master/README.md (source repository, 2026-11-17)
  - S7 https://raw.githubusercontent.com/iotsound/iotsound/master/core/sound-supervisor/src/SoundAPI.ts (source repository, 2026-11-17)
  - S8 https://raw.githubusercontent.com/iotsound/iotsound/master/docs/07-support.md (official documentation, 2026-11-17)
  - S9 https://forums.balena.io/t/can-t-find-my-device-when-searching-for-bluetooth-devices/36266?page=2 (forum, 2026-11-17)
- Setup and onboarding: Requires a balenaCloud account. A "Deploy with balena" button creates a fleet with the app pre-loaded; the user then adds a device, downloads the OS image, flashes it with balenaEtcher and powers on; the app downloads once the device appears in the dashboard (S2). An alternative path uses the balena CLI and `balena push` (S2). The docs state no further configuration is required unless a DAC HAT is used (S2). [STATED]
- Pairing and connection: "search for your device on your phone or laptop and pair" (S3). Default advertised name is `balenaSound <plugin> xxxx`, where xxxx is the first four characters of the device UUID (S3). The Bluetooth block runs a pairing agent, defaults to Secure Simple Pairing with no PIN, offers a LEGACY mode with PIN default `0000`, and reconnects on boot to known and trusted devices (S6). [STATED] Behaviour with a second simultaneous phone: NOT DETERMINED.
- Controls: No play/transport controls of its own are documented; control is from the sending device (S3). The sound-supervisor serves a web UI and HTTP API on a configurable port (S4, S7). Device management (variables, restart, logs) is through the balenaCloud dashboard (S2, S4). [STATED; the presence of a static UI at `/` is INFERRED from `express.static(... 'ui')` in S7.]
- Status feedback: Device online state is shown in the balenaCloud dashboard (S2). Service logs are referred to in the support guide (S8). [STATED] Lights or sounds on the device: NOT DETERMINED (none described).
- Settings exposed: Environment variables set in the balena dashboard at fleet or device scope (S4): `SOUND_MODE` (MULTI_ROOM, MULTI_ROOM_CLIENT, STANDALONE; multi-room is the default on most device types per S3), `SOUND_VOLUME` (app default 75 in S5), `SOUND_DEVICE_NAME`, `BLUETOOTH_DEVICE_NAME`, `AUDIO_OUTPUT` (app default AUTO in S5), `SOUND_INPUT_LATENCY`, `SOUND_OUTPUT_LATENCY`, `SOUND_SUPERVISOR_PORT`, `SOUND_DISABLE_BLUETOOTH` and other per-plugin disable switches (S4); `BLUETOOTH_HCI_INTERFACE` (default hci0), `BLUETOOTH_PAIRING_MODE` (default SSP), `BLUETOOTH_PIN_CODE` (default 0000) (S6). [STATED]
- Output selection: `AUDIO_OUTPUT` variable: `AUTO` detects with priority USB > DAC > HEADPHONES > HDMI; other values force DAC, Raspberry Pi headphone jack, or HDMI ports (S4). In multi-room mode the device that receives a stream becomes `master` automatically and other devices on the local network play in sync (S3). [STATED]
- Error communication: Through service logs viewed in the dashboard; the support guide quotes log lines such as "SBC decoding error" as the symptom to look for (S8). [STATED] On-device or in-app error messages: NOT DETERMINED.
- Programmatic surfaces: Environment variables (S4, S6). HTTP API in sound-supervisor with routes including `/ping`, `/config`, `/version`, `/mode` (POST), `/audio`, `/audio/volume` (GET and POST), `/audio/sinks`, `/device/restart`, `/device/reboot`, `/device/shutdown`, `/support` (S7) [INFERRED: read from the route registrations in the source file; no API reference page was opened]. `docker-compose.yml` can be edited before a CLI deploy to remove services (S2) [STATED].
- Reported friction:
  - Forum user (June 2020) reported the device could be seen but not connected, and that "the bluetooth discovery keeps automatically turning off"; after enabling discoverability by hand the connection held about 2 seconds (S9). [STATED]
  - Forum user (July 2021) reported the device not appearing as a speaker, later traced by the same user to having deployed the wrong application (S9). [STATED]
  - The project's own support guide describes intermittent audio cuts over Bluetooth on Raspberry Pi 3 B/B+ and attributes them to Wi-Fi/Bluetooth interference on the onboard chip (S8). [STATED by the project]
- Not determined: second-device behaviour; on-device status indication; what the sound-supervisor web UI shows.

### P03  HiFiBerryOS (legacy Buildroot releases), HiFiBerry
- Category: A
- Version or model examined: Legacy HiFiBerryOS as documented on the `master` branch. The README notice says HiFiBerryOS and HiFiBerryOS64 releases "are no longer maintained" and that a new system is developed in the `hbosng` branch (S1). The repository front page (default branch) README lists MPD, Spotify Connect, Roon, AirPlay 2 and Squeezelite players and does not list Bluetooth (S2). The maker's product page still lists Bluetooth ("not on Raspberry Pi 3B") among services and carries an April 2025 notice that the current version is not developed further (S3). Existence check result: the legacy releases exist and document a Bluetooth sink; Bluetooth in the successor is NOT DETERMINED.
- Sources:
  - S1 https://raw.githubusercontent.com/hifiberry/hifiberry-os/master/README.md (source repository, 2026-11-17)
  - S2 https://github.com/hifiberry/hifiberry-os (source repository, 2026-11-17)
  - S3 https://www.hifiberry.com/hifiberryos/ (official page, 2026-11-17)
  - S4 https://raw.githubusercontent.com/hifiberry/hifiberry-os/master/doc/services.md (source repository, 2026-11-17)
  - S5 https://raw.githubusercontent.com/hifiberry/hifiberry-os/master/doc/known-problems.md (source repository, 2026-11-17)
  - S6 https://raw.githubusercontent.com/hifiberry/create/master/Beocreate2/beo-extensions/bluetooth/menu.html (source repository, 2026-11-17)
  - S7 https://raw.githubusercontent.com/hifiberry/audiocontrol2/master/doc/api.md (source repository, 2026-11-17)
- Setup and onboarding: Image downloads per Raspberry Pi model and linked guides for installing and for configuring Wi-Fi are offered on the product page (S3). [STATED] Step-by-step content of those guides: NOT DETERMINED (not opened). For the successor, the front-page README describes a browser first-run wizard covering sound card selection, system naming and service configuration (S2). [STATED]
- Pairing and connection: The Bluetooth screen of the web UI has an Enabled switch and a "Start Bluetooth Pairing" action; the user chooses how long the product accepts new devices: For 1 Minute, For 2 Minutes, or Until stopped (S6). Screen text: "Previous devices can connect again without pairing, except after software updates." (S6). Changing the setting interrupts Bluetooth playback and disconnects devices (S6). [STATED in UI text]
- Controls: Browser UI derived from the Bang & Olufsen Beocreate project (S1). REST API for play, pause, next, previous, volume, mute and power-off of the active player (S7). [STATED]
- Status feedback: Player status and track metadata are available from the audiocontrol API (S7); MPRIS for Bluetooth is provided through mpris-proxy (S4). [STATED] What the UI displays during Bluetooth playback: NOT DETERMINED.
- Settings exposed: Bluetooth Enabled; pairing window length (S6). Configuration file `/etc/bluetooth/main.conf` and three systemd services (bluetoothd, bluealsa, bluealsa-aplay) are listed for the Bluetooth module (S4). Defaults: NOT DETERMINED. [STATED]
- Output selection: NOT DETERMINED for Bluetooth specifically. The README states only HiFiBerry sound cards are supported in the successor (S2). [STATED]
- Error communication: NOT DETERMINED. The known-problems document says Bluetooth initialisation is not fully reliable and names a reboot as the remedy (S5). [STATED]
- Programmatic surfaces: audiocontrol REST API: `/api/player/play`, `/pause`, `/playpause`, `/stop`, `/next`, `/previous`, `/api/player/status`, `/api/player/activate/<playername>`, `/api/track/metadata`, `/api/volume`, `/api/volume/mute`, `/api/system/poweroff` (S7). A plugin system for audiocontrol and a UI extension concept are described (S1). [STATED]
- Reported friction: NONE FOUND from users or reviewers in pages opened in this run. The maker's own known-problems list states that Bluetooth volume does not change the ALSA master volume and that Bluetooth start-up sometimes fails (S5). [STATED by the maker]
- Not determined: installation steps; UI display during Bluetooth playback; output selection; error messages; whether the successor accepts Bluetooth audio.

### P04  Raspberry Pi Audio Receiver (rpi-audio-receiver), Nico Kaiser
- Category: A
- Version or model examined: `main` branch README and `install.sh` as of 2026-11-17; README names Raspberry Pi OS 13 Lite as the requirement (S1).
- Sources:
  - S1 https://raw.githubusercontent.com/nicokaiser/rpi-audio-receiver/main/README.md (source repository, 2026-11-17)
  - S2 https://raw.githubusercontent.com/nicokaiser/rpi-audio-receiver/main/install.sh (source repository, 2026-11-17)
  - S3 https://github.com/nicokaiser/rpi-audio-receiver/issues?q=is%3Aissue+bluetooth (issue tracker, 2026-11-17)
- Setup and onboarding: The user downloads `install.sh` with wget and runs it with bash on a clean Raspberry Pi OS Lite; the script asks a yes/no question per component (Bluetooth, AirPlay 2, Spotify Connect) and prompts for hostname and a "pretty hostname" that becomes the Bluetooth name (S1, S2). README: "the installation process is not reversible, there is no uninstall" (S1). A USB Bluetooth dongle is listed as a requirement (S1). [STATED]
- Pairing and connection: The installer adds an agent that "accepts every connection" (S1); the agent runs with capability NoInputNoOutput and the adapter is set discoverable with no timeout (S2). A udev script turns discoverability off while a device is connected and on again at disconnect (S1, S2). README limitations: one Bluetooth device at a time; "The device is always open, new clients can connect at any time without authentication." (S1). Manual pairing through `bluetoothctl` and an optional PIN mode through edited service files are documented, with a note that PIN mode does not work with iOS (S1). [STATED]
- Controls: None on the receiver; installation and all changes are by shell. Optional A2DP volume control is enabled by adding `--plugin=a2dp` to the bluetoothd command line (S1). [STATED]
- Status feedback: NOT DETERMINED. No light, sound, display or notification is described in S1 or S2.
- Settings exposed: Installer prompts: hostname, pretty hostname, per-component yes/no (S2). Everything else is by editing files: `/etc/bluetooth/main.conf` (the installer writes Class, DiscoverableTimeout = 0, JustWorksRepairing=always, AutoEnable=true), `/etc/asound.conf`, `/boot/firmware/config.txt`, the bt-agent unit file (S1, S2). [STATED]
- Output selection: Audio goes to the ALSA default device; for I2S cards the user edits `/boot/firmware/config.txt` and `/etc/asound.conf` by hand (S1). [STATED]
- Error communication: NOT DETERMINED. No error surface is described; the README advises wiping the SD card and starting over when something goes wrong (S1). [STATED]
- Programmatic surfaces: Shell script, systemd units, udev rule and configuration files; no API (S1, S2). [INFERRED: no API is mentioned in either file and the installer creates only the files named above.]
- Reported friction (issue titles as listed on S3; issue bodies were not opened):
  - #173 "Bluetooth connection drops intermittently" (open, July 2024) (S3). [STATED]
  - #177 "Connecting to the device, then forgetting / removing it on the phone makes the pi unconnectable." (closed, Dec 2025) (S3). [STATED]
  - #161 "Error message while trying to connect: Pin oder Passkey wrong" (closed, Dec 2023) (S3). [STATED]
  - #155 "bluetooth volume not controllable" (closed, Jan 2024) (S3). [STATED]
  - #174 "Bluetooth currently playing song info" (open, July 2024) (S3). [STATED]
- Not determined: status feedback; error communication.

### P05  BlueALSA (bluez-alsa) with `bluealsa-aplay`, Arkadiusz Bokowy and contributors
- Category: A
- Version or model examined: `master` branch README and `bluealsa-aplay` manual page as of 2026-11-17 (copyright line 2016-2026). The README notes the daemon was renamed `bluealsad` and the CLI `bluealsactl` after release 4.3.1 (S1).
- Sources:
  - S1 https://raw.githubusercontent.com/arkq/bluez-alsa/master/README.md (source repository, 2026-11-17)
  - S2 https://raw.githubusercontent.com/arkq/bluez-alsa/master/doc/bluealsa-aplay.1.rst (official manual, 2026-11-17)
  - S3 https://github.com/arkq/bluez-alsa/issues?q=is%3Aissue+bluealsa-aplay (issue tracker, 2026-11-17)
- Setup and onboarding: Built and installed from source following INSTALL.md and a wiki guide (S1). The `bluealsad` daemon must be running before a device can pair, connect and be used; the README refers to a wiki guide for pairing and connecting on Linux (S1). The README describes the intended setting as small dedicated audio systems where applications talk directly to ALSA (S1). [STATED]
- Pairing and connection: Not handled by BlueALSA itself; done with BlueZ tools, per the wiki guide named in S1. `bluealsa-aplay` by default plays audio from all connected Bluetooth devices; a list of device addresses restricts it (S2). `--single-audio` lets only the first device to start play while others are paused, the next one playing when the first stops (S2). Without it, each stream opens its own connection to the ALSA device, which then needs to support mixing (S2). [STATED]
- Controls: Command-line options only. AVRCP volume requests from the phone can be applied to a local ALSA mixer control (S2). [STATED]
- Status feedback: `--list-devices` and `--list-pcms` print connected devices and PCMs; log output goes to stderr by default or to syslog with `--syslog`, with `--loglevel` and `--verbose` (S2). [STATED]
- Settings exposed (as command-line options, with stated defaults): `--pcm` (default `default`), `--pcm-buffer-time` (default four times the period time), `--pcm-period-time` (default 50000 for A2DP), `--volume` (auto, mixer, none, software; default auto), `--mixer-device` (default `default`), `--mixer-control` (default `Master`), `--mixer-index` (default 0), `--profile` (default A2DP), `--resampler`, `--single-audio`, `--dbus` (S2). [STATED]
- Output selection: `-D NAME, --pcm=NAME` names the ALSA playback device (S2). [STATED]
- Error communication: Log lines on stderr or syslog (S2). [STATED]
- Programmatic surfaces: D-Bus service `org.bluealsa` on the system bus, with a documented PCM interface; ALSA plug-ins that hide the D-Bus layer; `bluealsactl`; `bluealsa-aplay` options (S1, S2). [STATED]
- Reported friction (issue titles as listed on S3; bodies not opened):
  - #803 "Add multi-point support in bluealsa-aplay" (open, May 2026) (S3). [STATED]
  - #757 "In bluealsa-aplay the mixer device is not set by default same as the output device" (open, Mar 2025) (S3). [STATED]
  - #761 "No audiostream when connecting via bluetoothctl" (closed, May 2025) (S3). [STATED]
  - #735 "Bluetooth input (source) incorrectly setting alsa output volume" (closed, Nov 2024) (S3). [STATED]
  - The README itself says the wiki "is not indexed by web search engines" (S1). [STATED by the project]
- Not determined: NONE beyond pairing detail, which the project delegates to BlueZ.

### P06  squeezelite-esp32, sle118 / philippe44 and contributors
- Category: A
- Version or model examined: README on branch `master-v4.3` as of 2026-11-17 (S1). The README states ESP32-S3 modules have no Bluetooth audio (S1).
- Sources:
  - S1 https://raw.githubusercontent.com/sle118/squeezelite-esp32/master-v4.3/README.md (source repository, 2026-11-17)
  - S2 https://github.com/sle118/squeezelite-esp32/issues?q=is%3Aissue+bluetooth (issue tracker, 2026-11-17)
- Setup and onboarding: After flashing, the ESP32 starts its own Wi-Fi access point; the default build SSID and password are both "squeezelite"; the user joins it, opens 192.168.4.1, waits for the list of visible networks, picks one and enters credentials, then notes the address the device received for later configuration (S1). An optional step generates the squeezelite command line from predefined options in the web page (S1). To run without a Logitech Media Server the server name must be set to `-disable`; otherwise a safety feature reboots the device every few minutes when no server is found (S1). [STATED]
- Pairing and connection: The README lists "Stream from a Bluetooth device (iPhone, Android)" as a capability (S1). [STATED] Pairing procedure, PIN handling and behaviour with a second device: NOT DETERMINED (not described in S1).
- Controls: Web UI on the device; configurable hardware buttons, rotary encoder and a dedicated volume encoder that is hard-wired to volume up/down and play toggle "for LMS, BT, AirPlay and Spotify"; infrared remotes, where in AirPlay and Bluetooth mode only the native Logitech remotes are supported (S1). [STATED]
- Status feedback: Optional display with a `metadata_config` parameter that sets how metadata is shown for AirPlay and Bluetooth (artwork "does not work for Bluetooth"); green and red status LEDs assignable to GPIOs; optional LED strip VU meter (S1). [STATED] Meaning of LED states: NOT DETERMINED.
- Settings exposed: Stored as NVS parameters edited in the web UI's NVS editor (enabled under "credits"), including `dac_config`, `spdif_config`, `display_config`, `metadata_config`, `set_GPIO`, `rotary_config`, `led_brigthness`, button mappings (`actrls_config`) (S1). Named builds (SqueezeAMP, Muse) force some hardware settings (S1). [STATED] Bluetooth-specific parameter names and defaults: NOT DETERMINED.
- Output selection: Depending on hardware: local I2S DAC, SPDIF, or a Bluetooth speaker; selected through `dac_config` / `spdif_config` and the squeezelite `-o` option (S1). [STATED]
- Error communication: Serial monitor and an optional telnet server giving access to logs (S1). [STATED]
- Programmatic surfaces: NVS parameters; squeezelite command line; firmware update and recovery from the web UI's firmware tab; telnet log server (S1). [STATED] HTTP API: NOT DETERMINED.
- Reported friction (issue titles as listed on S2; bodies not opened):
  - #454 "[Request] Bluetooth: Configure visibility" (open, Nov 2024) (S2). [STATED]
  - #458 "[Request]: Bluetooth: Add support for more codecs than SBC" (closed as wontfix, Dec 2024) (S2). [STATED]
  - #428 "Speaker gives noise when playing simultaneously from Bluetooth and other source" (open, June 2024) (S2). [STATED]
- Not determined: pairing procedure; Bluetooth setting names and defaults; LED state meanings; HTTP API.

