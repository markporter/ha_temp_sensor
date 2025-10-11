# Deep Sleep Configuration Guide

## Overview
The ESP Home environmental sensor now supports configurable deep sleep mode that can be enabled or disabled without rewiring the device.

## Configuration Modes

### 🔄 **Normal Mode (Default)**
- **Deep Sleep**: Disabled
- **Sensor Updates**: Every 30 seconds
- **Battery Updates**: Every 60 seconds  
- **Power Consumption**: ~80-200mA active
- **Use Case**: Initial setup, testing, continuous monitoring

### 💤 **Deep Sleep Mode** 
- **Deep Sleep**: Enabled
- **Sensor Updates**: Once per wake cycle (every 15 minutes)
- **Battery Updates**: Once per wake cycle
- **Power Consumption**: ~20µA sleep, ~200mA for 10 seconds active
- **Use Case**: Battery-powered deployment, extended operation

## Switching Between Modes

### Method 1: Runtime Switching (Recommended for `deep_sleep_enabled: "true"` firmware)

If firmware was compiled with `deep_sleep_enabled: "true"`, you can switch modes instantly:

**To Disable Deep Sleep While Device is Sleeping:**
1. In Home Assistant, turn OFF the "Deep Sleep Mode" switch
2. Device will detect this on next wake-up and switch to normal mode
3. No restart required - change takes effect immediately

**To Enable Deep Sleep from Normal Mode:**
1. In Home Assistant, turn ON the "Deep Sleep Mode" switch  
2. Optionally adjust "Sleep Interval" setting
3. Click "Apply Sleep Settings" to enter sleep immediately

### Method 2: Configuration File (For Firmware Changes)
Edit the `env-sensor.yaml` file to set the default behavior:

```yaml
substitutions:
  # Firmware capability - enables deep sleep hardware support
  deep_sleep_enabled: "false"  # Hardware support disabled
  # deep_sleep_enabled: "true"   # Hardware support enabled (recommended)
```

**Important**: `deep_sleep_enabled` determines if deep sleep hardware support is compiled into firmware. The Home Assistant switch only works when this is set to "true".

### Method 2: Home Assistant Controls
Several controls are available in Home Assistant:
- **"Deep Sleep Mode" Switch**: Enable/disable deep sleep (takes effect immediately*)
- **"Sleep Interval" Number Input**: Adjust sleep duration (1-180 minutes)
- **"Apply Sleep Settings" Button**: Apply new interval immediately

*Note: The switch only works if firmware was compiled with `deep_sleep_enabled: "true"`. If firmware was compiled with deep sleep disabled, you must reflash to enable the feature.

## Hardware Requirements by Mode

### Normal Mode
- ✅ **D0-RST Connection**: Not required
- ✅ **USB Programming**: Works normally
- ✅ **Power Source**: USB or battery

### Deep Sleep Mode  
- ⚠️ **D0-RST Connection**: **REQUIRED** (GPIO16 to RST pin)
- ⚠️ **USB Programming**: Remove D0-RST jumper during programming
- ⚠️ **Power Source**: Battery recommended for portability

## Setup Workflow

### Initial Setup (Normal Mode)
1. **Wire everything EXCEPT D0-RST connection**
2. Keep `deep_sleep_enabled: "false"`
3. Connect via USB and program normally
4. Test all sensors and connectivity
5. Verify Home Assistant integration

### Switching to Deep Sleep Mode
1. **Add D0-RST jumper wire** (GPIO16 to RST pin)
2. Change `deep_sleep_enabled: "true"`
3. **Remove D0-RST jumper** temporarily
4. Upload firmware via USB
5. **Reconnect D0-RST jumper**
6. Switch to battery power
7. Device will now sleep/wake every 15 minutes

### Returning to Normal Mode  
1. **Remove D0-RST jumper** 
2. Change `deep_sleep_enabled: "false"`
3. Upload firmware via USB
4. Device will operate continuously

## Troubleshooting

### Can't Program Device
- ❌ **Most Likely**: D0-RST jumper is connected
- ✅ **Solution**: Remove D0-RST jumper during programming

### Device Not Waking from Deep Sleep
- ❌ **Most Likely**: D0-RST jumper not connected
- ✅ **Solution**: Verify GPIO16 (D0) connected to RST pin

### Poor Battery Life in Normal Mode
- ❌ **Expected**: Normal mode uses continuous power
- ✅ **Solution**: Switch to deep sleep mode for battery operation

### Device Appears Offline
- **Normal Mode**: Check WiFi connection, device should be always online
- **Deep Sleep Mode**: Expected behavior, device only online briefly every 15 minutes

## Configuration Examples

### Home Lab Setup (Always On)
```yaml
deep_sleep_enabled: "false"
sensor_update_interval: "30s"
battery_update_interval: "60s"
# Provides real-time monitoring, powered by USB
```

### Remote Deployment (Battery Powered)
```yaml
deep_sleep_enabled: "true" 
sleep_duration: "15min"
# Maximizes battery life, readings every 15 minutes
```

### Frequent Monitoring (Short Sleep)
```yaml
deep_sleep_enabled: "true"
sleep_duration: "5min"
# More frequent readings, moderate battery life
```

## Power Consumption Comparison

| Mode | Active Current | Sleep Current | Duty Cycle | Avg Current | Battery Life* |
|------|----------------|---------------|------------|-------------|---------------|
| Normal | 150mA | N/A | 100% | 150mA | ~17 hours |
| Deep Sleep (5min) | 200mA | 20µA | 3.3% | 6.6mA | ~16 days |
| Deep Sleep (15min) | 200mA | 20µA | 1.1% | 2.2mA | ~47 days |
| Deep Sleep (30min) | 200mA | 20µA | 0.6% | 1.2mA | ~87 days |
| Deep Sleep (60min) | 200mA | 20µA | 0.3% | 0.6mA | ~174 days |

*Estimates based on 2500mAh 18650 battery

## Advanced Configuration

### Adjustable Sleep Duration from Home Assistant

The sleep interval can now be adjusted directly from Home Assistant without reflashing firmware:

#### **Home Assistant Controls**
1. **Sleep Interval Number Input**: Set duration from 1-180 minutes
2. **Apply Sleep Settings Button**: Apply changes immediately  
3. **Sleep Interval Setting Sensor**: Monitor current setting

#### **Usage Examples**
- **Frequent Monitoring**: Set to 5 minutes for more data points
- **Standard Operation**: 15 minutes (default) for balanced monitoring  
- **Maximum Battery Life**: 60-180 minutes for extended operation

#### **How to Adjust**
1. In Home Assistant, find the device
2. Adjust "Sleep Interval" number input (1-180 minutes)
3. Click "Apply Sleep Settings" button to activate immediately
4. Device will use new interval for next sleep cycle

### Legacy Configuration File Method
You can still set default sleep time in the configuration:
```yaml
sleep_duration: "30min"  # Default interval (overridden by HA control)
```

### Battery Monitoring Thresholds
Customize low battery alerts:
```yaml
low_battery_threshold: "3.4"  # Higher threshold for earlier warning
low_battery_percentage: "15"  # Alert at 15% instead of 20%
```

## Best Practices

### Development Phase
- Start with `deep_sleep_enabled: "false"`
- Test all functionality first
- Add D0-RST connection only when ready for deployment

### Production Deployment  
- Enable deep sleep for battery operation
- Use removable jumper for D0-RST connection
- Monitor battery levels through Home Assistant
- Plan battery replacement schedule based on actual usage

### Maintenance
- Remove D0-RST jumper before any firmware updates
- Keep spare programmed devices for quick replacement
- Monitor device uptime to detect wake/sleep issues

## Home Assistant Integration

### Sleep Configuration Entities
The device provides these controls in Home Assistant:

#### **Configuration Controls**
- **Sleep Interval** (`number`) - Adjustable sleep duration (1-180 minutes)
- **Deep Sleep Mode** (`switch`) - Enable/disable deep sleep (requires restart)
- **Apply Sleep Settings** (`button`) - Apply interval changes immediately
- **Enter Deep Sleep** (`button`) - Force immediate sleep entry

#### **Monitoring Sensors**
- **Sleep Interval Setting** (`sensor`) - Current sleep interval display  
- **Battery Level** (`sensor`) - Battery percentage (0-100%)
- **Battery Voltage** (`sensor`) - Battery voltage in volts
- **Low Battery** (`binary_sensor`) - Low battery alert

#### **Environmental Sensors**
- **Temperature** (`sensor`) - Ambient temperature
- **Humidity** (`sensor`) - Relative humidity
- **Pressure** (`sensor`) - Atmospheric pressure
- **Gas Resistance** (`sensor`) - Air quality indicator

### Quick Setup in Home Assistant Dashboard
Add this card configuration for easy sleep interval control:

```yaml
type: entities
title: Environmental Sensor Sleep Control
entities:
  - entity: switch.environmental_sensor_01_deep_sleep_mode
    name: Deep Sleep Enabled
  - entity: number.environmental_sensor_01_sleep_interval
    name: Sleep Interval (minutes)
  - entity: button.environmental_sensor_01_apply_sleep_settings
    name: Apply Settings
  - entity: sensor.environmental_sensor_01_sleep_interval_setting
    name: Current Setting
  - entity: sensor.environmental_sensor_01_battery_level
    name: Battery Level
```

## Frequently Asked Questions

### **Q: If I disable deep sleep while the sensor is sleeping, will it wake up and stay awake?**
**A: Yes!** When the device wakes up from its current sleep cycle, it will check the Home Assistant switch state and stay in normal mode instead of going back to sleep.

### **Q: How long does it take for the switch change to take effect?**
**A: Maximum one sleep interval.** If the device is sleeping for 15 minutes and you disable deep sleep, it will detect the change within 15 minutes when it naturally wakes up.

### **Q: Can I force the device to wake up immediately?**
**A: No, you must wait for the next natural wake cycle.** The ESP8266 in deep sleep cannot receive commands until it wakes up on its timer.

### **Q: What happens if I toggle the switch while device is awake?**
**A: Changes take effect immediately.** The device will either enter deep sleep or switch to normal mode within seconds.

### **Q: Why doesn't the Home Assistant switch work?**
**A: Check firmware compilation.** The switch only works if firmware was compiled with `deep_sleep_enabled: "true"`. If compiled with `"false"`, you must reflash firmware to enable deep sleep hardware support.

### **Q: Can I change the sleep interval while device is sleeping?**
**A: Yes!** Adjust the interval and the device will use the new setting on its next wake cycle. Use the "Apply Sleep Settings" button to enter sleep with new interval immediately when device is awake.
