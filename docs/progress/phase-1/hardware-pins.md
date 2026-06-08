## Current hardware setup — Phase 1

This is the **current breadboard prototype** used for Phase 1, built around an **ESP32-C3 SuperMini** as the main node.

> **Prototype note:** this wiring is part of an ongoing verification phase. If you want to reuse it, **verify all connections before powering the board**, because the setup may change during testing and refinement.

### Sensors

#### BMP280
- **Model:** BMP280
- **Function:** temperature and atmospheric pressure
- **Interface:** I2C
- **I2C address:** `0x76` or `0x77`

**Wiring**
- `3.3V` → `VCC`
- `GND` → `GND`
- `GPIO6` → `SCL`
- `GPIO7` → `SDA`

**Library used**
- `Adafruit_BMP280`

### Displays

#### 0.96" I2C OLED
- **Model:** SSD1306-based OLED
- **Interface:** I2C
- **Resolution:** `128x64`

**Wiring**
- `3.3V` → `VCC`
- `GND` → `GND`
- `GPIO6` → `SCL`
- `GPIO7` → `SDA`

**Library used**
- `Adafruit_SSD1306`

#### 1.14" SPI TFT
- **Model:** ST7789-based TFT
- **Interface:** SPI
- **Resolution used in code:** `135x240`

**Wiring**
- `3.3V` → `VCC`
- `GND` → `GND`
- `GPIO2` → `SCK`
- `GPIO3` → `MOSI`
- `GPIO4` → `RES`
- `GPIO5` → `DC`
- `GPIO10` → `CS`
- `GPIO1` → `BLK`

**Library used**
- `Adafruit_ST7789`

### Firmware notes

The current firmware automatically detects the OLED first, and if it is not found it initializes the TFT instead. The display subsystem is designed so that the same node logic can work with either screen type.

### Current status

This hardware configuration represents the **current Phase 1 breadboard prototype** of the project, based on the **ESP32-C3 SuperMini**. It is a verification setup, not a final wiring standard, and it should always be checked and confirmed before actual use.
