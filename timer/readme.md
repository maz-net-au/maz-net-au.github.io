# mIoT_core - Timer

Current at firmware version 3.5.0
Last updated 14th Feb 2024

## What is it?
The timer system provides a way to create and manage timers that can trigger events at specific intervals. Timers can have an initial delay before starting, trigger events when they start and at each interval, and can be configured to either auto-repeat or require manual reset. This is particularly useful for scheduling periodic tasks or creating time-based automation sequences.

## Contents
* [Config](#config) All the possible configuration parameters that can be used to describe timers.
* [API](#api) HTTP requests that can be made to control timers.
* [Status](#status) A description of the output `timers` property from any API request.

## Config
The file `/config/timer.json` is read and parsed at boot to configure system timers.

### Version
The current timer config `_version` is **1**
This version will only increment when there are breaking config changes. Generally adding a field or deleting a field without adding a replacement would be considered "backwards compatible".

### Example
```json
{
    "_version": 1,
    "timers": [
        {
            "name": "hourly_check",
            "label": "Hourly Status Check",
            "delayStartMs": 5000,
            "intervalMs": 3600000,
            "onStart": "startupCheck",
            "onInterval": "hourlyCheck",
            "manualReset": false,
            "webDisplay": true,
            "startEnabled": true
        },
        {
            "name": "one_shot",
            "label": "Single Event Timer",
            "delayStartMs": 10000,
            "intervalMs": 0,
            "onStart": "delayedEvent",
            "manualReset": true,
            "webDisplay": true,
            "startEnabled": false
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

#### timers
An array of timer configurations.
* Type: array<object>
* Default: []
* Required: no

#### timers[].name
The internal name used to refer to this timer in configurations and APIs. Must be unique within this config file.
* Type: string
* Value: [a-zA-Z0-9_-]{1,20}
* Default: ""
* Required: yes

#### timers[].label
The name shown in the web UI to describe this timer.
* Type: string
* Value: .*
* Default: ""
* Required: no

#### timers[].delayStartMs
The time to wait before triggering the onStart event after the timer is started.
* Type: integer
* Range: 0+
* Units: milliseconds
* Default: 0
* Required: no

#### timers[].intervalMs
The time between interval events after the timer has started. Set to 0 for one-shot timers that only trigger the onStart event.
* Type: integer
* Range: 0+
* Units: milliseconds
* Default: 0
* Required: no

#### timers[].onStart
The name of an event set to trigger when the timer starts (after delayStartMs).
* Type: string
* Value: [a-zA-Z0-9_-]{1,20}
* Default: ""
* Required: no

#### timers[].onInterval
The name of an event set to trigger at each interval.
* Type: string
* Value: [a-zA-Z0-9_-]{1,20}
* Default: ""
* Required: no

#### timers[].manualReset
If true, the timer will stop after triggering an interval event and must be manually started again.
* Type: boolean
* Default: false
* Required: no

#### timers[].webDisplay
If true, this timer will be shown in the web UI with controls to start/stop it.
* Type: boolean
* Default: false
* Required: no

#### timers[].startEnabled
If true, this timer will automatically start when the device boots.
* Type: boolean
* Default: false
* Required: no

## API

### Get Status
Retrieve the current state of all timers.
* **GET** `/api/status`
* Response includes a `timers` array containing objects for each configured timer:
```json
{
    "timers": [
        {
            "name": "hourly_check",
            "label": "Hourly Status Check",
            "display": true,
            "isEnabled": true,
            "isStarted": true
        }
    ]
}
```

### Control Timer
Control a timer's state.
* **GET** `/api/timer?name=<timer_name>&action=<action>`
* Parameters:
  * name: The name of the timer to control
  * action: One of:
    * start: Start the timer if it's not already running
    * stop: Stop the timer
    * startorreset: Start the timer if it's stopped, or reset its counters if it's running
* Example: `/api/timer?name=hourly_check&action=start`

## Status
The timer status is returned as part of any API response that includes device state. Each timer object in the array contains:

* **name**: The configured name of the timer
* **label**: The display label for the timer
* **display**: Whether this timer should be shown in the web UI
* **isEnabled**: Whether the timer is currently running
* **isStarted**: Whether the timer has moved past its initial delay phase

## Timer States

A timer can be in one of several states:

1. **Stopped** (isEnabled=false, isStarted=false)
   - Timer is not running
   - Can be started with 'start' or 'startorreset' action

2. **Initial Delay** (isEnabled=true, isStarted=false)
   - Timer is running but waiting for delayStartMs to elapse
   - Will trigger onStart event when delay completes
   - Can be stopped with 'stop' action

3. **Running** (isEnabled=true, isStarted=true)
   - Timer has completed initial delay
   - Will trigger onInterval event at each interval
   - Can be stopped with 'stop' action
   - Can be reset with 'startorreset' action

4. **Manual Reset Required** (isEnabled=false, isStarted=false)
   - Timer has triggered an interval event with manualReset=true
   - Must be manually started again to continue
