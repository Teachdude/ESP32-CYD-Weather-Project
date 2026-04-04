# ESP32 CYD Weather Station (v5.14)

A lightweight, high-performance weather station built for the **ESP32-2432S028 (CYD display)**.
This project focuses on clean UI, smooth graphing, and reliable operation while teaching beginners how to configure the TFT_eSPI library for custom ESP32 displays.

---

## 📸 Screenshots

*(Add your images here after uploading them to `images/`)*

### Dashboard View

![Dashboard](images/dashboard.jpg)

### Graph View

![Graph](images/graph.jpg)

### Hardware Setup

![Setup](images/setup.jpg)

---

## 🌤️ Features

* Real-time readings:

  * Temperature
  * Humidity
  * Pressure
  * Dew Point
* Comfort level indicator
* Dual display modes:

  * Dashboard view
  * Graph view
* Optimized for long-term stability:

  * No dynamic `String` usage
  * Fixed buffers (`snprintf`)
  * NaN sensor protection
* Configurable TFT_eSPI setup for CYD displays

---

## ⚡ Hardware Required

* ESP32-2432S028 (CYD display)
* BME280 environmental sensor (I2C)
* Jumper wires

---

## 📦 Libraries Required

Install via Arduino IDE Library Manager:

* **TFT_eSPI**
* **Adafruit BME280**
* **Adafruit Unified Sensor**

---

## 🖥️ TFT_eSPI Configuration (Critical)

This project **requires creating a custom configuration file** for TFT_eSPI to work with the CYD display.

---

### Step 1: Locate the TFT_eSPI Library

1. Open Arduino IDE
2. Go to **Sketch → Include Library → Manage Libraries**
3. Search for **TFT_eSPI** and install it if you haven’t
4. Navigate to your Arduino libraries folder:

   ```
   Arduino/libraries/TFT_eSPI/
   ```

---

### Step 2: Create a Custom Config File

1. Inside the TFT_eSPI library folder, create a new file called:

```text
User_Setup_CYD.h
```

2. Paste this configuration:

```cpp
// USER SETUP FOR ESP32-2432S028 (CYD DISPLAY)
#define USER_SETUP_INFO "CYD ESP32-2432S028"

// Display driver
#define ILI9341_DRIVER

// Resolution
#define TFT_WIDTH  240
#define TFT_HEIGHT 320

// SPI Pins
#define TFT_MISO 12
#define TFT_MOSI 13
#define TFT_SCLK 14
#define TFT_CS   15
#define TFT_DC    2
#define TFT_RST  4   // Or -1 if tied to ESP32 reset

// Backlight
#define TFT_BL   21
#define TFT_BACKLIGHT_ON HIGH

// SPI frequency
#define SPI_FREQUENCY  40000000
#define SPI_READ_FREQUENCY 20000000

// Fonts
#define LOAD_GLCD
#define LOAD_FONT2
#define LOAD_FONT4
#define LOAD_FONT6
#define LOAD_FONT7
#define LOAD_FONT8
#define LOAD_GFXFF
#define SMOOTH_FONT
```

> **Tip:** Make sure the pin numbers match your specific CYD board.

---

### Step 3: Select Your Custom Config

1. Open `User_Setup_Select.h` inside the TFT_eSPI folder
2. Comment out other includes and add:

```cpp
#include <User_Setup_CYD.h>
```

3. Save the file

---

### Step 4: Verify Your Configuration

1. Open Arduino IDE
2. Go to **File → Examples → TFT_eSPI → Read_User_Setup**
3. Upload it to your ESP32
4. Open Serial Monitor and verify:

   * Correct display driver detected
   * Pins and resolution match your CYD display

If the values are correct, your display is ready.

---

## 🔧 Wiring Diagram

* **BME280 → ESP32 CYD Display (I2C)**

  * VCC → 3.3V
  * GND → GND
  * SCL → GPIO 22
  * SDA → GPIO 21

*(Upload your own wiring image here: `images/setup.jpg`)*

---

## 📁 Folder Structure Recommendation

Organize your GitHub repository for clarity:

```text
/ (root)
 ├── README.md
 ├── images/
 │   ├── dashboard.jpg
 │   ├── graph.jpg
 │   └── setup.jpg
 ├── config/
 │   └── User_Setup_CYD.h
 └── CYD_Weather_Station/
     └── CYD_Weather_Station_ver5_14.ino
```

---

## 🖥️ Running the Sketch

1. Open `CYD_Weather_Station_ver5_14.ino` in Arduino IDE
2. Select board:

   ```
   ESP32 Dev Module
   ```
3. Select correct port
4. Upload to your ESP32

---

## 📈 Display Modes

* **Dashboard View:** live sensor readings, comfort indicator
* **Graph View:** historical trends, min/max tracking

---

## 🔄 Updating Config

If you move to a new display or board:

1. Copy `User_Setup_CYD.h`
2. Adjust pins and driver
3. Update `User_Setup_Select.h`

---

## 🧠 Tips for Beginners

* Always back up your `User_Setup_CYD.h` before modifying
* Use descriptive names for screenshots and config files
* If your display shows white or blank screen:

  * Check `TFT_CS` and `TFT_DC` pins
  * Check SPI connections
  * Verify backlight pin

---

## 📄 License

Free to use, modify, and share.

---

## 🙌 Credits

Created as a hobby project focused on:

* ESP32 performance optimization
* TFT display UI design
* Reliable long-term operation

---

This README now **teaches someone from scratch** how to:

* Install libraries
* Create a custom TFT_eSPI config file
* Connect hardware
* Run and verify the sketch

---

