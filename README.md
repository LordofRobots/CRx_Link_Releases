# LoR CRx Link / Battle Core releases

Official binary releases. Source remains in the private repository; this public
repository contains installation information and versioned engineering assets.

## Current engineering release

**Battle Core 0.18.9 / CRx Link 1.49 (Android version code 50)**

- Battle Core REV0.1, classic ESP32 / 4 MB flash.
- BLE protocol 6; configuration schema 14.
- [Download v0.18.9](https://github.com/LordofRobots/CRx_Link_Releases/releases/tag/v0.18.9)
  or browse [all releases](https://github.com/LordofRobots/CRx_Link_Releases/releases).

Install the companion APK before updating the Core. This is an engineering
release; the qualification limits below remain open.

## Install or update

1. Install `LoR-CRx-Link-1.49.apk` on the Android phone as an update.
2. Connect to the paired Core, keep it disarmed and disconnect the gamepad.
3. Accept the firmware offer, or use Firmware / Update on Robot or Check
   Firmware Update in Expert Setup.
4. Keep the app open and phone nearby through download, transfer and reboot
   confirmation. Progress uses acknowledged bytes and shows time estimates.

Ordinary OTA preserves supported settings, app ownership and controller pairing.
Offers require matching hardware, compatible source protocol and a newer
firmware version. Existing schema-13 S3/S4 idle assignments migrate unchanged;
S1/S2 idle starts disabled. Older configurations start all idle disabled.

For blank/erased Cores, use `Battle-Core-0.18.9-USB-Install.zip` and the included
`USB_INSTALL.md`. Its full 4 MB image is written at 0x0 and deliberately destroys
settings, ownership, controller bonds, storage and rollback images. It is not
the OTA application BIN.

## New in 0.18.9 / app 1.49

- **Battery:** added LiHV and Lead-acid; the selected cell count remains visible.
  A typical 6 V lead-acid battery is 3S and a 12 V battery is 6S. These profiles
  affect monitoring, not charger settings or the ESC cutoff. New chemistry
  saves require their Core 0.18.9 capabilities. Before downgrading, save a
  profile supported by the older software.
- **Connection:** a 15-second recent-advertisement window, direct connections,
  Try connect for grey saved Cores, separate View saved, Rescan and automatic
  scan-error/cooldown recovery. Missing adverts cannot prove a Core is off.
- **Basic:** S1/S2 Maximum Command is Expert-only; Basic stages both at 100%.
  Visible drive/turn/output limits are preserved. Save while disarmed applies
  the draft; switching views alone does not write the robot.

## System features

- **Common available outputs:** Custom exposes S1–S4; two-wheel exposes S3/S4;
  four-wheel reserves every output for drive mixing. Available cards share
  mapping, limits, response, range and smoothing settings.
- **Optional idle:** eligible analog ESC mappings have gamepad Min Idle/Stop
  assignments. Shared buttons can control multiple outputs with separate floors
  and maximums. Stop clears idle while throttle stays active. Disarm clears all
  latches; a fresh press is needed after rearming.
- **Predictable Basic mode:** advanced behavior resets to Basic defaults in the
  draft. Inputs, direction and visible power limits remain available. Save while
  disarmed applies the draft; returning to Expert keeps those Basic defaults.
- **ESC Sync:** supported AM32/BLHeli_S reads and verified writes mirror throttle
  mode into Core settings. Pending enabled outputs inhibit arming. Re-sync after
  replacing or externally reconfiguring an ESC; unsupported ESCs need manual mode.
- **Compact UI:** contextual help, editable slider values, balanced Setup/Sessions
  cards, visible Disarm state and visual board attitude.
- **Core OTA:** size/SHA-256/image/version checks, acknowledged progress, bounded
  retries and cancellation, time estimates and authenticated reboot confirmation.

AM32/BLHeli_S configuration is guarded and schema-qualified. BLHeli_32 remains
a command-only beta without full parameter/current firmware readback. ESC
firmware flashing is excluded.

## App updates and Google Play

This engineering channel distributes manually installed APK updates. The app
currently updates Battle Core firmware, not its own APK. Google Play publication,
managed signing and optional Play-managed in-app update prompts are future work.
Any update channel must preserve Android package identity and compatible signing.

## Disarm and qualification limits

Disarmed S pins are held LOW with control frames stopped. LOW removes the signal;
it does not remove motor power or prove a universal internal ESC disarm delay.
Verify the actual ESC's signal-loss shutdown. A requested disarmed maintenance
session may temporarily use its selected wire and must return it LOW.

Physical output timing during boot/reset/brownout/controller loss, powered
acceptance of the new all-output idle feature, the broader ESC/controller matrix,
power-loss rollback, radio-wide reconnect soaks and production signing remain open. CRSF hardware and
bidirectional DShot electrical behavior are not fully qualified. Unsaved Setup
drafts can be discarded when the form is rebuilt; save deliberately or retain
tuning in a profile. Publication is not a safety certification.

## Compatibility and provenance

Protocol-5 firmware uses exact-Core authenticated update-only migration. Full
protocol-6 access requires `pair=ecdh1`; older protocol-6 engineering builds
without it require serial updating.

Release assets include APK, OTA BIN, clean USB ZIP, instructions, manifest,
checksums, two independent clean-build records and `reproducibility.json`.
The APK, OTA image, USB ZIP and instructions are compared byte-for-byte before
publication. Each build runs 160 Android tests in 31 suites, assembly/lint,
generated schemas, native control/configuration/output-safety/ESC-session checks,
fresh ESP-IDF compilation and the bounded AM32 callback-stack check.

These are engineering artifacts. The measured 2:23–2:28 Pixel/Core OTA times
from the earlier release are historical observations, not a guarantee for the
new candidate, every phone or every radio environment.
