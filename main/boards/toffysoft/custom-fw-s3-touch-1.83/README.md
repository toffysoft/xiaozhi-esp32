# toffysoft Custom FW - ESP32-S3 Touch 1.83

Custom-firmware board definition for the Waveshare ESP32-S3-Touch-LCD-1.83
hardware. The hardware is identical to `waveshare/esp32-s3-touch-lcd-1.83`;
this directory exists only to give the firmware its own reported identity and
therefore its own OTA update channel.

## Why a separate board type

`AGENTS.md` states that board identity affects OTA compatibility. If a custom
build reported `esp32-s3-touch-lcd-1.83`, the stock OTA channel could push
Waveshare's official firmware over it. Reporting
`toffysoft-custom-fw-s3-touch-1.83` keeps the two channels apart.

Do not change `type` or `name` in `config.json` once devices are in the field:
a device that reports an unknown type has no OTA channel to check against.

## Reported identity

| Field | Value |
|---|---|
| `manufacturer` | `toffysoft` |
| `type` | `toffysoft-custom-fw-s3-touch-1.83` |
| `name` | `toffysoft-custom-fw-s3-touch-1.83` |
| `target` | `esp32s3` |

## Hardware

| Part | Detail |
|---|---|
| MCU | ESP32-S3, 16 MB flash, 8 MB PSRAM |
| Display | 240x284 ST7789 over SPI3, backlight on GPIO40 |
| Touch | CST816S on the shared I2C bus (reset GPIO39, interrupt GPIO13) |
| Audio | ES8311 codec + ES7210 mic array via `BoxAudioCodec`, PA on GPIO46 |
| Power | AXP2101 PMIC at 0x34, with `PowerSaveTimer` |
| Buttons | BOOT on GPIO0, power on GPIO41 |

Pin assignments live in `config.h` and match the Waveshare reference exactly.
On-device AEC is enabled, and a double-click on BOOT toggles it at runtime.

## Build

```bash
# Linux / macOS
source /path/to/esp-idf/export.sh

# Windows PowerShell
. C:\esp\esp-idf-v6.0.2\export.ps1

python scripts/build.py toffysoft/custom-fw-s3-touch-1.83
```

Add `--language th-TH` to build the Thai locale, or `--wake-word <model>` to
pick a wake word. Run `python scripts/build.py --list-boards` to confirm the
board is registered.

## Flash

```bash
python -m esptool --chip esp32s3 --port <PORT> write-flash -z 0x0 build/merged-binary.bin
```

Flashing the merged binary erases NVS, so Wi-Fi provisioning has to be redone
afterwards.

## Keeping in sync with upstream

The sources here are byte-identical to
`main/boards/waveshare/esp32-s3-touch-lcd-1.83/` except for the class name,
the log tag, and `config.json`. Diff the two directories when pulling upstream
changes so fixes to the Waveshare board can be applied here as well.
