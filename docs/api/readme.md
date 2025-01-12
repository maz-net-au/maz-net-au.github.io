# HTTP API Reference

## Core APIs

### Device Status
```http
GET /api/status
```
Retrieve current device state including:
- Hardware information
- Network status
- Feature states
- Configuration details

### System Control
```http
GET /api/reboot
```
Restart the device

```http
GET /api/reset
```
Clear WiFi configuration (revert to AP mode)

```http
GET /api/resetall
```
Factory reset (clear all configuration)

```http
GET /api/logs
```
Retrieve debug logs (if enabled)

### Firmware Updates
```http
GET /api/updatecheck
```
Check for firmware updates from trusted hub

```http
GET /api/updateto?version=<version>
```
Update to specific firmware version

## Feature APIs

### RGB LED Control
```http
GET /api/rgb?pattern=<pattern>&colour=#RRGGBB
```
Control WS2812 LED strips
- pattern: LedPatterns enum value
- colour: Optional hex color

### PWM LED Control
```http
GET /api/pwmled?name=<name>&pattern=<pattern>&colour=#RRGGBB
```
Control PWM-based LEDs
- name: LED configuration name
- pattern: LedPatterns enum value
- colour: Optional hex color

Alternative channel control:
```http
GET /api/pwmled?name=<name>&r=<value>&g=<value>&b=<value>&w=<value>&c=<value>&o=<value>
```
Set individual channel values 0 to 2^BitDepth-1

### Digital Control
```http
GET /api/digital?name=<name>&action=<action>
```
Control digital pins
- name: Pin configuration name
- action: on|off|toggle

### Timer Control
```http
GET /api/timer?name=<name>&action=<action>
```
Control timers
- name: Timer configuration name
- action: start|stop|startorreset

### Event Control
```http
GET /api/event?name=<name>
```
Trigger event sets
- name: EventSet configuration name

### Variable Control
```http
GET /api/variable?name=<name>&op=<op>&value=<value>
```
Modify variables
- name: Variable name
- op: MathOperator enum value
- value: Integer value

### IR Control
```http
GET /api/ir?capture=<name>
```
Start capturing IR code with given name

```http
GET /api/ir?finish
```
Finish IR code capture

```http
GET /api/ir?cancel
```
Cancel IR code capture

```http
GET /api/ir?name=<name>
```
Send stored IR code

### Camera Control
```http
GET /api/camera?action=capture
```
Capture single image

```http
GET /api/camera?action=stream
```
Start MJPEG stream

## File System APIs

### List Files
```http
GET /fs/list
```
Return array of all files on filesystem

### Get File
```http
GET /fs/get?filename=<name>
```
Retrieve specific file content

### Delete File
```http
GET /fs/delete?filename=<name>
```
Remove file from filesystem

### Upload File
```http
POST /fs/set?filename=<name>
```
Write or overwrite file
- Body contains file content

## Device Setup

### Configure Device
```http
POST /api/setup
```
Configure device settings. JSON body can include:
```json
{
    "name": "device name",
    "id": "device id",
    "hardwareModel": "model name",
    "ssid": "wifi name",
    "password": "wifi password",
    "isHidden": false,
    "deleteSsid": "wifi to remove",
    "apSSID": "ap mode name",
    "apPass": "ap mode password",
    "hubAddress": "http://hub.address"
}
```

## Response Format

Most APIs return a JSON object representing device state:

```json
{
    "device": {
        "id": "device_id",
        "name": "device_name",
        "version": "3.5.0"
    },
    "network": {
        "ip": "192.168.1.100",
        "mac": "AA:BB:CC:DD:EE:FF"
    },
    "features": {
        "analog": [...],
        "digital": [...],
        "rgb": {...},
        "timers": [...],
        "variables": [...],
        "ir": {...}
    }
}
```

## Common Enumerations

### EventTypes
```cpp
None = 0
DigitalPin = 1
Url = 2
RGB = 3
Timer = 4
AnalogPin = 5
EventSet = 6
Variable = 7
```

### LedPatterns
```cpp
Off = 0
Solid = 1
RainbowCycle = 2
Pulse = 3
ColourCycle = 4
Toggle = 99
```

### MathOperator
```cpp
Nop = 0
Delete = 1
Equals = 2
Plus = 3
Minus = 4
Multiply = 5
Divide = 6
Modulus = 7
```

## Error Handling

- Failed requests return HTTP error codes
- Error details provided in response body
- Common errors:
  - 400: Invalid parameters
  - 404: Resource not found
  - 500: Internal error

## Best Practices

1. **Rate Limiting**
   - Avoid rapid repeated requests
   - Use appropriate intervals for polling
