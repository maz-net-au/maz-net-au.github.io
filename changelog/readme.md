# Changelog
A history of the significant changes being made on this system. Unfortunately I forgot to put dates against them but a lot of the versions are in the git commit messages.

[Latest Release](#version-310)

[Most Recent Changes](#version-351)
## Version 3.5.1
+ b.0 @ 20th Jan 2025
  - An initial working implementation to control a Mitsubishi heat pump (MSZ-AP)
+ b.1 @ 22nd Jan 2025
  - Fixing copy/paste bugs in the web API for AC
  - updated website to v2.3.0 to contain AC controls
+ b.2 @ 1st Feb 2025
  - Added power meter files to start working on them
  - made the ac request a settings update after sending though changes
  - web v2.3.1 show dirty AC state
+ b.3 @ 4th Feb 2025
  - Delay AC settings updates until 1 sec after the last serial.write
+ b.4 @ 8th Feb 2025
  - Clean up some log messages
  - Update debug API for digital pins (to test what is connected to what in new hardware)
  - Reduce status messages to nothing if the feature is inactive
  - Looped in power meter implementation behind mIoT_FEATURE_POWERMETER flag

## Version 3.5.0
+ b.0 @ 14th Feb 2024
  - Working IR implementation. Need better / longer range hardware
+ b.1 @ 1st Dec 2024
  - Added BGR support for cheap aliexpress christmas lights
+ b.2 @ 27th Dec 2024
  - Updated website to fix setup page and work with the new hub proxy
+ b.3 @ 2nd Jan 2025
  - Added a rolling uptime counter to the status message for debugging reboots
  - Show estimated boot time on the setup page (based on now() - uptime)
  - Added reset reason to device status for debugging reboots
+ b.4 @ 8th Jan 2025
  - Allow collection and request of debug logs remotely with /api/logs (when mIoT_REMOTEDEBUG is defined)
+ b.5 @ 12th Jan 2025
  - Separated serial and remote logging. Renamed mIoT_DEBUG to mIoT_SERIALDEBUG
+ b.6 @ 15th Jan 2025
  - Attempt to plug device into AC unit
  - Website 2.2.3 fixed a race condition that setup log tailing when remote debug was enabled

## Version 3.4.0
+ b.0
  - Started adding IR module. WIP and not running yet
+ b.1 @ 19th Jan 2024
  - Someone finally worked out what was wrong with platformIO so I've moved to that build system.
+ b.2 @ 20th Jan 2024
  - Testing OTA etc works from PIO
+ b.3 @ 2nd Feb 2024
  - WiFi connections now specify AP MAC address to better control connecting to mesh networks
  - Fixed a bug where networks were reporting the wrong rssi
  - Created a wifi roaming system where it'll reconnect to another saved network if it has much better signal
  - Fixed a bug where if booting was interrupted at step 1 (wifi), the next boot wouldn't correctly start its own AP
+ b.4 @ 3rd Feb 2024
  - Bugfix: make the network roaming change be backwards compatible.
  - Updated ESPFSSync to support the new structire with /releases/\[version\]/firmware.bin with the version subfolder.
  - Fixed a bug where new versions of ESPFSSync wouldn't upgrade the firmware of a device when using the newer "firmwareFilename" json property.
+ b.5 @ 7th Feb 2024
  - Using the preprocessor define for 'mIoT_HARDWARE_GENERATION' instead of the large case statement that I forgot to update
  - Using the preprocessor define for 'mIoT_VERSION' as part of the firmware update check
  - Simplified .gitignore
  - Wrote a firmware migration component to go from 3.3.0.2 to 3.4.0.5
+ b.6 @ 8th Feb 2024
  - Fixing a bug where animations don't resume if using pattern "Toggle" with an RGB strip

**SORRY ABOUT THE LONG INTERVAL**

## Version 3.3.0
+ b.0 "focus on 8285"
  - support gzipped website
  - remove web templating - you will need to update your website to get the headers filled in again
  - streamlined web handlers for static content (used some rewrite rules)
  - updated website to v2.0.0 (gzipped)
  - replaced compiled init page html with a gzipped byte array in PROGMEM
  - eliminate web.json (move trustedHub into wifi.json because it lives with the local network config)
  - eliminate webVersion.txt by putting it into the script file and letting everything reference it from there. no longer shown in /api/status
+ b.1 @ 27th Jan 2023
  - added a filesystem format command
  - Put the debug statement behind a preprocessor define so that doing a non-debug build makes the code 2k smaller
  - fixed some website typos. now v2.0.2
  - built a tool to help with packing the website. minifies the javascript and css, and then gzips everything
+ b.2 @ 29th Jan 2023
  - added wifi_status function. 1 for AP mode, 2 for STA connection, 3 for both
  - Website only has one active request at a time (no spam when device is unavailable)
  - Updated ESPFSSync project to do a managed upgrade of older devices which converts their config to newer formats with some rollback support. It'll apply multiple upgrade steps if required.
  - Wrote the upgrade module for v2.1.0.0 to v3.2.0.1
  - Wrote the upgrade module for v3.0.0.0 to v3.3.0.2

## Version 3.2.0
+ b.0 @ 26th Nov 2022
  - Variables can have a min and max set and can wrap or limit when these are exceeded
  - When a variable hits a limit, the onOverflow event is triggered
  - when a variable changes (explicitly a different value than it had before) onChange is triggered
  - I've tried to stop var change event loops locking up the device. its best effort, don't make silly configs or you'll win silly prizes
  - Event system values, variables, etc are all now explicitly int32

+ b.1 @ 3rd Dec 2022
  - fixed the bug where esp32 didn't know how much space it was using on the filesystem
  - digital pin resetButton config removed. If you needed it this project isn't for you.
  - digital pin statusIndicator config removed. Never implemented
  - set the digital pin debounceDelayMs default to 50 milliseconds

## Version 3.1.1
+ b.0
  - Added AsyncHTTPRequest for triggered Url events. Adds 6k to binary size.
+ b.1 @ 14th Nov 2022
  - Made it work.
  - Used AsyncHTTPRequest for the sync request as well to save just over 1.5k of space. Async request net cost was 4.4k
+ b.2
  - I'd had to put back the separate implementation of the sync request() because i cannot call an async (request) from the callback of an async (webserver)
  - conditions of EventTypes::Url and setting a var from an EventTypes::Url are both sync

## Version 3.1.0
+ b.0 @ 19th Oct 2022
  - Allow lists of conditions on events (with the option to select and/or for the whole set) while being backwards compatible
+ b.1 @ 20th Oct 2022
  - Disabled temp file location. not enough space on some FS
+ b.2
  - Fixed a bug where i was making request URLs lower case, but event names are case sensitive
+ b.3 @ 5th Nov 2022
  - Fixed HW version names. replaced mIoT_HARDWARE_GENERATION with mIoT_Helpers::boardType() because using preprocessor tokens when i dont need to is dumb

## Version 3.0.1
+ b.0 @ 26th Sep 2022
  - another pass at esp32 support. filesystem code is working now. just needs OTA support
  - ESP32 OTA working
  - Added ESP32-cam support (for AI Thinker ESP32-CAM at least). it doesn't work but i'm not sure why. It's being checked-in to see if anyone else will have a look
  - added a random delay on broadcast response that hopefully avoids killing the requestor unless you have thousands of devices
  - Renamed mIoT_HARDWARE_GENERATION values to not have spaces. not sure what these are even being used for and could possibly remove them entirely
  - Move broadcast response off port 80. I want to move my http listener above 1024 to not require admin privs on windows. a port can now be specified in the broadcast itself 'mIoT_REPORT_in:port'
  - Can also have trusted hub or APIs that are on non-standard ports using the format http://10.0.0.1:8080/
  - Re-enabled temp files being used to stop a network issue from corrupting a config that is being pushed
  - web->request() API changed internally to just take a url and a body. the url is parsed into scheme, host, port and path
  - free ram is reported in the /api/status and responses
  - added EventType of Function to access some built in functions as part of events. now (millis), rand_10 (random 0-9) rand_100 (random 0-99) rand_1000 (random 0-999)
+ b.1 9th Oct 2022
  - added a yield() to the end of hal::loop()
  - storing a flag that says if the broadcast listener is running so i can check before trying to read from it

## Version 3.0.0
+ b.0 @ 5th Jun 2022
  - Put all configs in a folder (and probably rename them)
  - add .json to the end of all configs
  - version configs and then my espsynctool (that needs a rename) can try and do some fw migration
  - attempt to minimise ram usage when loading config files. loading directly from the filesystem with a buffered stream
  - Make the ssid list not auto-select anything and fail when no ssid is set (initialisation page)
  - Keep AP mode active until successful connection to wifi at least once (stops a typo making life hard for you)
  - encourage setting of AP name and/or AP password
  - Move device name and ID out of wifiConfig and into generalConfig. Then make wifiConfig deploy from a shared location
  - support multiple wifi networks being saved. find the strongest signal and join it?
  - if there's no wifi and no reset button configured, find a SAFE way to fall back to AP mode
  - Allow the device to go back into AP mode if it cant find the wifi network
  - updated build system to PlatformIO. it sucks. it's electron, but it supports building for multiple targets
  - reverted build to arduino studio. platformIO makes a binary 200kb (50%) larger, with the same code
  - include a flag in the status to say when there's no usable reset button and/or no custom AP details configured.
  - make web ui for configuring the AP mode. store a flag if its pw has been changed
  - See if I can get RSSI
  - try and use progmem for boot / config loading failure and auto-bypass
  - show warning on webpage if AP password is still the default
  - minify javascript and css files
  - if there's no index.html, show the wifi setup page (otherwise you'd just show a blank page)
  - separate the chunks to be behind different #ifdefs for later use or something mIoT_FEATURE_DIGITAL, mIoT_FEATURE_ANALOG, mIoT_FEATURE_PWMLED, mIoT_FEATURE_SERIALLED, mIoT_FEATURE_VARIABLES
  - finally fixed the bug where smashing the rgb colour to colour makes it suddnely revert back to the original colour if it was mid-transition
  - make timer collection size grow in chunks (like vars) starting at 8 and growing by 4 or something
  - Investigate a shorter way to define events actions and conditions [type].[target].[action].[value] and [type].[operator].[value] or something
+ b.1 @ 13th Jun 2022
  - optimised string storage
  - status response now streams feature by feature to reduce ram usage
  - status object now contains a list of features enabled in a build
+ b.2 @ 13th Jun 2022
  - enabling PWM LED in the hal (WIP so disabled for now)
  - more PWM work. new update() call and new api\
+ b.3 @ 22nd Jun 2022
  - clamp analog values from 0 to 2^BitDepth
  - set up animators on pwm
  - pwm done
+ b.4
  - pwm UI sliders use the new r,g,b,w,c,o api to set channels one at a time without affecting the others
  - website 1.0.18 has pwm channel support
+ b.5 @ 18th Jul 2022
  - website 1.1.2 has working pwm support
+ b.6 @ 19th Jul 2022
  - fixed a bug with swapped channels on the individual channel API for pwm
  - website 1.1.3 fixed a bug where colour was being reset on change of pattern for pwm
  - website 1.1.3 Allows hub address and hardware model to be cleared from settings
  - stop caching device details and features when requesting status from HAL
+ b.7 @ 31st Aug 2022
  - gave up on adjusting block size. its not possible to make it smaller on the esp8285 spi flash chip
  - report used filesystem space in /status
  - increased pwm led status size a little
  - default timer, event and variable status to [] when none are set
+ b.8
  - fixed a bug with initial pwm state not being applied unless it was an animation
+ b.9 @ 12th Sep 2022
  - fixed a bug with timer intervals set to 0. if interval is 0 the timer doesn't execute anymore
  - any event action or else action can have a sourceType and sourceName instead of a value
  - api requests now return -1 if they fail (not ideal but better than 0)
  - added variable display as time or datetime
  - updated website to v1.1.5 to support new variable displays

## Version 2.2.1
+ b.0 @ 3rd May 2022
  - add more device details to the FW check call. FW ver, HW ver, FS layout and maybe build date? then the FW tool can auto-find the right file
  - gives firmware prefix in the response to /api/status as well as showing on the settings page of web 1.0.11
  - store last fw file uploaded
  - try and generate the expected firmware file name for a given version
  - make a function that tells the device to reach out for a given firmware version so all devices can be told to update at once
  - make web version accessible in /api/status and still display it on the page web 1.0.12
+ b.1 @ 4th May 2022
  - bugfix: else action values weren't being loaded from config correctly (used to update variables)
  - fixed bugs in the website (format config json for display, made the config box bigger, variable config is now selectable) v1.0.13

## Version 2.2.0
+ b.0 
  - Trying to work towards ESP32 compatability while not breaking exp8266
  - Changed the ESP32 Dev Module define to be correct. ESP32-Cam is going to report as the same
+ b.1
  - Extended the event system to take a 3rd parameter. an int "value". this isn't being used by any of the old parts so not a breaking change yet
  - Create a "variables" class that can hold some state and do basic math operations
  - added variable class API access /api/variable?name=test&op=3&value=1 would add 1 to test
+ b.2
  - making auto-save work
+ b.3
  - fixing typos and adding debugging
+ b.4 28th Apr 2022
  - allowing variables to be used as event conditions

## Version 2.1.0
+ b.0 @ 25th Nov 2021
  - Conditions now run elseActions if provided when the condition isn't met. while not a breaking change its not small
+ b.1
  - added some extra print outs about network errors. turns out you should put http:// or https:// on the front of web requests
+ b.2
  - warned about not setting url scheme when making a web request
+ b.3 @ 30th Dec 2021
  - increasing json buffer to handle minified configs better
  - fixing initialOnState for rgb led strips
  - updated the website to v1.0.8 to handle when no digital inputs or outputs are specified (and now the json is minified it removes the empty arrays)
+ b.4 @ 8th Jan 2022
  - Detect FS size (to aid in choosing the correct bin to load via OTA)
  - Fixed a bug I created by doing host.toLowerCase() in the network request
+ b.5 @ 10th Jan 2022
  - Fixed a bug in timerStatus, the value wasn't being returned
  - Stopping a timer sets "started" to false also
  - Renamed internal pause() function to stop() as this is what it was doing

## Version 2.0.0
+ b.0
  - removed reset
  - instead of "reset" on hardware button on boot. just enable AP mode temporarily (failsafe)
  - move hardware button name and status indicator name to the general config
  - load the general config first. this can then be used for meta operations (wifi signal)
  - change apName to use apMode instead, which allows you to store a password for the AP mode
+ b.1 @ 7th Nov 2021
  - Renamed EventTypes::Led to EventTypes::RGB
  - BREAKING CHANGE: Changed LedPatterns::Toggle to 99 and LedPatterns::Off to 0. It just makes more sense
  - Updated the website to support the above change v1.0.6
  - Added the build date and time to the website because I just discovered those macros
  - RGB leds no longer have a "set" button in the web UI. it just changes when you tell it to do something.
+ b.2
  - adding getApi to the network so i can do conditions based on calling an API and atoi'ing the body
+ b.3
  - rewriting the RGB led work to support different types of LEDs
+ b.4
  - HAL::triggerEvent missing a break after EventSet
  - Reading configs directly into a Json doc where possible, trying to reduce memory usage
  - Reworked memory allocation for json in and out in general

## Version 1.0.12
+ b.0
  - rewrite analog pin to support multiple
  - analog pin status now an array
  - removed analog pin status "isActive"
  - getCurrentValue for an analog pin now takes a pin name and returns a percentage
  - updated website to match new analog setup v1.0.3
+ b.1
  - adding /api/analog?name=___&value=100 to network handlers
  - allowing a defaultValue in output analog pin config to set that % value at startup
+ b.2
  - PWM LED support (for 5050 or smart bulbs) // by treating them as analog pins
  - changing the analog event handler to take a raw value (so the API does too)
  - percentages are now floats
  - updated website to auto-update on an interval v1.0.4
  - Scan for wifi networks when uninit and let people select them from a list via the web ui
  - make an API that sets the initial wifi or add a setup page in code
  - updated website to give sliders for setting analog values v1.0.5
+ b.3 @ 3rd Nov 2021
  - Updated to show board type on website

## Version 1.0.11:
  - add a rename function for the filesystem
  - disabled putting files in /temp/ and renaming them (some devices dont appear to support rename)
  - reworked how digital output pins show their status, handle negation and respond to state requests. something something bugs coming home to roost
+ b.4
  - added mac address and ip address to network status (should mac be in device?)

## Version 1.0.10:
 + b.0 @ 8th Jul 2021
  - Stop animator timer when transitioning from an animated one to another animated
  - Increased config size for digital pins
  - Added pinOn and pinOff to help find what is connected to what with new hardware
  - Add MAC address to setup page
  - Added isActive to the digital pin config that sets the default state
  - CURRENTLY HAS A BREAKING CHANGE WHERE I ADDED /api at the front of the firmware calls
  - An endpoint to allow the external flash tool to easily scan the network range and find devices
  - Added a broadcast listener on port 13981 to find devices on the network

## Version 1.0.9:
  - Fixed a bug in the RGB api handler 
  - Finish transitionStep() and add transitionTimeMs to config
  - Moved all network events to a list in the eventbroker that trigger on loop()
  - Added transition rate to RGB config
  - Used a different timer for transitions
  - Linear blend RGB colour from current value to target value

## Version 1.0.8:
  - Fixing event status
  - Show events on web UI
  - Events can be triggered by API on /api/event?name
  - Timers are now included in status
  - Timers can be triggered by API on /api/timer?name&action    
  - show timers in web ui (copy events but with start, stop and reset)
  - make web ui hide empty sections better

## Version 1.0.7:
  - Removed full reset because it's not useful right now
  - Fixed bug where reset is triggered with the board disconnected from pull-down
  -   -> hold button on boot -> purple. hold for 3 sec -> turns red. release button within 3 sec -> green means it reset
  - Added /api/reset to just reset network settings and key
  - Added /api/resetall to completely reset all configs
  - Disable softAP when connected to another AP
  - Show FW and HW version on webpage
  - Added /fs/get?filename to replace /api/conf
  - Renamed /api/listfiles to /fs/list
  - Added /fs/set?filename to replace /api/config
  - Added /fs/delete?filename to delete files
  - Web - Refresh icon goes red when unable to connect to device
  - Add separate reboot button for the web ui
  - Added reset button to web ui

## Version 1.0.6:
  - Increased the size of the json object allocations in getState() to match the config allocations
  - Added api to get a list of all the files on the device /api/listfiles

## Version 1.0.5:    
  - Added device id to the status struct
  - Added device name to the status struct
  - Add endpoint to reset device

## Version 1.0.4:
  - Added Network::HubAnnounce
  - Send version message on boot (to log reboots and track versions)
  - Added Network::HubRegistration
  - Checks for and attempt registration to hub

## Version 1.0.3:
  - First multi-device release. Bug fixes.
  - Changed the path for checking firmware to be /device/checkfw