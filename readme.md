# mIoT_core

ESP-based smart home software designed for customizable IoT devices and home automation. This project provides a flexible platform for creating and managing smart devices with a web-based control interface.

## Quick Start

1. **Setup**
   - Install VSCode + PlatformIO
   - Clone repository
   - Flash firmware
   - Connect to device AP (mIoT_#####, pw: setup123)
   - Configure at http://192.168.4.1

<a href="/assets/device_initialisation.png">
  <img src="/assets/device_initialisation.png" alt="Device Initialisation Screen" style="max-height: 400px; width: auto;">
</a><br/>
*The current device initialisation screen.*

2. **Configuration**
   - Use ESPFSSync to upload website
      (option 1)
   - Configure device settings
      (use option 4 to create a local folder)
   - Push configuration files
      (option 3)
   - Reboot
      (option 6)

See the [Development Guide](/docs/development/readme.md) for detailed setup instructions.

## Documentation

### Core Features
* [AC Control](/ac/readme.md) - Mitsubishi heat pump integration and control
* [Analog](/analog/readme.md) - Sensors and variable voltage control
* [Digital](/digital/readme.md) - Buttons, switches, and relays
* [PWM LED](/pwm/readme.md) - LED brightness with RGBWCO support
* [RGB LED](/rgb/readme.md) - Addressable LED strips and patterns
* [Timers](/timer/readme.md) - Scheduled events and intervals
* [Variables](/variables/readme.md) - State management and calculations

### System Reference
* [Development Guide](/docs/development/readme.md) - Build and contribute
* [API Reference](/docs/api/readme.md) - HTTP endpoints
* [Hardware Guide](/docs/hardware/readme.md) - Supported boards and setup
* [Enums](/enums/readme.md) - System enumerations
* [Structs](/structs/readme.md) - Data structures
* [Changelog](/changelog/readme.md) - Version history

## Project Components

### ESP_IoT_Core
Main microcontroller code for ESP-based boards (C++)
- Core functionality and hardware abstraction
- Network communication and event handling
- Feature implementations

### DeviceWebsite
Command and control website deployed to each device
- HTML/JS/CSS interface
- Device configuration and control
- Status monitoring

<a href="/assets/desktop_main_screen_ac.png">
  <img src="/assets/desktop_main_screen_ac.png" alt="Desktop Interface" style="max-height: 400px; width: auto;">
</a><br/>
*Desktop view showing AC control and device status*

<a href="/assets/mobile_view_rgb.png">
  <img src="/assets/mobile_view_rgb.png" alt="Mobile Interface" style="max-height: 400px; width: auto;">
</a><br/>
*Mobile view showing RGB LED control*

<a href="/assets/settings_screen.png">
  <img src="/assets/settings_screen.png" alt="Configuration Interface" style="max-height: 400px; width: auto;">
</a><br/>
*Device configuration interface*


### Configs
Json based configuration files for device management
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

<a href="/assets/sync_tool.png">
  <img src="/assets/sync_tool.png" alt="ESPFSSync Tool" style="max-height: 400px; width: auto;">
</a><br/>
*ESPFSSync tool for firmware, file system and configuration management*

### IoTHome
Central hub web application (.NET Core)
- Device management
- Automation control
- Status monitoring
- Event coordination

<a href="/assets/WIP_dashboard_screen.png">
  <img src="/assets/WIP_dashboard_screen.png" alt="Hub dashboard" style="max-height: 400px; width: auto;">
</a><br/>
*Work in progress: Hub dashboard for easily controlling many devices.*

## Contributing
* Work in feature branches off `dev`
* Submit pull requests to `dev`
* Stable releases tagged from `master`

<a href="/assets/dev_environment.png">
  <img src="/assets/dev_environment.png" alt="Development Environment" style="max-height: 400px; width: auto;">
</a><br/>
*VSCode with PlatformIO setup for mIoT development*

See the [Development Guide](/docs/development/readme.md) for detailed contribution guidelines.

## License
See [LICENSE](LICENSE) file
