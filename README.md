# pico-web-dmx
Simple experiment to create a WiFi-attached DMX driver using a Raspberry Pi Pico W

## Software
* The hard part of this project - a DMX protocol engine - exists thanks to [this project](https://github.com/jostlowe/Pico-DMX) from Jostein Løwer.
* My solution here blends the Pico W's ability to run a http server with some input data processing to drive DMX fixtures.

## DMX Basics
* DMX is a protocol that defines 512 "slots" that can be used by all manner of devices - from simple switches, through to complex moving head units. Each group of 512 slots is known as a Universe.
* Each "slot" contains a value from 0 to 255, that represent 256 variations of 0% to 100%. In data terms, each value is a `byte`.
  * Each lighting fixture has an address. This sets the "from" slot to read values. E.g. a 2 channel dimmer with address 1 would use slots 1 and 2 for brightness, and a 3 channel RGB LED fixture might use slots 11, 12 and 13 for its red, blue and green colour mix if it's address was set to 11.
  * Simple devices such as a switch might define that a value up to 128 means OFF, and more than 128 means ON.
  * A simple dimmer channel would offer 256 levels of brightness where 0 = 0% and 255 = 100%.
  * As noted, RGB fixture might use 3 slots to define brightness of the red, green and blue colours in the unit.
  * A RGBW fixture would add a 4th slot to define the white colour.
  * A moving head might use 9 slots to define, red, blue, green, white, rotation-X, rotation-Y, selected gobo, gobo rotation, strobe rate, etc, etc.

## How this software works
It should be noted that this project is all about SIMPLE control of DMX devices. For complex use-cases, look elsewhere! That isn't to say that this software can't control complex fixtures - it can, but in a very simple way - i.e. it can't perform a slow coordinated head pan for a fleet of moving heads, but it could easily set those heads to a new position and dimmers to particular values - i.e. scene/snapshot recall.

* The Pico W runs a web server.
* A user would execute a software command to send data to the Pico. This command could emit from a computers "command line" using curl (usually installed on most if not all modern computers), or a custom web page using fetch.
* The data format required is __very__ simple and comprises a simple list of numbers, each separated by a comma and without spaces.
  * For example: `1,2,3,4`.
  * This would send a DMX update setting slot 1 to 1, slot 2 to 2, slot 3 to 3 and slot 4 to 4. All other slots (the remaining 508) would all be set to 0.
  * If you had 4 dimmers on your DMX network, the 1st dimmer brightness would be 0.39%, the 2nd 0.78%, the 3rd 1.11% and the 4th 1.5%.
  * If the input was `0,128,255,64,192` and each slot was a dimmer channel, then the brightness of each would be 0%, 50%, 100%, 25% and 75% respectively.
  * To set 512 slots to different values, you must send 512 numbers, otherwise the slots would be set to 0.
  * It's down to your implementation to remember what slot values might be set if you want to recall them at a later date.

## Parts required for this cheap (less than $15) project
* A Raspberry Pi Pico W https://www.raspberrypi.com/products/raspberry-pi-pico/ - a low cost $8 microcontroller using the dual-core RP2040 processor. https://www.google.com/search?q=pico+w+buy
  
  ![image](https://github.com/bevand10/pico-web-dmx/assets/5892030/d0a5f3b6-b22b-427c-b304-86d5560cd694)

* A TTL to RS485 serial IO board such as this $4 board. https://www.google.com/search?q=MAX3485+TTL+To+RS485+Module

![image](https://github.com/bevand10/pico-web-dmx/assets/5892030/20c6705d-f332-4163-911e-9ccac005fb5c)

## Software
