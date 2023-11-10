---
layout: post
title: "C++ // Arduino code - Final Project"
date:   2023-11-07 10:33:30 +0200
author: "TN"

***Code version 1***

Code works fine. The fan blows for 5 seconds, then shuts off for 1 second. Whenever I take the USB cable off from the computer, the fan shuts off. I need to find a way to make the fan, battery and Arduino independent from the computer.

I need to explore further with voltage and ampere, and how much the fan can handle.

---
{% raw %}```C++
#include <Arduino.h>
#include <Adafruit_I2CDevice.h>
#include <SPI.h>
 #include <Adafruit_TinyUSB.h>
// Define the pin connected to the MOSFET gate
const int mosfetPin = 6; // Change this to the actual pin you are using

void setup() {
  // Set the MOSFET pin as an OUTPUT
  pinMode(mosfetPin, OUTPUT);
}

void loop() {
  // Set the fan speed using PWM
  analogWrite(mosfetPin, 200); // Adjust the PWM value (0-255) to control the fan speed
  delay(5000); // Run at this speed for 5 seconds

  // Turn off the fan
  analogWrite(mosfetPin, 0);
  delay(1000); // Wait for 5 seconds before repeating
}
```{% endraw %}

***Code version 2***

Find ways to include gyro into the code where the PWM will activate whenever the arduino/gyro moves.