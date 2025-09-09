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

# for send and receive using OLED & C3
```cpp
#include <esp_now.h>
#include <WiFi.h>
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

// ===== OLED CONFIG =====
#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, -1);

// ===== PEER CONFIG =====
// Replace with the MAC address of the *other ESP32/ESP32-C3*
uint8_t peerAddress[] = {0x24, 0x6F, 0x28, 0xAB, 0xCD, 0xEF};

// 16-byte encryption key (same on all devices)
uint8_t lmk[16] = { 0x01, 0x02, 0x03, 0x04,
                    0x05, 0x06, 0x07, 0x08,
                    0x09, 0x0A, 0x0B, 0x0C,
                    0x0D, 0x0E, 0x0F, 0x10 };

// ===== MESSAGE BUFFERS =====
String incomingMessage = "";

// ===== CALLBACK: Data Sent =====
void OnDataSent(const uint8_t *mac_addr, esp_now_send_status_t status) {
  Serial.print("Send Status: ");
  Serial.println(status == ESP_NOW_SEND_SUCCESS ? "Success" : "Fail");
}

// ===== CALLBACK: Data Received =====
void OnDataRecv(const uint8_t * mac, const uint8_t *incomingData, int len) {
  incomingMessage = String((char*)incomingData);
  Serial.print("Received: ");
  Serial.println(incomingMessage);

  // Display on OLED
  display.clearDisplay();
  display.setTextSize(1);
  display.setTextColor(SSD1306_WHITE);
  display.setCursor(0,0);
  display.println("Received:");
  display.println(incomingMessage);
  display.display();
}

void setup() {
  Serial.begin(115200);

  // ===== OLED INIT =====
  if(!display.begin(SSD1306_SWITCHCAPVCC, 0x3C)) {
    Serial.println("SSD1306 allocation failed");
    for(;;);
  }
  display.clearDisplay();
  display.setTextSize(1);
  display.setTextColor(SSD1306_WHITE);
  display.setCursor(0,0);
  display.println("Ready...");
  display.display();

  // ===== WIFI INIT =====
  WiFi.mode(WIFI_STA);

  // ===== ESP-NOW INIT =====
  if (esp_now_init() != ESP_OK) {
    Serial.println("Error initializing ESP-NOW");
    return;
  }
  
  esp_now_register_send_cb(OnDataSent);
  esp_now_register_recv_cb(OnDataRecv);

  // ===== ADD PEER (with encryption) =====
  esp_now_peer_info_t peerInfo = {};
  memcpy(peerInfo.peer_addr, peerAddress, 6);
  peerInfo.channel = 0;
  peerInfo.encrypt = true;
  memcpy(peerInfo.lmk, lmk, 16);

  if (esp_now_add_peer(&peerInfo) != ESP_OK) {
    Serial.println("Failed to add peer");
    return;
  }
}

void loop() {
  // Check for Serial input to send
  if (Serial.available()) {
    String message = Serial.readStringUntil('\n');
    esp_now_send(peerAddress, (uint8_t *)message.c_str(), message.length() + 1);

    // Also show what we sent on the OLED
    display.clearDisplay();
    display.setTextSize(1);
    display.setTextColor(SSD1306_WHITE);
    display.setCursor(0,0);
    display.println("Sent:");
    display.println(message);
    display.display();
  }
}
```
