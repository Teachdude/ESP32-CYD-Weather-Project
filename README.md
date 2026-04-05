# CYD Weather Station — "Cyber Lime" Edition

A polished, feature-rich indoor weather station for the **ESP32-2432S028** ("Cheap Yellow Display") driven by a **BME280** environmental sensor. Displays real-time temperature, humidity, barometric pressure, dew point, comfort level, and a long-term pressure trend graph — all on the built-in 2.8″ ILI9341 TFT display.

**Current version:** v5.15 · "Cyber Lime" palette

---

## Screenshots

> _Add your own photos here. Replace the placeholders below with actual images of your build._

| Dashboard Screen | Pressure Trend Graph |
|:---:|:---:|
| ![Dashboard](images/dashboard.jpg) | ![Graph](images/graph.jpg) |

_To add images: create an `images/` folder in your repository, place your photos there, and update the paths above._

---

## Features

- **Two alternating screens** — Dashboard (15 s) and Pressure Trend Graph (15 s)
- **Dashboard** shows temperature (large), humidity %, barometric pressure (inHg), dew point, and comfort level
- **Pressure trend graph** — up to 96 averaged slots, configurable interval (1–480 min), with MIN/MAX stats, Y-axis scale, trend arrow, and NOW reading
- **Comfort level** — DRY / COMFORTABLE / HUMID / OPPRESSIVE, derived from dew point, colour-coded
- **Breathing dot** — animated live indicator pulsing between electric lime and soft violet
- **"Cyber Lime" colour palette** — deep navy background, soft violet borders and labels, electric lime sensor data; designed around human peak photopic sensitivity (~555 nm) for maximum legibility on TN panels
- **PWM backlight dimming** — adjustable 0–100 % brightness, NVS-persisted across reboots
- **All settings NVS-persisted** — altitude, graph interval, temperature unit, and brightness survive power cycles
- **Serial command interface** — configure at runtime without reflashing
- **Hardware watchdog** — auto-reboots on I2C lockup or main-loop stall
- **Sensor error recovery** — up to 5 reinitialisation attempts on boot before clean restart
- **NaN validation** — transient I2C glitches are silently skipped; the graph and display are never corrupted
- **Zero heap String allocations** during normal display operation — all formatting uses stack `char` buffers

---

## Hardware

### Required Components

| Component | Notes |
|---|---|
| ESP32-2432S028 ("CYD") | The "Cheap Yellow Display" — ESP32 with built-in 2.8″ ILI9341 TFT |
| BME280 breakout module | Temperature, humidity, and pressure sensor; I2C address `0x76` (SDO pin LOW) |
| Dupont / jumper wires | 4 wires: VCC, GND, SDA, SCL |
| USB-C or Micro-USB cable | For power and programming (depends on your CYD revision) |

> **Note:** There are two variants of the ESP32-2432S028. One has an inverted display panel. If your display looks washed out or colours are wrong, toggle the `INVERT_DISPLAY` flag in the sketch (see [Configuration](#configuration)).

### BME280 Wiring

The CYD exposes its I2C bus on a dedicated connector. Wire the BME280 as follows:

| BME280 Pin | CYD Pin | Notes |
|---|---|---|
| VCC | 3.3 V | **Use 3.3 V — not 5 V** |
| GND | GND | |
| SDA | GPIO 27 | Defined as `I2C_SDA` in sketch |
| SCL | GPIO 22 | Defined as `I2C_SCL` in sketch |

```
BME280 Module          CYD Board
┌──────────┐           ┌─────────────┐
│  VCC ────┼───────────┼─ 3.3V       │
│  GND ────┼───────────┼─ GND        │
│  SDA ────┼───────────┼─ GPIO 27    │
│  SCL ────┼───────────┼─ GPIO 22    │
└──────────┘           └─────────────┘
```

> **I2C address:** Ensure your BME280's SDO pin is pulled LOW (connected to GND). This sets the I2C address to `0x76`, which the sketch expects. If your module has SDO pulled HIGH, it will appear at `0x77` and the sketch will show SENSOR ERROR. Either pull SDO LOW or change `BME280_ADDR` to `0x77` in the sketch.

---

## Flashing the Pre-compiled Binary

If you just want to run the firmware without installing Arduino IDE or any libraries, use one of the pre-compiled binaries from the `binaries/` folder.

### Which binary do I need?

The ESP32-2432S028 was produced in two hardware variants with opposite display panel polarities. Flash the wrong one and the display will look washed out or show incorrect colours.

| File | Use when |
|---|---|
| `CYD_Weather_Station_v5.15_INVERTED.bin` | Display looks correct with this — try this one first |
| `CYD_Weather_Station_v5.15_NORMAL.bin` | Use this if the INVERTED version looks wrong |

There is no risk in trying both — just reflash if the first one looks off. Neither binary will damage your hardware.

### Method 1 — ESP Web Flasher (easiest, no software to install)

Espressif provides a browser-based flash tool that works entirely in Google Chrome or Microsoft Edge — no drivers or software required.

1. Connect your CYD to your PC via USB
2. Open **[https://espressif.github.io/esptool-js/](https://espressif.github.io/esptool-js/)** in Chrome or Edge
3. Click **Connect** and select your CYD's COM port from the popup
4. Set the flash address to **`0x0`**
5. Click **Choose File** and select the `.bin` file
6. Click **Program**
7. Wait for the progress bar to complete, then press the **RST** button on your CYD

### Method 2 — ESP32 Flash Download Tool (Windows GUI)

Espressif's official Windows GUI tool is straightforward and reliable.

1. Download the **Flash Download Tool** from [https://www.espressif.com/en/support/download/other-tools](https://www.espressif.com/en/support/download/other-tools)
2. Run the tool and select **ESP32** as the chip type and **Develop** as the work mode
3. In the first row, click the **...** button and browse to your `.bin` file
4. Set the address field next to it to **`0x0`**
5. Make sure the checkbox to the left of the row is **ticked**
6. Set **COM** to your CYD's port and **BAUD** to `921600`
7. Click **START** and wait for the `FINISH` message
8. Press the **RST** button on your CYD

### Method 3 — esptool.py (command line, all platforms)

If you have Python installed, `esptool.py` is the most reliable method and works on Windows, macOS, and Linux.

**Install esptool:**
```bash
pip install esptool
```

**Flash the binary** (replace `COM3` with your actual port — on macOS/Linux it will be something like `/dev/ttyUSB0` or `/dev/tty.usbserial-0001`):

```bash
esptool.py --chip esp32 --port COM3 --baud 921600 write_flash 0x0 CYD_Weather_Station_v5.15_INVERTED.bin
```

**Full example with erase** (use this if the device behaves unexpectedly after flashing):
```bash
esptool.py --chip esp32 --port COM3 --baud 921600 erase_flash
esptool.py --chip esp32 --port COM3 --baud 921600 write_flash 0x0 CYD_Weather_Station_v5.15_INVERTED.bin
```

### After flashing

Once flashed and running, all settings (altitude, interval, unit, brightness) are configured via the serial command interface — no reflashing required. Connect a serial monitor at **115200 baud** and use the commands listed in the [Serial Commands](#serial-commands) section. The first thing you should set is your local altitude:

```
ALT=145
```

Replace `145` with your actual altitude in metres. See the [Configuration](#configuration) section for how to find this value.

---

## Software Requirements

### Arduino IDE

Arduino IDE 2.x is recommended. Arduino IDE 1.8.x will also work.

### ESP32 Board Package

In Arduino IDE, go to **File → Preferences** and add this URL to the **Additional boards manager URLs** field:

```
https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package_esp32_index.json
```

Then go to **Tools → Board → Boards Manager**, search for `esp32`, and install the **esp32 by Espressif Systems** package. **Version 3.x or later is required** — the sketch uses the core v3.x `ledcAttach()` / `ledcWrite()` API and the `esp_task_wdt_config_t` struct watchdog API, both of which are not available in core v2.x.

### Board Settings

| Setting | Value |
|---|---|
| Board | ESP32 Dev Module |
| CPU Frequency | 240 MHz |
| Flash Mode | QIO |
| Flash Size | 4 MB |
| Partition Scheme | Default 4MB with spiffs |
| Upload Speed | 921600 |
| Port | Your CYD's COM / tty port |

### Required Libraries

Install all of the following via **Tools → Manage Libraries**:

| Library | Author | Notes |
|---|---|---|
| TFT_eSPI | Bodmer | Display driver — **requires manual configuration** (see below) |
| Adafruit BME280 Library | Adafruit | Sensor driver |
| Adafruit Unified Sensor | Adafruit | Dependency of the BME280 library |

`Wire.h`, `Preferences.h`, `WiFi.h`, `math.h`, and `esp_task_wdt.h` are all included with the ESP32 Arduino core — no separate installation required.

---

## TFT_eSPI Configuration

This is the most important setup step. TFT_eSPI requires a **`User_Setup.h`** configuration file that tells it which display driver, SPI pins, and fonts to use. A wrong or missing configuration is the most common cause of a blank or scrambled display.

### Step 1 — Locate the TFT_eSPI library folder

The library is installed in your Arduino libraries directory. The typical locations are:

| OS | Path |
|---|---|
| Windows | `C:\Users\<YourName>\Documents\Arduino\libraries\TFT_eSPI\` |
| macOS | `~/Documents/Arduino/libraries/TFT_eSPI/` |
| Linux | `~/Arduino/libraries/TFT_eSPI/` |

### Step 2 — Back up the existing User_Setup.h

Before making changes, make a copy of the existing `User_Setup.h` as a backup:

```
TFT_eSPI/
├── User_Setup.h          ← edit this file
├── User_Setup.h.bak      ← make a copy first
└── User_Setup_Select.h   ← leave this file alone
```

### Step 3 — Replace User_Setup.h with the CYD configuration

Open `User_Setup.h` in any plain-text editor (Notepad, VS Code, etc.), **delete all existing content**, and paste in the following configuration exactly:

```cpp
// ─────────────────────────────────────────────────────────────────────────────
// TFT_eSPI User_Setup.h — ESP32-2432S028 ("CYD") with ILI9341 2.8" display
// ─────────────────────────────────────────────────────────────────────────────

// ── Display driver ───────────────────────────────────────────────────────────
#define ILI9341_DRIVER          // 2.8" 320x240 ILI9341 panel on the CYD

// ── Display resolution ───────────────────────────────────────────────────────
#define TFT_WIDTH  240
#define TFT_HEIGHT 320

// ── SPI pin assignments — CYD (ESP32-2432S028) ───────────────────────────────
#define TFT_MISO  12            // SPI MISO
#define TFT_MOSI  13            // SPI MOSI
#define TFT_SCLK  14            // SPI clock
#define TFT_CS    15            // Chip select
#define TFT_DC     2            // Data / Command
#define TFT_RST   -1            // Reset — tied to ESP32 RST, not a separate GPIO
#define TFT_BL    21            // Backlight — controlled via LEDC PWM in sketch

// ── Touch controller ─────────────────────────────────────────────────────────
// The CYD has an XPT2046 resistive touch controller on the same SPI bus.
// This sketch does not use touch, but the pin must be defined to avoid
// conflicts with other SPI devices.
#define TOUCH_CS  33

// ── Fonts — load only what the sketch uses ───────────────────────────────────
// Font 1  — tiny (6x8)         used for Y-axis scale labels
// Font 2  — small (16px)       used for fixed labels (ALT, HUMIDITY, etc.)
// Font 4  — medium (26px)      used for humidity % and baro values
// Font 7  — large 7-segment    used for the main temperature number
#define LOAD_GLCD               // Font 1 — built-in Adafruit GLCD font
#define LOAD_FONT2              // Font 2 — small (16px)
#define LOAD_FONT4              // Font 4 — medium (26px)
#define LOAD_FONT6              // Font 6 — large (48px) — included for completeness
#define LOAD_FONT7              // Font 7 — 7-segment style (48px) — main temp display
#define LOAD_FONT8              // Font 8 — large (75px) — included for completeness
#define LOAD_GFXFF              // FreeFont support — required by TFT_eSPI
#define SMOOTH_FONT             // Anti-aliased font rendering

// ── SPI speed ────────────────────────────────────────────────────────────────
#define SPI_FREQUENCY        55000000   // 55 MHz — maximum reliable speed for CYD
#define SPI_READ_FREQUENCY   20000000   // 20 MHz for reads
#define SPI_TOUCH_FREQUENCY   2500000   // 2.5 MHz for touch (not used in sketch)
```

Save the file and close your text editor.

### Step 4 — Verify User_Setup_Select.h is not overriding your setup

Open `User_Setup_Select.h` and confirm that only this one line is uncommented:

```cpp
#include <User_Setup.h>
```

All other `#include` lines in that file should be commented out (prefixed with `//`). If any other setup file is active it will override your `User_Setup.h`.

### Step 5 — Confirm the setup loaded correctly

After uploading the sketch, open the **Serial Monitor** at **115200 baud**. You should see a boot message like:

```
>> BOOT -- Altitude: 100.0 m | Interval: 7.5 min | Unit: Fahrenheit | Brightness: 75 %
```

If the display stays blank or shows garbage, double-check your `User_Setup.h` — specifically the pin definitions and driver selection.

---

## Configuration

### INVERT_DISPLAY Flag

Near the top of the sketch, find this line:

```cpp
#define INVERT_DISPLAY  true
```

The ESP32-2432S028 was manufactured in two hardware variants. One variant has an inverted display panel; the other does not. If your display shows washed-out colours or the background appears white instead of dark, change this value:

```cpp
#define INVERT_DISPLAY  false   // try this if colours look wrong
```

Recompile and upload after changing this flag.

### Altitude Calibration

The sketch converts raw sensor pressure to sea-level equivalent pressure using your altitude. The default is 100 m, but accurate barometric readings require calibration for your actual location.

**To find your altitude:**
1. Look up the nearest airport METAR at [aviationweather.gov](https://aviationweather.gov/metar) or a local aviation service
2. Note the altimeter setting in inHg
3. Compare it to the sketch's `NOW:` reading on the graph screen
4. Use the `ALT=` serial command to fine-tune until they match

For reference, Angeles City, Philippines (Clark International Airport) is approximately **145 m** above sea level. The BME280 has a factory tolerance of ±0.03 inHg, so a small residual offset is normal.

---

## Serial Commands

Connect a serial monitor at **115200 baud** with **newline line ending**. All commands are case-insensitive.

| Command | Example | Description |
|---|---|---|
| `ALT=` | `ALT=145` | Set altitude in metres (−500 to 8849). Saved to NVS. Affects sea-level pressure conversion. |
| `INT=` | `INT=15` | Set graph slot interval in minutes (1–480). Saved to NVS. Resets history immediately. |
| `UNIT=` | `UNIT=C` | Set temperature unit — `C` for Celsius, `F` for Fahrenheit. Saved to NVS. |
| `BRT=` | `BRT=60` | Set backlight brightness 0–100 %. Saved to NVS. Values below 10 % are reset to 75 % on next boot. |
| `STATUS` | `STATUS` | Print all current settings and live sensor readings to serial. |

After each command the sketch shows a confirmation overlay on the display for 2 seconds and prints a confirmation to serial.

**STATUS output example:**

```
── STATUS ──────────────────────────────────────────
  Altitude   : 145.0 m
  Interval   : 15 min  (24-hr window)
  Unit       : Celsius
  Brightness : 75 %
  Buffer     : FILLING  (12 / 96 slots)
  Temp       : 28.4 C
  Humidity   : 71.0 %
  Pressure   : 29.92 inHg
────────────────────────────────────────────────────
```

### Graph Interval and Window Size

The graph stores 96 pressure slots. The total history window is `interval × 96`:

| Interval | Window |
|---|---|
| 7 min (default ~7.5 min) | ~12 hours |
| 15 min | 24 hours |
| 30 min | 48 hours |
| 60 min | 4 days |
| 480 min (maximum) | 32 days |

---

## Comfort Level Reference

Comfort level is derived from the **dew point** temperature, which is a more accurate measure of perceived humidity than relative humidity alone.

| Display | Dew Point | Colour | Description |
|---|---|---|---|
| DRY | < 10 °C | Cyan | Cool and fresh |
| COMFORTABLE | 10 – 15.5 °C | Green | Pleasant |
| HUMID | 15.6 – 21 °C | Orange | Warm and sticky |
| OPPRESSIVE | > 21 °C | Red | Hot and uncomfortable |

---

## Troubleshooting

### Display is blank after uploading

**Most likely cause:** The NVS stored a brightness value of 0 % from a previous `BRT=0` command. On first boot with working backlight dimming, the display will be completely dark.

**Fix:** Open the serial monitor at 115200 baud and send `BRT=75`. The display should immediately come on. A boot safeguard prevents values below 10 % from being applied on subsequent reboots.

If the display is still blank after sending `BRT=75`, the issue is likely the `User_Setup.h` configuration. Recheck the pin definitions, especially `TFT_CS`, `TFT_DC`, and the driver selection (`ILI9341_DRIVER`).

### Display shows scrambled colours or a white screen

Toggle the `INVERT_DISPLAY` flag in the sketch between `true` and `false`, then recompile and upload. The two hardware variants of the CYD require opposite settings.

### SENSOR ERROR on boot

The sketch will attempt to reinitialise the BME280 up to 5 times, showing `Retrying... (X/5)` on screen. If all 5 attempts fail, it shows `RESTARTING...` and performs a clean reboot.

Common causes:
- **Wrong I2C address** — confirm your BME280's SDO pin state. SDO LOW = address `0x76`, SDO HIGH = address `0x77`. The sketch uses `0x76`.
- **Wiring error** — double-check SDA to GPIO 27 and SCL to GPIO 22.
- **Power issue** — ensure the BME280 is connected to 3.3 V, not 5 V.
- **Pull-up resistors** — most BME280 breakout modules include on-board pull-ups. If yours does not, add 4.7 kΩ pull-up resistors from SDA and SCL to 3.3 V.

### Brightness control not working (stuck at full brightness)

This is caused by `ledcAttach()` being called before `tft.init()`. TFT_eSPI reclaims the backlight GPIO during `tft.init()`, overriding any LEDC setup done earlier. The sketch already handles this correctly by calling `ledcAttach()` after the full TFT init block. If you have modified the sketch and moved the LEDC calls, restore them to their position after `tft.fillScreen()`.

### Pressure readings seem incorrect

Use the `ALT=` command to calibrate your altitude. Compare the sketch's `NOW:` reading (visible on the graph screen) against the altimeter setting from a nearby airport METAR. Adjust `ALT=` up or down until they match. A residual difference of ±0.03 inHg is within the BME280's factory tolerance.

### Temperature reads slightly high

The BME280 is a self-heating sensor — it generates a small amount of heat during operation that can bias temperature readings 1–3 °C above ambient. This is a hardware characteristic of the BME280 and cannot be fully corrected in software. To minimise self-heating, ensure the BME280 module is mounted with airflow around it rather than enclosed in a tight case.

### Graph shows flat line or no data

The graph requires at least 2 committed pressure slots before it will draw a line. On first boot the display shows `COLLECTING DATA...` until the second slot is committed. At the default interval of ~7.5 minutes, this takes about 15 minutes. Send `INT=1` to use a 1-minute interval for testing, then restore your preferred interval with `INT=15` (or whichever you prefer).

---

## Project Structure

```
CYD-Weather-Station/
├── CYD_Weather_Station_ver5_15.ino   ← main sketch
├── README.md                          ← this file
└── images/
    ├── dashboard.jpg                  ← add your own photo
    └── graph.jpg                      ← add your own photo
```

---

## Technical Notes

### Anti-Flicker Architecture

The display uses a **static / dynamic layer split** to minimise SPI traffic and eliminate flicker. Static elements (panel fills, borders, fixed labels like "HUMIDITY" and "BARO inHg") are drawn only when a change has occurred — on mode switch, after a serial command, or after the confirmation overlay clears. Dynamic elements (sensor values, breathing dot, trend arrow) are drawn every second using targeted `fillRect()` erase regions rather than full-screen redraws.

### Pressure History

Pressure readings are averaged into slots using a double-precision moving accumulator before being committed to a 96-element circular buffer. This prevents floating-point precision loss at high sample counts. The graph plots the entire committed history chronologically from oldest to newest.

### Memory Design

The sketch makes zero heap `String` allocations during normal display operation. All text formatting uses `snprintf()` into stack-allocated `char` buffers. The `getComfortLevel()` function returns a `const char*` pointer directly to a flash-resident string literal — no heap copy is made. This design eliminates cumulative heap fragmentation over long runtimes.

---

## License

MIT License

Copyright (c) 2026 Paul Miller

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

---

## Author

**Paul Miller**

Built and refined iteratively through 15 versions, with a focus on display stability, memory efficiency, and long-term reliability on the ESP32-2432S028 hardware.
