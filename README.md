# LoR CRx Link / Battle Core releases

Official binary releases. The source repository is private; this repository
contains public installation information and versioned release assets.

## Current engineering release

**Battle Core 0.18.5 / Android app 1.39 (version code 40)**

- Hardware: Battle Core **REV0.1**, classic **ESP32**, **4 MB flash**.
- BLE application protocol: **6**. Configuration schema: **11**.
- [Download the release](https://github.com/LordofRobots/CRx_Link_Releases/releases/tag/v0.18.5)
  or browse [all releases](https://github.com/LordofRobots/CRx_Link_Releases/releases).

Install the companion APK before updating an older Core. This is an engineering
release; the qualification limits below still apply.

## Install or update

1. Install **LoR-CRx-Link-1.39.apk** on the Android phone.
2. Connect to the paired Core, leave it disarmed and disconnect the gamepad.
3. Accept the offered firmware update, or tap **FIRMWARE / UPDATE** on Robot.
   Expert Setup also has **CHECK FIRMWARE UPDATE**.
4. Review the overall estimate. The app shows acknowledged transfer progress,
   estimated remaining time and elapsed time through reboot confirmation.
5. Keep the app open and the phone nearby until the new firmware is confirmed.

Ordinary OTA preserves configuration and ownership. Releases are offered only
for matching hardware, an approved source protocol and a strictly newer version.
The app explains an up-to-date/newer Core, incompatible firmware or a failed
check. Same-version and older images are not offered as upgrades.

For a **blank or erased Core**, download **Battle-Core-0.18.5-USB-Install.zip**.
Follow its **USB_INSTALL.md** and verify the checksums. The merged clean image is
written at **0x0**. Clean installation erases settings, phone ownership,
controller bonds, storage and the previous rollback image. The standalone
**Battle-Core-0.18.5.bin** is the OTA application, not a complete clean image.
Physically isolate actuator power and identify the actual board/port before USB
flashing; do not infer the target from an old COM-port number.

## What changed

- **Disarmed S1–S4:** no PWM/DShot frames and an active LOW clamp. The GPIO
  refresh path preserves that clamp through peripheral cleanup and late callbacks.
- **Simpler Setup:** Basic starts with essential settings and Arcade driving;
  Expert reveals all controls. Style changes remain drafts until Save.
- **Input controls:** standard sliders, editable numeric values and a robot-name
  field that stays visible above the keyboard. Two-wheel ESC drive has independent
  direction, start floor and maximum commands.
- **ESC configuration:** guarded AM32/BLHeli_S settings and profiles. BLHeli_32
  remains a clearly labeled command-only beta with no full parameter or installed
  firmware read path. ESC firmware flashing is excluded.
- **Core OTA:** explicit check/status, real acknowledged progress, temporary
  low-latency timing, bounded queue/retry handling, new-boot confirmation and time
  estimates. Pixel/Core bench updates improved from 4:26 to 2:23–2:28; the final
  app 1.39 trial used zero retries. Network, phone and radio conditions vary.

## Disarmed output behavior

LOW removes the control signal; it does **not** remove actuator power. The ESC's
own signal-loss protection must stop its motor. Configure and verify that
behavior on each attached ESC. A universal internal ESC disarm delay is not
claimed. Reversible PWM commonly uses 1500 µs for neutral; 1000 µs can mean
reverse, so a numeric zero-throttle pulse is not interchangeable with signal
removal. A deliberately requested disarmed ESC configuration session may use its
selected wire temporarily, then returns it LOW.

## Compatibility and evidence

Protocol-5 Cores use authenticated, exact-Core update-only migration. Full
protocol-6 access requires secure **pair=ecdh1** capability. Older protocol-6
engineering builds without it require serial updating.

Release assets include **SHA256SUMS.txt**, two independent committed-source build
records and **reproducibility.json**. APK, OTA image and USB package are compared
between the clean builds. Downloaded firmware size/SHA-256 and the ESP image are
checked before activation; the same Core must reconnect with the target version
before the app reports installation. The Core independently validates healthy
startup probation.

Software builds and recorded bench tests do not certify all ESC/phone models,
electrical waveforms, power-loss rollback, CRSF receiver hardware or competition
safety. Production signing/key management remains open; SHA-256 verifies bytes
against the fetched manifest and does not replace publisher signing.
