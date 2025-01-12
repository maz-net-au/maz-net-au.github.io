# Hardware Guide

## Supported Boards

### ESP8266 Based (4MB)
- **ESP-12E/F/S**
  - FS: 3MB
  - OTA: 512KB
  - Full website support
  - All features available

- **WeMos D1 Mini**
  - FS: 3MB
  - OTA: 512KB
  - Full website support
  - Recommended for development

### ESP8285 Based (2MB)
- **ESP-02S Module**
  - FS: 1MB
  - OTA: 512KB
  - Website support
  - Standard features

- **ESP-02M Module**
  - FS: 1MB
  - OTA: 512KB
  - Website support
  - Standard features

- **Tuya TYWE3S**
  - FS: 1MB
  - OTA: 512KB
  - Website support
  - Common in smart devices

### Limited Memory (1MB)
- **Tuya TYWE2S**
  - FS: 64KB
  - OTA: 470KB
  - No website support
  - Basic features only

- **Tuya TYWE2L**
  - FS: 64KB
  - OTA: 470KB
  - No website support
  - Basic features only

- **DMP-L1**
  - FS: 64KB
  - OTA: 470KB
  - No website support
  - Basic features only

## Memory Configurations

### Flash Size Layouts
- **8M (1MB)**
  - FS: 64KB
  - OTA: 470KB
  - No website support

- **16M (2MB)**
  - FS: 1MB
  - OTA: 512KB
  - Website support

- **32M (4MB)**
  - FS: 3MB
  - OTA: 512KB
  - Full feature support

## Pin Configurations

### Digital Pins
- Input pins support:
  - Buttons
  - Switches
  - Sensors
  - Pull-up/down configuration

- Output pins support:
  - Relays
  - LEDs
  - Motors
  - Level shifters

### Analog Pins
- Input pins (ADC):
  - Voltage sensing
  - Light sensors
  - Temperature sensors
  - Position sensors

- Output pins (PWM):
  - LED dimming
  - Motor speed control
  - Signal generation
  - Voltage control

### Special Function Pins
- **I2C**
  - SDA
  - SCL
  - Device communication

- **SPI**
  - MOSI
  - MISO
  - SCK
  - CS

- **Serial**
  - TX
  - RX
  - Debug communication

## Hardware Setup

### Initial Configuration
1. Power requirements
   - 3.3V operation
   - Clean power supply
   - Adequate current

2. Flash mode
   - GPIO0 to GND for flashing
   - Reset for boot mode
   - Serial connection

### Common Configurations

1. **Basic LED Control**
   ```
   GPIO2 - LED
   GND   - LED (via resistor)
   ```

2. **Button Input**
   ```
   GPIO4 - Button
   3.3V  - Button
   ```

3. **Relay Control**
   ```
   GPIO5 - Relay IN
   5V    - Relay VCC
   GND   - Relay GND
   ```

4. **RGB LED Strip**
   ```
   GPIO3 - LED Data
   5V    - LED VCC
   GND   - LED GND
   ```

## Troubleshooting

### Common Issues

1. **Boot Failures**
   - Check GPIO0 state
   - Verify power supply
   - Monitor serial output

2. **Flash Issues**
   - Verify flash size
   - Check filesystem layout
   - Validate upload settings

3. **Connection Problems**
   - Check antenna connection
   - Verify power stability
   - Monitor signal strength

### Hardware Reset

If a digital pin device named "button1" is configured:
1. Hold high for 3+ seconds on boot
2. Release within next 3 seconds
3. Device performs reset

## Best Practices

1. **Power Supply**
   - Use stable power source
   - Add decoupling capacitors
   - Consider current requirements

2. **Pin Selection**
   - Avoid boot pins
   - Use appropriate protection
   - Consider voltage levels

3. **Physical Setup**
   - Proper heat dissipation
   - Signal integrity
   - EMI considerations
