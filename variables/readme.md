# mIoT_core - Variables

Current at firmware version 3.5.0
Last updated 14th Feb 2024

## What is it?
The variables system provides a way to store and manipulate numeric values that can be used throughout the mIoT system. Variables can be used in event conditions, trigger events when they change, automatically persist their values, and display in various formats. They support basic mathematical operations and can have minimum/maximum value constraints with configurable overflow behavior.

## Contents
* [Config](#config) All the possible configuration parameters that can be used to describe variables.
* [API](#api) HTTP requests that can be made to manipulate variables.
* [Status](#status) A description of the output `variables` property from any API request.

## Config
The file `/config/variables.json` is read and parsed at boot to configure system variables.

### Version
The current variables config `_version` is **1**
This version will only increment when there are breaking config changes. Generally adding a field or deleting a field without adding a replacement would be considered "backwards compatible".

### Example
```json
{
    "_version": 1,
    "variables": [
        {
            "name": "counter",
            "label": "Event Counter",
            "value": 0,
            "displayAs": 1,
            "autoSave": true,
            "minimumSaveIntervalMs": 10000,
            "minimum": 0,
            "maximum": 100,
            "overflowType": 0,
            "onChange": "counterChanged",
            "onOverflow": "counterReset"
        },
        {
            "name": "temperature",
            "label": "Current Temperature",
            "value": 22,
            "displayAs": 2,
            "minimum": -10,
            "maximum": 50
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

#### variables
An array of variable configurations.
* Type: array<object>
* Default: []
* Required: no

#### variables[].name
The internal name used to refer to this variable in configurations and APIs. Must be unique within this config file.
* Type: string
* Value: [a-zA-Z0-9_-]{1,20}
* Default: ""
* Required: yes

#### variables[].label
The name shown in the web UI to describe this variable.
* Type: string
* Value: .*
* Default: ""
* Required: no

#### variables[].value
The initial value of the variable.
* Type: integer
* Range: -2147483647 to 2147483647
* Default: 0
* Required: no

#### variables[].displayAs
How the variable's value should be formatted for display. See: [DisplayAs](/enums/#displayas) for values.
* Type: enum
* Values:
  * 0: None
  * 1: Integer
  * 2: Float (3 decimal places)
  * 3: Percentage (0 decimal places)
  * 4: Percentage (3 decimal places)
  * 5: Boolean
  * 6: Time (minutes)
  * 7: DateTime (epoch)
* Default: 1 (Integer)
* Required: no

#### variables[].autoSave
If true, changes to this variable will be automatically saved to the config file.
* Type: boolean
* Default: false
* Required: no

#### variables[].minimumSaveIntervalMs
When autoSave is true, this is the minimum time between saves to prevent excessive writes.
* Type: integer
* Range: 1-60000
* Units: milliseconds
* Default: 10000
* Required: no

#### variables[].minimum
The minimum allowed value for this variable.
* Type: integer
* Range: -2147483647 to 2147483647
* Default: -2147483647
* Required: no

#### variables[].maximum
The maximum allowed value for this variable.
* Type: integer
* Range: -2147483647 to 2147483647
* Default: 2147483647
* Required: no

#### variables[].overflowType
How to handle values that exceed the minimum/maximum range. See: [OverflowTypes](/enums/#overflowtypes) for values.
* Type: enum
* Values:
  * 0: Limit (clamp to min/max)
  * 1: Wrap (wrap around to other end of range)
* Default: 0 (Limit)
* Required: no

#### variables[].onChange
The name of an event set to trigger when this variable's value changes.
* Type: string
* Value: [a-zA-Z0-9_-]{1,20}
* Default: ""
* Required: no

#### variables[].onOverflow
The name of an event set to trigger when this variable's value exceeds its min/max range.
* Type: string
* Value: [a-zA-Z0-9_-]{1,20}
* Default: ""
* Required: no

## API

### Get Status
Retrieve the current state of all variables.
* **GET** `/api/status`
* Response includes a `variables` array containing objects for each configured variable:
```json
{
    "variables": [
        {
            "name": "counter",
            "label": "Event Counter",
            "value": 42,
            "displayAs": 1,
            "persistent": true,
            "isDirty": false
        }
    ]
}
```

### Modify Variable
Perform a mathematical operation on a variable.
* **GET** `/api/variable?name=<var_name>&op=<operation>&value=<value>`
* Parameters:
  * name: The name of the variable to modify
  * op: Operation number from [MathOperator](/enums/#mathoperator):
    * 0: No operation
    * 1: Delete variable
    * 2: Set to value
    * 3: Add value
    * 4: Subtract value
    * 5: Multiply by value
    * 6: Divide by value
    * 7: Modulo by value
  * value: The value to use in the operation
* Example: `/api/variable?name=counter&op=3&value=1` (increment counter by 1)

## Status
The variables status is returned as part of any API response that includes device state. Each variable object in the array contains:

* **name**: The configured name of the variable
* **label**: The display label for the variable
* **value**: The current value
* **displayAs**: How the value should be formatted for display
* **persistent**: Whether this variable is automatically saved
* **isDirty**: Whether this variable has unsaved changes
