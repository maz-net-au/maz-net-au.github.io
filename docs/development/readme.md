# Development Guide

## Build System

### PlatformIO (Recommended)
1. Install VSCode and PlatformIO extension
2. Pull down the repository
3. Open project in VSCode
4. Use PlatformIO to upload to board

<a href="/assets/dev_environment.png">
  <img src="/assets/dev_environment.png" alt="Development Environment" style="max-height: 400px; width: auto;">
</a><br/>
*VSCode with PlatformIO setup for mIoT development*

### Arduino Studio (Alternative)

<details>

If you prefer Arduino Studio v1.8.18+:

1. **Board Manager URLs**
   ```
   https://dl.espressif.com/dl/package_esp32_index.json
   http://arduino.esp8266.com/stable/package_esp8266com_index.json
   https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package_esp32_dev_index.json
   ```

2. **Required Board Packages**
   - esp8266 version 3.0.2
   - esp32 version 2.0.2

3. **Required Libraries**
   * WiFi (1.2.7+)
   * AccelStepper (1.61.0+)
   * ArduinoJson (6.18.5+)
   * Effortless-SPIFFS (2.1.4+)
   * ESPFlash (1.0.0+)
   * NeoPixelBus (2.6.9+)
   * QList (0.6.7+)
   * ESP32httpUpdate (2.1.145+)
   * ESP32 AnalogWrite (0.1.0+)

4. **Manual Library Additions**
   * [ESPAsyncWebServer](https://github.com/me-no-dev/ESPAsyncWebServer)
   * [ESPAsyncTCP](https://github.com/me-no-dev/ESPAsyncTCP)
   
   Download as ZIP and use Sketch -> Include Library -> Add ZIP library
</details>

## Project Structure

### ESP_IoT_Core
Main microcontroller code for ESP-based boards (C++)
- Core functionality
- Hardware abstraction
- Network communication
- Event handling

### DeviceWebsite
Command and control website deployed to each device
- HTML/JS/CSS interface
- Device configuration
- Real-time control
- Status monitoring

### Configs
Configuration file storage and management
- Device settings
- Network configuration
- Hardware setup
- Event definitions

### ESPFSSync
OTA functionality console application (C#)
- File system updates
- Firmware updates
- Configuration management
- Device discovery

### IoTHome
Central hub web application (.NET Core)
- Device management
- Automation control
- Status monitoring
- Event coordination

## Contributing

1. **Branch Management**
   - Create feature branches from `dev`
   - Submit pull requests back to `dev`
   - Stable releases tagged from `master`

2. **Code Standards**
   - Follow existing code style
   - Document new features
   - Update changelog
   - Test on supported hardware

3. **Testing**
   - Verify on multiple board types
   - Test all affected features
   - Check memory usage
   - Validate OTA updates

## Debugging
1. **Local Debugging**
   - Enable `mIoT_SERIALDEBUG` in build
   - Access logs via serial
   - Monitor device status

2. **Remote Debugging**
   - Enable `mIoT_REMOTEDEBUG` in build
   - Access logs via `/api/logs`
   - Monitor device status

3. **Common Issues**
   - Filesystem corruption
   - Network connectivity
   - Memory limitations
   - Hardware compatibility

4. **Tools**
   - Serial monitor
   - ESPFSSync diagnostics
   - Web interface debugging
   - Network scans
