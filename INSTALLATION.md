# Installing CRx Link 1.60 and Battle Core 0.18.15

Download files from the [official release](https://github.com/LordofRobots/CRx_Link_Releases/releases/tag/v0.18.15).
Use this release only with Battle Core REV0.1, classic ESP32, 4 MB flash, and
Android 8.0 or newer. Export a setup profile before updating.

## 1. Install the Android app

Download `LoR-CRx-Link-1.60.apk` onto the phone, open it and allow installation
from the downloading browser/file app if Android asks. Install it as an update
to retain existing app data. The app does not update its own APK. If Android
reports a signing conflict, stop and check that the installed app came from the
same official channel; uninstalling can erase saved app data.

Keep Bluetooth on and allow Nearby devices permissions. Enable Location only
where Android requires it for Bluetooth discovery. For an already owned Core,
tap Connect and select it. For a clean/new Core, short-press PAIR to open app
enrollment, then select that Core and complete the app pairing flow. A long PAIR
hold is for gamepad replacement, not app enrollment or ESP32 download mode.

## 2. Update an existing Core over Bluetooth

1. Physically isolate motor/actuator power. Turn off/disconnect the controlling
   gamepad or radio and use Disarm. Verify live Core state is disarmed before
   starting firmware maintenance.
2. Connect with app 1.60. Tap Firmware on Robot, or Check firmware update in
   Expert Setup, and accept the compatible 0.18.15 offer.
3. Keep the app open, phone nearby and control-electronics power stable through
   download, acknowledged transfer and reboot verification.
4. Wait for authenticated reconnection to the same Core with firmware 0.18.15
   and startup telemetry. Transfer completion alone does not confirm installation.
5. Review saved source, mappings, limits and ESC settings with motor power still
   isolated before performing neutral, disconnect and mode-switch checks.

Ordinary OTA preserves supported settings and pairing. The manifest accepts
REV0.1 source protocols 5-6; protocol 5 is update-only. Older protocol-6 builds
without `pair=ecdh1` require USB installation. Schema 15 remains current; older
schemas migrate using the existing supported migration. Downgrades are not
settings-preserving. OTA updates the application only, not the bootloader.

## 3. Blank boards or a complete USB installation

Use `Battle-Core-0.18.15-USB-Install.zip` and follow its included `USB_INSTALL.md`
([online copy](https://github.com/LordofRobots/CRx_Link_Releases/releases/download/v0.18.15/USB_INSTALL.md)). Verify the checksums and positively identify the
Core and COM port. The complete 4 MB `Battle-Core-USB-Clean.bin` is written at
**0x0**; the separate `Battle-Core-0.18.15.bin` is the OTA application and must not
be substituted for it. Clean installation erases all Core settings, app ownership,
gamepad bonds, storage and rollback images. Review/restore settings afterward.

For CRSF, the USB package also installs the quiet bootloader. A previously
installed logging bootloader remains in place after an application-only OTA.
No runtime serial console is available in this firmware; use app diagnostics.

## 4. Select and check the control input

**Gamepad:** choose Gamepad in Setup and Save to Core. Use the existing saved
controller or hold PAIR for three seconds to replace it. CRSF is inactive and its
UART driver is released. Phone Bluetooth remains available.

**CRSF / ELRS:** connect receiver TX to Core RXD0/GPIO3, receiver RX to
TXD0/GPIO1, and appropriate receiver power/common ground. Configure the receiver
for CRSF, 420000 baud, uninverted, and use AETR channel order. Select CRSF in Setup
and Save to Core. Gamepad management pauses, retaining its saved relationship.

UART0 shares the programming pins. **Disconnect/isolate receiver TX while USB
flashing. Before reconnecting receiver TX, isolate the USB programmer TX from
RXD0** unless hardware already switches these drivers. Closing a serial monitor
alone does not provide electrical isolation. A download-mode/no-sync error often
means another transmitter is still driving RXD0.

CH1 is right X, CH2 right Y, CH3 left Y and CH4 left X. CH3 must be centered for
reversible drive neutral. CH6/CH7 are analog auxiliaries, low=off; CH8-CH13 are
switches. CH5 and CH14-CH16 are metadata and cannot be output actions. Setup shows
the channel names for each assignment. These channel assignments are also shown in Setup.

A healthy selected source can arm automatically after 500 ms of neutral mapped
controls and zero enabled output demand, subject to the shared safety/readiness
gates. No CH5 arm switch is needed. CRSF loses authority after more than 100 ms
without valid RC packets or immediately on fresh uplink LQ=0. App Stop/Disarm is
an explicit override; CRSF requires actual radio disconnect/reconnect to release
it. Switching modes clears prior authority/history and requires new qualification.

Open the safety card's information icon to see exact blocking channels/output
demands, RC packet age and link statistics. Resolve any ESC setup requirement by
reading Sync or deliberately saving a matching manual throttle mode. Keep actuator
power isolated until neutral, input loss, app Stop and both mode transitions have
been checked. Live radio/motor/electrical qualification remains pending for this
engineering release; see [release details](https://github.com/LordofRobots/CRx_Link_Releases/releases/tag/v0.18.15).
