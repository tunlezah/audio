# Interface patterns across the recorded products

This file lists, for each of the eight interface surfaces, the distinct ways the 21 products in `01-products.md` handle it, with the record numbers that do it and a count. It contains descriptions and counts only.

How to read it:

- P numbers refer to records in `/home/user/audio/interfaces/01-products.md`; the URLs, source types, dates opened (all 2026-11-17) and evidence grades for every item are in those records. Nothing here goes beyond what a record states.
- A product can appear under more than one way on the same surface. Counts are of records, not of units sold or of importance.
- A product is listed under a way only where its record states it (STATED) or marks it INFERRED. Items resting on an INFERRED statement in the record are marked (inf.). Absence from a list means the record does not establish it, not that the product lacks it.
- Where a record covers two models (P19: Roam and Move 2; P21: Authentics 200 and 500) it is counted once.
- Category key: A = P01-P07, B = P08-P11, C = P12-P16, D = P17-P21.

---

## 1. Setup and onboarding

| Way | Records | Count |
|---|---|---|
| Write an OS image to boot media, then finish configuration in a browser on the local network | P01 P03 | 2 |
| Create a fleet in a cloud device-management account, flash the image it issues, and let the application download to the device | P02 | 1 |
| Run an interactive install script in a shell on an existing OS, answering a prompt per component | P04 | 1 |
| Build and install from source and start the daemons by hand | P05 | 1 |
| Compile the function into firmware by writing code against a library | P07 | 1 |
| Device starts its own Wi-Fi access point; the user joins it and configures in a web page at a fixed address | P06 | 1 |
| Power, an audio cable and the phone's Bluetooth settings only; no app, account or network | P08 P09 P10 P11 | 4 |
| Home-network connection together with the vendor mobile app comes first | P12 P14 P15 P16 P17 P19 P21 | 7 |
| Maker states Bluetooth cannot be used until the Wi-Fi/app setup has been done | P16 P19 | 2 |
| Bluetooth pairing has to be switched on in the vendor app before first use, and the phone is stated to need the same Wi-Fi network | P18 | 1 |
| App uses Bluetooth LE to find the device during Wi-Fi setup | P12 P15 | 2 |
| An account with the maker's online service is part of setup | P02 P17 | 2 |
| A host application has to enable the sink; the operating system does not do it by itself | P20 | 1 |
| First start completes without user action after automatic reboots | P01 | 1 |
| Fallback path offered when the first network setup method fails (own hotspot or own Wi-Fi network) | P01 P15 | 2 |
| NOT DETERMINED (whole field) | P13 | 1 |

## 2. Pairing and connection

How a new phone gets paired:

| Way | Records | Count |
|---|---|---|
| Receiver is discoverable and accepts pairing with no action on the receiver and no code | P02 P04 | 2 |
| Pairing window opened by pressing or holding a button on the unit | P08 P09 P10 P11 P12 P14 P16 P19 P21 | 9 |
| Pairing enabled or started from a web page or vendor app | P01 P03 P12 P15 P17 P18 | 6 |
| Pairing window opened by a spoken command | P17 | 1 |
| Unit enters pairing mode by itself at power-on or when no known device is found | P08 P09 P10 P12 P19 | 5 |
| Pairing window has a stated time limit (1 or 2 minutes or until stopped; 60 seconds; three minutes) | P03 P11 P21 | 3 |
| Optional confirmation code shown and confirmed on both sides | P01 | 1 |
| Optional legacy PIN mode | P02 P04 | 2 |
| Pairing done with operating-system Bluetooth tools outside the product | P04 P05 P20 (inf.) | 3 |
| Device name seen by the phone is set by the user or developer | P01 P02 P04 P07 | 4 |
| Device name seen by the phone is printed in the manual | P08 P09 P11 P14 P15 P21 | 6 |

Second and later devices:

| Way | Records | Count |
|---|---|---|
| One device connected at a time, stated | P04 P08 | 2 |
| Two or more devices connected at once, stated | P05 P10 P21 | 3 |
| More than one client implied by plural wording | P01 (inf.) | 1 |
| Number of remembered devices stated (six; eight; eight) | P08 P09 P11 | 3 |
| An action on the receiver lets a new device in while another is connected (button hold or app option) | P08 P10 P12 P19 P21 | 5 |
| Most recently paired device is the one connected | P18 | 1 |
| First device to start playing holds the output; others wait (option) | P05 | 1 |
| Discoverability switched off while a device is connected | P04 | 1 |

Reconnection, disconnection and clearing:

| Way | Records | Count |
|---|---|---|
| Automatic reconnection to a known device, stated | P02 P09 P10 P12 P16 P17 P19 P21 | 8 |
| Automatic disconnect after an idle period (setting) | P01 P16 P19 | 3 |
| A mode setting governs whether an incoming Bluetooth stream takes over the player (Manual, Automatic, Guest, Disabled) | P13 | 1 |
| Clear-all-pairings action (web command, button hold, power-button sequence, or spoken command) | P01 P10 P11 P18 | 4 |
| Per-device connect, disconnect and forget from a web page or app | P01 P12 P18 | 3 |
| Pairing procedure not described in the pages opened | P06 P07 | 2 |

## 3. Controls

| Way | Records | Count |
|---|---|---|
| Physical controls limited to power and pairing | P08 P11 | 2 |
| Physical controls beyond power and pairing (transport, volume, input, mode or source) | P06 P09 P10 P12 P13 P15 P21 | 7 |
| A button dedicated to Bluetooth | P14 P16 P19 P21 | 4 |
| Pairing shares a button or button combination with another function | P08 P09 P10 P12 P19 | 5 |
| Browser page served by the device | P01 P02 (inf.) P03 P06 | 4 |
| Vendor mobile app | P09 P10 P12 P13 P14 P15 P16 P17 P18 P19 P21 | 11 |
| Spoken commands for Bluetooth functions | P17 P18 | 2 |
| Voice assistant present, stated as not available in Bluetooth mode | P21 | 1 |
| Remote-control handset | P06 P12 P13 P15 | 4 |
| Shell commands | P01 P04 P05 | 3 |
| Cloud dashboard for device management | P02 | 1 |
| Controls are whatever the host application provides | P20 | 1 |
| Transport commands sent from the receiver side to the phone, documented | P01 P07 P21 | 3 |
| Phone's volume requests applied on the receiver, documented (option or mode) | P04 P05 P07 | 3 |
| Volume ceiling applied to Bluetooth sessions | P01 | 1 |
| Playback and volume left to the phone, stated | P02 P08 P17 P18 | 4 |

## 4. Status feedback

| Way | Records | Count |
|---|---|---|
| One light whose blink or steady state shows pairing and connected | P08 P09 P11 P12 P14 P16 P19 P21 | 8 |
| Light colour shows the codec in use | P08 P09 | 2 |
| Light colour shows the active input or mode | P12 P15 | 2 |
| Text or graphic display on the unit | P06 P10 | 2 |
| Chimes or tones at pairing and connection | P16 P19 | 2 |
| Spoken prompt or announcement | P09 P17 | 2 |
| Spoken answer to a status question | P18 | 1 |
| Overlay in the web page while Bluetooth is the active source, with track metadata where the phone provides it | P01 | 1 |
| Paired or connected devices listed in a web page or app | P01 P12 P18 | 3 |
| Status read through an API (source or mode value, state property, metadata) | P01 P03 P12 P13 P15 P20 | 6 |
| Status through command-line listing, logs or a cloud dashboard | P02 P05 | 2 |
| Indicator can be switched off by the user | P09 | 1 |
| Status light off unless enabled in the app | P16 | 1 |
| Maker's light-meaning table contains no Bluetooth state | P17 | 1 |
| Lights present, meanings not established | P06 P13 | 2 |
| None built in; left to the developer's code | P07 | 1 |
| NOT DETERMINED (whole field) | P04 | 1 |

## 5. Settings exposed, and their defaults

Where settings are changed:

| Way | Records | Count |
|---|---|---|
| Browser page served by the device | P01 P03 P06 | 3 |
| Environment variables in a cloud dashboard | P02 | 1 |
| Hand-edited configuration files or command-line options | P04 P05 | 2 |
| Source code | P07 | 1 |
| Vendor mobile app | P10 P12 P14 P15 P16 P17 P18 P19 P21 | 9 |
| Buttons on the unit | P09 P10 | 2 |
| No user setting described | P08 (inf.) P11 (inf.) P20 (inf.) | 3 |

What is exposed:

| Setting | Records | Count |
|---|---|---|
| Bluetooth on/off | P01 P02 P03 P13 | 4 |
| Bluetooth device name | P01 P02 P04 P07 | 4 |
| Pairing security (confirmation on/off, pairing mode, PIN) | P01 P02 P04 | 3 |
| Pairing window length | P03 | 1 |
| Buffer or latency | P01 P02 P05 | 3 |
| Codec quality or codec enable list | P01 P10 | 2 |
| Equaliser applied to Bluetooth input | P10 | 1 |
| Start-up volume or volume ceiling | P01 P02 | 2 |
| Idle auto-disconnect | P01 P16 P19 | 3 |
| Automatic reconnect to the last device | P19 | 1 |
| Behaviour when a Bluetooth stream arrives (auto-switch mode) | P13 | 1 |
| Resume the previous source after Bluetooth ends | P01 | 1 |
| Indicator light or voice prompt on/off | P09 P16 | 2 |
| Renaming or hiding inputs | P14 | 1 |
| Paired-device list management | P01 P12 P17 P18 | 4 |

Defaults:

| Way | Records | Count |
|---|---|---|
| At least one Bluetooth-related default stated in the documentation | P01 P02 P05 P07 P09 P16 P19 | 7 |
| Stated defaults that leave a feature on: LED display and voice prompt (P09); secure simple pairing without PIN (P02) | P02 P09 | 2 |
| Stated defaults that leave a feature off: idle auto-disconnect (P16 P19); auto-connect (P19); status light (P16) | P16 P19 | 2 |
| Default auto-switch mode stated only by maker staff in a forum | P13 | 1 |
| Settings exist, defaults not established | P03 P10 P12 P17 P18 | 5 |

## 6. Output or destination selection

| Way | Records | Count |
|---|---|---|
| Fixed set of output connectors, no selector described | P08 P09 P10 P11 | 4 |
| Output device picked in a browser page | P01 | 1 |
| Output set by an environment variable, with an automatic detection order as the default | P02 | 1 |
| Output set in a configuration file, command-line option, stored parameter or code | P04 P05 P06 P07 | 4 |
| Output picked in the vendor app | P12 | 1 |
| Plays on the host system's audio endpoints | P20 | 1 |
| Bluetooth input can feed a multi-room group, stated | P01 P02 P12 P15 P16 P19 | 6 |
| Multi-room grouping exists; Bluetooth as the group source not established | P13 P14 P21 | 3 |
| Group membership has to be kept for sharing to continue, stated | P16 | 1 |
| Receiving device becomes group master automatically | P02 | 1 |
| The same product can also send audio out to a Bluetooth speaker or headphones | P01 P06 P09 P12 P13 | 5 |
| Bluetooth switched off in a particular speaker role (surround) | P16 P19 | 2 |
| NOT DETERMINED (whole field) | P03 P17 P18 | 3 |

## 7. Error and failure communication

| Way | Records | Count |
|---|---|---|
| Symptom-keyed troubleshooting text in the manual or help pages | P02 P08 P10 P11 P14 P17 P21 | 7 |
| Light pattern for a failed Bluetooth pairing | P14 | 1 |
| Light pattern for non-Bluetooth faults (network, firmware, hardware) | P12 P14 | 2 |
| Message in the web page | P01 (inf.) | 1 |
| Text logs (utility, dashboard, stderr/syslog, serial or telnet) | P01 P02 P05 P06 | 4 |
| Error value returned to a calling program | P01 P20 | 2 |
| Documentation's stated remedy is to re-pair, clear pairings, power-cycle, reboot, reset or reinstall | P03 P04 P08 P10 P11 P14 P15 P17 P21 | 9 |
| Documentation attributes an audio fault to radio interference on the onboard chip | P02 | 1 |
| NOT DETERMINED (whole field; any remedy text is listed above) | P03 P04 P07 P13 P15 P16 P17 P18 P19 | 9 |

## 8. Programmatic surfaces

| Way | Records | Count |
|---|---|---|
| HTTP API on the device over the local network | P01 P02 (inf.) P03 P12 P13 P15 | 6 |
| ... in which Bluetooth appears as a source or mode value | P01 P12 P13 P15 | 4 |
| ... with commands specific to Bluetooth (restart or switch the service, answer a pairing request, set the auto-switch mode) | P01 P13 | 2 |
| ... with no Bluetooth pairing or device-list command found | P12 (inf.) P15 (inf.) | 2 |
| D-Bus interface | P03 P05 | 2 |
| Environment variables | P02 | 1 |
| Configuration files or stored key-value parameters | P03 P04 P06 | 3 |
| Command-line utilities | P01 P04 P05 | 3 |
| Code-level class API (library or operating-system API) | P07 P20 | 2 |
| Change notification without repeated polling (long poll or event) | P13 P20 | 2 |
| Plug-in or extension mechanism | P02 P03 | 2 |
| Driver packages for third-party control systems | P13 | 1 |
| Vendor app is the only surface named | P09 P10 | 2 |
| No programmatic surface described | P08 (inf.) P11 (inf.) | 2 |
| NOT DETERMINED (whole field) | P14 P16 P17 P18 P19 P21 | 6 |

---

## Tally

Products per category: A 7 (P01-P07), B 4 (P08-P11), C 5 (P12-P16), D 5 (P17-P21). Total 21.

Fields marked NOT DETERMINED in `01-products.md`, by surface (whole field / partly):

| Surface | Whole field | Partly |
|---|---|---|
| 1. Setup and onboarding | 1 | 5 |
| 2. Pairing and connection | 0 | 12 |
| 3. Controls | 0 | 1 |
| 4. Status feedback | 1 | 8 |
| 5. Settings exposed | 0 | 11 |
| 6. Output selection | 3 | 6 |
| 7. Error communication | 9 | 2 |
| 8. Programmatic surfaces | 6 | 2 |
| Total | 20 | 47 |

Rows in this file that are themselves NOT DETERMINED rows: 5 (surfaces 1, 4, 6, 7, 8).

BLOCKED: Yamaha MusicCast (starting point, category C) is absent from every count above because no official page describing its Bluetooth input could be opened (HTTP 403/404, or navigation-only pages).
BLOCKED: Bluesound help-centre articles returned HTTP 403, so P13 appears under few ways on surfaces 1, 2, 4 and 7.
