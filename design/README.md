# ESP Home Environmental Sensor - Design Document

## Project Overview

This project creates a battery-powered environmental sensor using ESP Home for integration with Home Assistant. The sensor measures temperature, humidity, pressure, and gas resistance using a BME680 sensor, with data transmitted via WiFi to your home automation system.

## Hardware Components

### Core Components
- **Wemos D1 Mini (ESP8266)** - Main microcontroller with WiFi capability
- **BME680 Sensor** - Environmental sensor for temperature, humidity, pressure, and gas
- **18650 Li-ion Battery** - Direct connection for portable power
- **18650 Battery Holder** - Secure mounting and easy replacement

### Additional Components (Optional)
- **TP4056 Charging Module** - For battery charging capability
- **3.3V Voltage Regulator** - If additional voltage regulation is needed
- **Pull-up Resistors (10kΩ)** - For I2C communication (may be built into modules)
- **Enclosure** - Weather-resistant housing for outdoor deployment

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

### Power Consumption Analysis

#### Active Mode (WiFi transmission)
- ESP8266: ~80-170mA
- BME680: ~12mA (during measurement)
- **Total Active**: ~200mA peak

#### Deep Sleep Mode
- ESP8266: ~20µA
- BME680: ~0.15µA (standby)
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
- **Sensor readings**: Every 15 minutes
- **Battery status**: Every 15 minutes
- **WiFi diagnostics**: Every 15 minutes

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
| 18650 Battery | 1 | $5-8 |
| Battery Holder | 1 | $2-3 |
| Jumper Wires | 1 set | $2-3 |
| Enclosure | 1 | $5-10 |
| **Total** | | **$32-49** |

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
