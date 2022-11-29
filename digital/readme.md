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

### Example
```
{
	"_version": 2,
	"input": [
		{
			"name": "button1",
			"label": "Light Switch",
			"webDisplay": true,
			"pin": 16,
			"deviceType": 2,
			"negate": false,
			"debounceDelayMs": 100,
			"onEventName": "longPressTest"
		}
	],
	"output": [
		{
			
		}
	]
}
```

### Fields

#### _version
This is used by the system to determine what properties to expect and for converting between firmware versions.
* Type: integer
* Range: > 0
* Default: 1
* Required: no (but recommended)

#### input
A digital device that sends data INTO the system is described here (buttons, switches and some sensors).
* Type: array<object>
* Default: []
* Required: no

> ##### name
> The internal name used to refer to this device in configurations and APIs. Must be unique and I strongly recommend making it reasonable short and basic alphanumeric only.
> * Type: string
> * Value: [a-zA-Z0-9_-]{1,20}
> * Default: ""
> * Required: yes
>
> ##### label
> The name shown in the web UI to describe this device when webDisplay is true. (e.g. "Light switch"). Don't go too crazy with special characters or length.
> * Type: string
> * Value: .*
> * Default: ""
> * Required: no
>
> ##### webDisplay
> If true, a control is rendered for this device in the web UI. It will be shown with a Toggle button.
> * Type: boolean
> * Default: false
> * Required: no
>
> ##### pin
> The GPIO pin number that the digital device is connected to. This can often be different to the number printed on the hobby board so you might need to check your datasheet. This goes into the digitalRead(<pin>) call within the loop()
> * Type: integer
> * Range: 0-255
> * Default: -1
> * Required: yes
>
> ##### deviceType
