---
layout: post
title: "C++ // Arduino code"
date:   2023-11-07 10:33:30 +0200
author: "TN"
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
  analogWrite(mosfetPin, 128); // Adjust the PWM value (0-255) to control the fan speed
  delay(5000); // Run at this speed for 5 seconds

  // Turn off the fan
  analogWrite(mosfetPin, 0);
  delay(5000); // Wait for 5 seconds before repeating
}
```{% endraw %}