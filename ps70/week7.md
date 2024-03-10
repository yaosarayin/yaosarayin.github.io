# Electronic Output Devices

I did week 6 and 7 pretty much together. You can see my final product of LED output with photoresister input on the week 6 page.

The hardest part this week was literally trying to solder the fricking wires onto the LED strips. After 2 days of failure I decided to SWITCH SOLDERING STATIONS and wow, apparently I am not the one who's bad at soldering, it's the iron...

And because I was being a bit stupid and decided to try the wires on the other end of the strip that came from the manufacturer, I accidentally FRIED my ESP32 and had to get another one. :( Now we have a Wemos LOLIN32 board, whose inout is slightly different

Lessons for soldering:
- Use the SHINY irons!
- Always solder pins onto boards with them positioned on a beardboard. Otherwise the pins may be wobbly or just slanted...
<img src="../img/week7/goodiron.jpg" alt="goodiron"> 

GOOD SOLDERING IRON (UP).

<img src="../img/week7/badiron.jpg" alt="badiron"> 

BAD SOLDERING IRON (UP).

I realized that this new board is very bad and I don't like it at all (see more details on week 6's page) I ran into some prolems with connecting to the board... 

First error: A fatal error occurred: Failed to connect to ESP32: Wrong boot mode detected (0x13)! The chip needs to be in download mode.

Second error: A fatal error occurred: Failed to connect to ESP32: No serial data received.

Solved it after downloading another CH340 driver from here: https://www.wemos.cc/en/latest/ch340_driver.html AND connecting GPIO pin 0 to ground to manually set the board into BOOT mode (see this Github issue thread here: https://github.com/espressif/arduino-esp32/issues/333), since this board only has a reset button and not a boot button. I also read online that you need to make sure GPIO2 pin is also grounded during upload, which I double checked that it is. 

Running into weird problem where the LED only shines after I successfully upload the code once AND THEN fail to upload the code another time... I suspect that it is probbaly because I need to get rid of the BOOT jumper cable right after upload / during the upload? Unsure about this mystery still... I finally figured out a "reliable" way to upload, which is to disconnect and reconnet the micro USB everytime before uploading, ground GPIO0, press upload, and then unground it right after it starts "writing." 

Crazy that I have to do that just to upload... The board is also very very finicky where sometimes the LED strip would work and sometimes not... I just have to turn it on anf off a couple of times... I need a better board!

But anyways, I looked at the output to the LED pin when I was trying to debug the board too. It seems to be working in these pictures but sometimes the LED strip still doesn't light up reliably... I need to debug more / try another strip.

<img src="../img/week7/7-1.jpg" alt="7-2"> 
Waveform zoomed all the way in of pin 33 (output pin that controls the arduino)

<img src="../img/week7/7-2.jpg" alt="7-2"> 
Waveform "packets"

<img src="../img/week7/7-3.jpg" alt="7-2"> 
Setup when debugging with oscillascope

Code! 
```C
#include "BluetoothA2DPSink.h"
#include <Adafruit_NeoPixel.h>
#ifdef __AVR__
 #include <avr/power.h> // Required for 16 MHz Adafruit Trinket
#endif

#define GREEN_LED 14 // Use GPIO 14 as LED pin
#define WHITE_LED 13// Use GPIO 14 as LED pin
#define sensorPin 4 // GPIO 4


// Which pin on the Arduino is connected to the NeoPixels?
#define LED_PIN  33
// How many NeoPixels are attached to the Arduino?
#define LED_COUNT 9

Adafruit_NeoPixel strip(LED_COUNT, LED_PIN, NEO_GRB + NEO_KHZ800);
BluetoothA2DPSink a2dp_sink;
int sensorValue = 0;  // variable to store the value coming from the sensor
int redValue = 0;
int greenValue = 0;
int blueValue = 0;

int lowSensorVal = 1100;
int highSensorVal = 3200;

void setup() {
    Serial.begin(115200);
    pinMode(GREEN_LED, OUTPUT); // Initialize the LED pin as an output
    pinMode(WHITE_LED, OUTPUT); // Initialize the LED pin as an output
      // These lines are specifically to support the Adafruit Trinket 5V 16 MHz.
      // Any other board, you can remove this part (but no harm leaving it):
    #if defined(__AVR_ATtiny85__) && (F_CPU == 16000000)
      clock_prescale_set(clock_div_1);
    #endif
      // END of Trinket-specific code.

      strip.begin();           // INITIALIZE NeoPixel strip object (REQUIRED)
      strip.show();            // Turn OFF all pixels ASAP
      strip.setBrightness(50); // Set BRIGHTNESS to about 1/5 (max = 255)
    

    i2s_pin_config_t my_pin_config = {
        .mck_io_num = I2S_PIN_NO_CHANGE,    
        .bck_io_num = 27,
        .ws_io_num = 26,
        .data_out_num = 25,
        .data_in_num = I2S_PIN_NO_CHANGE
    };

    a2dp_sink.set_pin_config(my_pin_config);
    a2dp_sink.start("djbox");

    // In the setup function:
    a2dp_sink.set_stream_reader(audio_data_callback);


}

void loop() {

  sensorValue = analogRead(sensorPin);
  Serial.println(sensorValue);

  // Map the input value to RGB values
  redValue = map(sensorValue, lowSensorVal, highSensorVal, 255, 0);   // Example gradient: Red fades as the input increases
  greenValue = map(sensorValue, lowSensorVal, highSensorVal, 0, 255); // Green brightens as the input increases
  blueValue = map(sensorValue, lowSensorVal, highSensorVal, 0, 0);    // Blue stays constant (adjust as needed)

  // Fill along the length of the strip in various colors...
  // colorWipe(strip.Color(255,   0,   0), 50); // Red
  // colorWipe(strip.Color(  0, 255,   0), 50); // Green
  // colorWipe(strip.Color(  0,   0, 255), 50); // Blue
  // // Do a theater marquee effect in various colors...
  // theaterChase(strip.Color(127, 127, 127), 50); // White, half brightness
  // theaterChase(strip.Color(127,   0,   0), 50); //  Red, half brightness
  // theaterChase(strip.Color(  0,   0, 127), 50); // Blue, half brightness
  // rainbow(10);             // Flowing rainbow cycle along the whole strip
  // theaterChaseRainbow(50); // Rainbow-enhanced theaterChase variant

  colorWipe(strip.Color(redValue,   greenValue,   blueValue),0);
  delay(10);
}

volatile int16_t lastVolume = 0;

void audio_data_callback(const uint8_t *data, uint32_t len) {
  //   int16_t *samples = (int16_t*) data;
//   Serial.println(*samples);

    if (len >= 2048) { // Adjust based on your data packet size
        int32_t sum = 0;
        for (int i = 0; i < 1024; i += 4) { // Skip every other sample for simplicity
            int16_t sample = (int16_t)((data[i + 1] << 8) | data[i]);
            sum += abs(sample);
        }
        lastVolume = sum / 256; // Simplify by averaging volume
        // Serial.println(lastVolume);
          // Simple condition to turn LED on/off based on simulated volume
      if(lastVolume > 500) {
        Serial.println("light!");
        digitalWrite(GREEN_LED, HIGH); // Turn LED on
        colorWipe(strip.Color(255,   0,   0), 50); // Red
      } else if (lastVolume > 300){
        digitalWrite(WHITE_LED, HIGH); // Turn LED on
        rainbow(10);             // Flowing rainbow cycle along the whole strip
      } else {
        digitalWrite(GREEN_LED, LOW); // Turn LED off
        digitalWrite(WHITE_LED, LOW); // Turn LED off
      }

    }
}


// Fill strip pixels one after another with a color. Strip is NOT cleared
// first; anything there will be covered pixel by pixel. Pass in color
// (as a single 'packed' 32-bit value, which you can get by calling
// strip.Color(red, green, blue) as shown in the loop() function above),
// and a delay time (in milliseconds) between pixels.
void colorWipe(uint32_t color, int wait) {
  for(int i=0; i<strip.numPixels(); i++) { // For each pixel in strip...
    strip.setPixelColor(i, color);         //  Set pixel's color (in RAM)
    strip.show();                          //  Update strip to match
    delay(wait);                           //  Pause for a moment
  }
}


// Theater-marquee-style chasing lights. Pass in a color (32-bit value,
// a la strip.Color(r,g,b) as mentioned above), and a delay time (in ms)
// between frames.
void theaterChase(uint32_t color, int wait) {
  for(int a=0; a<10; a++) {  // Repeat 10 times...
    for(int b=0; b<3; b++) { //  'b' counts from 0 to 2...
      strip.clear();         //   Set all pixels in RAM to 0 (off)
      // 'c' counts up from 'b' to end of strip in steps of 3...
      for(int c=b; c<strip.numPixels(); c += 3) {
        strip.setPixelColor(c, color); // Set pixel 'c' to value 'color'
      }
      strip.show(); // Update strip with new contents
      delay(wait);  // Pause for a moment
    }
  }
}

// Rainbow cycle along whole strip. Pass delay time (in ms) between frames.
void rainbow(int wait) {
  // Hue of first pixel runs 5 complete loops through the color wheel.
  // Color wheel has a range of 65536 but it's OK if we roll over, so
  // just count from 0 to 5*65536. Adding 256 to firstPixelHue each time
  // means we'll make 5*65536/256 = 1280 passes through this loop:
  for(long firstPixelHue = 0; firstPixelHue < 5*65536; firstPixelHue += 256) {
    // strip.rainbow() can take a single argument (first pixel hue) or
    // optionally a few extras: number of rainbow repetitions (default 1),
    // saturation and value (brightness) (both 0-255, similar to the
    // ColorHSV() function, default 255), and a true/false flag for whether
    // to apply gamma correction to provide 'truer' colors (default true).
    strip.rainbow(firstPixelHue);
    // Above line is equivalent to:
    // strip.rainbow(firstPixelHue, 1, 255, 255, true);
    strip.show(); // Update strip with new contents
    delay(wait);  // Pause for a moment
  }
}


// Rainbow-enhanced theater marquee. Pass delay time (in ms) between frames.
void theaterChaseRainbow(int wait) {
  int firstPixelHue = 0;     // First pixel starts at red (hue 0)
  for(int a=0; a<30; a++) {  // Repeat 30 times...
    for(int b=0; b<3; b++) { //  'b' counts from 0 to 2...
      strip.clear();         //   Set all pixels in RAM to 0 (off)
      // 'c' counts up from 'b' to end of strip in increments of 3...
      for(int c=b; c<strip.numPixels(); c += 3) {
        // hue of pixel 'c' is offset by an amount to make one full
        // revolution of the color wheel (range 65536) along the length
        // of the strip (strip.numPixels() steps):
        int      hue   = firstPixelHue + c * 65536L / strip.numPixels();
        uint32_t color = strip.gamma32(strip.ColorHSV(hue)); // hue -> RGB
        strip.setPixelColor(c, color); // Set pixel 'c' to value 'color'
      }
      strip.show();                // Update strip with new contents
      delay(wait);                 // Pause for a moment
      firstPixelHue += 65536 / 90; // One cycle of color wheel over 90 frames
    }
  }
}

```