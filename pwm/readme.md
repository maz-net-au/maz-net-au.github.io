# mIoT_core - PWM LED

Current at firmware version 3.5.0
Last updated 14th Feb 2024

## What is it?
PWM LED control allows for sophisticated control of LED lighting using Pulse Width Modulation. This module supports up to 6 channels per light (RGBWCO - Red, Green, Blue, Warm white, Cool white, Other), with features like color transitions, patterns, and animations. It's particularly useful for controlling LED strips, smart bulbs, or any multi-channel LED setup that requires precise brightness control.

## Contents
* [Config](#config) All the possible configuration parameters that can be used to describe PWM LED devices.
* [API](#api) HTTP requests that can be made to control PWM LED devices.
* [Status](#status) A description of the output `pwmLed` property from any API request.

## Config
The file `/config/pwmLed.json` is read and parsed at boot to configure PWM LED devices.

### Version
The current PWM LED config `_version` is **1**
This version will only increment when there are breaking config changes. Generally adding a field or deleting a field without adding a replacement would be considered "backwards compatible".

### Example
```json
{
    "_version": 1,
    "bitDepth": 10,
    "animationWaitTime": 200,
    "transitionStepInterval": 20,
    "transitionTime": 1000,
    "lights": [
        {
            "name": "desk_light",
            "displayName": "Desk Light",
            "usesRGBControl": true,
            "initialPattern": 1,
            "initialColour": "FF00000000",
            "initialOnState": true,
            "animationWaitTime": 200,
            "transitionStepInterval": 20,
            "transitionTime": 1000,
            "channels": [
                {
                    "colour": 1,
                    "pin": 12,
                    "displayName": "Red Channel"
                },
                {
                    "colour": 2,
                    "pin": 13,
                    "displayName": "Green Channel"
                },
                {
                    "colour": 3,
                    "pin": 14,
                    "displayName": "Blue Channel"
                }
            ]
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
The bit depth used for PWM control. This determines the resolution of brightness control.
* Type: integer
* Common values: 8 (0-255), 10 (0-1023), 12 (0-4095)
* Required: yes

#### animationWaitTime
Default time in milliseconds between animation steps for all lights.
* Type: integer
* Range: 1-60000
* Units: milliseconds
* Default: 200
* Required: no

#### transitionStepInterval
Default time in milliseconds between transition steps for all lights.
* Type: integer
* Range: 1-1000
* Units: milliseconds
* Default: 20
* Required: no

#### transitionTime
Default time in milliseconds for color transitions for all lights.
* Type: integer
* Range: 0-60000 (0 disables transitions)
* Units: milliseconds
* Default: 1000
* Required: no

#### lights
An array of light configurations, each representing a multi-channel LED device.
* Type: array<object>
* Default: []
* Required: no

#### lights[].name
The internal name used to refer to this light in configurations and APIs. Must be unique within this config file.
* Type: string
* Value: [a-zA-Z0-9_-]{1,20}
* Default: "UnnamedLight"
* Required: yes

#### lights[].displayName
The name shown in the web UI to describe this light.
* Type: string
* Value: .*
* Default: "Unnamed Light"
* Required: no

#### lights[].usesRGBControl
If true, this light can be controlled using RGB color values in addition to individual channel control.
* Type: boolean
* Default: false
* Required: no

#### lights[].initialPattern
The pattern to set when the device boots. See: [LedPatterns](/enums/#ledpatterns) for available patterns.
* Type: enum
* Values:
  * 0: Off
  * 1: Solid
  * 2: RainbowCycle
  * 3: Pulse
  * 4: ColourCycle
  * 99: Toggle
* Default: 0 (Off)
* Required: no

#### lights[].initialColour
The color to set when the device boots. Format is a hex string representing RGBWCO values.
* Type: string
* Format: 12-character hex string (2 chars per channel)
* Example: "FF0000000000" (full red)
* Default: "000000000000"
* Required: no

#### lights[].initialOnState
Whether the light should be turned on when the device boots.
* Type: boolean
* Default: false
* Required: no

#### lights[].animationWaitTime
Override the default animation wait time for this specific light.
* Type: integer
* Range: 1-60000
* Units: milliseconds
* Required: no

#### lights[].transitionStepInterval
Override the default transition step interval for this specific light.
* Type: integer
* Range: 1-1000
* Units: milliseconds
* Required: no

#### lights[].transitionTime
Override the default transition time for this specific light.
* Type: integer
* Range: 0-60000 (0 disables transitions)
* Units: milliseconds
* Required: no

#### lights[].channels
An array of channel configurations for this light.
* Type: array<object>
* Required: yes

#### lights[].channels[].colour
The type of this channel. See: [ChannelColour](/enums/#channelcolour) for values.
* Type: enum
* Values:
  * 0: Other
  * 1: Red
  * 2: Green
  * 3: Blue
  * 4: Warm White
  * 5: Cool White
* Required: yes

#### lights[].channels[].pin
The GPIO pin number that this channel is connected to.
* Type: integer
* Range: 0-255
* Default: -1
* Required: yes

#### lights[].channels[].displayName
A custom name for this channel shown in the web UI.
* Type: string
* Value: .*
* Default: Automatically set based on colour type
* Required: no

## API

### Get Status
Retrieve the current state of all PWM LED devices.
* **GET** `/api/status`
* Response includes a `pwmLed` object containing:
```json
{
    "pwmLed": {
        "maxChannelValue": 1023,
        "lights": [
            {
                "name": "desk_light",
                "label": "Desk Light",
                "on": true,
                "colour": "FF0000000000",
                "pattern": 1,
                "rgbcontrol": true,
                "channels": [
                    {
                        "colour": 1,
                        "label": "Red Channel",
                        "value": 1023
                    },
                    {
                        "colour": 2,
                        "label": "Green Channel",
                        "value": 0
                    },
                    {
                        "colour": 3,
                        "label": "Blue Channel",
                        "value": 0
                    }
                ]
            }
        ]
    }
}
```

### Update Individual Channels
Set specific channel values for a light.
* **GET** `/api/pwmled?name=<light_name>&r=<value>&g=<value>&b=<value>&w=<value>&c=<value>&o=<value>`
* Parameters:
  * name: The name of the light to control
  * r,g,b,w,c,o: Values for each channel (-1 to keep current value)
* Example: `/api/pwmled?name=desk_light&r=1023&g=0&b=0`

### Set Pattern
Set a light's pattern and optionally its color.
* **GET** `/api/pwmled?name=<light_name>&pattern=<pattern>&colour=<colour>`
* Parameters:
  * name: The name of the light to control
  * pattern: Pattern number from LedPatterns enum
  * colour: (optional) Hex color string
* Example: `/api/pwmled?name=desk_light&pattern=1&colour=FF0000`

## Status
The PWM LED status is returned as part of any API response that includes device state. The response includes:

* **maxChannelValue**: The maximum value for any channel based on bit depth
* **lights**: Array of light objects containing:
  * **name**: The configured name of the light
  * **label**: The display name for the light
  * **on**: Whether the light is currently on
  * **colour**: Current color as a hex string
  * **pattern**: Current pattern number
  * **rgbcontrol**: Whether RGB control is enabled
  * **channels**: Array of channel objects containing:
    * **colour**: Channel type number
    * **label**: Channel display name
    * **value**: Current channel value
