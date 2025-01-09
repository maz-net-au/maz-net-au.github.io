# mIoT_core - Analog

Current at firmware version 3.5.0
Last updated 14th Feb 2024

## What is it?
Analog devices are those that can have varying levels of input or output, represented as values between 0 and the maximum value determined by the bit depth (e.g., 0-1023 for 10-bit ADC). For inputs, this could be sensors like potentiometers, light sensors, or temperature sensors. For outputs, this could be LED brightness control, motor speed control, or any device that accepts variable voltage levels.

## Contents
* [Config](#config) All the possible configuration parameters that can be used to describe analog devices to make them available to the system.
* [API](#api) HTTP requests that can be made to read from or control analog pins.
* [Status](#status) A description of the output `analog` property from any API request.

## Config
The file `/config/analog.json` is read and parsed at boot to configure analog input and output pins.

### Version
The current analog config `_version` is **1**
This version will only increment when there are breaking config changes. Generally adding a field or deleting a field without adding a replacement would be considered "backwards compatible".

### Example
```json
{
    "_version": 1,
    "bitDepth": 10,
    "pins": [
        {
            "name": "light_sensor",
            "label": "Light Level",
            "pin": 34,
            "direction": 0,
            "intervalMs": 1000,
            "webDisplay": true
        },
        {
            "name": "led_dimmer",
            "label": "LED Brightness",
            "pin": 25,
            "direction": 1,
            "webDisplay": true,
            "defaultPercentValue": 50
        }
    ]
}
```

### Fields

#### _version
This is used by the system to determine what properties to expect and for converting between firmware versions.
* Type: integer
* Range: 0+
* Default: 1
* Required: no (but recommended)

#### bitDepth
The bit depth of the analog-to-digital converter (ADC). This determines the maximum value range for analog readings and writes.
* Type: integer
* Common values: 10 (0-1023), 12 (0-4095)
* Required: yes

#### pins
An array of analog pin configurations for both input and output devices.
* Type: array<object>
* Default: []
* Required: no

#### pins[].name
The internal name used to refer to this device in configurations and APIs. Must be unique within this config file and should be reasonably short and basic alphanumeric only.
* Type: string
* Value: [a-zA-Z0-9_-]{1,20}
* Default: ""
* Required: yes

#### pins[].label
The name shown in the web UI to describe this device when webDisplay is true (e.g. "Light Sensor", "LED Brightness").
* Type: string
* Value: .*
* Default: ""
* Required: no

#### pins[].pin
The GPIO pin number that the analog device is connected to. This can often be different from the number printed on the hobby board so you might need to check your datasheet.
* Type: integer
* Range: 0-255
* Default: -1
* Required: yes

#### pins[].direction
Specifies whether this is an input or output pin.
* Type: integer
* Values: 0 (INPUT), 1 (OUTPUT)
* Required: yes

#### pins[].intervalMs
For input pins only. How frequently to read the analog input value. If set to -1, the pin will not be read automatically.
* Type: integer
* Range: -1 or 1-60000
* Units: milliseconds
* Default: -1
* Required: no

#### pins[].webDisplay
If true, a control is rendered for this device in the web UI. For inputs, this shows the current value as a percentage. For outputs, this shows a slider to control the value.
* Type: boolean
* Default: false
* Required: no

#### pins[].defaultRawValue
For output pins only. The initial raw value (based on bit depth) to set when the device boots.
* Type: integer
* Range: 0 to (2^bitDepth - 1)
* Default: -1 (no default value)
* Required: no

#### pins[].defaultPercentValue
For output pins only. The initial percentage value to set when the device boots. This is converted to a raw value based on the bit depth.
* Type: float
* Range: 0.0-100.0
* Default: -1 (no default value)
* Required: no

## API

### Get Status
Retrieve the current state of all analog pins.
* **GET** `/api/status`
* Response includes an `analog` array containing objects for each configured pin:
```json
{
    "analog": [
        {
            "name": "light_sensor",
            "label": "Light Level",
            "value": 45.5,
            "rawValue": 466,
            "maxValue": 1023,
            "display": true,
            "direction": 0
        }
    ]
}
```

### Set Value
Set the value of an output analog pin.
* **GET** `/api/analog?name=<pin_name>&value=<raw_value>`
* Parameters:
  * name: The name of the analog pin to control
  * value: The raw value to set (0 to maxValue based on bit depth)
* Example: `/api/analog?name=led_dimmer&value=512`

## Status
The analog status is returned as part of any API response that includes device state. Each analog pin object in the array contains:

* **name**: The configured name of the pin
* **label**: The display label for the pin
* **value**: The current value as a percentage (0-100)
* **rawValue**: The current raw ADC value (0 to maxValue)
* **maxValue**: The maximum possible value based on bit depth (2^bitDepth - 1)
* **display**: Whether this pin should be shown in the web UI
* **direction**: 0 for input, 1 for output
