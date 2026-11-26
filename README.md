# LoR CRx Link / Battle Core releases

Official binary releases. Source remains in the private repository; this public
repository contains installation information and versioned engineering assets.

## Current engineering release

**Battle Core 0.18.11 / CRx Link 1.56 (Android version code 57)**

- Battle Core REV0.1, classic ESP32 / 4 MB flash.
- BLE protocol 6; configuration schema 15.
- [Download v0.18.11](https://github.com/LordofRobots/CRx_Link_Releases/releases/tag/v0.18.11)
  or browse [all releases](https://github.com/LordofRobots/CRx_Link_Releases/releases).

Install the companion APK before updating the Core. This is an engineering
release; the qualification limits below remain open.

## Install or update

1. Install `LoR-CRx-Link-1.56.apk` on the Android phone as an update.
2. Connect to the paired Core, keep it disarmed and disconnect the gamepad.
3. Accept the firmware offer, or use Firmware / Update on Robot or Check
   Firmware Update in Expert Setup.
4. Keep the app open and phone nearby through download, transfer and reboot
   confirmation. Progress uses acknowledged bytes and shows time estimates.

Ordinary OTA preserves supported settings, app ownership and controller pairing.
Offers require matching hardware, compatible source protocol and a newer
firmware version. Existing schema-13 S3/S4 idle assignments migrate unchanged;
S1/S2 idle starts disabled. Older configurations start all idle disabled.
Export a setup profile before upgrading. Older firmware cannot read schema 15
and is not a configuration-preserving downgrade. Review configuration before
reconnecting actuator power after a downgrade or restore.

For blank/erased Cores, use `Battle-Core-0.18.11-USB-Install.zip` and the included
`USB_INSTALL.md`. Its full 4 MB image is written at 0x0 and deliberately destroys
settings, ownership, controller bonds, storage and rollback images. It is not
the OTA application BIN.

## New in 0.18.11 / app 1.56

- **ESC Sync:** learns Forward only/Reversible mode without copying calibration.
  Schema 12-14 synced ESC outputs migrate to standard 1000/1500/2000 us PWM;
  manual/servo calibration, mappings, limits and idle assignments are preserved.
- **ESC startup:** two seconds of authorized zero throttle before ESC throttle
  or idle. Early idle waits; Stop cancels it; disarm clears it and holds pins LOW.
  The dashboard shows ESC STARTUP. This does not measure ESC readiness.
- **Basic:** standard gamepad deadband and disabled impact haptics join the
  existing behavior defaults. Changes stay in the draft until Save to Core.
  Returning to Expert retains the Basic draft; disarm clears active idle.
- **Setup:** safe FAILSAFE_LATCHED saves match Core policy; armed, qualifying,
  unknown and maintenance states remain blocked.
- **UI:** saturated red/white Armed and green/black Disarmed state cards;
  standard compact information icons with unchanged explanation dialogs.

The latest vector icon's Pixel visual check is pending after USB disconnection.
Powered S3 motor response remains unverified; isolated-power controller checks
confirmed its idle/Stop commands and startup zero override.

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
acceptance of the new all-output idle feature, the broader ESC/controller matrix and startup timing,
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
publication. Each build runs 164 Android tests in 32 suites, assembly/lint,
generated schemas, native control/configuration/output-safety/ESC-session checks,
fresh ESP-IDF compilation and the bounded AM32 callback-stack check.

These are engineering artifacts. The measured 2:23–2:28 Pixel/Core OTA times
from the earlier release are historical observations, not a guarantee for the
new candidate, every phone or every radio environment.
