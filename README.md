# ESP32 CYD Weather Station (v5.14)

A lightweight, optimized weather station sketch for the **ESP32-2432S028 (CYD display)**.
This project reads environmental data and displays it in a smooth, real-time dashboard with graphing support.

---

## 🌤️ Features

* Real-time display of:

  * Temperature
  * Humidity
  * Pressure
  * Dew Point
  * Comfort level indicator
* Historical graphing (multiple data points stored and displayed)
* Dashboard + graph UI modes
* Optimized for **low memory usage**

  * Eliminates dynamic `String` allocations
  * Uses `snprintf()` and static buffers
* Stable sensor initialization with NaN handling
* Fast display updates (multiple refreshes per second)

---

## ⚡ Hardware Used

* ESP32-2432S028 (CYD display)
* BME280 environmental sensor (I2C)

---

## 📦 Libraries Required

Install these via Arduino IDE Library Manager:

* Adafruit BME280
* Adafruit Unified Sensor
* TFT_eSPI (configured for CYD display)

---

## 🔧 Setup

1. Connect the BME280 via I2C:

   * SDA → ESP32 SDA
   * SCL → ESP32 SCL

2. Configure `TFT_eSPI` for your CYD display (if not already set up)

3. Open the `.ino` file in Arduino IDE

4. Select board:

   ```
   ESP32 Dev Module
   ```

5. Upload to your device

---

## 🖥️ Display Modes

### Dashboard View

* Live readings
* Comfort level indicator
* Clean, minimal UI

### Graph View

* Historical trends
* Min/Max tracking
* Smooth updates

---

## 🧠 Optimization Highlights (v5.14)

This version focuses heavily on performance and memory stability:

* **Zero heap fragmentation during runtime**
* Replaced all `String` usage in display code
* Faster parsing for commands (no dynamic allocation)
* Improved reliability on startup (NaN sensor protection)

These changes make the sketch ideal for **long-term uptime projects**.

---

## 🌡️ Units

The sketch supports switching between:

* Celsius (°C)
* Fahrenheit (°F)

Handled internally with efficient parsing (no memory overhead).

---

## 📈 Data Handling

* Maintains a rolling buffer of historical readings
* Used for graph rendering and min/max calculations
* Designed to avoid corruption from invalid sensor values

---

## 🚀 Future Improvements (Ideas)

* WiFi weather upload (MQTT / HTTP)
* SD card logging
* Touch UI enhancements
* Multiple sensor support

---

## 📄 License

Feel free to use, modify, and share this project.

---

## 🙌 Credits

Created as a hobby project to explore ESP32 performance, UI design, and efficient embedded coding.

---
