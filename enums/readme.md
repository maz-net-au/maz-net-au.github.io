## DigitalDeviceTypes
The type of digital device. 

| Value        | Name      | Comments                                   |
|--------------|-----------|--------------------------------------------|
| 0 | None | Disables this device from triggering any events. |
| 1 | Toggle | A toggle device latches into a new state and will stay there until operated on again. (e.g. a switch). Events on toggle devices will trigger on each rising AND falling edge. |
| 2 | Momentary | A momentary device automatically resets it's state (e.g. a push button). This kind of device is permanently in one state and only momentarily changes when operated on before reverting to its previous state. Events on momentary devices will trigger only on the initial rising or falling edge and not on the immediately following state reset. |


## EventTypes
The built in event system routes requests to sections of the firmware based on the event type. Names must be unique within any given type or the event will trigger only on the first one found. This enum is also used for SourceType in condtions and when setting variables so the comments describe 

| Value        | Name      | Comments                                   |
|--------------|-----------|--------------------------------------------|
| 0 | None | Used as the default so that if it hasn't been specified nothing will happen. Better than triggering things randomly.<br>Event: N/A<br>Source: N/A |
| 1 | DigitalPin | Event: Set a digital device active, inactive or toggle its current state. <br>Source: Returns 1 for active and 0 for inactive. |
| 2 | Url | Event: Requests url asynchronously and ignores the response. <br>Source: Returns the body of the request response converted to an int32 synchronously. |
| 3 | RGB | Event: Set the colour and/or pattern for a serial RGB strip LED. <br>Source: Returns the nominal brightness 0 - 255. |
| 4 | Timer | Event: Starts, stops or resets a timer.<br>Source: Returns 0 -  3. 1 if enabled. 2 if in the delayed start time. 3 if running. |
| 5 | AnalogPin | Event: Writes an output on an analog pin.<br>Source: Returns the the value read from an analog pin. |
| 6 | EventSet | Event: Triggers another set of events synchronously.<br>Source: N/A |
| 7 | Variable | Event: Performs some [MathOperator](#mathoperator) on the variable.<br>Source: Returns the current value of the variable (int32) |
| 8 | PWMLED | Event: Set the brightness, colour and/or pattern PWM led (can be RGBWCO or just a single LED).<br>Source: N/A |
| 9 | Functions | Internal functions used to surface non-config state of the system.<br>Event: N/A<br>Source: "now" time since boot in milliseconds. "rand_10" returns 0 - 9, "rand_100" returns 0 - 99. "rand_1000" returns 0 - 999. |


## ChannelColour 
A colour assigned to a channel (pin) for PWM LED control. This is what determines which pin gets set when using a hex code to set the colour (e.g. #RRGGBBWWCCOO)

| Value        | Name      | Comments                                   |
|--------------|-----------|--------------------------------------------|
| 0 | Other | Usually used for IR or UV. |
| 1 | Red | |
| 2 | Green | |
| 3 | Blue | |
| 4 | WarmWhite | |
| 5 | CoolWhite | |
| 6 | Other | |


## LedStripTypes 
Colour order / type of the serial RGB strip. (Older WS2811's are GRB)

| Value        | Name      | Comments                                   |
|--------------|-----------|--------------------------------------------|
| 0 | None | Default. Disables the LED strip |
| 1 | RGB | |
| 2 | GRB | |


## LedPatterns 
Sets the display mode of a set of LEDs. Toggle is a transition between Off <-> Any other state.

| Value        | Name      | Comments                                   |
|--------------|-----------|--------------------------------------------|
| 0 | Off | Turns the strip off while retaining the previous pattern and colour state. |
| 1 | Solid | a.k.a "On". Sets the whole strip to be one colour. |
| 2 | RainbowCycle | Serial RGB only. Animated. Sets each LED to a different colour evenly spaced hue values and then animates this cycling along the strip. |
| 3 | Pulse | Animated. Fades a solid colour off and back again. |
| 4 | ColourCycle | Animated. Sets all LEDs to one colour, and animates through different hues. |
| 99 | Toggle | Transient. If Off it restores the saved pattern and colour. Otherwise sets the strip Off. | 


## ComparisonOperator 
Used for conditions in events. A simple set of logical comparison operators applied to some source input and a specified value.

| Value        | Name      | Comments                                   |
|--------------|-----------|--------------------------------------------|
| 0 | Nop | No operation. Default. Does nothing. |
| 1 | GreaterThan | > |
| 2 | GreaterThanOrEqual | >= |
| 3 | EqualTo | == |
| 4 | LessThanOrEqual | <= |
| 5 | LessThan | < |
| 6 | NotEqualTo | != |


## BooleanOperator 
Used for multiple conditions in an event set.

| Value        | Name      | Comments                                   |
|--------------|-----------|--------------------------------------------|
| 0 | And | &&<br>Default. All conditions must be true before the events are triggered. |
| 1 | Or | \|\|<br>If any one condition is true the events are triggered. |


## MathOperator 
Operations that can be applied to a variable and a specified value.

| Value        | Name      | Comments                                   |
|--------------|-----------|--------------------------------------------|
| 0 | Nop | Default. Does nothing |
| 1 | Delete | Deletes the variable, frees it's memory.<br> |
| 2 | Equals | =<br>Sets the variable to the value. |
| 3 | Plus | + |
| 4 | Minus | - |
| 5 | Multiply | \*<br>Be careful of int32 [overflow](#overflowtypes). |
| 6 | Divide | \\<br>Integer division |
| 7 | Modulus | % |


## DisplayAs 
Different ways that a variable can be interpreted to be rendered on the web UI.

| Value        | Name      | Comments                                   |
|--------------|-----------|--------------------------------------------|
| 0 | None | Hidden. Works similarly to webDisplay = false for other web controls |
| 1 | Integer | Just display the value as a number. No conversion. |
| 2 | Float_3 | Divide the integer value by 1000 and display as a decimal value. (E.g. 12345 would be 12.345) |
| 3 | Percent | Same as integer, but with a % sign at the end. |
| 4 | Percent_3 | Same as Float_3 but with a % sign at the end |
| 5 | Boolean | 0 is displayed as "false", any non-zero value is displayed as "true" |
| 6 | TimeMinutes | Read as the number of minutes since midnight, and converted to HH:MM. (E.g. 1276 would show "21:16") |
| 7 | DateTimeEpoch | Shows a date and time as represented by the number of seconds since 1st Jan 1970. (E.g. 1669913563 would be "Thu Dec 01 16:52:43 2022") |


## OverflowTypes 
This determines what happens when a variable exceeds its defined minimum or maximum value.

| Value        | Name      | Comments                                   |
|--------------|-----------|--------------------------------------------|
| 0 | Limit | Default. The value is clamped to the minimum or maximum value it exceeded. |
| 1 | Wrap | At max+1 the value is set to the minimum and continues counting up. At min-1 the value is set to the maximum and continues counting down. |
