# Wiring Diagram - ESP Home Environmental Sensor

This diagram shows the electrical connections for the Wemos D1 Mini with environmental sensors (BME680 or DHT22) and 18650 battery.

## Component Layout - BME680 (I2C)

```mermaid
graph TD
    %% Object Definitions
    WemosD1["Wemos D1 Mini<br/>ESP8266"]
    BME680["BME680 Sensor<br/>Environmental"]
    Battery["18650 Li-ion<br/>Battery 3.7V"]
    BatteryHolder["18650<br/>Battery Holder"]
    
    %% Power Distribution
    VINBus["VIN Bus<br/>(3.7V)"]
    V33Bus["3.3V Bus"]
    GNDBus["GND Bus"]
    
    %% Connections - Power
    Battery -->|"Battery +"| BatteryHolder
    Battery -->|"Battery -"| BatteryHolder
    BatteryHolder -->|"Battery +"| VINBus
    BatteryHolder -->|"Battery -"| GNDBus
    VINBus -->|"VIN"| WemosD1
    GNDBus -->|"GND"| WemosD1
    
    %% Power Distribution from Wemos
    WemosD1 -->|"3.3V Out"| V33Bus
    WemosD1 -->|"GND"| GNDBus
    
    %% Sensor Power
    V33Bus -->|"VCC"| BME680
    GNDBus -->|"GND"| BME680
    
    %% I2C Communication
    WemosD1 -->|"D2 (GPIO4)<br/>SDA"| BME680
    WemosD1 -->|"D1 (GPIO5)<br/>SCL"| BME680
    
    %% Deep Sleep Wake Connection (CRITICAL)
    WemosD1 -->|"D0 (GPIO16) to RST Pin<br/>Deep Sleep Wake"| WemosD1
    
    %% Link Styles
    %% Battery -->|"Battery +"| BatteryHolder
    linkStyle 0 stroke:red,stroke-width:3px
    %% Battery -->|"Battery -"| BatteryHolder
    linkStyle 1 stroke:black,stroke-width:3px
    %% BatteryHolder -->|"Battery +"| VINBus
    linkStyle 2 stroke:red,stroke-width:3px
    %% BatteryHolder -->|"Battery -"| GNDBus
    linkStyle 3 stroke:black,stroke-width:3px
    %% VINBus -->|"VIN"| WemosD1
    linkStyle 4 stroke:red,stroke-width:2px
    %% GNDBus -->|"GND"| WemosD1
    linkStyle 5 stroke:black,stroke-width:2px
    %% WemosD1 -->|"3.3V Out"| V33Bus
    linkStyle 6 stroke:yellow,stroke-width:2px
    %% WemosD1 -->|"GND"| GNDBus
    linkStyle 7 stroke:black,stroke-width:2px
    %% V33Bus -->|"VCC"| BME680
    linkStyle 8 stroke:orange,stroke-width:2px
    %% GNDBus -->|"GND"| BME680
    linkStyle 9 stroke:black,stroke-width:2px
    %% WemosD1 -->|"D2 (GPIO4)<br/>SDA"| BME680
    linkStyle 10 stroke:blue,stroke-width:2px
    %% WemosD1 -->|"D1 (GPIO5)<br/>SCL"| BME680
    linkStyle 11 stroke:green,stroke-width:2px
    %% WemosD1 -->|"D0 (GPIO16)<br/>Deep Sleep Wake"| WemosD1
    linkStyle 12 stroke:black,stroke-width:2px
```

## Component Layout - DHT22 (Digital GPIO)

```mermaid
graph TD
    %% Object Definitions
    WemosD1["Wemos D1 Mini<br/>ESP8266"]
    DHT22["DHT22 Sensor<br/>Temperature/Humidity"]
    Battery["18650 Li-ion<br/>Battery 3.7V"]
    BatteryHolder["18650<br/>Battery Holder"]
    
    %% Power Distribution
    VINBus["VIN Bus<br/>(3.7V)"]
    V33Bus["3.3V Bus"]
    GNDBus["GND Bus"]
    
    %% Connections - Power
    Battery -->|"Battery +"| BatteryHolder
    Battery -->|"Battery -"| BatteryHolder
    BatteryHolder -->|"Battery +"| VINBus
    BatteryHolder -->|"Battery -"| GNDBus
    VINBus -->|"VIN"| WemosD1
    GNDBus -->|"GND"| WemosD1
    
    %% Power Distribution from Wemos
    WemosD1 -->|"3.3V Out"| V33Bus
    WemosD1 -->|"GND"| GNDBus
    
    %% Sensor Power
    V33Bus -->|"VCC"| DHT22
    GNDBus -->|"GND"| DHT22
    
    %% DHT22 Data Line (with pull-up)
    WemosD1 -->|"D5 (GPIO14)<br/>Data Signal"| DHT22
    
    %% Deep Sleep Wake Connection (CRITICAL)
    WemosD1 -->|"D0 (GPIO16) to RST Pin<br/>Deep Sleep Wake"| WemosD1
    
    %% Link Styles
    %% Battery -->|"Battery +"| BatteryHolder
    linkStyle 0 stroke:red,stroke-width:3px
    %% Battery -->|"Battery -"| BatteryHolder
    linkStyle 1 stroke:black,stroke-width:3px
    %% BatteryHolder -->|"Battery +"| VINBus
    linkStyle 2 stroke:red,stroke-width:3px
    %% BatteryHolder -->|"Battery -"| GNDBus
    linkStyle 3 stroke:black,stroke-width:3px
    %% VINBus -->|"VIN"| WemosD1
    linkStyle 4 stroke:red,stroke-width:2px
    %% GNDBus -->|"GND"| WemosD1
    linkStyle 5 stroke:black,stroke-width:2px
    %% WemosD1 -->|"3.3V Out"| V33Bus
    linkStyle 6 stroke:yellow,stroke-width:2px
    %% WemosD1 -->|"GND"| GNDBus
    linkStyle 7 stroke:black,stroke-width:2px
    %% V33Bus -->|"VCC"| DHT22
    linkStyle 8 stroke:orange,stroke-width:2px
    %% GNDBus -->|"GND"| DHT22
    linkStyle 9 stroke:black,stroke-width:2px
    %% WemosD1 -->|"D5 (GPIO14)<br/>Data Signal"| DHT22
    linkStyle 10 stroke:purple,stroke-width:2px
    %% WemosD1 -->|"D0 (GPIO16)<br/>Deep Sleep Wake"| WemosD1
    linkStyle 11 stroke:black,stroke-width:2px
```

## Pin Mapping Details

### Wemos D1 Mini Pinout - BME680 Configuration
| Physical Pin | GPIO | Function | Connection |
|--------------|------|----------|------------|
| VIN | - | Power Input | 18650 Battery + |
| GND | - | Ground | 18650 Battery - |
| 3.3V | - | 3.3V Output | BME680 VCC |
| D1 | GPIO5 | I2C Clock | BME680 SCL |
| D2 | GPIO4 | I2C Data | BME680 SDA |
| D0 | GPIO16 | Deep Sleep Wake | RST Pin (REQUIRED for deep sleep) |
| RST | - | Reset | D0 Pin (REQUIRED for deep sleep) |

### Wemos D1 Mini Pinout - DHT22 Configuration
| Physical Pin | GPIO | Function | Connection |
|--------------|------|----------|------------|
| VIN | - | Power Input | 18650 Battery + |
| GND | - | Ground | 18650 Battery - |
| 3.3V | - | 3.3V Output | DHT22 VCC |
| D5 | GPIO14 | Digital Data | DHT22 Data (with pull-up) |
| D0 | GPIO16 | Deep Sleep Wake | RST Pin (REQUIRED for deep sleep) |
| RST | - | Reset | D0 Pin (REQUIRED for deep sleep) |

### BME680 Sensor Pinout
| BME680 Pin | Function | Connection |
|------------|----------|------------|
| VCC | Power (3.3V) | Wemos 3.3V |
| GND | Ground | Wemos GND |
| SDA | I2C Data | Wemos D2 (GPIO4) |
| SCL | I2C Clock | Wemos D1 (GPIO5) |

### DHT22 Sensor Pinout
| DHT22 Pin | Function | Connection |
|-----------|----------|------------|
| VCC | Power (3.3V) | Wemos 3.3V |
| GND | Ground | Wemos GND |
| Data | Digital Signal | Wemos D5 (GPIO14) with 4.7kΩ pull-up |

## Connection Notes

### Power Distribution (Both Sensors)
- **18650 Battery**: Provides 3.7V nominal (4.2V fully charged, 3.0V depleted)
- **VIN Pin**: Can accept 5-12V, has onboard regulator to 3.3V
- **Direct Connection**: Battery connects directly to VIN for maximum efficiency
- **Current Path**: Battery → VIN → Internal Regulator → 3.3V → Sensor

### BME680 I2C Communication
- **Pull-up Resistors**: Most BME680 breakout boards include built-in 10kΩ pull-ups
- **Bus Speed**: Configured for 100kHz for reliable operation
- **Address**: BME680 typically uses 0x77 (some modules use 0x76)
- **Pins**: D1 (GPIO5) = SCL, D2 (GPIO4) = SDA

### DHT22 Digital Communication
- **Pull-up Resistor**: Requires 4.7kΩ pull-up resistor on data line (D5/GPIO14)
- **Communication Protocol**: Proprietary single-wire digital protocol
- **Signal Timing**: Timing is critical; ESPHome handles this automatically
- **Reading Interval**: Typically takes 2-3 seconds per reading
- **Temperature & Humidity Only**: Does not provide pressure or gas resistance

### Deep Sleep Wake Connection (CRITICAL)
- **Required Connection**: D0 (GPIO16) MUST be connected to RST pin
- **Purpose**: Allows ESP8266 to wake itself from deep sleep after timer expires
- **Implementation**: Short jumper wire or PCB trace between D0 and RST pins
- **Without This**: Device will never wake from deep sleep and appear "dead"
- **Note**: This connection is specific to ESP8266; ESP32 handles this internally

## Physical Assembly Tips

### Mounting Considerations
1. **Compact Layout**: Keep sensor away from heat-generating components
2. **Ventilation**: Ensure sensor has access to ambient air
   - **BME680**: Requires good ventilation for accurate gas resistance readings
   - **DHT22**: More tolerant; simple placement works fine
3. **Strain Relief**: Secure all wire connections
4. **Access**: Easy battery replacement without disassembly
5. **Pull-up Resistor (DHT22)**: Keep 4.7kΩ pull-up resistor close to data pin

### Wire Gauge Recommendations
- **Power (VIN, GND)**: 22-24 AWG for current capacity
- **BME680 I2C (SDA, SCL)**: 26-28 AWG sufficient for data lines
- **DHT22 Data**: 26-28 AWG with inline pull-up resistor
- **Deep Sleep (D0 to RST)**: 30 AWG or thin jumper wire
- **Total Length**: Keep sensor wires under 12 inches for reliability

### Connector Options
- **JST Connectors**: For removable battery connection
- **Pin Headers**: For breadboard prototyping
- **Direct Solder**: For permanent installation
- **Terminal Blocks**: For field serviceability

### Sensor Comparison

| Feature | BME680 | DHT22 |
|---------|--------|-------|
| Communication | I2C | Digital (GPIO) |
| Measurements | Temp, Humidity, Pressure, Gas | Temp, Humidity only |
| I2C Address | 0x76 or 0x77 | N/A |
| Pins Required | 4 (VCC, GND, SDA, SCL) | 3 (VCC, GND, Data) |
| Pull-up Resistor | Built-in (usually) | External 4.7kΩ required |
| Accuracy | ±0.75°C, ±3%RH | ±2°C, ±5%RH |
| Cost | ~$20-30 | ~$5-10 |
| Best For | Advanced air quality monitoring | Simple temp/humidity tracking |
