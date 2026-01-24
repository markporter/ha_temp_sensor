# ESP Home Environmental Sensor - Design Document

## Project Overview

This project creates a battery-powered environmental sensor using ESP Home for integration with Home Assistant. The reference build uses a BME680 sensor (temperature, humidity, pressure and gas resistance) but a lower-cost DHT22 (temperature & humidity) variant is also supported; data is transmitted via WiFi to your home automation system.

## Hardware Components

### Core Components
- **Wemos D1 Mini (ESP8266)** - Main microcontroller with WiFi capability
- **BME680 Sensor** - Environmental sensor for temperature, humidity, pressure, and gas
- **DHT22 (AM2302)** - Low-cost temperature & humidity sensor (alternative to BME680)
- **18650 Li-ion Battery** - Direct connection for portable power
- **18650 Battery Holder** - Secure mounting and easy replacement

## Technical Specifications

### Wemos D1 Mini Specifications
- **MCU**: ESP8266EX
- **Operating Voltage**: 3.3V
- **Input Voltage**: 5-12V (via USB/VIN) or 3.3V (direct)
- **Digital I/O Pins**: 11
- **Analog Input**: 1 (A0)
- **WiFi**: 802.11 b/g/n 2.4GHz
- **Flash Memory**: 4MB

### BME680 Sensor Specifications
- **Temperature Range**: -40 to +85°C (±1°C accuracy)
- **Humidity Range**: 0-100% RH (±3% accuracy)
- **Pressure Range**: 300-1100 hPa (±1 hPa accuracy)
- **Gas Sensor**: VOC detection
- **Interface**: I2C or SPI (using I2C in this project)
- **Operating Voltage**: 3.3V
- **Current Consumption**: 2.1µA @ 1Hz sampling

### DHT22 Sensor Specifications
- **Model**: DHT22 (AM2302) — single-wire digital temperature & humidity sensor
- **Temperature Range**: -40 to +80°C
- **Humidity Range**: 0-100% RH
- **Resolution**: 0.1°C (temperature), 0.1% RH (humidity)
- **Typical Accuracy**: ±0.5°C (temp, typical), ±2–5% RH (humidity, typical) — see datasheet for guaranteed limits
- **Interface**: Single-wire digital (timed pulse protocol) — requires a single GPIO and a pull-up resistor
- **Operating Voltage**: 3.3–5.5V (3.3V compatible on ESP boards)
- **Recommended Pull-up**: 4.7kΩ (typical for 3.3V systems)
- **Sampling Rate**: ~0.5 Hz max (allow ≥2 seconds between readings); for battery builds use much longer intervals (15s+)
- **Typical Current**: 1.5–2.5 mA (during measurement), ~50–60 µA (quiescent/standby)
- **Notes**: Slower and less feature-rich than BME680 (no pressure/gas). Good low-cost option for temperature/humidity only.
- **Reference**: Consult the manufacturer's datasheet for guaranteed tolerances and timing requirements.

### Power Consumption Analysis

#### Active Mode (WiFi transmission)
- ESP8266: ~80-170mA
- BME680: ~12mA (during measurement)
- DHT22: ~1.5–2.5mA (during measurement, typical)
- **Total Active**: ~200mA peak

#### Deep Sleep Mode
- ESP8266: ~20µA
- BME680: ~0.15µA (standby)
- DHT22: ~50–60µA (quiescent, typical)
- **Total Sleep**: ~20µA

#### Battery Life Estimation (2500mAh 18650)
- **Measurement every 15 minutes**: 
  - Active time: 10 seconds every 15 minutes
  - Sleep time: 14 minutes 50 seconds
  - **Estimated battery life**: 6-12 months

## Pin Connections

### Wemos D1 Mini to BME680 (I2C)
| Wemos D1 Pin | BME680 Pin | Function |
|--------------|------------|----------|
| 3.3V         | VCC        | Power    |
| G (GND)      | GND        | Ground   |
| D2 (GPIO4)   | SDA        | I2C Data |
| D1 (GPIO5)   | SCL        | I2C Clock|

### Wemos D1 Mini to DHT22 (Digital)
| Wemos D1 Pin | DHT22 Pin | Function |
|--------------|-----------|----------|
| 3.3V         | VCC       | Power (use 3.3V for best compatibility)
| G (GND)      | GND       | Ground   |
| D5 (GPIO14)  | Data      | Single-wire data (use 4.7kΩ pull-up to 3.3V)

### Deep Sleep Wake Connection (CRITICAL)
| Wemos D1 Pin | Wemos D1 Pin | Function |
|--------------|------------|----------|
| D0 (GPIO16)  | RST        | Deep Sleep Wake |

**⚠️ IMPORTANT**: The D0 to RST connection is absolutely required for deep sleep to function on ESP8266. Without this connection, the device will enter deep sleep but never wake up.

### Power Connections
| Component | Connection | Notes |
|-----------|------------|-------|
| 18650 Battery (+) | Wemos VIN | Direct battery connection |
| 18650 Battery (-) | Wemos GND | Common ground |

## Documentation

For detailed setup and configuration information, see:
- **[Wiring Diagram](wiring-diagram.md)** - Complete visual wiring diagram with Mermaid charts
- **[Assembly Guide](assembly-guide.md)** - Step-by-step assembly and testing instructions  
- **[Deep Sleep Configuration](deep-sleep-guide.md)** - Configurable power management modes

## Power Management Strategy

### Deep Sleep Implementation
- **Sleep Duration**: 15 minutes between readings (configurable)
- **Wake Reasons**: Timer-based wake from deep sleep
- **Boot Process**: Quick sensor reading → WiFi connect → transmit → sleep

#### Wired vs. Battery (deep-sleep) modes
- **Battery mode (recommended)**: Enable deep sleep in the ESPHome config (for this repo set `deep_sleep_enabled: "true"` or include the `deep_sleep:` block). Device wakes, measures, reports, then sleeps — maximizes battery life. Typical recommended sampling: **15 minutes** (default) — increase to 30–60 minutes for longer life.
- **Wired mode**: Disable deep sleep (`deep_sleep_enabled: "false"` or remove the `deep_sleep:` block). Device remains awake and may sample much more frequently (1–60s) depending on your use case and network load.
- **How to switch**: toggle the `deep_sleep_enabled` substitution (see `esphome_code/*.yaml`) or comment/uncomment the `deep_sleep:` section in the device YAML.

> Tip: For production battery builds ensure the D0 (GPIO16) → RST jumper is installed; otherwise enable/disable deep sleep only for testing when device is powered by USB.

### Battery Monitoring
- **Method**: ADC reading of battery voltage
- **Frequency**: With each sensor reading
- **Low Battery Threshold**: 3.2V (configurable)
- **Battery Level Calculation**: Based on Li-ion discharge curve

## ESP Home Integration Features

### Sensor Entities
- **Temperature** (°C/°F)
- **Humidity** (%RH)
- **Pressure** (hPa/inHg)
- **Gas Resistance** (Ω) - Air quality indicator
- **Battery Level** (%)
- **Battery Voltage** (V)
- **WiFi Signal Strength** (dBm)

### Device Classes
- Temperature: `temperature`
- Humidity: `humidity`
- Pressure: `pressure`
- Battery: `battery`
- Signal Strength: `signal_strength`

### Update Intervals
- **Battery (deep-sleep)**: Default **15 minutes** between full wake→measure→transmit cycles (configurable). Recommended range: **15–60 minutes** for long-life battery operation.
- **Wired (no deep-sleep)**: Device can sample much more frequently — typical range **1–60s** depending on need and network load.
- **Sensor-specific guidance**:
  - **DHT22**: hardware minimum ~2s between readings (use ≥2s; for stability and battery life use ≥15s when wired, ≥15 minutes in deep-sleep battery mode).
  - **BME680**: temperature/pressure/humidity can be polled faster, but the **gas** sensor benefits from longer intervals and averaging — recommend **≥60s** for meaningful gas readings and **≥15 minutes** for battery gas sampling.
- **Battery status & WiFi diagnostics**: Align with sensor readings in deep-sleep mode to avoid extra wake cycles (commonly every 15 minutes).

## Enclosure Considerations

### Requirements
- **IP Rating**: IP65 or higher for outdoor use
- **Ventilation**: Small holes for pressure/humidity accuracy
- **Size**: Accommodate Wemos D1, BME680, and battery
- **Access**: Easy battery replacement

### Mounting Options
- Wall mount with screws
- Magnetic mount for metal surfaces
- Adhesive mount for smooth surfaces
- Pole/post mounting bracket

## Assembly Notes

1. **Deep Sleep Connection**: **CRITICAL** - D0 (GPIO16) must be connected to RST pin for deep sleep functionality
2. **Soldering**: Direct wire connections or use of prototyping board
3. **I2C Pull-ups**: Most BME680 modules include built-in pull-up resistors
4. **Battery Connection**: Ensure proper polarity; consider using JST connectors
5. **Testing**: Test all connections before final assembly, especially D0-RST jumper
6. **Calibration**: BME680 may require burn-in period for gas sensor accuracy

## Software Features

### ESP Home Configuration
- Deep sleep power management
- Battery level monitoring with low-battery alerts
- WiFi connection handling with retry logic
- OTA update capability
- Diagnostic sensors for troubleshooting

### Home Assistant Integration
- Automatic device discovery
- Historical data logging
- Automation triggers based on sensor values
- Low battery notifications
- Device unavailable alerts

## Troubleshooting Guide

### Common Issues
1. **Device not connecting to WiFi**
   - Check WiFi credentials
   - Ensure 2.4GHz network
   - Check signal strength at location

2. **Sensor readings incorrect**
   - Verify I2C connections
   - Check sensor orientation
   - Allow warm-up time for gas sensor

3. **Poor battery life**
   - Verify deep sleep configuration
   - Check for WiFi connection issues
   - Monitor current consumption

4. **Intermittent operation**
   - Check battery voltage
   - Verify solder connections
   - Monitor for brown-out conditions

## Bill of Materials (BOM)

| Component | Quantity | Estimated Cost |
|-----------|----------|----------------|
| Wemos D1 Mini | 1 | $3-5 |
| BME680 Breakout | 1 | $15-20 |
| DHT22 Breakout | 1 | $2-6 |
| 18650 Battery | 1 | $5-8 |
| Battery Holder | 1 | $2-3 |
| Jumper Wires | 1 set | $2-3 |
| Enclosure | 1 | $5-10 |
| **Total** | | **$32-49** |

> Note: DHT22-based builds are lower-cost (see DHT22 row); the total shown is for the BME680 reference build.

## Future Enhancements

- Solar charging capability
- LoRaWAN connectivity for remote locations
- Additional sensors (UV, light level, rain detection)
- Local data logging with SD card
- Display for local readings

## Safety Considerations

- Use proper 18650 batteries with protection circuits
- Ensure correct polarity connections
- Consider fuse protection for battery circuit
- Use appropriate enclosure rating for environment
- Follow local electrical codes for installations
