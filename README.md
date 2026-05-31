# geysersteam-firmware
Adding http-OTA firmware update for ESP32-S3
1)Arduino Platform configure /tools/partition scheme ->> Default or Minimal spiffs or a partition with OTA(that will reserve the space for the new firmware to be dowloaded)
2)add ota_update.h to your folder or copy and paste on the Arduino top right, hit the 3 dots--> New Tab
3) Create the manifest.json file and the new device folder on the main by selecting add file and on the name  place the folder name and manifest.json "foldername/manifest.json"
4) On your code, add at the beginning "#include "ota_update.h"
6)In setup(), after serial connection add: Serial.println("Device ID: " + getDeviceID()); // to report ID based on MAC
and before setup() add:
// ─────────────────────────────────────────────────
// Get unique device ID from ESP32 MAC
// ─────────────────────────────────────────────────
String getDeviceID() {
  uint8_t mac[6];
  WiFi.macAddress(mac);
  char id[13];
  snprintf(id, sizeof(id), "%02X%02X%02X%02X%02X%02X",
           mac[0],mac[1],mac[2],mac[3],mac[4],mac[5]);
  return String(id);
}
and in Setup() after WiFi connects and discovery is done, add the first check:
cpp// Check for firmware update on boot (silent)
checkForUpdate(tft, false);  // false = show "checking..." in header
7) In the include libraries section, make sure you have:  
#include <Adafruit_GFX.h>
#include <Adafruit_GC9A01A.h>
#include <WiFi.h>
#include <WebServer.h>
#include <ESPmDNS.h>
#include <Preferences.h>
#include <WiFiManager.h>
#include <Adafruit_NeoPixel.h>
#include <OneWire.h>
#include <DallasTemperature.h>
#include <ESP32Encoder.h>
#include <HTTPClient.h>       // ← already there for your web server
#include <HTTPUpdate.h>       // ← add this
#include <WiFiClientSecure.h> // ← add this
#include <ArduinoJson.h>      // ← add this if not already there
#include "bg_image.h"
8)In loop(), add two things(for displays with touch):
void loop() {
  // Periodic background check every hour
  otaCheckIfDue(tft);

  // Handle tap on update banner
  int tx, ty;
  if (millis()-lastTouch>180 && getTouch(tx,ty)) {
    lastTouch = millis();

    // Check banner tap FIRST before normal touch handling
    if (otaAvailable && otaHandleBannerTap(tft, tx, ty)) {
      fullRedraw = true;  // redraw after update dialog
      return;
    }
      // Draw update banner if available (at bottom of screen)
  if (otaAvailable) drawOtaBanner(tft);

  drawUI();
}

    handleTouch(tx, ty);
  }
for non touch display:
9)after WiFi connects in Setup() add:
if (wifiConnected) {
  checkForUpdate();
}
