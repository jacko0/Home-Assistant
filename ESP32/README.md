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


# Line by Line Explanation

## Device Identification

```yaml
esphome:
  name: warfield
  friendly_name: warfield
```

**esphome:** This is the main configuration section that tells ESPHome this is an ESPHome device.

**name: warfield** - This is the unique identifier for your device on the network. It's like the device's hostname. You'll access it at `warfield.local` on your network.

**friendly_name: warfield** - This is the human-readable name that shows up in Home Assistant and the web interface. Think of it like a display name.

## Hardware Platform

```yaml
esp8266:
  board: esp01_1m
```

**esp8266:** Specifies you're using an ESP8266 microcontroller (not an ESP32 or other chip).

**board: esp01_1m** - Defines the specific board variant. The ESP-01 is a very small ESP8266 module with 1MB of flash memory. This tells ESPHome exactly which pins and memory configuration to use. Think of it like selecting your exact computer model so the software knows what hardware it's working with.

## Logging

```yaml
# Enable logging
logger:
```

**logger:** Enables logging functionality. This lets you see what the device is doing in real-time - like debug messages, errors, sensor readings, etc. It's similar to console output when you're coding. By default, logs are sent over the serial port and can also be viewed in the web interface.

## Home Assistant Integration

```yaml
# Enable Home Assistant API
api:
  encryption:
    key: ""
```

**api:** Enables the native API that allows Home Assistant to communicate with this device.

**encryption:** Adds security to the communication between Home Assistant and your device.

**key:** This is a unique encryption key (like a password, but for machines). It ensures that only devices/systems with this key can communicate with your ESP8266. Think of it as an API key that encrypts the data being sent back and forth.

## Over-The-Air Updates

```yaml
ota:
  - platform: esphome
    password: ""
```

**ota:** OTA stands for "Over-The-Air". This allows you to update the firmware on your ESP8266 wirelessly without physically connecting a USB cable.

**platform: esphome** - Specifies that you're using ESPHome's OTA update system.

**password:** A password required to upload new firmware. This prevents unauthorized people from uploading malicious code to your device over your network.

## WiFi Configuration

```yaml
wifi:
  networks:
    - ssid: !secret warfield_ssid     # Primary
      password: !secret warfield_password
      priority: 10
    - ssid: !secret gchq_ssid         # Secondary
      password: !secret gchq_password
      priority: 5
```

**wifi:** Main WiFi configuration section.

**networks:** You can configure multiple WiFi networks. The device will try to connect to whichever is available.

**ssid: !secret warfield_ssid** - SSID is your WiFi network name. The `!secret` part means "look up this value in the secrets.yaml file" instead of hardcoding it here. This is a security best practice - like using environment variables instead of hardcoding passwords in your code.

**password: !secret warfield_password** - The WiFi password, also stored in secrets.yaml.

**priority: 10** - Higher numbers = higher priority. The device will try to connect to the priority 10 network first. If that fails, it tries priority 5. Think of it like a ranked list of preferred networks.

## Fallback Access Point

```yaml
  # Enable fallback hotspot (captive portal) in case wifi connection fails
  ap:
    ssid: "Warfield Fallback Hotspot"
    password: "DgNoPJEvMrVK"
```

**ap:** Creates an Access Point mode (the device becomes a WiFi hotspot itself).

This is a safety feature. If the device can't connect to either of your configured WiFi networks, it creates its own WiFi network that you can connect to directly. This way, you're never locked out of the device. Think of it like a recovery mode.

**ssid:** The name of the fallback WiFi network the device creates.

**password:** The password to connect to that fallback network.

## Captive Portal

```yaml
captive_portal:
```

**captive_portal:** When you connect to the fallback hotspot, this creates a captive portal (like when you connect to hotel WiFi and get redirected to a login page). In this case, it redirects you to a configuration page where you can set up the WiFi again.

## Web Server

```yaml
# Web server for viewing sensor data directly (optional)
web_server:
  port: 80
```

**web_server:** Enables a built-in web server on the device.

**port: 80** - The standard HTTP port. This means you can type `http://warfield.local` in your browser and see a web page showing all your sensor data, device status, etc. It's like having a mini website running on your ESP8266.

## One-Wire Bus Setup

```yaml
one_wire:
  - platform: gpio
    pin: GPIO2
```

**one_wire:** Configures the One-Wire protocol. This is a communication protocol that allows multiple sensors to share a single data wire. Dallas DS18B20 temperature sensors use this protocol.

**platform: gpio** - Specifies that the one-wire bus is connected to a GPIO (General Purpose Input/Output) pin.

**pin: GPIO2** - The specific physical pin on the ESP8266 where you've connected the data line of your temperature sensors. All your DS18B20 sensors connect to this one pin (plus power and ground).

## Temperature Sensors

```yaml
sensor:
  - platform: dallas_temp
    name: "Flow Temperature"
    id: flow_temp
    index: 0
    update_interval: 30s
    accuracy_decimals: 1
```

**sensor:** Main section for all sensor configurations.

**platform: dallas_temp** - Specifies you're using Dallas/Maxim temperature sensors (DS18B20 chips).

**name: "Flow Temperature"** - The human-readable name that appears in Home Assistant and the web interface.

**id: flow_temp** - An internal identifier you can use to reference this sensor in other parts of the config (like automations or calculations).

**index: 0** - When you have multiple DS18B20 sensors on the same one-wire bus, each gets an index number (0, 1, 2, etc.). This tells ESPHome "this is the first sensor on the bus". Think of it like array indexing in programming.

**update_interval: 30s** - How often to read the temperature. Every 30 seconds, the ESP will query this sensor and get a new reading.

**accuracy_decimals: 1** - How many decimal places to display. "1" means you'll see temperatures like 21.5°C instead of 21.53°C.

```yaml
  - platform: dallas_temp
    name: "Return Temperature"
    id: return_temp
    index: 1
    update_interval: 30s
    accuracy_decimals: 1
```

This is the second temperature sensor, identical configuration to the first except:

**index: 1** - This is the second sensor on the one-wire bus. In your heating system, this measures the return temperature while index 0 measures the flow temperature.

## Uptime Sensor

```yaml
  - platform: uptime
    name: "Temperature Monitor Uptime"
    update_interval: 60s
```

**platform: uptime** - This is a built-in sensor that tracks how long the device has been running since its last reboot.

**name:** What it's called in Home Assistant.

**update_interval: 60s** - Updates every 60 seconds. You'll see values like "5 hours, 23 minutes" showing how long the device has been online without rebooting.

This is useful for monitoring device stability - if you see frequent restarts, you might have a power or software issue.

---

## Summary

This configuration creates a temperature monitoring device that:

1. Connects to your WiFi (with a backup network and fallback hotspot)
2. Reads two DS18B20 temperature sensors every 30 seconds
3. Sends data to Home Assistant via encrypted API
4. Hosts a web page where you can view the temperatures
5. Can be updated wirelessly
6. Tracks how long it's been running

The device measures the flow and return temperatures in a heating system, which helps you monitor heating efficiency - the temperature difference tells you how much heat is being delivered.
