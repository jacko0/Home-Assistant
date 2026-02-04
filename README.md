# Jacko's Home Assistant Configurations

Personal configuration files and ESPHome YAML definitions for my Home Assistant instance.

## Repository Structure

- **ESP32/**  
  ESPHome configurations for various ESP32-based devices (sensors, relays, etc.).  
  These YAML files are compiled and flashed to ESP32 boards to integrate custom hardware into Home Assistant.

- **Raspberry-Pi-Pico/** (or Pico/)  
  Configurations, scripts, or MicroPython code related to Raspberry Pi Pico boards used in Home Assistant projects.

- **.gitignore**  
  Standard ignore patterns (including `.DS_Store`, secrets, caches).

- **.gitattributes** (optional)  
  Line-ending normalisation settings (can be safely removed if not required).

## Usage

1. **ESPHome devices**  
   - Navigate to the `ESP32/` folder.  
   - Each `.yaml` file represents one device (filename typically matches the `name:` field for clarity).  
   - Example workflow:  
     ```bash
     esphome config.yaml validate
     esphome config.yaml run --device 192.168.x.x   # or .local hostname
