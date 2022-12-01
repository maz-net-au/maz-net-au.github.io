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
| 0 | None | Used as the default so that if it hasn't been specified nothing will happen. Better than triggering things randomly. |
| 1 | DigitalPin | Event: Set a digital device active, inactive or toggle its current state. \
Source: Returns 1 for active and 0 for inactive. |
| 2 | Url | Event: Requests url asynchronously and ignores the response. Source: Returns the body of the request response converted to an int32 synchronously. |
| 3 | RGB | Event: Set the colour and/or pattern for a serial RGB strip LED.<br>Source: Returns the nominal brightness 0 - 255. |
| 4 | Timer | Event: Starts, stops or resets a timer.
Source: Returns 0 -  3. 1 if enabled. 2 if in the delayed start time. 3 if running. |
| 5 | AnalogPin | Event: Writes an output on an analog pin.
Source: Returns the the value read from an analog pin. |
| 6 | EventSet | Event: Triggers another set of events synchronously. 
Source: N/A |
| 7 | Variable | Event: Performs some [MathOperator](#mathoperator) on the variable.
Source: Returns the current value of the variable (int32) |
| 8 | PWMLED | Event: Set the brightness, colour and/or pattern PWM led (can be RGBCW or just a single LED).
Source: N/A |
| 9 | Functions | Event: N/A
Source: "now" time since boot in milliseconds. "rand_10" returns 0 - 9, "rand_100" returns 0 - 99. "rand_1000" returns 0 - 999. |


## ChannelColour 
#### Other = 0,
#### Red = 1,
#### Green = 2,
#### Blue = 3,
#### WarmWhite = 4,
#### CoolWhite = 5


## LedStripTypes 
#### None = 0,
#### RGB = 1,
#### GRB = 2


## LedPatterns 
#### Off = 0,
#### Solid = 1, // a.k.a On
#### RainbowCycle = 2, // the full rainbow of colours across the strip being chased/cycled
#### Pulse = 3, // fade a solid colour in and out
#### ColourCycle = 4, // one colour at a time, moving through the rainbow
#### Toggle = 99 // toggles IsOn


## ComparisonOperator 
#### Nop = 0,
#### GreaterThan = 1,
#### GreaterThanOrEqual = 2,
#### EqualTo = 3,
#### LessThanOrEqual = 4,
#### LessThan = 5,
#### NotEqualTo = 6


## BooleanOperator 
#### And = 0,
#### Or = 1


## MathOperator 
#### Nop = 0,
#### Delete = 1,
#### Equals = 2,
#### Plus = 3,
#### Minus = 4,
#### Multiply = 5,
#### Divide = 6,
#### Modulus = 7


## DisplayAs 
#### None = 0,
#### Integer = 1,
#### Float_3 = 2,
#### Percent_0 = 3,
#### Percent_3 = 4,
#### Boolean = 5, 
#### TimeMinutes = 6,
#### DateTimeEpoch = 7


## OverflowTypes 
#### Limit=0,
#### Wrap=1
