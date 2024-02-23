# Microcontroller Programming!

So this week my goal was to make somethig ~musical~ that will go with my little motor-controlled DJ table.

Initially I just wanted to control a speaker with Arduino and play some pre-downloaded music. Then Bobby suggested that I use the version o ESP32 that has built in WIFi and Bluetooth controls!

For a while I literally was failing to connect to the board with my Windows computer and getting this error:

```
A fatal error occurred: Failed to connect to ESP32: No serial data received.
```

Fixed it after downloading the correct VCP Driver! 

<img src="../img/week4/4-3.png" alt="VCP driver">

Then, I followed instructions on <a href="https://github.com/pschatzmann/ESP32-A2DP">THIS GITHUB OVER HERE</a> and successfully connected my phone's bluetooth to play music!

The next step is to sync some LED lights with the music. Here is the final product syncing to the amplitude of the music!


