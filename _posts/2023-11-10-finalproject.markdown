---
layout: post
title: "C++ // Arduino code - Final Project"
date:   2023-11-07 10:33:30 +0200
author: "TN"
---

***Code version 1***

{% raw %}```C++
#include <Arduino.h>
#include <Adafruit_I2CDevice.h>
#include <SPI.h>
 #include <Adafruit_TinyUSB.h>
// Define the pin connected to the MOSFET gate
const int mosfetPin = 6; 

void setup() {
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

The code is functioning correctly. Currently, the fan operates for 5 seconds, followed by a 1-second off period. However, when I disconnect the USB cable from the computer, the fan stops. I'm looking for a solution to make the fan, battery, and Arduino independent of the computer.

I plan to go deeper into understanding voltage and amperage, determining the fan's capacity, and exploring ways to integrate the gyro into the code. Ideally, I want the PWM to activate whenever the Arduino or gyro moves.

***Code version 2***

{% raw %}```C++
#include <Arduino.h>
#include <Adafruit_I2CDevice.h>
#include <SPI.h>
#include <Adafruit_TinyUSB.h>
#include <Arduino_LSM6DS3.h>

// Define the pin connected to the MOSFET gate
const int mosfetPin = 6; 

void setup() {
  Serial.begin(9600);

  // Set the MOSFET pin as an OUTPUT
  pinMode(mosfetPin, OUTPUT);

  // Initialize the LSM6DS3 sensor
  if (!IMU.begin()) {
    Serial.println("Failed to initialize LSM6DS3!");
    while (1);
  }
}

void loop() {
  float x, y, z;
  
  // Read gyro data
  IMU.readAcceleration(x, y, z);

  // Calculate the magnitude of acceleration
  float accelMagnitude = sqrt(x * x + y * y + z * z);

  // Print accelerometer data for debugging
  Serial.println("Acceleration Magnitude: " + String(accelMagnitude));

  if (accelMagnitude > 1.5) { // Adjust the threshold
    // Motion detected, turn on the fan
    analogWrite(mosfetPin, 200); //  control the fan speed
    delay(10000); // Run at this speed for 10 seconds
  } else {
    // No motion, turn off the fan
    analogWrite(mosfetPin, 0);
  }

  delay(1000); // Adjust the delay as needed
}

```{% endraw %}

The code works perfectly fine. I may need to adjust the accelMagnitude, which is the feature determining when the fan should activate based on the gyro ratio. Additionally, I have to enhance the PWM feature in the code to optimize electricity usage and extend battery life.

***Code version 3***

Still working on it.