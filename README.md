# LoR CRx Link / Battle Core releases

Official engineering binaries and installation instructions. Source remains in
the private source repository.

## Current release

**Battle Core 0.18.15 / CRx Link 1.60 (Android versionCode 61)**

Battle Core REV0.1, classic ESP32 / 4 MB flash; Android 8.0+; BLE protocol 6 and
configuration schema 15.

[Download the release](https://github.com/LordofRobots/CRx_Link_Releases/releases/tag/v0.18.15)
| [Installation guide](INSTALLATION.md) | [USB instructions](USB_INSTALL.md)
| [All releases](https://github.com/LordofRobots/CRx_Link_Releases/releases)

## What's new

- **Gamepad or CRSF:** dedicated input handlers feed the same safety, system
  functions, mapping and drive/output path. Switching clears old authority and
  drive history, then requires fresh neutral qualification. The inactive handler
  pauses; phone Bluetooth remains available.
- **ELRS/CRSF support:** dedicated UART0 at 420000 baud, a 100 ms valid-RC watchdog
  and immediate disarm on fresh uplink LQ=0. Statistics freshness is 1 second.
  Healthy input permits arming through the shared 500 ms neutral/readiness gates.
  CH5 is metadata, not an arm switch.
- **Useful diagnostics:** Setup selectors show radio channels. Safety help names
  blocking inputs and mapped output demand, plus RC packet age and uplink LQ.
  The dashboard distinguishes radio waiting/timeout/loss from gamepad pairing.
- **ESC setup:** the live lockout notice names unresolved Sync outputs and opens
  ESC setup. Supported Sync readback or matching manual mode clears the gate.
- **Reliability:** both watchdogs are required for output authorization; fault
  states show amber; recordings recover completed journal rows and support save
  retry; per-Core Setup drafts survive navigation/readback without automatic saves.

## Install

Install `LoR-CRx-Link-1.60.apk` first, then connect with actuator power isolated
and use Firmware on Robot for the disarmed OTA upgrade. Keep the app open through
authenticated reboot confirmation. OTA preserves supported settings and pairing.
The app does not update its own APK.

Blank/recovery boards use `Battle-Core-0.18.15-USB-Install.zip`. Its complete image
is written at **0x0** and erases configuration, ownership, gamepad bonds, storage
and rollback images. `Battle-Core-0.18.15.bin` is the OTA application; the two
images are not interchangeable. See the [step-by-step guide](INSTALLATION.md).

For CRSF, isolate receiver TX from RXD0 while flashing; isolate USB programmer TX
before reconnecting receiver TX for operation. UART0 has no runtime serial
console. A complete USB install also installs the quiet bootloader; OTA replaces
only the application.

## Existing system features

- Shared S1-S4 settings for available custom outputs; drivetrain ownership follows
  Custom, two-wheel and four-wheel selection.
- Gamepad Min Idle/Stop for eligible analog ESC mappings; Stop clears idle while
  throttle remains active. Disarm clears transient latches.
- Basic/Expert drafts apply only after Save to Core; limits, direction and
  mappings remain explicit.
- Supported AM32/BLHeli_S configuration, throttle-mode Sync and manual modes;
  BLHeli_32 is command-only beta. ESC firmware flashing is excluded.
- Authorized two-second zero-throttle ESC startup, output leases/watchdogs,
  authenticated maintenance and version/size/SHA-256-checked Core OTA.

## Compatibility and engineering limits

The manifest accepts REV0.1 source protocols 5-6. Protocol-5 migration is
update-only; full protocol-6 access requires `pair=ecdh1`. Earlier protocol-6
engineering builds without it need USB updating. Schema 15 is unchanged; older
firmware cannot preserve its settings on downgrade. Export a setup profile first.

Disarmed pins held LOW remove the control signal, not motor power. Keep actuator
power isolated for setup and bench checks. Live physical Gamepad/CRSF switching,
radio reconnect/loss soaks, powered ESC/motor behavior, electrical shutdown timing,
interrupted-power rollback and production signing remain open qualification.
Publication is an engineering release, not certification of those pending checks.

Release assets include the APK, OTA BIN, clean USB ZIP, installation instructions,
manifest, SHA-256 checksums, independent build records and reproducibility evidence.
