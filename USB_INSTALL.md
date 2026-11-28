# Battle Core USB clean installation

This package installs firmware on a blank or erased **Battle Core REV0.1, classic ESP32, 4 MB flash**. The ZIP filename identifies the firmware version. No source checkout, Android build, or ESP-IDF installation is needed. Do not use it on ESP32-S3/C3 or other hardware revisions.

**Clean installation erases all saved Core settings, app ownership, gamepad bonds, recordings/storage, and the previous firmware/rollback image.** Export any wanted profiles first. For an ordinary update that preserves settings, use the Android app's OTA update instead.

## What is included

- `Battle-Core-USB-Clean.bin`: complete 4,194,304-byte flash image; write at **0x0**, not 0x10000.
- `USB_INSTALL.md`: these instructions.
- `SHA256SUMS.txt`: SHA-256 checksums for the image and instructions.

The merged image contains the bootloader at 0x1000, partition table at 0x8000, initial OTA data at 0xD000, and application at 0x10000. All other bytes are erased (0xFF), including configuration and the second OTA slot. The separate release file `Battle-Core-<version>.bin` is only the OTA application; it is not a substitute for this complete image.

## Windows procedure

1. Physically disconnect actuator/motor power. Keep the robot restrained; USB may power only the Core/control electronics. Turn off gamepads and the RC transmitter during installation and initial startup. If a CRSF receiver uses TXD0/RXD0, isolate its TX signal from Core RXD0 while programming; the USB programmer and receiver must not drive that input together.
2. Download `Battle-Core-<version>-USB-Install.zip` from the official [release page](https://github.com/LordofRobots/CRx_Link_Releases/releases). Extract it into a folder. Open PowerShell in that folder.
3. Install Python 3.10 or newer with the Windows Python launcher, then install the supported esptool 5.x command-line utility:

```powershell
py -m pip install "esptool>=5,<6"
py -m esptool version
```

4. Verify the image before using it. This command must print `SHA-256 verified`:

```powershell
$expected = ((Get-Content .\SHA256SUMS.txt | Where-Object { $_ -match '  Battle-Core-USB-Clean\.bin$' }) -split '\s+')[0]
if (-not $expected -or (Get-FileHash .\Battle-Core-USB-Clean.bin -Algorithm SHA256).Hash -ne $expected) { throw 'Firmware checksum mismatch: do not flash' }
'SHA-256 verified'
```

5. Connect the Core using a USB data cable. In Device Manager, identify its COM port by unplugging/reconnecting that specific Core. Close serial monitors and other applications using the port. Replace **COM8** below with the port you identified; do not assume every Core is COM8.
6. Read its flash identification before erasing anything. Confirm it is the intended classic ESP32 and has 4 MB flash:

```powershell
py -m esptool --chip esp32 --port COM8 flash-id
```

7. Only after checking the board/port and accepting the data loss, erase and install:

```powershell
py -m esptool --chip esp32 --port COM8 erase-flash
```

Wait for erase to succeed. If it fails, stop; do not continue with the next command.

```powershell
py -m esptool --chip esp32 --port COM8 --baud 460800 write-flash --flash-mode dio --flash-freq 40m --flash-size 4MB 0x0 .\Battle-Core-USB-Clean.bin
```

Wait for successful write/hash-verification output. If the write fails, leave actuator power disconnected and retry troubleshooting before use. An optional explicit full-image readback check is:

```powershell
py -m esptool --chip esp32 --port COM8 verify-flash 0x0 .\Battle-Core-USB-Clean.bin
```

8. Reset or unplug/reconnect USB. With gamepads/the radio transmitter still off and actuator power still disconnected, confirm startup and the expected firmware version in the Android app. This firmware disables the serial console, bootloader logs and panic printing because UART0 is shared with CRSF; use BLE diagnostics instead. Install the companion APK from the same release on the phone, pair the app, re-pair the desired gamepad, restore/review settings, and qualify safe behavior before reconnecting actuator power. Either selected control source can automatically qualify arming; do not assume a connected controller leaves the Core disarmed. For CRSF operation, isolate USB programmer TX from Core RXD0 before reconnecting receiver TX, unless the board already switches/isolates those drivers. Select the desired source in Setup and Save to Core. See the [complete installation guide](https://github.com/LordofRobots/CRx_Link_Releases/releases/download/v0.18.15/INSTALLATION.md).

## Troubleshooting and other operating systems

If download mode is detected but esptool gets no sync reply, verify receiver TX is isolated from Core RXD0. If esptool cannot open the port, check the data cable, port selection, USB-to-serial driver, and that no serial monitor is open. If download-mode entry fails, use the board's documented BOOT/RESET procedure if available; the Battle Core PAIR button is for Bluetooth pairing, not a substitute for a BOOT button. If flashing is unreliable, retry at `--baud 115200` with a stable USB connection. Never use `--force` to bypass chip/security checks.

On Linux/macOS, use `python3 -m pip install "esptool>=5,<6"` (in a virtual environment if required), replace `py` with `python3`, and replace COM8 with the positively identified serial device. The image address and flash settings are unchanged.

Official tooling reference: [Espressif esptool basic commands](https://docs.espressif.com/projects/esptool/en/latest/esp32/esptool/basic-commands.html).

These are engineering firmware artifacts, not production safety certification. Package byte integrity and layout are verified; outstanding hardware/security qualification remains documented in the release notes.
