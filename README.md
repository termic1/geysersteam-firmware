# geysersteam-firmware
Adding http-OTA firmware update for ESP32-S3
1)Arduino Platform configure /tools/partition scheme ->> Default or Minimal spiffs or a partition with OTA(that will reserve the space for the new firmware to be dowloaded)
2)add ota_update.h to your folder or copy and paste on the Arduino top right, hit the 3 dots--> New Tab
3) Create the manifest.json file and the new device folder on the main by selecting add file and on the name  place the folder name and manifest.json "foldername/manifest.json"
4) On your code, add at the beginning "#include "ota_update.h"
6)In setup(), after WiFi connects and discovery is done, add the first check:
cpp// Check for firmware update on boot (silent)
checkForUpdate(tft, false);  // false = show "checking..." in header
7)In loop(), add two things:
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
