# ESP Home Environmental Sensor - Assembly Guide

## Overview
This guide provides step-by-step instructions for assembling and configuring your ESP Home environmental sensor using a Wemos D1 Mini, BME680 sensor, and 18650 battery.

## Required Tools
- Soldering iron (25-40W recommended)
- Solder (60/40 rosin core)
- Wire strippers
- Small screwdrivers
- Multimeter (for testing)
- Heat shrink tubing or electrical tape
- Breadboard (for initial testing)

## Safety Precautions
⚠️ **Important Safety Notes:**
- Always verify battery polarity before connecting
- Use batteries with built-in protection circuits
- Work in a well-ventilated area when soldering
- Disconnect power when making connections
- Double-check all connections before applying power

## Pre-Assembly Testing

### 1. Component Verification
Before assembly, test each component individually:

**Test Wemos D1 Mini:**
1. Connect to computer via USB cable
2. Install ESP Home or Arduino IDE
3. Upload a simple blink sketch
4. Verify WiFi connectivity

**Test BME680 Sensor:**
1. Connect sensor to breadboard with Wemos D1
2. Use I2C scanner sketch to verify address (0x76 or 0x77)
3. Read basic sensor values to ensure functionality

**Test Battery:**
1. Check voltage with multimeter (should be 3.7V nominal)
2. Verify capacity if possible
3. Ensure battery fits properly in holder

## Assembly Steps

### Step 1: Prepare Components

**Wemos D1 Mini Preparation:**
1. Check for bent pins, straighten if necessary
2. Test all GPIO pins for continuity
3. Verify 3.3V output with multimeter

**BME680 Sensor Preparation:**
1. Check breakout board for cold solder joints
2. Verify I2C address jumpers if present
3. Note pin layout (VCC, GND, SDA, SCL)

**Battery Holder Setup:**
1. Check spring contacts for corrosion
2. Test fit with 18650 battery
3. Verify polarity markings

### Step 2: Create Wiring Harness

**Required Wire Lengths:**
- Power wires (VIN, GND): 4-6 inches
- I2C wires (SDA, SCL): 3-4 inches
- Deep sleep jumper (D0 to RST): 1-2 inches
- A0 (reserved): leave unconnected (on-device battery monitoring disabled)

**Wire Color Coding (Recommended):**
- Red: Positive power (VIN, VCC)
- Black: Ground (GND)
- Blue: SDA data line
- Yellow: SCL clock line
- Purple: Deep sleep jumper (D0 to RST)
- Green: spare / reserved (A0 unused by firmware)

**Prepare Wires:**
1. Strip 1/4 inch from each end
2. Tin wire ends with solder
3. Label each wire for identification
4. Test continuity with multimeter

### Step 3: Solder Connections

**Safety First:**
- Work in well-ventilated area
- Use appropriate temperature (350-375°C)
- Clean iron tip regularly
- Use flux for better solder flow

**Wemos D1 Mini Connections:**
1. **VIN Pin**: Solder red wire (from battery +)
2. **GND Pin**: Solder black wire (from battery -)
3. **3.3V Pin**: Solder red wire (to BME680 VCC)
4. **D1 (GPIO5)**: Solder yellow wire (to BME680 SCL)
5. **D2 (GPIO4)**: Solder blue wire (to BME680 SDA)
6. **D0 to RST**: **CRITICAL** - Solder short jumper wire between D0 and RST pins for deep sleep
7. **A0 Pin**: Not used by firmware (leave unconnected). On-device ADC battery monitoring has been disabled to preserve battery life.

**BME680 Sensor Connections:**
1. **VCC Pin**: Red wire from Wemos 3.3V
2. **GND Pin**: Black wire to common ground
3. **SDA Pin**: Blue wire from Wemos D2
4. **SCL Pin**: Yellow wire from Wemos D1

**Battery Holder Connections:**
1. **Positive Terminal**: Red wire to Wemos VIN
2. **Negative Terminal**: Black wire to Wemos GND
3. Use JST connector for removable connection (recommended)

### Step 4: Initial Testing

**Before Enclosure Assembly:**
1. **Visual Inspection**: Check all solder joints
2. **Continuity Test**: Verify connections with multimeter
3. **Power Test**: Insert battery and measure voltages
4. **Communication Test**: Upload test sketch to verify I2C

**Power Verification:**
- Battery voltage at VIN: 3.7V (nominal)
- 3.3V output at sensor VCC: 3.3V ±0.1V
- Current draw (active): <200mA
- Current draw (sleep): <50µA

### Step 5: Software Configuration

**ESPHome Setup:**
1. Install ESPHome on your computer
2. Copy provided `env-sensor.yaml` configuration
3. Create `secrets.yaml` with your WiFi credentials
4. Compile and upload firmware

**Initial Configuration:**
```bash
# Install ESPHome
pip3 install esphome

# Create secrets file
cp secrets.yaml.template secrets.yaml
# Edit secrets.yaml with your values

# Compile firmware
esphome compile env-sensor.yaml

# Upload firmware (first time via USB)
esphome upload env-sensor.yaml --device /dev/ttyUSB0
```

**Configuration Steps:**
1. **WiFi Setup**: Enter SSID and password in secrets.yaml
2. **Home Assistant Integration**: Copy API key to Home Assistant
3. **OTA Setup**: Set secure password for over-the-air updates
4. **Deep Sleep**: Verify sleep duration meets your needs

### Step 6: Calibration and Testing

**Sensor Calibration:**
1. **Temperature**: Compare with known reference, adjust offset
2. **Humidity**: Use salt solution for 75% RH reference point
3. **Pressure**: Compare with local weather station
4. **Gas Sensor**: Requires 48-hour burn-in period

<!-- Battery monitoring removed: firmware no longer publishes battery-voltage or battery-percentage entities. For battery health checks use an external meter or a dedicated low-power monitor. -->

**Field Testing:**
1. **Range Test**: Verify WiFi connectivity at installation location
2. **Power Test**: Monitor battery consumption over 24 hours
3. **Data Validation**: Compare readings with reference sensors
4. **Sleep Cycle**: Verify proper deep sleep operation

### Step 7: Enclosure Installation

**Enclosure Preparation:**
1. **Ventilation Holes**: Drill small holes for pressure/humidity accuracy
2. **Cable Glands**: Install weatherproof cable entries
3. **Mounting Points**: Prepare attachment points for installation
4. **Desiccant**: Consider moisture absorption packets

**Component Mounting:**
1. **Secure MCU**: Use standoffs or mounting tape
2. **Sensor Position**: Away from heat sources, good airflow
3. **Battery Access**: Easy replacement without disassembly
4. **Antenna Position**: Clear of metal enclosure for good WiFi

**Weatherproofing:**
1. **Seal Entry Points**: Use appropriate gaskets
2. **Conformal Coating**: Optional protection for PCBs
3. **IP Rating**: Verify enclosure meets environment requirements
4. **UV Protection**: Ensure UV-stable materials for outdoor use

### Step 8: Home Assistant Integration

**Device Discovery:**
1. Home Assistant should auto-discover the device
2. Accept the device in Integrations page
3. Verify all sensor entities appear

**Entity Configuration:**
1. **Friendly Names**: Set meaningful names for each sensor
2. **Device Classes**: Verify correct device classes assigned
3. **Units**: Confirm temperature, pressure units as preferred
4. **History**: Enable long-term statistics for trending

**Automation Setup:**
<!-- Low-battery automation examples removed: firmware no longer exposes battery-level entities. Use external monitoring or scheduled maintenance instead. -->

## Troubleshooting

### Common Issues and Solutions

**Device Not Connecting to WiFi:**
- Verify SSID and password in secrets.yaml
- Check 2.4GHz WiFi network (ESP8266 doesn't support 5GHz)
- Test WiFi signal strength at installation location
- Try manual WiFi setup via captive portal

**Sensor Readings Incorrect:**
- Verify I2C connections (SDA, SCL)
- Check I2C address in configuration (0x76 vs 0x77)
- Allow sensor warm-up time (especially gas sensor)
- Check for electromagnetic interference

**Poor Battery Life:**
- Verify deep sleep is working (check logs)
- Check for WiFi connection issues causing retries
- Monitor current consumption with multimeter
- Ensure no unnecessary sensors or features enabled

**Device Not Waking from Deep Sleep:**
- **CRITICAL**: Verify D0 (GPIO16) is connected to RST pin
- Check solder joint quality on D0-RST connection
- Test continuity between D0 and RST pins with multimeter
- Ensure deep sleep duration is set correctly
- Check for brown-out conditions preventing wake

**Device Repeatedly Restarting:**
- Check battery voltage under load
- Verify power supply capacity
- Look for brown-out detector triggers in logs
- Check for loose connections

### Diagnostic Tools

**Serial Monitor:**
```bash
# View device logs
esphome logs env-sensor.yaml --device 192.168.1.100
```

**I2C Scanner Sketch:**
```cpp
// Upload to verify BME680 address
#include <Wire.h>
void setup() {
  Wire.begin(4, 5);  // SDA=GPIO4, SCL=GPIO5
  Serial.begin(115200);
  Serial.println("I2C Scanner");
}
void loop() {
  for(byte address = 1; address < 127; address++) {
    Wire.beginTransmission(address);
    if(Wire.endTransmission() == 0) {
      Serial.printf("Found device at 0x%02X\n", address);
    }
  }
  delay(5000);
}
```

**Power Consumption Test:**
- Use multimeter in series with battery connection
- Monitor current in active and sleep modes
- Calculate estimated battery life

## Maintenance

### Regular Maintenance Tasks

**Monthly:**
- Check battery voltage and capacity
- Verify sensor accuracy against references
- Clean enclosure and check for moisture ingress
- Review Home Assistant logs for errors

**Quarterly:**
- Replace desiccant packets if used
- Check mounting security
- Verify weatherproof seals
- Update firmware if new version available

**Annually:**
- Replace battery (typical Li-ion lifespan)
- Calibrate sensors if drift detected
- Check enclosure UV damage for outdoor units
- Review and optimize configuration

### Performance Monitoring

**Key Metrics to Track:**
- Battery voltage trend over time
- WiFi signal strength variations
- Sensor reading stability
- Deep sleep cycle consistency
- OTA update success rate

**Alerting Setup:**
Configure Home Assistant alerts for:
- Battery level below 20%
- Device offline for >1 hour
- Sensor readings outside expected ranges
- Failed OTA update attempts

## Appendix

### Bill of Materials Detail
| Component | Part Number | Supplier | Notes |
|-----------|-------------|----------|-------|
| Wemos D1 Mini | ESP8266 | Various | Clone versions OK |
| BME680 Breakout | Various | Adafruit, Sparkfun | Check I2C address |
| 18650 Battery | Protected Cell | Samsung, Panasonic | Avoid unprotected |
| Battery Holder | BH-18650 | Various | Spring contacts |
| Enclosure | IP65 Rated | Various | Size for components |

### Reference Links
- [ESPHome Official Documentation](https://esphome.io/)
- [BME680 Datasheet](https://www.bosch-sensortec.com/products/environmental-sensors/gas-sensors/bme680/)
- [Wemos D1 Mini Pinout](https://wiki.wemos.cc/products:d1:d1_mini)
- [Home Assistant Integration](https://www.home-assistant.io/integrations/esphome/)

### Configuration Files Location
- Main config: `esphome_code/env-sensor.yaml`
- Secrets template: `esphome_code/secrets.yaml.template`
- Wiring diagram: `design/wiring-diagram.md`
- Full documentation: `design/README.md`
