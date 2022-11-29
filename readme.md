# mIoT_core
My own ESP-based smart home software
It is very much for my own use and will be a constant work in progress.	

## Note to developers
Please work in feature branches off dev and send me a pull request back to dev. I'll tag stable builds off the master branch as needed.

## Project Structure
This project contains 3 separate components:
* **ESP_IoT_Core** is the main microcontroller code for ESP-based (ESP8266, ESP8285, ESP32) boards (C++)
* **DeviceWebsite** is the command and control website deployed to each individual device (HTML, JS, CSS)
* **ESPFSSync** is a VS2019 console app used to perform OTA functions. E.g. update the file system or firmware on a live device. (C#)
* **IoTHome** is a VS2019 .net Core web application designed to run on an RPi3 (or better) and act as a central hub. (C#)


## Dependencies
### ESP_IoT_Core
I'm using **Arduino Studio v1.8.18** but later v1 versions should work with the same instructions. The following instructions assume you're doing the same.

You'll need to add the ESP boards to Arduino Studio if you haven't already by going to preferences and adding the all the following urls to the "Additional Boards Manager URLs" (comma separated)
https://dl.espressif.com/dl/package_esp32_index.json,
http://arduino.esp8266.com/stable/package_esp8266com_index.json,
https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package_esp32_dev_index.json

In the board manager add esp8266 version 3.0.2 and esp32 version 2.0.2
ESP32 and ESP32S2 is all a work in progress / untested / not working at the moment.


The following libraries are required (installed via the Arduino Studio library manager)
* WiFi (tested with 1.2.7)
* AccelStepper (tested with 1.61.0)
* ArduinoJson (tested with 6.18.5)
* Effortless-SPIFFS (2.1.4)
* ESPFlash (tested with 1.0.0)
* NeoPixelBus (tested with 2.6.9)
* QList (tested with 0.6.7)
* ESP32httpUpdate (2.1.145)
* ESP32 AnalogWrite (0.1.0)

Additionally, you need to manually add the following to your libraries: 
* ESPAsyncWebServer @ https://github.com/me-no-dev/ESPAsyncWebServer
* ESPAsyncTCP @ https://github.com/me-no-dev/ESPAsyncTCP

I've been downloading the master branch as a zip from github and using Sketch -> Include Library -> Add ZIP library in Arduino studio to add them to the right place.

## Building
Opening the ESP_IoT_core.ino file in Arduino Studio with all the above dependencies installed should be all you need to do. Set up the board as below (not an exhaustive list), select the correct COM port and click the Upload button (You'll need to short IO0 to Gnd on boot to go into the bootloader before you'll be able to upload new firmware over serial).

Once the firmware is running on the board, you should be able to see a new WiFi access point called mIoT_##### where the 5 digit number is randomly generated. Connect to this with the default password **setup123** on your phone. Then browse to http://192.168.4.1 you should see the basic setup page where you can specify a WiFi network you want the device to join and provide the password. On reboot the device should connect to the specified network.

If it is a new device, there will be no hardware configured and no website deployed.
To start with, open the ESPFSSync tool as Administrator and type **scan** to search for devices on the local network using a broadcast,(you have to be on the same wifi network). It should give you the IP address of any devices that respond. You may need to allow port 80 inbound on your firewall to get the responses.
- Type in the IP address of the device you just flashed to select the device.
- On the main device menu select the first option to upload the website files.
- Browse to the device on the same IP using your browser.
- Go to the settings page and fill out the details.
	- The trusted hub address needs to start with "http://" and is the only location the device will accept a firmware file from.
- Usually I  select the fourth option to download the configs (to create the local folder named for the device ID)
- Modify and create the config files in the local folder
- Use option three to push the configs to the device and type "Y" to reboot it afterwards.

## Board Setup (Tuya and some other standard modules)
It's important that you consistently select the same filesystem layout for a board otherwise it will map incorrectly and after you update, all the files that were on the filesystem are no longer there. For this reason, I've been making notes in defines.h to remind me what to set for different boards. /api/status gives FS info and if you deploy the default website, there is a readout of the current filesystem layout at the top of the settings page on the module's website.

### Hardware (board selection in arduino studio):
    * Modules:
      - Generic ESP8266 (2MB) - FS:1MB, OTA:512KB
      - Generic ESP8266 (4MB) - FS:3MB, OTA:512KB
      - Generic ESP8285 (1MB) - FS:64KB, OTA:470KB - No website support
      - Generic ESP8285 (2MB) - FS:1MB, OTA:512KB

    * Tested Boards:
      - ESP-12E (4MB) - use Generic ESP8266
      - ESP-12F (4MB) - use Generic ESP8266
      - ESP-12S (4MB) - use Generic ESP8266
      - ESP-02S Module (2MB) - use Generic ESP8285
      - ESP-02M Module (2MB) - use Generic ESP8285
      - WeMos D1 Mini (4MB) - use LOLIN(WEMOS) D1 R2 & mini
      - Tuya TYWE3S (2MB) - use Generic ESP8266
      - Tuya TYWE2S (1MB) - use Generic ESP8285
      - Tuya TYWE2L (1MB) - use Generic ESP8285
      - DMP-L1 (1MB) - use Generic ESP8285

  * File system layout for different flash sizes:
    - 8M (1MB) - FS:64KB, OTA:470KB - No website support
    - 16M (2MB) - FS:1MB, OTA:512KB
    - 32M (4MB) - FS:3MB, OTA:512KB

## HTTP APIs
A quick reference for APIs that can be called on the devices. Most calls return a json object that represents a the state of the device after the action is performed.
* GET **/api/status** - Explicitly retrieve the current state of the device as a json object without performing any action.
* GET **/api/reboot** - Reboot the device.
* GET **/api/reset** - Clear the wifi configuration (the device will revert to AP mode).
* GET **/api/resetall** - Clear all configuration (the device will have no timers, events, or hardware configured and will revert to AP wireless mode).
* GET **/api/rgb?pattern=**`<enums.h:LedPatterns>`**&colour=**`<#RRGGBB>` - (colour is optional) - Set the WS2812 LED strip to the selected pattern.
* GET **/api/digital?name=**`<digitalPinConfig:name>`**&action=**`<on|off|toggle>` - Perform the chosen action on a digital pin device selected by the name parameter
* GET **/api/timer?name=**`<timerConfig:name>`**&action=**`<start|stop|startorreset>` - Perform the chosen action on the timer selected by name. The **startorreset** action will start the timer if it is currently stopped, or if it is already running the elapsed time with be reset to 0.
* GET **/api/event?name=**`<eventsConfig:name>` - Trigger the events configured in the EventSet specified by name. This will still evaluate configured conditions before triggering.
* GET **/api/variable?name=`<variableConfig:name>`&op=`<enums.h:MathOperator>`&value=`<integer>`** - Do the operation with the value to the variable's current value.
* GET **/fs/list** - Return a json array containing a list of all files on the filesystem.
* GET **/fs/get?filename=**`<string>` - Return the specified file from the filesystem.
* GET **/fs/delete?filename=**`<string>` - Delete a file on the device.
* POST **/fs/set?filename=**`<string>` - Write the posted data as a file with the specified name. If the file already exists it will be overwritten.

## Enumerations
Because the core is written in C++ which isn't so good with reflection or knowing the text names of it's types, you'll need to define enum values in the json configs using their number. I have included a short commonly used list of them here, but the rest are in **enums.h** in the code.

### EventTypes
None = 0 \
DigitalPin = 1 \
Url = 2 \
RGB = 3 \
Timer = 4 \
AnalogPin = 5 \
EventSet = 6 \
Variable = 7

### LedPatterns
Off = 0 \
Solid = 1 \
RainbowCycle = 2 \
Pulse = 3 \
ColourCycle = 4 \
Toggle = 99

### ComparisonOperator
Nop = 0 \
GreaterThan = 1 \
GreaterThanOrEqual = 2 \
EqualTo = 3 \
LessThanOrEqual = 4 \
LessThan = 5 \
NotEqualTo = 6

### MathOperator
Nop = 0 \
Delete = 1 \
Equals = 2 \
Plus = 3 \
Minus = 4 \
Multiply = 5 \
Divide = 6 \
Modulus = 7

## Things to document better
* defines.h: contains a changelog and some preprocessor defines that change the build of the MCU code.
* Hardware reset: If a digital pin device named button1 is pulled high for at least 3 seconds on boot, and then low within 3 seconds after that a lite (/api/reset) reset is performed.
* Document the configuration files, what options they have, what they mean and how to push them.
* Setting up the ESPFSSync tool (putting the website in the correct folder, creating deviceConfigs and firmware folders).