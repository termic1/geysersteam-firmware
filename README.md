# geysersteam-firmware
Adding http-OTA firmware update for ESP32-S3
1)Arduino Platform configure /tools/partition scheme ->> Default or Minimal spiffs or a partition with OTA(that will reserve the space for the new firmware to be dowloaded)
2)add ota_update.h to your folder or copy and paste on the Arduino top right, hit the 3 dots--> New Tab
