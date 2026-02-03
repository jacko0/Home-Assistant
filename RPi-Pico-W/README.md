# Jacko's ESPHome Pi (RPi-Pico-W)

ESPHome configuration and documentation for a Raspberry Pi Pico W running ESPHome with:
- BME280 (temperature/humidity/pressure) on I2C
- SSD1306 128x64 OLED (I2C)
- Onboard LED configured as a controllable light

Config file: https://github.com/jacko0/pi/blob/4331fa0a7b97982258bc05a8a2756a25a83836e2/esphome-web-00680a.yaml

---

## Contents

- Hardware
- Wiring / Pinout
- Features
- Configuration (secrets & values)
- Flashing & running with ESPHome
- Home Assistant integration / entity names
- Troubleshooting
- Customization
- Contributing & License

---

## Hardware

- Raspberry Pi Pico W (RP2040)
- BME280 environmental sensor (I2C)
- SSD1306 128x64 OLED display (I2C)
- USB cable for flashing / power

---

## Wiring / Pinout

The YAML config uses the following I2C pins for the Pico W:

- SDA: GPIO4
- SCL: GPIO5

I2C device addresses used in this config:
- BME280: `0x76` (change to `0x77` if your module uses that address)
- SSD1306 OLED: `0x3C`

Onboard LED:
- Configured via `output` in the YAML on pin `32` and exposed as a light named "Onboard LED" (see the YAML for mapping details).

---

## Features

- BME280 provides temperature, humidity, and pressure readings (update interval: 60s).
- SSD1306 displays time, date, and sensor readings (refresh every 30s).
- Onboard LED exposed as a controllable light (and toggled once per second by an interval in the config).
- SNTP time synchronized to `Europe/London` timezone.
- API enabled for Home Assistant integration.
- OTA updates enabled (platform: `esphome`).

---

## Configuration

Secrets (create a `secrets.yaml` file in the same directory as the YAML or configured for your ESPHome environment):

```yaml
# secrets.yaml
wifi_ssid: "your-wifi-ssid"
wifi_password: "your-wifi-password"
```

Key items in the config (see the linked YAML for complete details):
- `esphome.name`: `esphome-web-00680a`
- Time: SNTP with id `ha_time`, timezone `Europe/London`
- Font: Roboto (gfonts)
- Display: SSD1306 128x64, address `0x3C`
- BME280: address `0x76`, update_interval `60s`
- Logger: periodic custom logs every 5s for temperature/humidity/pressure

---

## Flashing & Running

Prerequisites:
- Install ESPHome (CLI or Dashboard). See https://esphome.io for installation instructions.

Common commands (from the directory containing `esphome-web-00680a.yaml`):

- Validate / compile:
  ```
esphome compile esphome-web-00680a.yaml
  ```

- Flash via USB (first time):
  ```
esphome run esphome-web-00680a.yaml
  ```
  This will compile and attempt to upload. If the device is connected via a serial adapter, specify the device path:
  ```
esphome run esphome-web-00680a.yaml --device /dev/ttyUSB0
  ```

- View logs:
  ```
esphome logs esphome-web-00680a.yaml
  ```

- Once initially flashed with network credentials, OTA updates are available via the ESPHome dashboard or `esphome upload --device <hostname_or_ip> ...`.

---

## Home Assistant Integration

The YAML enables the native ESPHome API, so the device should be automatically discovered by Home Assistant if both are on the same network and API is enabled in HA.

Main entity names (defined in the YAML):
- Sensor: `Pico W Temperature` (entity id will be `sensor.pico_w_temperature`)
- Sensor: `Pico W Humidity`
- Sensor: `Pico W Pressure`
- Light: `Onboard LED` (light entity)

Note: Entity IDs may include the device name and can differ if you change `friendly_name` or sensor names in the config.

---

## Display Layout

The OLED uses a small Roboto font and prints:
- Time (HH:MM:SS)
- Date (DD/MM/YYYY)
- Temperature (°C)
- Humidity (%)
- Pressure (hPa)

Update interval for the display is set to 30s in the YAML. The displayed values reference sensor IDs: `pico_temp`, `pico_hum`, and `pico_pressure`.

---

## Troubleshooting

- No Wi-Fi / can't connect:
  - Verify `secrets.yaml` values.
  - Check logs with `esphome logs`.
- I2C devices not detected:
  - Double-check wiring (SDA/SCL).
  - Try enabling I2C scan (set `scan: true`) or run an I2C scan from ESPHome to determine addresses.
  - Confirm BME280 address (`0x76` or `0x77`) and SSD1306 address (`0x3C`).
- Display garbage / incorrect text:
  - Ensure `model: "SSD1306 128x64"` matches your hardware.
  - Check the font inclusion (gfonts) and font ID used in the display lambda.
- OTA failing:
  - Ensure initial flash was done over USB at least once and device is reachable on the network.

---

## Customization

- Change update intervals for sensors or display by editing `update_interval`.
- Modify timezone under the `time:` component.
- Change pins for I2C or the LED to match different wiring or board variants.
- Replace the font or tweak the display lambda to change layout or add icons.

---

## Contributing

PRs and improvements welcome. Suggested changes:
- Add a wiring diagram for the physical assembly.
- Add build and deployment GitHub Actions for automatic compilation.
- Add alternate configs for different BME280 addresses or display sizes.

---

## License

This repository does not specify a license. Add a `LICENSE` file if you want to open-source this project under a specific license.

