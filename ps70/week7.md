# Electronic Output Devices

I did week 6 and 7 pretty much together. You can see my final product of LED output with photoresistor input on the week 6 page.

The hardest part this week was trying to solder the wires onto the LED strips. After two days of failure, I decided to switch soldering stations, and it turns out that the issue wasn't my soldering skills—it was the iron!

Because I was being a bit reckless and tried the wires on the other end of the strip that came from the manufacturer, I accidentally fried my ESP32 and had to get another one. Now I have a WEMOS LOLIN32 board, which has a slightly different input.

**Lessons for soldering:**
- Use the shiny irons!
- Always solder pins onto boards with them positioned on a breadboard. Otherwise, the pins may be wobbly or slanted...

<div style="display: flex; flex-wrap: wrap; align-items: center;">
    <div style="flex: 1; padding: 5px;">
        <img src="../img/week7/goodiron.jpg" alt="Good soldering iron">
        <p>Good soldering iron (up).</p>
    </div>
    <div style="flex: 1; padding: 5px;">
        <img src="../img/week7/badiron.jpg" alt="Bad soldering iron">
        <p>Bad soldering iron (up).</p>
    </div>
</div>

I realized that this new board is not great (see more details on week 6's page). I ran into some problems with connecting to the board.

**First error:** A fatal error occurred: Failed to connect to ESP32: Wrong boot mode detected (0x13)! The chip needs to be in download mode.

**Second error:** A fatal error occurred: Failed to connect to ESP32: No serial data received.

I solved it after downloading another CH340 driver from [here](https://www.wemos.cc/en/latest/ch340_driver.html) and connecting GPIO pin 0 to ground to manually set the board into BOOT mode (see this [Github issue thread](https://github.com/espressif/arduino-esp32/issues/333)). Since this board only has a reset button and not a boot button, I also read online that you need to ensure GPIO2 pin is also grounded during upload, which I double-checked.

I encountered a weird problem where the LED only shines after I successfully upload the code once and then fail to upload the code another time. I suspect this is because I need to remove the BOOT jumper cable right after upload or during the upload. I'm still unsure about this mystery. I finally figured out a "reliable" way to upload, which involves disconnecting and reconnecting the micro USB every time before uploading, grounding GPIO0, pressing upload, and then ungrounding it right after it starts "writing."

It's crazy that I have to do that just to upload. The board is also very finicky, where sometimes the LED strip works and sometimes it doesn't. I just have to turn it on and off a few times. I need a better board!

Nonetheless, I looked at the output to the LED pin when trying to debug the board. It seems to be working in these pictures, but sometimes the LED strip still doesn't light up reliably. I need to debug more or try another strip.

<div style="display: flex; flex-wrap: wrap; align-items: center;">
    <div style="flex: 1; padding: 5px;">
        <img src="../img/week7/7-1.jpg" alt="Waveform zoomed in on pin 33">
        <p>Waveform zoomed all the way in of pin 33 (output pin that controls the Arduino).</p>
    </div>
    <div style="flex: 1; padding: 5px;">
        <img src="../img/week7/7-2.jpg" alt="Waveform packets">
        <p>Waveform "packets".</p>
    </div>
</div>

<div style="display: flex; flex-wrap: wrap; align-items: center;">
    <div style="flex: 1; padding: 5px;">
        <img src="../img/week7/7-3.jpg" alt="Setup when debugging with oscilloscope">
    </div>
    <p>Setup when debugging with oscilloscope.</p>
</div>

I realized that I broke the original LED strip I was using. Luckily, I am now an expert at soldering these things, and I ended up making a lot of them. Although I didn't plan to, it was super fun. I think I'll have a side project of making an LED display with light sensors and custom letters. Here's what it looks like now:

<div style="display: flex; flex-wrap: wrap; align-items: center;">
    <div style="flex: 1; padding: 5px;">
        <img src="../img/week7/7-4.jpg" alt="LED display">
    </div>
    <div style="flex: 1; padding: 5px;">
        <video controls width="100%">
            <source src="../img/week7/7-1-MOV.MOV" type="video/mp4">
        </video>
    </div>
</div>
