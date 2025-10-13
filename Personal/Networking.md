# Single packet struct
## elements
- sender (6 byte mac)
- destination (6 byte mac)
- Time (8 bytes unsigned long)
- packet number (char)
- total packets (char)
- Content Type (Char)
- Content (233 bytes)
>[!question] should I use this struct for strings to or do the default string for esp_now



