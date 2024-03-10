# Electronic Output Devices

Lessons for soldering:
- Use the SHINY irons!
- Always solder pins onto boards with them positioned on a beardboard. Otherwise the pins may be wobbly or just slanted...

Fried my ESP32, had to get a new one. Now we have a Wemos LOLIN32 board. Pinout is slightly different

Ran into some prolems with connecting to the board... 

First error:

A fatal error occurred: Failed to connect to ESP32: Wrong boot mode detected (0x13)! The chip needs to be in download mode.

Second error: A fatal error occurred: Failed to connect to ESP32: No serial data received.

Solved it after downloading another CH340 driver from here: https://www.wemos.cc/en/latest/ch340_driver.html AND connecting GPIO pin 0 to ground to manually set the board into BOOT mode (see this Github issue thread here: https://github.com/espressif/arduino-esp32/issues/333), since this board only has a reset button and not a boot button. I also read online that you need to make sure GPIO2 pin is also grounded during upload, which I double checked that it is. 

Running into weird problem where the LED only shines after I successfully upload the code once AND THEN fail to upload the code another time... I suspect that it is probbaly because I need to get rid of the BOOT jumper cable right after upload / during the upload? Unsure about this mystery still... moving on...

So the next goal is to control these LEDs with 