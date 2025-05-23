# mIoT_core - Digital

Current at firmware version 3.2.0.1
Last updated 30 Nov 2022

## What is it?
Digital devices are those that generally have two states, on and off. For example, inputs like push buttons and switches, or outputs like the control pin of a relay. It could also be used to control an LED if you only wanted the ability to turn it on or off at full brightness, otherwise you'd use the PWM feature.

## Contents
* [Config](#config) All the possible configuration parameters that can be used to describe the digital devices to make them available to the system.
* [API](#api) HTTP requests that can be made to change the state of the device's digital pins.
* [Status](#status) A description of the output `digital` property from any API request.

## Config
The file `/config/digital.json` is read and parsed at boot to receive input from or enable control of digital devices.

### Version
The current digital config `_version` is **2**
This version will only increment when there are breaking config changes. Generally adding a field or deleting a field without adding a replacement would be considered "backwards compatible".

### Example
> { \
> &nbsp;&nbsp;&nbsp;&nbsp;"[_version](#_version)":&nbsp;2, \
> &nbsp;&nbsp;&nbsp;&nbsp;"[input](#input)":&nbsp;[ \
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{ \
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"[name](#inputname)":&nbsp;"button1", \
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"[label](#inputlabel)":&nbsp;"Light&nbsp;Switch", \
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"[webDisplay](#inputwebdisplay)":&nbsp;true, \
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"[pin](#inputpin)":&nbsp;16, \
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"[deviceType](#inputdevicetype)":&nbsp;[2](/enums/#digitaldevicetype), \
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"[negate](#inputnegate)":&nbsp;false, \
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"[debounceDelayMs](#inputdebouncedelayms)":&nbsp;100, \
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"[onEventName](#inputoneventname)":&nbsp;"longPressTest" \
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"[offEventName](#inputoffeventname)":&nbsp;"" \
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;} \
> &nbsp;&nbsp;&nbsp;&nbsp;], \
> &nbsp;&nbsp;&nbsp;&nbsp;"[output](#output)":&nbsp;[ \
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{ \
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"[name](#outputname)":&nbsp;"relay1", \
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"[label](#outputlabel)":&nbsp;"Light", \
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"[webDisplay](#outputwebdisplay)":false, \
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"[pin](#outputpin)":12, \
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"[deviceType](#outputdevicetype)":[2](/enums/#digitaldevicetype), \
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"[negate](#outputnegate)":true, \
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"[isActive](#outputisactive)":false \
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;} \
> &nbsp;&nbsp;&nbsp;&nbsp;] \
> }

### Fields

#### _version
This is used by the system to determine what properties to expect and for converting between firmware versions.
* Type: integer
* Range: 0
* Default: 1
* Required: no (but recommended)

#### input
A digital device that sends data INTO the system is described here (buttons, switches and some sensors).
* Type: array<object>
* Default: []
* Required: no

The internal name used to refer to this device in configurations and APIs. Must be unique within this config file and I strongly recommend making it reasonably short and basic alphanumeric only.
#### input.name
* Type: string
* Value: [a-zA-Z0-9_-]{1,20}
* Default: ""
* Required: yes

#### input.label
The name shown in the web UI to describe this device when webDisplay is true. (e.g. "Light switch"). Don't go too crazy with special characters or length.
* Type: string
* Value: .*
* Default: ""
* Required: no

#### input.webDisplay
If true, a control is rendered for this device in the web UI. It will be shown with a Toggle button with the label described above.
* Type: boolean
* Default: false
* Required: no

#### input.pin
The GPIO pin number that the digital device is connected to. This can often be different to the number printed on the hobby board so you might need to check your datasheet. This goes into the digitalRead(pin) call within the loop()
* Type: integer
* Range: 0-255
* Default: -1
* Required: yes

#### input.deviceType
The type of the device. See: [DigitalDeviceType](/enums/#digitaldevicetype) for more info.
* Type: enum
* Default: 0 (None)
* Required: yes

#### input.negate
If this is set to true then the device is read as active low and pulling the digital pin to ground would be read as the button being pressed etc.
* Type: boolean
* Default: false
* Required: no

#### input.debounceDelayMs
How long a button state change has to be detected for before events are triggered. This is because some mechanical switches rapidly turn on and off as the metal contacts bounce against each other and you normally wouldn't want to read this as multiple button presses.
* Type: integer
* Range: 0 - 5000
* Units: milliseconds
* Default: 50
* Required: no

#### input.onEventName
When a pin state changes from inactive to active this is the name of the event set that is executed. Digital device type is not taken into account for this event and it is always triggered based on the state change. For momentary push buttons it is recommended in most cases that you only set onEventName OR offEventName, not both. It is case sensitive.
* Type: string
* Value: [a-zA-Z0-9_-]{1,20}
* Default: ""
* Required: no

#### input.offEventName
When a pin state changes from active to inactive this is the name of the event set that is executed. Digital device type is not taken into account for this event and it is always triggered based on the state change. For momentary push buttons it is recommended in most cases that you only set onEventName OR offEventName, not both. It is case sensitive.
* Type: string
* Value: [a-zA-Z0-9_-]{1,20}
* Default: ""
* Required: no

### output
A digital device that receives a digital signal FROM the system is described here (relays, mosfets, sometimes LEDs).
* Type: array<object>
* Default: []
* Required: no

#### output.name
The internal name used to refer to this device in configurations and APIs. Must be unique within this config file and I strongly recommend making it reasonably short and basic alphanumeric only.
* Type: string
* Value: [a-zA-Z0-9_-]{1,20}
* Default: ""
* Required: yes

#### output.label
The name shown in the web UI to describe this device when webDisplay is true. (e.g. "Light"). Don't go too crazy with special characters or length.
* Type: string
* Value: .*
* Default: ""
* Required: no

#### output.pin
The GPIO pin number that the digital device is connected to. This can often be different to the number printed on the hobby board so you might need to check your datasheet. This goes into the digitalWrite(pin, value) call within the loop()
* Type: integer
* Range: 0-255
* Default: -1
* Required: yes

#### output.deviceType
The type of the device. See: [DigitalDeviceType](/enums/#digitaldevicetype) for more info.
* Type: enum
* Default: 0 (None)
* Required: yes

#### output.negate
If this is set to true then the device is written as active low and turning it "on" would pull the pin to ground.
* Type: boolean
* Default: false
* Required: no

#### output.webDisplay
If true, a control is rendered for this device in the web UI. It will be shown with two buttons which are "on" and "off" with the label described above. Pressing the "on" button will set the pin high or low, depending on the value of the negate field.
* Type: boolean
* Default: false
* Required: no

#### output.isActive
If this is set to true, the device is turned "on" when the board boots. Whether this sets high or low depends on the value of the negate field.
* Type: boolean
* Default: false
* Required: no

## API

### Get Status
Retrieve the current state of all digital devices.
* **GET** `/api/status`
* Response includes a `digital` array containing objects for each configured device:
```json
{
    "digital": [
        {
            "name": "button1",
            "label": "Light Switch",
            "display": true,
            "isActive": false,
            "direction": "input"
        },
        {
            "name": "relay1",
            "label": "Light",
            "display": true,
            "isActive": true,
            "direction": "output"
        }
    ]
}
```

### Control Digital Output
Control the state of a digital output device.
* **GET** `/api/digital?name=<device_name>&action=<action>`
* Parameters:
  * name: The name of the digital device to control
  * action: One of:
    * on: Turn the device on (set pin high/low based on negate setting)
    * off: Turn the device off (set pin low/high based on negate setting)
    * toggle: Toggle between on and off states
* Example: `/api/digital?name=relay1&action=on`

## Status
The digital status is returned as part of any API response that includes device state. Each digital device object in the array contains:

* **name**: The configured name of the device
* **label**: The display label for the device
* **display**: Whether this device should be shown in the web UI
* **isActive**: Whether the device is currently active (on)
* **direction**: Whether this is an "input" or "output" device

### Input Device States
For input devices (buttons, switches, etc.):
* **isActive**: true when the input is detected as active (button pressed, switch on)
* The active state is determined by:
  1. Reading the GPIO pin value
  2. Applying the negate setting if configured
  3. Debouncing for the configured delay period
  4. Triggering onEventName/offEventName events on state changes

### Output Device States
For output devices (relays, LEDs, etc.):
* **isActive**: true when the output is set to its active state
* The physical pin state is determined by:
  1. The isActive value
  2. The negate setting (inverts the pin state if true)
  3. Set via GPIO pin write
