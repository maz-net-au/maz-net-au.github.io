# mIoT_core - RGB LED

Current at firmware version 3.5.0
Last updated 14th Feb 2024

## What is it?
The RGB LED module provides control for addressable LED strips (WS2812, SK6812, etc.) with support for both status indicators and decorative lighting. It supports multiple LED strip types (RGB, GRB, BGR), various animation patterns, smooth transitions between states, and dedicated status LEDs that can indicate device states.

## Contents
* [Config](#config) All the possible configuration parameters that can be used to describe RGB LED devices.
* [API](#api) HTTP requests that can be made to control RGB LED strips.
* [Status](#status) A description of the output `rgb` property from any API request.

## Config
The file `/config/led.json` is read and parsed at boot to configure RGB LED devices.

### Version
The current RGB LED config `_version` is **1**
This version will only increment when there are breaking config changes. Generally adding a field or deleting a field without adding a replacement would be considered "backwards compatible".

### Example
```json
{
    "_version": 1,
    "type": 2,
    "statusCount": 1,
    "stripCount": 30,
    "activeStatusColourRGB": "002000",
    "inactiveStatusColourRGB": "200000",
    "animationWaitTime": 20,
    "transitionStepInterval": 20,
    "transitionTime": 1000,
    "initialPattern": 1,
    "initialColour": "FF0000",
    "initialOnState": true
}
```

### Fields

#### _version
This is used by the system to determine what properties to expect and for converting between firmware versions.
* Type: integer
* Range: 0+
* Default: 1
* Required: no (but recommended)

#### type
The type of LED strip being used. See: [LedStripTypes](/enums/#ledstriptypes) for values.
* Type: enum
* Values:
  * 0: None
  * 1: RGB
  * 2: GRB
  * 3: BGR
* Default: 0 (None)
* Required: yes

#### statusCount
Number of LEDs at the start of the strip to use as status indicators.
* Type: integer
* Range: 0+
* Default: 0
* Required: no

#### stripCount
Number of LEDs to use for decorative lighting (after status LEDs).
* Type: integer
* Range: 0+
* Default: 0
* Required: yes

#### activeStatusColourRGB
The color to show for active status LEDs in hex RGB format.
* Type: string
* Format: 6-character hex RGB (e.g., "002000" for dim green)
* Default: "002000"
* Required: no

#### inactiveStatusColourRGB
The color to show for inactive status LEDs in hex RGB format.
* Type: string
* Format: 6-character hex RGB (e.g., "200000" for dim red)
* Default: "200000"
* Required: no

#### animationWaitTime
Time in milliseconds between animation steps.
* Type: integer
* Range: 1-1000
* Units: milliseconds
* Default: 20
* Required: no

#### transitionStepInterval
Time in milliseconds between transition steps when changing patterns or colors.
* Type: integer
* Range: 1-1000
* Units: milliseconds
* Default: 20
* Required: no

#### transitionTime
Total time for transitions between states.
* Type: integer
* Range: 0-60000 (0 disables transitions)
* Units: milliseconds
* Default: 1000
* Required: no

#### initialPattern
The pattern to set when the device boots. See: [LedPatterns](/enums/#ledpatterns) for values.
* Type: enum
* Values:
  * 0: Off
  * 1: Solid
  * 2: RainbowCycle
  * 3: Pulse
  * 4: ColourCycle
  * 99: Toggle
* Default: 1 (Solid)
* Required: no

#### initialColour
The color to set when the device boots in hex RGB format.
* Type: string
* Format: 6-character hex RGB (e.g., "FF0000" for red)
* Default: "FFFFFF"
* Required: no

#### initialOnState
Whether the strip should be turned on when the device boots.
* Type: boolean
* Default: false
* Required: no

## API

### Get Status
Retrieve the current state of the RGB LED strip.
* **GET** `/api/status`
* Response includes an `rgb` object containing:
```json
{
    "rgb": {
        "stripCount": 30,
        "stripPattern": 1,
        "stripColour": "FF0000",
        "stripOn": true,
        "transitioning": false,
        "statuses": [
            {
                "id": 0,
                "colour": "002000",
                "on": true
            }
        ]
    }
}
```

### Set Pattern and Color
Set the strip's pattern and optionally its color.
* **GET** `/api/rgb?pattern=<pattern>&colour=<colour>`
* Parameters:
  * pattern: Pattern number from LedPatterns enum
  * colour: (optional) 6-character hex RGB color
* Example: `/api/rgb?pattern=1&colour=FF0000`

### Control Status LED
Control a status LED's state.
* **GET** `/api/rgb/status?id=<led_id>&action=<action>`
* Parameters:
  * id: The index of the status LED
  * action: One of:
    * on: Turn the LED on
    * off: Turn the LED off
    * active: Set to active color
    * inactive: Set to inactive color
    * locate: Toggle locate mode (blue flash)
    * colour: Set to specific color (requires colour parameter)
* Example: `/api/rgb/status?id=0&action=active`

## Status
The RGB LED status is returned as part of any API response that includes device state. The response includes:

* **stripCount**: Number of LEDs in the decorative strip
* **stripPattern**: Current pattern number
* **stripColour**: Current color in hex RGB format
* **stripOn**: Whether the strip is currently on
* **transitioning**: Whether the strip is mid-transition
* **statuses**: Array of status LED objects containing:
  * **id**: Status LED index
  * **colour**: Current color in hex RGB format
  * **on**: Whether the LED is on

## Patterns

The RGB LED module supports several built-in patterns:

1. **Off** (0)
   - All LEDs turned off
   - Previous pattern is remembered for Toggle

2. **Solid** (1)
   - All LEDs set to the same color
   - Color specified by `colour` parameter

3. **RainbowCycle** (2)
   - Rainbow colors move along the strip
   - Each LED offset slightly from its neighbors
   - Creates a moving rainbow effect

4. **Pulse** (3)
   - All LEDs fade between full brightness and off
   - Uses the specified color
   - Smooth transitions create breathing effect

5. **ColourCycle** (4)
   - All LEDs change through rainbow colors together
   - Creates synchronized color changing effect

6. **Toggle** (99)
   - Switches between Off and the previous pattern
   - Maintains pattern and color settings
