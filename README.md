# Jacko's Warfield Temperature Monitor

ESPHome-based temperature monitoring system for heating flow and return temperatures using Dallas/DS18B20 sensors.

## Hardware

- **Board**: ESP8266 (ESP-01 1M variant)
- **Sensors**: 2x Dallas DS18B20 temperature sensors
- **Connection**: One-wire bus on GPIO2

## Features

- **Dual Temperature Monitoring**
  - Flow temperature measurement
  - Return temperature measurement
  - 30-second update intervals
  - 0.1°C precision

- **Connectivity**
  - Primary WiFi network with fallback support
  - Home Assistant API integration (encrypted)
  - Built-in web server (port 80)
  - Fallback AP mode if WiFi fails

- **Management**
  - OTA (Over-The-Air) updates
  - Uptime tracking
  - Captive portal for configuration

## Configuration

### Required Secrets

Create a `secrets.yaml` file in your ESPHome directory:

```yaml
warfield_ssid: "your_primary_network"
warfield_password: "your_primary_password"
gchq_ssid: "your_secondary_network"
gchq_password: "your_secondary_password"
```

### WiFi Priority

The device connects to networks in this order:
1. Primary network (priority 10)
2. Secondary network (priority 5)
3. Fallback hotspot if both fail

### Fallback Hotspot

If WiFi connection fails:
- **SSID**: Warfield Fallback Hotspot
- **Password**: DgNoPJEvMrVK

## Sensor Setup

The Dallas temperature sensors are connected via one-wire protocol:
- **Bus Pin**: GPIO2
- **Sensor 0**: Flow Temperature
- **Sensor 1**: Return Temperature

## Installation

1. Install [ESPHome](https://esphome.io/guides/getting_started_command_line.html)
2. Create your `secrets.yaml` file
3. Compile and upload:
   ```bash
   esphome run warfield.yaml
   ```

## Accessing the Device

- **Home Assistant**: Auto-discovered via API
- **Web Interface**: `http://warfield.local` (or device IP)
- **Logs**: Use ESPHome dashboard or CLI

## Security Notes

⚠️ **Important**: The API encryption key and OTA password in this configuration should be regenerated for production use. Generate new credentials with:

```bash
esphome wizard warfield.yaml
```
