# ESP32 CYD Weather Station (v5.14)

A fast, lightweight, and reliable weather station built for the **ESP32-2432S028 (CYD display)**. Monitor real-time environmental data with an intuitive dashboard and historical graphing. Optimized for embedded systems with long-term stability in mind.

---

## 📋 Table of Contents

- [Features](#-features)
- [Hardware Requirements](#-hardware-required)
- [Software & Libraries](#-libraries-required)
- [Setup & Configuration](#-tftspi-configuration-important)
- [Installation](#-installation)
- [Usage & Display Modes](#-display-modes)
- [Technical Details](#-technical-details)
- [Troubleshooting](#-troubleshooting)
- [Future Roadmap](#-future-improvements)
- [License & Credits](#-license)

---

## 🌤️ Features

### Real-Time Monitoring
- **Temperature** – Precise thermal readings
- **Humidity** – Relative moisture levels
- **Pressure** – Atmospheric pressure tracking
- **Dew Point** – Calculated humidity comfort indicator

### Display & UI
- **Dual Display Modes:**
  - Dashboard view for live sensor data
  - Graph view for historical trends and analysis
- Fast and smooth updates with minimal latency
- Comfortable readability with clean, organized layout

### Performance & Stability
Engineered for reliable, long-term operation:
- No dynamic `String` allocations (prevents memory fragmentation)
- Fixed-size buffers using `snprintf` (predictable memory usage)
- NaN sensor protection (graceful handling of bad readings)
- Optimized SPI communication for minimal overhead

---

## ⚡ Hardware Required

| Component | Details |
|-----------|---------|
| **ESP32 Board** | ESP32-2432S028 (CYD display variant) |
| **Sensor** | BME280 environmental sensor (I2C connection) |
| **Display** | 2.8" ILI9341 TFT LCD (integrated with CYD) |

**Connection:**
- BME280 I2C: Connect SDA to GPIO21, SCL to GPIO22 (standard ESP32 I2C pins)

---

## 📦 Libraries Required

Install the following libraries via **Arduino IDE → Sketch → Include Library → Manage Libraries:**

| Library | Purpose | Version |
|---------|---------|---------|
| **TFT_eSPI** | Display driver (with custom CYD configuration) | Latest |
| **Adafruit BME280** | Environmental sensor driver | Latest |
| **Adafruit Unified Sensor** | Sensor abstraction layer | Latest |

**Arduino IDE Version:** 1.8.x or 2.x recommended

---

## 🖥️ TFT_eSPI Configuration (IMPORTANT)

⚠️ **This project requires custom TFT_eSPI setup for proper CYD display functionality.**

### 📁 Option 1: Recommended (Use Included File)

1. **Copy** the provided configuration file:
   ```
   config/User_Setup_CYD.h
   ```

2. **Paste it** into your TFT_eSPI library installation:
   - Windows: `Documents\Arduino\libraries\TFT_eSPI\`
   - Linux: `~/Arduino/libraries/TFT_eSPI/`
   - macOS: `~/Documents/Arduino/libraries/TFT_eSPI/`

3. **Edit** the file:
   ```
   TFT_eSPI/User_Setup_Select.h
   ```

4. **Add this line** near the top (uncomment or add if missing):
   ```cpp
   #include <User_Setup_CYD.h>
   ```

5. **Save and close** the file.

### 📁 Option 2: Manual Setup (Alternative)

If the included file is not available, create the file manually:

1. **Create** a new file in TFT_eSPI:
   ```
   TFT_eSPI/User_Setup_CYD.h
   ```

2. **Copy and paste** the configuration:
   ```cpp
   // User Setup for CYD (ESP32-2432S028)
   #define USER_SETUP_INFO "CYD ESP32-2432S028"
   
   // Display Driver
   #define ILI9341_DRIVER
   
   // Display Resolution
   #define TFT_WIDTH  240
   #define TFT_HEIGHT 320
   
   // SPI Pin Definitions
   #define TFT_MISO 12
   #define TFT_MOSI 13
   #define TFT_SCLK 14
   #define TFT_CS   15
   #define TFT_DC    2
   #define TFT_RST   4
   
   // Backlight Control
   #define TFT_BL   21
   #define TFT_BACKLIGHT_ON HIGH
   
   // SPI Speed (40 MHz is optimal for CYD)
   #define SPI_FREQUENCY 40000000
   
   // Font Loading
   #define LOAD_GLCD
   #define LOAD_FONT2
   #define LOAD_FONT4
   #define LOAD_FONT6
   #define LOAD_FONT7
   #define LOAD_FONT8
   #define LOAD_GFXFF
   
   // Enhanced Rendering
   #define SMOOTH_FONT
   ```

3. **Save** and include it in `User_Setup_Select.h` (see Option 1, step 4).

### 🧪 Verify Setup

Before uploading the weather station code:

1. **Open** the TFT_eSPI example:
   ```
   File → Examples → TFT_eSPI → Read_User_Setup
   ```

2. **Upload** it to your ESP32 and check the **Serial Monitor** (Tools → Serial Monitor, 115200 baud).

3. **Confirm** the output shows:
   ```
   CYD ESP32-2432S028
   Display width = 240
   Display height = 320
   ILI9341 driver loaded
   ```

If setup is incorrect, the display will not function properly.

---

## 🔧 Installation

### Prerequisites
- Arduino IDE (1.8.x or 2.x)
- USB cable for ESP32 programming
- ESP32 board support installed in Arduino IDE
- All required libraries installed (see [Libraries Required](#-libraries-required))
- TFT_eSPI configured (see [TFT_eSPI Configuration](#-tftspi-configuration-important))

### Steps

1. **Clone or download** this repository:
   ```bash
   git clone https://github.com/Teachdude/ESP32-CYD-Weather-Project.git
   ```

2. **Open** the main sketch in Arduino IDE:
   ```
   File → Open → CYD_Weather_Station/CYD_Weather_Station_ver5_14.ino
   ```

3. **Select the board:**
   - Go to: `Tools → Board → ESP32 → ESP32 Dev Module`

4. **Select the COM port:**
   - Go to: `Tools → Port → [Your ESP32 COM Port]`
   - (On Linux/Mac, typically `/dev/ttyUSB0` or `/dev/cu.usbserial-*`)

5. **Verify the sketch:**
   - Click: `Sketch → Verify/Compile` (Ctrl+R / Cmd+R)
   - Wait for the "Compilation complete" message.

6. **Upload to ESP32:**
   - Click: `Sketch → Upload` (Ctrl+U / Cmd+U)
   - Wait for the "Upload complete" message.

7. **Monitor output** (optional):
   - Open: `Tools → Serial Monitor` (115200 baud)
   - Observe sensor readings and debug info.

---

## 🎨 Screenshots

### Dashboard View
![Dashboard](images/dashboard.jpg)

**Shows:**
- Current temperature, humidity, pressure, and dew point
- Real-time sensor values with comfort indicator
- Minimal, clean layout optimized for quick reading

### Graph View
![Graph](images/graph.jpg)

**Shows:**
- Historical sensor data over time
- Min/Max values for selected timeframe
- Smooth scrolling for trend analysis

---

## 🖥️ Display Modes

### Dashboard View
Displays live sensor readings at a glance.

| Element | Purpose |
|---------|---------|
| **Temperature** | Current environmental temperature |
| **Humidity** | Relative humidity percentage |
| **Pressure** | Atmospheric pressure in hPa |
| **Dew Point** | Calculated moisture comfort level |
| **Comfort Indicator** | Visual comfort assessment |

**Switch to Graph:** Press button on CYD or send command via serial.

### Graph View
Visualizes historical trends over time.

| Feature | Details |
|---------|---------|
| **Rolling Buffer** | Stores last ~100 readings (configurable) |
| **Min/Max Tracking** | Shows high and low values in timeframe |
| **Smooth Updates** | Graph scrolls smoothly as new data arrives |
| **Multiple Graphs** | View individual sensor trends separately |

**Switch to Dashboard:** Press button on CYD or send command via serial.

---

## 📈 Data Handling

### Rolling Buffer System
- Stores recent sensor readings in a circular buffer
- Prevents memory overflow and fragmentation
- Automatically discards oldest data when buffer is full

### Data Validation
- **NaN Protection:** Invalid sensor reads are filtered out
- **Range Checking:** Values outside realistic ranges are rejected
- **Smoothing:** Optional moving average for noisy sensors

### Memory Efficiency
- Fixed buffer size (no dynamic allocation)
- Typically uses <50KB RAM for all data
- Suitable for long-term operation without memory leaks

---

## 🌡️ Temperature Units

The project supports both metric and imperial units:

| Unit | Symbol | Conversion |
|------|--------|-----------|
| **Celsius** | °C | Native (from BME280) |
| **Fahrenheit** | °F | C × 1.8 + 32 |

Change units in code:
```cpp
// In CYD_Weather_Station_ver5_14.ino
#define USE_FAHRENHEIT false  // Set to true for °F
```

**Note:** Conversion is computed on-the-fly without extra memory usage.

---

## 🚀 Future Improvements

Planned enhancements are tracked below. Contributions welcome!

### High Priority
- [ ] **WiFi Connectivity** – Send data to cloud via MQTT or HTTP
  - Real-time data logging to InfluxDB or similar
  - Remote access to historical data
  
- [ ] **SD Card Logging** – Log data to microSD for long-term archival
  - CSV export for analysis in Excel/Python
  - Offline data retention

### Medium Priority
- [ ] **Touchscreen UI** – Enhanced interaction with CYD's capacitive touch
  - Swipe between modes
  - Settings menu for calibration
  
- [ ] **Multiple Sensor Support** – Add additional environmental sensors
  - CO2, air quality (MQ-135)
  - Light level (BH1750)
  - Outdoor sensor via wireless link

### Low Priority
- [ ] **Web Dashboard** – Browser-based remote monitoring
- [ ] **Mobile App** – Native Android/iOS companion app
- [ ] **Voice Integration** – Alexa/Google Home support

---

## ⚙️ Technical Details

### Performance Characteristics
- **Sensor Update Rate:** ~1 reading per second
- **Display Refresh:** ~10 FPS for smooth animation
- **Memory Usage:** ~45KB heap (stable, non-growing)
- **Power Consumption:** ~2-3W average (WiFi disabled)

### Code Highlights
```cpp
// Example: Fixed buffer instead of dynamic String
char buffer[32];
snprintf(buffer, sizeof(buffer), "Temp: %.1f°C", temperature);
// vs. String temp = "Temp: " + String(temperature) + "°C";
```

### I2C Communication
- **BME280 Address:** 0x76 (default) or 0x77 (alternate)
- **Speed:** 400 kHz standard mode
- **Read Cycle:** Non-blocking; sensor data cached for display refresh

---

## 🐛 Troubleshooting

### Display Shows Nothing
**Symptom:** Black screen on startup

**Solutions:**
1. Verify TFT_eSPI configuration with `Read_User_Setup` example
2. Check USB power (CYD needs stable 5V supply)
3. Verify backlight pin (GPIO21) is powered
4. Check SPI pins (GPIO 12–15) are correctly wired
5. Try uploading the `TFT_eSPI → Basic Example` to isolate issue

---

### Sensor Not Detected
**Symptom:** "BME280 not found" in Serial Monitor

**Solutions:**
1. Check I2C wiring (SDA → GPIO21, SCL → GPIO22)
2. Verify BME280 address: Run `Wire Scanner` example
3. Ensure `Adafruit BME280` and `Adafruit Unified Sensor` libraries are installed
4. Check for loose connections; try wiggling wires gently
5. Test with a multimeter (check 3.3V on sensor power pin)

---

### Upload Fails
**Symptom:** "Failed to connect to ESP32" or timeout

**Solutions:**
1. **Reset the board:** Press the RESET button while uploading
2. **Check COM port:** Verify correct port in `Tools → Port`
3. **Install drivers:** Download CH340 or FTDI drivers for your OS
4. **Try bootloader mode:** Hold BOOT button while plugging in USB
5. **Try a different USB cable:** Some cables don't support data transfer

---

### Memory Leak or Crashes After Hours
**Symptom:** Program crashes or becomes unresponsive over time

**Solutions:**
1. This project avoids dynamic `String` to prevent leaks
2. Ensure no external WiFi code is enabled (add later)
3. Check buffer overflow in graph data (increase `MAX_DATA_POINTS`)
4. Monitor Serial output for repeated errors
5. Try the `ESP32: Restart` command in Arduino IDE

---

## 📄 License

This project is **free to use, modify, and redistribute** under the MIT License.

Feel free to:
- Use in personal or commercial projects
- Modify the code for your needs
- Share improvements with the community

Attribution appreciated but not required.

---

## 🙌 Credits

Created as an IoT hobby project with focus on:

- **ESP32 Performance Optimization** – Minimal heap fragmentation, non-blocking I/O
- **Embedded UI Design** – Clean, responsive display interface
- **Reliable Long-Term Operation** – Engineered for 24/7 stability

### Resources & Inspirations
- [TFT_eSPI Library](https://github.com/Bodmer/TFT_eSPI) by Bodmer
- [Adafruit BME280](https://github.com/adafruit/Adafruit_BME280_Library) sensor library
- [ESP32 Community](https://github.com/espressif/esp-idf) and forums

---

**Last Updated:** April 4, 2026  
**Version:** 5.14