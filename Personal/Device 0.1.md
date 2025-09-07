# Screen wiring
## ESP32 side
apparently by default when not specified the esp will use VSPI 

|          |          |          |          |         |
| -------- | -------- | -------- | -------- | ------- |
| **SPI**  | **MOSI** | **MISO** | **SCLK** | **CS**  |
| **VSPI** | GPIO 23  | GPIO 19  | GPIO 18  | GPIO 5  |
| **HSPI** | GPIO 13  | GPIO 12  | GPIO 14  | GPIO 15 |