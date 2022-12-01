## DigitalDeviceTypes
The type of digital device. 
#### None = 0,
#### Toggle = 1,
	A toggle device latches into a new state and will stay there until operated on again. (e.g. a switch). Events on toggle devices will trigger on each rising AND falling edge.
#### Momentary = 2
	A momentary device automatically resets it's state (e.g. a push button). This kind of device is permanently in one state and only momentarily changes when operated on before reverting to its previous state. Events on momentary devices will trigger only on the initial rising or falling edge and not on the immediately following state reset.

## EventTypes 
#### None = 0,
#### DigitalPin = 1,
#### Url = 2,
#### RGB = 3,
#### Timer = 4,
#### AnalogPin = 5,
#### EventSet = 6,
#### Variable = 7,
#### PWMLED = 8,
#### Functions = 9,

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

## LedPatterns #### Off = 0,
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

## OverflowTypes #### Limit=0,
#### Wrap=1
};