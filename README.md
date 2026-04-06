# Mini Weather Station using Arduino, DHT11 and OLED

A simple mini weather station built with **Arduino Uno**, **DHT11 temperature & humidity sensor**, and a **128×64 I2C OLED display**.  
It measures room temperature and humidity and shows the values in real time on the OLED screen.

---

## Features

- Reads **temperature (°C)** and **humidity (%)** using DHT11  
- Displays data on a **128×64 SSD1306 I2C OLED**  
- Prints readings to the **Serial Monitor** for debugging  
- Built on a breadboard, easy to reproduce and modify

---

## Components

- Arduino Uno Rev3  
- DHT11 sensor module (3‑pin: VCC, GND, DATA)  
- 128×64 OLED display (I2C, 4‑pin: VCC, GND, SCL, SDA)  
- Breadboard  
- Jumper wires (male–male and male–female)  
- USB A‑to‑B cable

---

## Circuit Connections

### Power

- Arduino 5V → Breadboard + rail  
- Arduino GND → Breadboard − rail  

### DHT11 (3‑pin module)

- DHT11 VCC → Breadboard + (5 V)  
- DHT11 GND → Breadboard − (GND)  
- DHT11 DATA → Arduino **digital pin 2**

### OLED (SSD1306, I2C, 4‑pin)

- OLED VCC → Breadboard + (5 V)  
- OLED GND → Breadboard − (GND)  
- OLED SCL → Arduino **A5 (SCL)**  
- OLED SDA → Arduino **A4 (SDA)**  

The DHT11 and OLED share the same 5V and GND rails and use different signal pins.

---

## Libraries

Install via **Arduino Library Manager**:

- `DHT sensor library` (Adafruit)  
- `Adafruit Unified Sensor`  
- `Adafruit SSD1306`  
- `Adafruit GFX Library`

---

## Finding the OLED I2C Address

To detect the OLED I2C address, upload an I2C scanner sketch.  
In this project, the scanner reported:

```text
I2C device found at address 0x3C
```

So the code uses `0x3C` in `display.begin()`.

---

## Arduino Code

Create `weather_station.ino` and paste:

```cpp
#include "DHT.h"
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, -1);

#define DHTPIN 2
#define DHTTYPE DHT11
DHT dht(DHTPIN, DHTTYPE);

void setup() {
  Serial.begin(9600);
  dht.begin();

  if(!display.begin(SSD1306_SWITCHCAPVCC, 0x3C)) {  // I2C address
    Serial.println("OLED not found!");
    for(;;);
  }

  display.clearDisplay();
  display.setTextSize(1);
  display.setTextColor(SSD1306_WHITE);
  display.setCursor(0,0);
  display.println("Weather Station");
  display.display();
  delay(2000);
}

void loop() {
  float h = dht.readHumidity();
  float t = dht.readTemperature();

  Serial.print("Humidity: ");
  Serial.print(h);
  Serial.print(" %  Temp: ");
  Serial.print(t);
  Serial.println(" C");

  display.clearDisplay();

  display.setTextSize(2);
  display.setCursor(0,0);
  display.print("T:");
  display.print(t);
  display.println("C");

  display.setTextSize(2);
  display.setCursor(0,30);
  display.print("H:");
  display.print(h);
  display.println("%");

  display.display();
  delay(2000);
}
```

---

## How It Works

1. The Arduino powers the DHT11 and OLED from the 5 V and GND rails.  
2. In `setup()`, the DHT11 sensor and OLED display are initialized.  
3. A startup message “Weather Station” is shown on the OLED.  
4. In `loop()`, the DHT11 readings (`readTemperature()` and `readHumidity()`) are taken every 2 seconds.  
5. The values are printed to Serial Monitor and displayed on the OLED in large font.

---

## Usage

1. Build the circuit according to the connections above.  
2. Install the required libraries.  
3. Upload `weather_station.ino` to the Arduino Uno.  
4. Open Serial Monitor (9600 baud) to see readings.  
5. Check the OLED for live **temperature** and **humidity**.

---

## Future Improvements

- Track and display min/max temperature and humidity  
- Add LED or buzzer alerts for threshold values  
- Add a real-time clock (RTC) to show time/date  
- Port to ESP8266/ESP32 and upload data to the cloud

---

## License

Add your preferred license here (e.g. MIT, or “All rights reserved”).
