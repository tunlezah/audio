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

---

## Category B: commercial Bluetooth audio receivers

### P07  Audioengine B1 Bluetooth Music Receiver, Audioengine
- Category: B
- Version or model examined: "B1 Next Gen" as listed on the maker's shop page at $189.00 and "In Stock" (S1); quickstart guide PDF `B1_Manual_44-6.pdf` (S2).
- Sources:
  - S1 https://audioengine.com/shop/wirelessaudio/b1-bluetooth-music-receiver/ (official page, 2026-11-17)
  - S2 https://audioengine.com/wp-content/uploads/2026/06/B1_Manual_44-6.pdf (official manual, 2026-11-17)
  - S3 https://the-gadgeteer.com/2015/01/12/audioengine-b1-bluetooth-music-receiver-review/ (review, 2026-11-17)
- Setup and onboarding: Three steps in the quickstart guide: connect power, at which point the B1 "will automatically go into pair mode" and the front button starts flashing; connect the RCA cable (or an optical cable) to the music system; select the device on the phone (S2). The shop page states no software, drivers, apps, network setup or passwords are needed (S1). [STATED]
- Pairing and connection: On the phone, select "Audioengine B1"; the indicator turns solid (S2). To add another device the same steps are repeated; the guide states the B1 connects to one device at a time and remembers up to six (S2). To drop the current device and return to pair mode, press and hold the pair button until the indicator flashes (S2). [STATED]
- Controls: One combined power/pair button on the front panel (S1, S2). Volume and transport are on the sending device ("Play your music and adjust volume levels as desired") (S2). No app (S1). [STATED]
- Status feedback: One front indicator: flashing white = pair mode; solid white = device paired; solid orange = aptX HD (S2). [STATED] No sounds or display are described.
- Settings exposed: None described in S1 or S2. [INFERRED: both documents were read in full and name no setting other than the pair button.]
- Output selection: Analog RCA output and Toslink optical output on the rear panel (S2). A selector between them is not described; whether both are live at once: NOT DETERMINED. [STATED for the connectors]
- Error communication: No error indication on the unit beyond the indicator states. The guide carries a troubleshooting list keyed by symptom, for example pair light solid but device cannot connect (remedy listed: disconnect other devices or hold the pair button until it flashes), connected but no audio, low volume, audio cutting out (remedies listed include moving the units, turning off the phone's Wi-Fi, re-pairing, power cycling) (S2). [STATED]
- Programmatic surfaces: None described (S1, S2). [INFERRED: neither document mentions an app, API or configuration file, and S1 states no software is needed.]
- Reported friction:
  - Reviewer Bill Henderson (The Gadgeteer, 12 Jan 2015, original B1 model) wrote that the B1 "has to be unpaired before another Bluetooth device" can connect and found the disconnect/reconnect tedious; the maker's appended reply said holding the Pair button 3 seconds releases the current connection (S3). [STATED] This review predates the "Next Gen" model examined.
- Not determined: whether RCA and optical outputs are active together; standby or auto-off behaviour; reconnection behaviour at power-on.

### P08  iFi ZEN Blue 3, iFi audio
- Category: B
- Version or model examined: ZEN Blue 3, User Manual Ver1.0 (S2); listed "In stock" at £299 on the maker's product page (S1). The unit also has transmit (TX) and DAC modes; this record covers receive (RX) mode.
- Sources:
  - S1 https://ifi-audio.com/products/zen-blue-3/ (official page, 2026-11-17)
  - S2 https://cdn.shopify.com/s/files/1/0879/9741/5761/files/ZEN-Blue-3-User-Manual_Ver1.0.pdf?v=1732613351 (official manual, 2026-11-17)
  - S3 https://primeaudio.org/ifi-zen-blue-3-a-smarter-way-to-add-bluetooth-to-your-setup/ (review, 2026-11-17)
- Setup and onboarding: Attach the antenna, power by USB-C or 5 V DC, long-press the power switch (2 s or more), choose RX mode with the operating mode selector (cycles RX > TX > DAC), connect an output (S2). [STATED] A numbered first-use sequence is not given in the manual; the order above follows the manual's item list [INFERRED: assembled from items 1, 2, 8-15 of S2].
- Pairing and connection: At switch-on the unit blinks blue while it searches for a previously paired device; if none is found it enters pairing mode on its own and blinks blue/red (S2). Pairing mode can be forced with a long press (3 s or more) of the rightmost front button (S2). The name shown on the phone is "iFi Lossless Audio" (S2). Up to 8 paired devices are stored; pairing a 9th removes the first from the list (S2). [STATED] Simultaneous connections: NOT DETERMINED.
- Controls: Front buttons: power (long press), mode selector, input selection / volume (volume applies to TX mode only), TX codec / low latency (not adjustable in RX mode), and a pairing button that also toggles the LED display (single tap) and the Bluetooth voice prompt (double tap) (S2). The manual refers to a "GAIA APP" for a reset setting (S2). Music is controlled from the sending device's app (S2). [STATED]
- Status feedback: The colour of the centre "iFi" logo shows the codec received: off = SBC, yellow = AAC, blue = aptX, magenta = aptX HD, green = aptX Adaptive, white = aptX Lossless, cyan = LDAC, red = LHDC/HWA (S2). A second LED shows sample rate: yellow = 44.1/48 kHz, white = 88.2 kHz and above (S2). A Bluetooth-mode LED blinks blue (searching) or blue/red (pairing) (S2). Voice prompts announce Bluetooth events (S2). A DC power LED lights when powered from a 5 V supply (S2). [STATED]
- Settings exposed: LED display on/off, "on by default"; Bluetooth voice prompt on/off, "on by default"; operating mode (S2). [STATED]
- Output selection: In RX mode the balanced 4.4 mm, RCA and S/PDIF (optical/coaxial) outputs are all listed as outputs; no selector is described (S2). [STATED]
- Error communication: No error indicator is described. For repeated connection failure in TX mode the manual directs the user to check for other paired devices or use the reset setting in the GAIA app (S2). [STATED]
- Programmatic surfaces: A "GAIA APP" is named for reset (S2). Further app functions: NOT DETERMINED. No API or configuration file is described. [STATED]
- Reported friction:
  - Reviewer David Becker (Prime Audio, 11 July 2025) listed as a drawback: "LED indicator system is hard to decipher without the manual" (S3). [STATED]
- Not determined: simultaneous connections; content of voice prompts; app functions beyond reset.

### P09  FiiO BR13, FiiO
- Category: B
- Version or model examined: BR13 as described on the maker's product page and FAQ pages opened 2026-11-17 (S1-S6). The unit also has USB DAC and S/PDIF input modes; this record covers Bluetooth input.
- Sources:
  - S1 https://fiio.com/br13 (official page, 2026-11-17)
  - S2 https://www.fiio.com/newsinfo/876062.html (official documentation, FAQ "Button operation instruction", 2026-11-17)
  - S3 https://fiio.com/newsinfo/876065.html (official documentation, FAQ "How to use the BR13 as a Bluetooth receiver?", 2026-11-17)
  - S4 https://www.fiio.com/newsinfo/876063.html (official documentation, FAQ on the FiiO Control app, 2026-11-17)
  - S5 https://www.fiio.com/newsinfo/876074.html (official documentation, FAQ on two devices, 2026-11-17)
  - S6 https://www.fiio.com/newsinfo/876061.html (official documentation, FAQ "does not work normally", 2026-11-17)
  - S7 https://www.fiio.com/br13_faq (official page, FAQ index, 2026-11-17)
- Setup and onboarding: Power by USB; hold the play/pause button about 3 s to power on; short-press IN to reach Bluetooth input; the screen shows a pairing prompt; select the BR13 on the phone (S2, S3). [STATED]
- Pairing and connection: With no stored device the unit enters pairing on its own; with a stored device it tries to reconnect first and enters pairing only if that fails; holding IN about 3 s forces pairing while a device is connected (S3). Two source devices can be connected at once: pair the first, hold IN about 3 s again, pair the second (S5, S1). Holding EQ about 3 s clears all pairing records (S2, S3). [STATED] Which of two connected devices plays, and how the unit switches: NOT DETERMINED.
- Controls: Five buttons: play/pause (also power and, held 10 s while off, factory reset), volume +, volume −, IN (cycles Bluetooth, USB, Optical, Coaxial), EQ (cycles JAZZ, POP, ROCK, CLAS, MEM1, MEM2, OFF) (S2). The FiiO Control app connects after normal Bluetooth pairing via "My devices" (S4). 60 volume steps on the unit (S1). [STATED]
- Status feedback: An always-on LCD shows the active codec, EQ and other information; the screen shows a pairing prompt in pairing state (S1, S3). [STATED] Sounds: NOT DETERMINED.
- Settings exposed: On the unit: input, EQ preset, volume (S2). In the app: 7 EQ presets and 2 ten-band parametric EQs, digital up-sampling / 48 kHz compatibility mode, aptX LL priority switch, codec on/off toggles (S1, S3, S4). EQ applies only in Bluetooth and USB input modes (S4). Defaults: NOT DETERMINED. [STATED]
- Output selection: Optical, coaxial and RCA outputs; S/PDIF input can be passed through by a bypass function (S1). A selector among outputs for Bluetooth input is not described: NOT DETERMINED. [STATED for the connectors]
- Error communication: No error display is described. The FAQ gives two remedies for "does not work normally": re-plug USB power; hold EQ about 3 s to clear pairings and reconnect (S6). [STATED]
- Programmatic surfaces: FiiO Control app; over-the-air firmware updates (S1, S4). No API or configuration file is described. [STATED]
- Reported friction: NONE FOUND in a page opened in this run. A review at https://headfonics.com/fiio-br13-review/ returned HTTP 403 on 2026-11-17 and is not used.
- Not determined: active-device arbitration with two phones; setting defaults; output arbitration.

### P10  Pro-Ject BT Box S2 HD, Pro-Ject Audio Systems
- Category: B
- Version or model examined: BT Box S2 HD, user guide Revision 2021.3.4 (S2); shown on the maker's site with colour options and a 2026 review link (S1).
- Sources:
  - S1 https://www.project-audio.com/en/product/bt-box-s2-hd/ (official page, 2026-11-17)
  - S2 https://www.project-audio.com/wp-content/uploads/2021/08/BTBoxS2HD-Userguide.pdf (official manual, 2026-11-17)
  - S3 https://www.henleyaudio.co.uk/Media/downloads/products/PJAABTBOXS2HD/PJAABTBOXS2HD_User%20Guide.pdf (official manual hosted by a distributor; its text is the non-HD "BT Box S2" guide, Revision 2021.04.22, 2026-11-17)
- Setup and onboarding: Connect RCA outputs to a line input on the amplifier or the optical output to a DAC/AVR, attach the supplied antenna, connect the 5 V supply, press the power button; the blue LED blinks fast for about 2 seconds at start-up (S2). [STATED]
- Pairing and connection: Pressing the Pair button makes the unit visible and connectable; pairing mode ends on success or after 60 seconds (S2). The phone shows "BT_Box_S2 HD" and presents a pairing code to confirm on the phone (S2). The product page states up to 8 sources are memorised and pairing takes 3 to 5 seconds (S1). [STATED] In the non-HD guide the user confirms pairing by pressing the power button on the unit while the LED blinks fast, older phones use code "0000", and a new connection is possible only while the LED blinks slowly (S3). [STATED, for the non-HD model]
- Controls: Two front buttons: power/standby and Pair (S2). The non-HD model has an output selector button in place of Pair (S3). No app is mentioned. [STATED]
- Status feedback: Blue LED above the Pair button: slow blinking = waiting, no device connected; fast blinking = pairing mode; steady = device connected (S2). Non-HD model: the output LED blinks three times when aptX becomes active (S3). [STATED]
- Settings exposed: None beyond pairing mode (S2). [INFERRED: the five-page guide names no other adjustable item.]
- Output selection: RCA analogue and optical outputs (S1, S2); the HD guide describes no selector. On the non-HD model a front button switches between analogue and digital output with an LED for digital (S3). [STATED]
- Error communication: No error indicator. The guide gives one remedy for connection problems: a memory reset by holding the power button while reconnecting power, for 5 more seconds, after which the LED blinks and stops (S2). [STATED]
- Programmatic surfaces: None described (S1, S2). [INFERRED: no app, API or file is mentioned in either document.]
- Reported friction: NONE FOUND (no review or forum page for this model was opened in this run).
- Not determined: whether both outputs are live together on the HD model; simultaneous connections; reconnection behaviour.

