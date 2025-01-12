# mIoT_core

ESP-based smart home software designed for customizable IoT devices and home automation. This project provides a flexible platform for creating and managing smart devices with a web-based control interface.

## Quick Start

1. **Setup**
   - Install VSCode + PlatformIO
   - Clone repository
   - Flash firmware
   - Connect to device AP (mIoT_#####, pw: setup123)
   - Configure at http://192.168.4.1

2. **Configuration**
   - Use ESPFSSync to upload website
   - Configure device settings
   - Push configuration files

See the [Development Guide](/docs/development/readme.md) for detailed setup instructions.

## Documentation

### Core Features
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
* Work in feature branches off `dev`
* Submit pull requests to `dev`
* Stable releases tagged from `master`

See the [Development Guide](/docs/development/readme.md) for detailed contribution guidelines.

## License
See [LICENSE](LICENSE) file
