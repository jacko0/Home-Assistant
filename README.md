# ESP32 Configurations and Device Setups

The ESP32 is a powerful microcontroller with integrated Wi-Fi and Bluetooth capabilities. Here are some important configurations and setup instructions:

## Prerequisites
- Ensure you have the ESP32 development board.
- Install the Arduino IDE or PlatformIO.

## Basic Setup
1. **Connecting ESP32**: Connect your ESP32 board to your computer using a USB cable.
2. **Installing Board Support**: In the Arduino IDE, go to `File` -> `Preferences`, and add the following URL to the `Board URL` field:
   ```plaintext
   https://dl.espressif.com/dl/package_esp32_index.json
   ```
3. **Select Board**: Navigate to `Tools` -> `Board` -> `ESP32 Arduino` and select your board model.

## Wi-Fi Configuration
To configure Wi-Fi on your ESP32, use the following code:
```cpp
#include <WiFi.h>

const char* ssid = "your_SSID";
const char* password = "your_PASSWORD";

void setup() {
  Serial.begin(115200);
  WiFi.begin(ssid, password);

  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.println("Connecting to WiFi...");
  }
  Serial.println("Connected to WiFi");
}

void loop() {
  // Your code here
}
```

## Bluetooth Setup
To enable Bluetooth capabilities, include the following library:
```cpp
#include <BluetoothSerial.h>
```