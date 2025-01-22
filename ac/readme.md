# mIoT_core - AC Control

Current at firmware version 3.5.0
Last updated 14th Feb 2024

## What is it?
AC Control provides integration with Mitsubishi heat pumps (MSZ-AP series) through serial communication, allowing control over power, temperature, operating mode, fan speed, and vane positions. This enables automation and remote control of compatible heat pump units through the web API.

## Contents
* [Config](#config) All the possible configuration parameters that can be used to set up heat pump control.
* [API](#api) HTTP requests that can be made to control the heat pump.
* [Status](#status) A description of the output `ac` property from any API request.

## Config
The file `/config/ac.json` is read and parsed at boot to configure heat pump control settings.

### Version
The current AC config `_version` is **1**
This version will only increment when there are breaking config changes. Generally adding a field or deleting a field without adding a replacement would be considered "backwards compatible".

### Example
```json
{
    "_version": 1,
    "allowExternalControl": true,
    "autoConnect": true,
    "settingsUpdateInterval": 5000,
    "roomTempUpdateInterval": 10000,
    "receiveInterval": 1000,
    "onChange": "acChanged",
    "onConnect": "acConnected",
    "presets": [
        {
            "name": "comfort",
            "label": "Comfort Mode",
            "power": true,
            "mode": 1,
            "temp": 23,
            "fan": 2,
            "swingV": 3,
            "swingH": 3,
            "webDisplay": true
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

#### allowExternalControl
Whether to allow external control of the heat pump (e.g., from the physical remote).
* Type: boolean
* Default: true
* Required: no

#### autoConnect
Whether to automatically attempt reconnection if the connection is lost.
* Type: boolean
* Default: true
* Required: no

#### settingsUpdateInterval
How frequently to poll the heat pump for settings updates.
* Type: integer
* Range: 1000-60000
* Units: milliseconds
* Default: 5000
* Required: no

#### roomTempUpdateInterval
How frequently to poll the heat pump for room temperature updates.
* Type: integer
* Range: 1000-60000
* Units: milliseconds
* Default: 10000
* Required: no

#### receiveInterval
How frequently to check for incoming messages from the heat pump.
* Type: integer
* Range: 100-5000
* Units: milliseconds
* Default: 1000
* Required: no

#### onChange
The name of an event set to trigger when heat pump settings change.
* Type: string
* Value: [a-zA-Z0-9_-]{1,20}
* Default: ""
* Required: no

#### onConnect
The name of an event set to trigger when connection is established.
* Type: string
* Value: [a-zA-Z0-9_-]{1,20}
* Default: ""
* Required: no

#### presets
An array of preset configurations that can be loaded.
* Type: array<object>
* Default: []
* Required: no

#### presets[].name
The internal name used to refer to this preset in configurations and APIs. Must be unique within this config file.
* Type: string
* Value: [a-zA-Z0-9_-]{1,20}
* Default: ""
* Required: yes

#### presets[].label
The name shown in the web UI to describe this preset when webDisplay is true.
* Type: string
* Value: .*
* Default: ""
* Required: no

#### presets[].power
Whether the heat pump should be powered on when this preset is loaded.
* Type: boolean
* Default: false
* Required: no

#### presets[].mode
The operating mode to use when this preset is loaded.
* Type: integer
* Values:
  * 1: HEAT
  * 2: DRY
  * 3: COOL
  * 7: FAN
  * 8: AUTO
* Default: 8 (AUTO)
* Required: no

#### presets[].temp
The temperature setting to use when this preset is loaded.
* Type: integer
* Range: 16-31
* Units: degrees Celsius
* Default: 24
* Required: no

#### presets[].fan
The fan speed to use when this preset is loaded.
* Type: integer
* Values:
  * 0: AUTO
  * 1: QUIET
  * 2: LOW
  * 3: MEDIUM
  * 5: HIGH
  * 6: MAX
* Default: 0 (AUTO)
* Required: no

#### presets[].swingV
The vertical vane position to use when this preset is loaded.
* Type: integer
* Values:
  * 0: AUTO
  * 1: HIGHEST
  * 2: HIGH
  * 3: MIDDLE
  * 4: LOW
  * 5: LOWEST
  * 7: SWING
* Default: 0 (AUTO)
* Required: no

#### presets[].swingH
The horizontal vane position to use when this preset is loaded.
* Type: integer
* Values:
  * 0: AUTO
  * 1: LEFT_MAX
  * 2: LEFT
  * 3: MIDDLE
  * 4: RIGHT
  * 5: RIGHT_MAX
  * 8: WIDE
  * 12: SWING
* Default: 0 (AUTO)
* Required: no

#### presets[].webDisplay
Whether this preset should be shown as a button in the web UI.
* Type: boolean
* Default: false
* Required: no

## API

### Get Status
Retrieve the current state of the heat pump.
* **GET** `/api/status`
* Response includes an `ac` object containing:
```json
{
    "ac": {
        "connected": true,
        "roomTemperature": 24,
        "isDirty": false,
        "settings": {
            "presetName": "comfort",
            "presetLabel": "Comfort Mode",
            "power": true,
            "mode": 1,
            "temperature": 23,
            "fan": 2,
            "verticalVane": 3,
            "horizontalVane": 3
        },
        "presets": [
            {
                "name": "comfort",
                "label": "Comfort Mode",
                "webDisplay": true
            }
        ]
    }
}
```

### Control Heat Pump
Control the settings of the heat pump. There are two ways to control the heat pump:

#### Method 1: Single Parameter Update
* **GET** `/api/ac?target=<target>&action=<action>&value=<value>`
* Parameters:
  * target: One of:
    * "power": Control power state (value: 0 or 1)
    * "mode": Set operating mode (value: 1-8)
    * "temp": Set temperature (value: 16-31)
    * "fan": Set fan speed (value: 0-6)
    * "swingV": Set vertical vane position (value: 0-7)
    * "swingH": Set horizontal vane position (value: 0-12)
  * action: One of:
    * "update": Update a specific setting
    * "preset": Load a preset configuration (target becomes preset name)
    * "off": Turn off the heat pump
  * value: The value to set (required for "update" action)
* Examples:
  * `/api/ac?target=power&action=update&value=1`
  * `/api/ac?target=temp&action=update&value=23`
  * `/api/ac?target=comfort&action=preset`
  * `/api/ac?target=any&action=off`

#### Method 2: Multiple Parameter Update
* **GET** `/api/ac` with any combination of the following parameters:
* Parameters (all optional):
  * power: true/false to control power state
  * mode: Operating mode (1-8)
  * temperature: Temperature setting (16-31)
  * fan: Fan speed (0-6)
  * verticalVane: Vertical vane position (0-7)
  * horizontalVane: Horizontal vane position (0-12)
* Examples:
  * `/api/ac?power=true&temperature=23&fan=2`
  * `/api/ac?mode=1&verticalVane=3&horizontalVane=3`

## Status
The AC status is returned as part of any API response that includes device state. The AC object contains:

* **connected**: Whether communication with the heat pump is established
* **roomTemperature**: The current room temperature in Celsius (-999 if not available)
* **isDirty**: Whether there are pending changes not yet confirmed by the heat pump
* **settings**: The current or pending heat pump settings:
  * **presetName**: Name of the currently active preset (if any)
  * **presetLabel**: Display label of the currently active preset
  * **power**: Whether the unit is powered on
  * **mode**: The current operating mode:
    * 1: HEAT
    * 2: DRY
    * 3: COOL
    * 7: FAN
    * 8: AUTO
  * **temperature**: The current temperature setting (16-31°C)
  * **fan**: The current fan speed:
    * 0: AUTO
    * 1: QUIET
    * 2: LOW
    * 3: MEDIUM
    * 5: HIGH
    * 6: MAX
  * **verticalVane**: The current vertical vane position:
    * 0: AUTO
    * 1: HIGHEST
    * 2: HIGH
    * 3: MIDDLE
    * 4: LOW
    * 5: LOWEST
    * 7: SWING
  * **horizontalVane**: The current horizontal vane position:
    * 0: AUTO
    * 1: LEFT_MAX
    * 2: LEFT
    * 3: MIDDLE
    * 4: RIGHT
    * 5: RIGHT_MAX
    * 8: WIDE
    * 12: SWING
* **presets**: Array of available presets with their names, labels, and display settings
