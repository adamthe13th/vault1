# Screen wiring
## ESP32 side
apparently by default when not specified the esp will use VSPI 

|          |          |          |          |         |
| -------- | -------- | -------- | -------- | ------- |
| **SPI**  | **MOSI** | **MISO** | **SCLK** | **CS**  |
| **VSPI** | GPIO 23  | GPIO 19  | GPIO 18  | GPIO 5  |
| **HSPI** | GPIO 13  | GPIO 12  | GPIO 14  | GPIO 15 |
![[Pasted image 20250907190440.png]]
``` cpp
#include <Adafruit_GFX.h>
#include <Adafruit_ST7789.h>
#include <SPI.h>

// Define pins
#define TFT_CS     5   // or -1 if no CS pin
#define TFT_RST    4
#define TFT_DC     2

// Hardware SPI pins on ESP32: SCK=18, MOSI=23
Adafruit_ST7789 tft = Adafruit_ST7789(TFT_CS, TFT_DC, TFT_RST);

void setup() {
  Serial.begin(115200);
  Serial.println("ST7789 Test");

  tft.init(240, 240);   // Change to 240x320 if your display is that size
  tft.setRotation(2);   // Adjust rotation if text is upside down
  tft.fillScreen(ST77XX_BLACK);

  tft.setTextSize(2);
  tft.setTextColor(ST77XX_WHITE);
  tft.setCursor(20, 50);
  tft.println("Hello ESP32!");

  tft.setTextColor(ST77XX_GREEN);
  tft.setCursor(20, 100);
  tft.println("ST7789 Display");
}

void loop() {
  // Nothing for now
}
```

