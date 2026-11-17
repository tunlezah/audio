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
- Reported friction (forum thread "Bluetooth never pairs", https://moodeaudio.org/forum/showthread.php?pid=56378, forum, 2026-11-17; a first attempt returned HTTP 503, a later attempt on the same day succeeded; the thread concerns moOde 9.0.0 to 9.1.5, which predate the series examined):
  - A user (July 2024) reported that PIN pairing never succeeded and the phone kept asking for the PIN again. [STATED]
  - The project founder replied in the same thread that PIN pairing also failed with his iPhone while some Android users had reported it working. [STATED]
  - A user (Dec 2024, moOde 9.1.5) reported the pairing agent could not be switched on or off in the UI; the founder replied that with the agent stopped a client may connect but the "Bluetooth Active" screen does not appear and no audio plays. [STATED]
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

### P07  ESP32-A2DP (Arduino library, `BluetoothA2DPSink`), Phil Schatzmann
- Category: A
- Version or model examined: `main` branch README and the project wiki page "A2DP Sink" as of 2026-11-17 (S1, S2). This is a library that a developer builds into ESP32 firmware; it has no end-user interface of its own.
- Sources:
  - S1 https://raw.githubusercontent.com/pschatzmann/ESP32-A2DP/main/README.md (source repository, 2026-11-17)
  - S2 https://github.com/pschatzmann/ESP32-A2DP/wiki/A2DP-Sink (official documentation, project wiki, 2026-11-17)
- Setup and onboarding: The developer writes a sketch that constructs `BluetoothA2DPSink` with an output object and calls `start("MyMusic")`; the README states this creates a Bluetooth device with that name and sends output to default I2S pins (BCK 14, WS 15, DATA 22) for an external DAC (S1). [STATED]
- Pairing and connection: The name given to `start()` is what the phone sees (S1). [STATED] PIN or confirmation handling, discoverability control, reconnection and behaviour with a second device: NOT DETERMINED; the wiki page links to separate pages for auto-reconnect and other topics that were not opened (S2).
- Controls: From firmware code: AVRCP commands `play()`, `pause()`, `stop()`, `next()`, `previous()`, `fast_forward()`, `rewind()` sent to the phone (S2). The README states A2DP and AVRCP are supported and that HFP, HSP and stand-alone AVRCP are not (S1). [STATED]
- Status feedback: None built in; the wiki names metadata/notification callbacks and volume synchronisation through AVRC on separate pages (S2). [STATED] Connection-state callbacks: NOT DETERMINED.
- Settings exposed: In code: device name, I2S pin configuration (`pin_bck`, `pin_ws`, `pin_data`), choice of output class, data callbacks (S2). [STATED] Defaults stated: pins 14/15/22 (S1, S2).
- Output selection: In code: I2S stream to an external DAC, the ESP32 internal DAC (stated as GPIO25 and GPIO26), any class derived from Arduino `Print`, or a raw-data callback (`set_stream_reader`) with I2S output switched off (S1, S2). Stated PCM format: 44.1 kHz, two channels, 16-bit (S2). [STATED]
- Error communication: NOT DETERMINED. The examples use the serial port (`Serial.begin(115200)`) and one example prints "Failed to initialize I2S!" (S2). [STATED]
- Programmatic surfaces: C++ class API as listed under Controls, Settings and Output selection (S1, S2). [STATED]
- Reported friction: NONE FOUND (no issue-tracker, forum or review page for this project was opened in this run).
- Not determined: pairing security options; reconnection; second-device behaviour; connection-state callbacks; error surface.

---

## Category B: commercial Bluetooth audio receivers

### P08  Audioengine B1 Bluetooth Music Receiver, Audioengine
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

### P09  iFi ZEN Blue 3, iFi audio
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

### P10  FiiO BR13, FiiO
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

### P11  Pro-Ject BT Box S2 HD, Pro-Ject Audio Systems
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

---

## Category C: network streamers and multi-room systems

### P12  WiiM Pro, WiiM (Linkplay)
- Category: C
- Version or model examined: WiiM Pro, User Manual Version 1.10 dated 08/07/2026 (S1). FAQ articles cover WiiM Pro and Pro Plus (S2) and WiiM devices generally (S3).
- Sources:
  - S1 https://www.wiimhome.com/pdf/WiiM%20Pro%20User%20Manual.pdf (official manual, 2026-11-17)
  - S2 https://faq.wiimhome.com/en/support/solutions/articles/72000645816-how-to-pair-mobile-device-with-wiim-pro-wiim-pro-plus-for-music-streaming (official documentation, 2026-11-17)
  - S3 https://faq.wiimhome.com/en/support/solutions/articles/72000649963-use-the-enter-pairing-mode-option-in-the-wiim-home-app-for-bluetooth-pairing (official documentation, 2026-11-17)
  - S4 https://www.wiimhome.com/pdf/HTTP%20API%20for%20WiiM%20Products.pdf (official documentation, Version 1.2, 2026-11-17)
  - S5 https://forum.wiimhome.com/threads/how-to-put-wiim-amp-into-bt-pairing-mode.3654 (forum, 2026-11-17)
- Setup and onboarding: Five steps in the manual: connect the unit to the audio system, power on, install the WiiM Home app, use the app to join the unit to the network, configure in the app (S1). Bluetooth LE broadcasting is switched on automatically during Wi-Fi setup so the app can find the unit (S1). The LED flashes white slowly when the unit is ready for setup (S1). [STATED]
- Pairing and connection: Three ways into pairing mode: switch the input to Bluetooth in the app or with the remote (the unit enters pairing mode by itself if no device is connected); hold Play on the separately sold remote for 3 seconds; hold Volume + and Volume − on the unit together for 2 seconds (S1, S2). The phone then selects the WiiM in its Bluetooth list (S1). For a second phone: the same methods, or "Enter Pairing Mode" in the app, which disconnects the current phone first (S2, S3). Selecting an input other than Bluetooth or Wi-Fi disconnects Bluetooth; selecting Bluetooth again tries to reconnect the paired phone (S2). The app's "Bluetooth Connections" screen lets the user disconnect, connect or forget each phone (S3). Bluetooth input supports A2DP and AVRCP with SBC and AAC (S1). [STATED] Number of stored devices: NOT DETERMINED.
- Controls: Capacitive touch buttons on the unit (play/pause with hold functions for setup mode and factory restore, preset, volume +/−), the WiiM Home app, an optional remote, and voice assistants through other devices (S1). Models with a built-in screen have an Input menu with the same pairing option (S3). [STATED]
- Status feedback: One three-colour LED (S1). Stated table: fast flashing white = boot; slow flashing white = ready to set up; slow flashing green = Bluetooth ready to pair; solid green = Bluetooth mode, paired; solid white = connected to Wi-Fi; solid light green = line-in; solid orange = S/PDIF in; solid yellow = no network; slow flashing red = hardware error; white-and-green and white-and-red patterns for connecting, update and factory restore (S1). The app shows the current input and connections (S3). [STATED]
- Settings exposed: Input selection, audio output selection, Bluetooth Connections list (S1, S3). Bluetooth-specific settings beyond these and their defaults: NOT DETERMINED. [STATED]
- Output selection: "Select an Audio Output" in the app chooses among the unit's outputs, including Bluetooth out to headphones or speakers (S1). The manual states all inputs, Bluetooth included, can be the source for a WiiM multi-room group: the user selects the source, then taps the Group icon on the device and picks other WiiM devices (S1). [STATED]
- Error communication: LED: solid yellow for no network, slow flashing red for hardware error (S1). [STATED] Messages in the app for a failed Bluetooth pairing: NOT DETERMINED.
- Programmatic surfaces: HTTPS GET API `https://<ip>/httpapi.asp?command=...`, mostly JSON responses: `getStatusEx`, `getPlayerStatus` (the `mode` field value 41 means BT), `setPlayerCmd:switchmode:bluetooth`, volume, mute, presets, reboot, shutdown timer (S4). [STATED] Bluetooth pairing commands in the API: none appear in S4 [INFERRED: a search of the document text for "bluetooth" found only the mode value and the switchmode value].
- Reported friction:
  - Forum user (June 2024, WiiM Amp) could not find how to enter pairing mode for an additional device and reported that holding the volume button appeared to factory-reset the unit; a community curator replied with a sequence (turn off Bluetooth on the paired phone, switch input away and back); the user reported that it worked (S5). [STATED] This thread concerns the WiiM Amp, not the Pro.
- Not determined: number of stored devices; Bluetooth setting defaults; in-app error messages.

### P13  Bluesound NODE, Bluesound (Lenbrook)
- Category: C
- Version or model examined: NODE as listed for sale on the maker's page on 2026-11-17 (S1); the N130 (2021) page is marked "THIS PRODUCT IS NO LONGER FOR SALE" (S2). BluOS Custom Integration API Version 1.7 (S3).
- Sources:
  - S1 https://www.bluesound.com/products/node (official page, 2026-11-17)
  - S2 https://www.bluesound.com/zz/amfile/file/download/file/3478/product/796 (official page, archived N130 listing, 2026-11-17)
  - S3 https://content-bluesound-com.s3.amazonaws.com/uploads/BluOS-Custom-Integration-API_v1.7.pdf (official documentation, 2026-11-17)
  - S4 https://www.bluesound.com/pages/downloads (official page, 2026-11-17)
  - S5 https://support1.bluesound.com/hc/de/community/posts/1500001220861-Bluetooth-pairing-pin?page=2 (forum, maker's support community, 2026-11-17)
- Setup and onboarding: NOT DETERMINED. The product page names the BluOS Controller app (iOS, Android, Windows, macOS) as the control point and lists a printed Quick Setup Guide in the box (S1). The maker's help-centre articles returned HTTP 403 (see BLOCKED lines in the tally).
- Pairing and connection: Bluetooth is two-way (receive and transmit), version 5.2 on the current model (S1). Four Bluetooth modes exist: Manual, Automatic, Guest, Disabled (S3). [STATED] A product support manager wrote in the community thread that players pair with one device at a time, that Automatic is the default, and directed users to Manual or Disabled (S5). [STATED by maker staff in a forum] Pairing procedure and PIN/confirmation behaviour in official documentation: NOT DETERMINED.
- Controls: Front panel with five touch preset buttons, touch volume slider, touch play/pause and a proximity sensor; BluOS Controller app; IR sensor with remote learning and an optional RC1 remote; Alexa skill for voice (S1, S2). [STATED]
- Status feedback: "Status Indicator: LED x 2" on the current model (S1). [STATED] Meaning of LED colours: NOT DETERMINED.
- Settings exposed: Bluetooth mode (four values) (S3). Analog output fixed or variable level (S1, S2). [STATED] Other settings and defaults in official documentation: NOT DETERMINED.
- Output selection: Analog RCA, coaxial, optical, USB audio, headphone, subwoofer and Bluetooth out are listed (S1). Players can be grouped for multi-room playback (S1); the API has `/AddSlave` to add a secondary player to a group (S3). [STATED] Whether the Bluetooth input can be the source for a group: NOT DETERMINED.
- Error communication: NOT DETERMINED.
- Programmatic surfaces: HTTP GET requests to `http://<player_ip>:11000/<request>` with UTF-8 XML responses; `/Status` and `/SyncStatus` with optional long polling (timeout and etag parameters); `/Volume`, `/Play` and other transport requests; `/RadioBrowse?service=Capture` lists inputs, where the example response includes a Bluetooth item with `inputType="bluetooth"`; `/audiomodes?bluetoothAutoplay=value` sets the Bluetooth mode (0 Manual, 1 Automatic, 2 Guest, 3 Disabled); discovery by mDNS and the Lenbrook Service Discovery Protocol (S3). Driver packages for Crestron, Control4, RTI, URC, Lutron and others are offered for download (S4). [STATED]
- Reported friction:
  - Community user (June 2021) asked for a pairing PIN, writing that anyone, neighbours included, could connect to the NODE over Bluetooth (S5). [STATED]
  - Community user (Jan 2025) reported an unexpected stream playing at full volume in the living room and compared this with a car stereo that asks for confirmation before pairing (S5). [STATED]
  - Community user (Nov 2024) replied to staff that the mode setting addresses input switching and not the request to accept only known devices (S5). [STATED]
- Not determined: setup steps; official pairing procedure; LED meanings; error communication; Bluetooth as a group source.

### P14  Denon Home 150 (HEOS Built-in), Denon
- Category: C
- Version or model examined: Denon Home 150, EU web manual (footer copyright 2019) as opened on 2026-11-17 (S1-S8). Current sale status: NOT DETERMINED (no product page was opened).
- Sources (all official manual pages under https://manuals.denon.com/denonhome150/EU/en/, 2026-11-17):
  - S1 https://manuals.denon.com/denonhome150/EU/en/DRDZSYujadfood.php (Pairing the speaker with your Bluetooth devices)
  - S2 https://manuals.denon.com/denonhome150/EU/en/OKNRSYcpepgmip.php (Status LED table)
  - S3 https://manuals.denon.com/denonhome150/EU/en/OKNRSYdlldopvo.php (Basic troubleshooting index)
  - S4 https://manuals.denon.com/denonhome150/EU/en/DRDZSYnxtudihl.php (Cannot pair a Bluetooth mobile device)
  - S5 https://manuals.denon.com/denonhome150/EU/en/FIQOSYpbsvbvqv.php (Cannot hear Bluetooth music)
  - S6 https://manuals.denon.com/denonhome150/EU/en/DRDZSYsockmvew.php (Managing inputs)
  - S7 https://manuals.denon.com/denonhome150/EU/en/DRDZSYjysxebjh.php (Listening to the same music in multiple rooms)
  - S8 https://manuals.denon.com/denonhome150/EU/en/DRDZSYulwcygef.php (Using your speaker)
- Setup and onboarding: The speaker is connected to the network and the HEOS app installed on a mobile device before use (S8). [STATED] Step detail of first setup: NOT DETERMINED (setup pages not opened).
- Pairing and connection: Turn on Bluetooth on the phone; press and hold the Bluetooth button on the rear panel for 3 seconds and release when the status LED shows the pairing pattern; choose "Denon Home 150" on the phone (S1). Note in the manual: "Disconnect and reconnect Bluetooth connections from your Bluetooth device." (S1). [STATED] Number of stored devices and second-device behaviour: NOT DETERMINED.
- Controls: Rear Bluetooth button (S1); HEOS app for rooms, inputs and grouping (S6, S7, S8); the manual index lists Alexa voice operation and a Quick Select function (S3). [STATED]
- Status feedback: One status LED. Stated patterns: Bluetooth pairing = two blinks; Bluetooth paired = one blink; Bluetooth pairing failed = steady for 3 seconds; further patterns for network connecting/connected/failed, WPS, volume change, mute, firmware update, firmware error, setup error and hardware error (S2). [STATED] LED colours were not captured from the page: NOT DETERMINED.
- Settings exposed: In the HEOS app under Settings > Music Sources > Inputs the user picks the speaker and can rename or hide inputs (S6). [STATED] Bluetooth-specific settings: NOT DETERMINED.
- Output selection: Grouping in the HEOS app: up to 32 speakers in a system and up to 16 in one group, played in sync (S7). [STATED] Whether Bluetooth audio can be the source for a group: NOT DETERMINED (the page on playing from Bluetooth rendered without body text).
- Error communication: LED pattern "Bluetooth pairing failed" (steady for 3 seconds) and patterns for firmware, setup and hardware errors (S2). The manual's troubleshooting pages are keyed by symptom: for "Cannot pair" the stated cause is a malfunctioning connection on the phone side and the remedy is to power-cycle the phone, remove the pairing and pair again (S4); for "Cannot hear Bluetooth music" four causes are listed, including the speaker being in Wi-Fi or Aux mode and the phone being connected to another audio device (S5). [STATED]
- Programmatic surfaces: NOT DETERMINED.
- Reported friction: NONE FOUND (no review, forum or issue page for this product was opened in this run).
- Not determined: first-setup steps; stored-device count; LED colours; Bluetooth as a group source; programmatic surfaces.

### P15  Arylic S10 wireless streaming preamplifier, Arylic (Linkplay-based)
- Category: C
- Version or model examined: S10 User's Manual, file dated 2022-03-09 in its name (S1); HTTP API documentation for Arylic/Linkplay-based boards, examples with firmware 4.6.x (S2). Current sale status: NOT DETERMINED.
- Sources:
  - S1 https://cdn.shopify.com/s/files/1/0072/6899/1049/files/S10__20220309_00fd71a8-a714-4aa8-af61-d9cd8cbdccaa.pdf (official manual, 2026-11-17)
  - S2 https://developer.arylic.com/httpapi/ (official documentation, 2026-11-17)
- Setup and onboarding: Install the 4STREAM app; hold the POWER button while plugging in power until the LED flashes, release and wait for a slow flash; with the phone on 2.4 GHz Wi-Fi and with location and Bluetooth on, the app lists the device, asks for the network and password, then offers a preset or custom device name (S1). A fallback path: join the device's own `SoundSystem_xxxx` Wi-Fi from the phone and continue in the app (S1). With Ethernet the device appears in the app in about 10 seconds (S1). [STATED]
- Pairing and connection: Switch to Bluetooth in the app or with the remote, then find `SoundSystem_XXXX` in the phone's Bluetooth settings and pair (S1). [STATED] PIN/confirmation, stored-device count and second-device behaviour: NOT DETERMINED.
- Controls: 4STREAM app; infrared remote with source and six preset buttons; one button on the unit that changes source with a single press (Wi-Fi, Bluetooth, Line input, USB) (S1). [STATED]
- Status feedback: One LED: flashing during Wi-Fi setup; changes to blue in Bluetooth mode (S1). In the app's multiroom list a grey tick marks the master device and a green tick a slave (S1). The app's "Speaker info" shows IP address, MAC address, SSID and firmware version (S1). [STATED]
- Settings exposed: Device name, source mode, left/right/stereo channel per device, presets, factory restore, firmware update prompt in the app (S1). [STATED] Bluetooth-specific settings: NOT DETERMINED.
- Output selection: Multiroom grouping in the app: tap "+" on the device that is to be master and tick other devices (S1). The manual's FAQ states that online streaming, line input and Bluetooth "all can play multiroom mode" (S1). [STATED]
- Error communication: NOT DETERMINED. The manual gives a Wi-Fi reset (long-press POWER 5 seconds) for a changed router and a factory restore by three quick presses or through the app (S1). [STATED]
- Programmatic surfaces: HTTP GET `http://<device-ip>/httpapi.asp?command=<command>` with JSON or plain-text replies and no authentication described: `getStatusEx`, `getPlayerStatus` (mode 41 = Bluetooth input), `setPlayerCmd:switchmode:<source>`, `setPlayerCmd:vol:<0-100>`, `setPlayerCmd:mute:<n>`, `multiroom:getSlaveList`, `multiroom:SlaveKickout:<ip>`, `multiroom:Ungroup`, `ConnectMasterAp:JoinGroupMaster:...` (S2). [STATED] No Bluetooth pairing, disconnect or device-list command appears in S2 [INFERRED: the page was read for Bluetooth commands and only the source value and mode value were found].
- Reported friction: NONE FOUND (no review, forum or issue page for this product was opened in this run).
- Not determined: pairing security and limits; Bluetooth settings; error communication.

### P16  Sonos Era 100, Sonos
- Category: C
- Version or model examined: Era 100, Era 100 SL and Era 100 Pro as covered by the maker's support article opened 2026-11-17 (S1).
- Sources:
  - S1 https://support.sonos.com/en-ie/article/pair-sonos-era-100-with-bluetooth (official documentation, 2026-11-17)
  - S2 https://support.sonos.com/en-us/article/share-bluetooth-audio-with-your-sonos-system (official documentation, 2026-11-17)
  - S3 https://en.community.sonos.com/portable-speakers-229130/bluetooth-pairing-but-no-sound-6885924 (forum, 2026-11-17)
- Setup and onboarding: The speaker "needs to be set up with the Sonos app on WiFi" before Bluetooth can be used (S1). [STATED] Steps of that setup: NOT DETERMINED.
- Pairing and connection: Press and hold the Bluetooth button on the back until a chime sounds; the front status LED flashes blue; select Era 100 on the phone; a second chime and a solid blue LED for a few seconds confirm (S1). Further devices are paired by repeating the steps (S1). Reconnect from the phone's device list, or press the Bluetooth button and the speaker searches for its most recent connection (S1). Disconnect from the phone, or press the Bluetooth button to leave Bluetooth mode (S1). [STATED] Maximum number of paired devices: NOT DETERMINED.
- Controls: Bluetooth button on the back; Sonos app for settings and grouping (S1, S2). [STATED] Other controls on the unit: NOT DETERMINED (not covered by the pages opened).
- Status feedback: Chimes at pairing start, pairing success and reconnection; status LED flashing blue in pairing mode and solid blue briefly on connection (S1). The article states the status LED is disabled by default and can be switched on in the app so that a blue light stays on while a Bluetooth device is connected (S1). [STATED]
- Settings exposed: In the Sonos app: Bluetooth > Idle Auto-Disconnect (disconnects after 30 minutes of inactivity; off by default); Hardware > Status Light (S1). [STATED]
- Output selection: Bluetooth audio can be shared with the rest of the Sonos system: in the app's System tab tap the Grouping icon beside the product playing Bluetooth audio, select rooms, tap Apply; the product has to stay grouped to keep sharing (S2). The same is offered in the Sonos web app (S2). [STATED]
- Error communication: NOT DETERMINED. The article notes Bluetooth is disabled when the Era 100 is used as a surround speaker (S1). [STATED]
- Programmatic surfaces: NOT DETERMINED.
- Reported friction:
  - Community user (Oct 2023) reported that Bluetooth pairing succeeded and the LED was blue but no sound played from an iPhone or a Mac; re-pairing did not help; no fix is recorded in the thread (S3). [STATED]
  - A second user (Dec 2023) reported the same with a Bluetooth turntable after it had worked earlier; unpairing, re-pairing and factory resets did not help (S3). [STATED]
- Not determined: first-setup steps; paired-device limit; other on-unit controls; error communication; programmatic surfaces.

---

## Category D: smart speakers and other devices with Bluetooth-speaker as a secondary function

### P17  Amazon Echo speakers, Amazon
- Category: D
- Version or model examined: "Echo Device" generically, as covered by the maker's help articles opened 2026-11-17 (S1-S3); maker's news article published 17 March 2023, updated 28 December 2023 (S4). No single model was isolated.
- Sources:
  - S1 https://digprjsurvey.amazon.com/csad/help/node/GG8S76D3BYTGC424 (official documentation, "Pair Your Phone or Bluetooth Speaker to Your Echo Device", 2026-11-17; the same node on www.amazon.com returned HTTP 503)
  - S2 https://digprjsurvey.amazon.com/csad/help/node/G5TUMJ2NPD6F44UW (official documentation, "Echo Device Is Having Bluetooth Issues", 2026-11-17)
  - S3 https://digprjsurvey.amazon.com/csad/help/node/GKLDRFT7FP4FZE56 (official documentation, "What Do the Lights on Your Echo Speaker Mean?", 2026-11-17)
  - S4 https://www.aboutamazon.com/news/devices/connect-alexa-bluetooth (official page, 2026-11-17)
  - S5 https://discussions.apple.com/thread/256117866 (forum, 2026-11-17)
- Setup and onboarding: The Echo is first set up in the Alexa app (Wi-Fi and Amazon account) (S4). [STATED]
- Pairing and connection: By voice: say "Alexa, pair", which puts the Echo in pairing mode; choose the Echo in the phone's Bluetooth settings; Alexa announces the connection (S4). By app: put the phone in pairing mode, then Alexa app > Devices > the Echo > device settings > Bluetooth > add a new device (S1). Later: "Pair Bluetooth" or "Alexa, connect to my phone" reconnects; some devices reconnect on their own when in range; "Alexa, disconnect from Bluetooth" ends the connection (S1, S4). [STATED] Number of stored or simultaneous devices: NOT DETERMINED.
- Controls: Voice commands and the Alexa app (S1, S4). Playback is started on the phone (S4). [STATED]
- Status feedback: A spoken announcement when the phone connects (S4). The light-ring article lists meanings for yellow, cyan on blue, red, spinning teal and blue, orange, green, purple and white, and none of them relates to Bluetooth (S3). [STATED]
- Settings exposed: A Bluetooth section in the device's settings in the Alexa app, where devices are added and removed (S1, S2). [STATED] Defaults: NOT DETERMINED.
- Output selection: NOT DETERMINED. The news article says phone audio "should" come out of the Echo (S4).
- Error communication: NOT DETERMINED for on-device messages. The help article lists five remedies: update software, confirm the other device supports A2DP or AVRCP, move away from interference sources, charge the device and keep it near, remove the pairing and pair again (S2). [STATED]
- Programmatic surfaces: NOT DETERMINED.
- Reported friction:
  - Apple Support Community thread (started Aug 2025): the poster reported an iPhone connecting on its own to nearby Echo Dot devices not owned by the poster, with AirPods audio interrupted; later posters attributed the behaviour variously to the Alexa app's Bluetooth permission, Amazon Sidewalk, or an unrelated scale app (S5). [STATED; user-reported and unresolved as to cause]
- Not determined: stored/simultaneous device limits; output selection; on-device error messages; programmatic surfaces.

### P18  Google Nest and Home speakers and displays, Google
- Category: D
- Version or model examined: "Google Nest speaker or display" generically, as covered by the maker's help article opened 2026-11-17 (S1). No single model was isolated.
- Sources:
  - S1 https://support.google.com/googlenest/answer/7381505?hl=en (official documentation, "Play media on your Google Nest speaker or display using Bluetooth", 2026-11-17)
- Setup and onboarding: The article states the phone or computer has to be on the same Wi-Fi network as the speaker or display (S1). First use: in the Google Home app, touch and hold the device tile > Settings > Audio > Paired Bluetooth devices > Enable Pairing Mode (S1). [STATED] Initial device setup: NOT DETERMINED.
- Pairing and connection: After pairing mode is enabled, the user taps the speaker's name in the phone's (or Windows/Mac computer's) Bluetooth settings (S1). By voice: "Connect to Bluetooth" and "Disconnect Bluetooth" (S1). If several phones are paired, the speaker connects to the most recently paired one (S1). [STATED]
- Controls: Google Home app; voice commands preceded by "Ok Google" or "Hey Google": turn on Bluetooth, connect, disconnect, cancel pairing, clear all paired devices (S1). Playback is started on the phone (S1). [STATED]
- Status feedback: Voice queries "Is Bluetooth active?", "Is Bluetooth paired?", "Is Bluetooth connected?" are listed (S1). The app's Paired Bluetooth devices screen shows the paired device and its pairing date (S1). [STATED] What the speaker says or shows during pairing: NOT DETERMINED.
- Settings exposed: Enable Pairing Mode; Paired Bluetooth devices list with per-device Unpair; "Clear all paired Bluetooth devices" by voice (S1). [STATED] Defaults: NOT DETERMINED.
- Output selection: NOT DETERMINED.
- Error communication: NOT DETERMINED. The article's only note is that range varies and the phone is to be kept close (S1). [STATED]
- Programmatic surfaces: NOT DETERMINED.
- Reported friction: NONE FOUND in a page opened in this run. A Nest Community thread found by search redirected to the community front page, one press article returned HTTP 410 and another rendered with headline only (2026-11-17); none is used.
- Not determined: initial setup; device feedback during pairing; output selection; error communication; programmatic surfaces.

### P19  Sonos Roam and Move 2 (portable speakers), Sonos
- Category: D
- Version or model examined: "Roam" as covered by the maker's Roam article and "Move 2" as covered by the maker's Move 2 article, both opened 2026-11-17 (S1, S2). The sharing article lists Roam, Roam 2, Roam SL and Move 2 (S3).
- Sources:
  - S1 https://support.sonos.com/en-us/article/pair-sonos-roam-with-bluetooth (official documentation, 2026-11-17)
  - S2 https://support.sonos.com/en-us/article/pair-sonos-move-2-with-bluetooth (official documentation, 2026-11-17)
  - S3 https://support.sonos.com/en-us/article/share-bluetooth-audio-with-your-sonos-system (official documentation, 2026-11-17)
- Setup and onboarding: Both speakers are first set up on Wi-Fi with the Sonos app before Bluetooth can be used (S1, S2). [STATED]
- Pairing and connection: Roam: press and hold the power button on the back for two seconds and release; a tone sounds and the LED flashes blue; select Roam on the phone; a tone and solid blue LED confirm (S1). For a different phone, the same press first tries the last paired device; if that connects, a second two-second press enters pairing mode; if no known device is near, pairing mode starts by itself (S1). Move 2: press and hold the dedicated Bluetooth button until a chime; LED flashes blue; select the speaker on the phone; chime and solid blue for a few seconds (S2). Roam leaves Bluetooth when it sleeps or powers off and returns in Wi-Fi mode (S1). On Move 2 a press of the Bluetooth button leaves or enters Bluetooth mode (S2). [STATED] Maximum paired devices: NOT DETERMINED.
- Controls: One multi-purpose power button (Roam) or a dedicated Bluetooth button (Move 2); the Sonos app for Bluetooth settings and grouping (S1, S2, S3). [STATED]
- Status feedback: Tones or chimes at pairing start and at connection; LED blinking blue in pairing mode and solid blue when connected (S1, S2). [STATED]
- Settings exposed: In the Sonos app under the speaker's Bluetooth settings: Idle Auto-Disconnect (30 minutes; off by default) and Always Auto-Connect to the most recent device (off by default) (S1, S2). [STATED]
- Output selection: Roam, Roam 2, Roam SL and Move 2 are on the list of products that can share Bluetooth audio with other Sonos products by grouping in the app (S3). Two Roams or two Move 2s in a stereo pair play Bluetooth audio while on Wi-Fi; stereo pairing is not available away from Wi-Fi (S1, S2). Bluetooth is disabled while two Move 2s serve as surrounds (S2). [STATED]
- Error communication: NOT DETERMINED.
- Programmatic surfaces: NOT DETERMINED.
- Reported friction: NONE FOUND (no review or forum page specific to these two models was opened in this run).
- Not determined: paired-device limit; error communication; programmatic surfaces.

### P20  Windows `AudioPlaybackConnection` (PC as Bluetooth audio sink), Microsoft
- Category: D
- Version or model examined: API introduced in Windows 10 version 2004 (10.0.19041.0) (S1); how-to article dated 2026-08-23 (S2).
- Sources:
  - S1 https://learn.microsoft.com/en-us/uwp/api/windows.media.audio.audioplaybackconnection (official documentation, 2026-11-17)
  - S2 https://learn.microsoft.com/en-us/windows/uwp/audio-video-camera/enable-remote-audio-playback (official documentation, 2026-11-17)
  - S3 https://learn.microsoft.com/en-us/answers/questions/4123061/bluetooth-a2dp-snk-device-not-showing-up-in-sound (forum, Microsoft Q&A, 2026-11-17)
- Setup and onboarding: The article states that enabling the A2DP sink "is managed by apps ... rather than by the system" (S2). An app therefore has to be present that uses the API. [STATED] A built-in Windows settings screen for this function: NOT DETERMINED.
- Pairing and connection: The sample app watches for devices with `DeviceWatcher` and the selector string from the API; the code comment reads "Start watching for paired Bluetooth devices" (S2). The app then enables a connection for a chosen device (`TryCreateFromId`, `StartAsync`), which allows incoming connections from that device, and opens it (`Open`/`OpenAsync`), after which audio playing on the remote device is heard on the PC (S1, S2). [STATED] Pairing itself happens outside the API [INFERRED: the watcher enumerates already-paired devices according to the code comment in S2, and no pairing call appears in S1].
- Controls: Defined by the app. The sample UI has a device list, a status text and three buttons: Enable Audio Playback Connection, Release Audio Playback Connection, Open Connection (S2). [STATED]
- Status feedback: `State` property and `StateChanged` event with values including Opened and Closed; the sample shows "Connected", "Disconnected" or "Unknown" (S1, S2). [STATED]
- Settings exposed: None in the API beyond enable, open and release per device (S1). [INFERRED: the class lists two properties, eight methods and one event, none of which is a setting.]
- Output selection: Incoming audio plays on "the system's audio endpoints", with built-in speakers and wired headphones given as examples (S2). [STATED] A way to pick an endpoint for the Bluetooth stream: NOT DETERMINED.
- Error communication: `OpenAsync` returns a result whose `Status` is compared with `Success`; the sample shows "Disconnected (attempt failed)" otherwise (S2). `TryCreateFromId` returns null when no connection is available for the device (S2). [STATED]
- Programmatic surfaces: WinRT class `Windows.Media.Audio.AudioPlaybackConnection`: properties `DeviceId`, `State`; methods `GetDeviceSelector`, `TryCreateFromId`, `Start`/`StartAsync`, `Open`/`OpenAsync`, `Close`, `Dispose`; event `StateChanged` (S1). [STATED]
- Reported friction:
  - Microsoft Q&A asker (Sept 2023) used a third-party "Bluetooth Audio Receiver" app; after moving from Windows 10 to Windows 11 the phone's audio still played through the default output but the device no longer appeared under Sound > Recording, so per-device routing was unavailable; other users reported the same; the accepted answer (Jan 2025) is a user-supplied registry edit (S3). [STATED]
- Not determined: built-in settings UI; endpoint selection for the incoming stream.

### P21  JBL Authentics 200 and 500, JBL (Harman)
- Category: D
- Version or model examined: Authentics 200 and 500 Owner's Manual, file "OM_V8_EN" on the maker's Canadian site (S1). Current sale status: NOT DETERMINED (no product page was opened).
- Sources:
  - S1 https://ca.jbl.com/on/demandware.static/-/Sites-masterCatalog_Harman/default/dwcf00e3e7/pdfs/HA_JBL_Authentics%20200%20&%20500_OM_V8_EN.pdf (official manual, 2026-11-17)
- Setup and onboarding: Connect power; the status indicator pulses white slowly in Wi-Fi setup mode; install the JBL One app on a phone joined to the home Wi-Fi and follow the app; the indicator turns solid green when the app has found the speaker and solid white when it is on the network (S1). Voice assistants (Google Assistant and Amazon Alexa) are set up through the same app (S1). [STATED]
- Pairing and connection: Press the Bluetooth button to select the Bluetooth source; its indicator flashes white fast; within three minutes select "JBL Authentics 200" or "JBL Authentics 500" on the phone; the indicator turns solid white (S1). On returning to the Bluetooth source later the speaker reconnects the last active device automatically (S1). To add another device, press the Bluetooth button again while in the Bluetooth source; the manual states multi-point connection is supported and a second device can be paired without disconnecting the first (S1). [STATED]
- Controls: Bluetooth button; a playback button (one press play/pause, two presses next track, three presses previous track) that the manual lists as working for Bluetooth as well as network sources; rotary volume control; a "Moment" button for a playlist and settings personalised in the app; a microphone on/off slider; the JBL One app; voice assistants, with the note "Voice commands are not supported in Bluetooth mode" (S1). [STATED]
- Status feedback: Bluetooth button indicator: fast flashing white = pairing; solid white = connected; off = not connected. A separate status indicator covers start-up, software update and factory restore, and a voice-assistant LED covers assistant states (S1). [STATED]
- Settings exposed: Sound settings and factory restore in the manual's Settings chapter; presets in the JBL One app (S1). [STATED] Bluetooth-specific settings: NOT DETERMINED.
- Output selection: Multi-room playback and grouping are described for network streaming (S1). [STATED] Whether Bluetooth audio can be grouped: NOT DETERMINED.
- Error communication: No Bluetooth error indicator is described. Troubleshooting by symptom: "A device cannot be connected" (check Bluetooth is enabled; re-enter pairing mode if the speaker is paired with another device); poor audio quality (move the source closer) (S1). Factory restore by holding two buttons for more than 10 seconds (S1). [STATED]
- Programmatic surfaces: NOT DETERMINED.
- Reported friction: NONE FOUND (no review or forum page for this product was opened in this run).
- Not determined: Bluetooth settings; Bluetooth in multi-room groups; programmatic surfaces.

---

## Tally

Products per category (21 records):

| Category | Count | Records |
|---|---|---|
| A | 7 | P01 P02 P03 P04 P05 P06 P07 |
| B | 4 | P08 P09 P10 P11 |
| C | 5 | P12 P13 P14 P15 P16 |
| D | 5 | P17 P18 P19 P20 P21 |

Starting points from the brief: 10 of 11 recorded (P01 moOde, P02 balenaSound/IoTSound, P03 HiFiBerryOS, P08 Audioengine B1, P09 iFi ZEN Blue, P12 WiiM, P13 Bluesound NODE, P17 Amazon Echo, P18 Google Nest, P19 Sonos portable speakers). Yamaha MusicCast is not recorded (see BLOCKED). Found in this run: 11 (P04, P05, P06, P07, P10, P11, P14, P15, P16, P20, P21).

Existence and Bluetooth-input check on the starting points, as found on 2026-11-17: P01 current (release notes dated 2026-10-31); P02 exists, README states it is no longer in active development; P03 legacy releases exist and are stated unmaintained, Bluetooth in the successor NOT DETERMINED; P08 listed in stock; P09 listed in stock as ZEN Blue 3; P12 current manual dated 2026; P13 listed for sale; P17, P18, P19 covered by current maker help pages.

Fields marked NOT DETERMINED, counted over the eight interface fields of each record (168 fields in 21 records):

- Whole field NOT DETERMINED: 20
- Field holds a description and also at least one NOT DETERMINED point: 47

| Field | Whole field NOT DETERMINED | Partly NOT DETERMINED |
|---|---|---|
| Setup and onboarding | P13 (1) | P03 P14 P16 P18 P20 (5) |
| Pairing and connection | (0) | P02 P06 P07 P09 P10 P12 P13 P14 P15 P16 P17 P19 (12) |
| Controls | (0) | P16 (1) |
| Status feedback | P04 (1) | P02 P03 P06 P07 P10 P13 P14 P18 (8) |
| Settings exposed | (0) | P01 P03 P06 P10 P12 P13 P14 P15 P17 P18 P21 (11) |
| Output selection | P03 P17 P18 (3) | P08 P10 P13 P14 P20 P21 (6) |
| Error communication | P03 P04 P07 P13 P15 P16 P17 P18 P19 (9) | P02 P12 (2) |
| Programmatic surfaces | P14 P16 P17 P18 P19 P21 (6) | P06 P09 (2) |

Reported friction marked NONE FOUND: 9 records (P03 P07 P10 P11 P14 P15 P18 P19 P21).

Evidence grades used: STATED and INFERRED only. No statement is OBSERVED; no product was run.

BLOCKED lines:

BLOCKED: Yamaha MusicCast (starting point, category C) is not recorded. Yamaha owner's-manual PDFs on yamaha.com country sites and the MusicCast online manual returned HTTP 403 or 404, and the manual.yamaha.com pages that opened returned navigation without body text, so no official page describing its Bluetooth input could be read.
BLOCKED: Bluesound help-centre articles on support.bluos.net returned HTTP 403. P13 therefore rests on the product pages, the Custom Integration API document and one community thread; its setup, official pairing procedure, LED meanings and the definitions of the four Bluetooth modes are NOT DETERMINED.
BLOCKED: Candidates examined and not recorded because no official page could be opened: Volumio (help articles returned HTTP 404 or redirected to a login page), Bose smart speakers (support article URLs redirected to a generic page), Logitech Bluetooth Audio Receiver, 1Mii B06 Pro and Auris bluMe HD (only third-party manual mirrors or HTTP 403).
BLOCKED: Friction sources that could not be opened and are not used: headfonics.com BR13 review (HTTP 403), a Google Nest Community thread (redirected), techhive.com article (HTTP 410).
- Note: this run was interrupted once by an account usage limit and resumed on the same date; all dates opened remain 2026-11-17.
