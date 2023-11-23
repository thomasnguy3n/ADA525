---
layout: post
title: "C++ // Arduino code - Final Project"
date:   2023-11-23 10:33:30 +0200
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

{% raw %}```C++
#include <Arduino.h>
#include <Adafruit_I2CDevice.h>
#include <SPI.h>
#include <Adafruit_TinyUSB.h>
#include <Arduino_LSM6DS3.h>
#include <Arduino_LSM6DS3.h>

void fanLogic();



const int mosfetPin = 6; 

void setup() {
  Serial.begin(9600);

 
  pinMode(mosfetPin, OUTPUT);

 
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

  if (accelMagnitude > 1.5) { 
    // Motion detected, run the fan logic
    fanLogic();
  } else {
    // No motion, turn off the fan
    analogWrite(mosfetPin, 0);
  }

  delay(1000); // Adjust the delay as needed
}

void fanLogic() {
  // Run the fan at a certain PWM value for 10 seconds
  analogWrite(mosfetPin, 200); // Adjust the PWM value (0-255) 
  delay(10000); // Run at this speed for 10 seconds

  // Turn off the fan for 1 second every 3 seconds within the 10-second period
  for (int i = 0; i < 10; i++) {
    analogWrite(mosfetPin, 0); // Turn off the fan
    delay(1000); // Off for 1 second
    analogWrite(mosfetPin, 200); // Turn on the fan
    delay(2000); // On for 2 seconds
  }

  // Turn off the fan at the end of the 10-second period
  analogWrite(mosfetPin, 0);
}

```{% endraw %}

***Code version 4***

{% raw %}```C++
#include <Arduino.h>
#include <Adafruit_I2CDevice.h>
#include <SPI.h>
#include <Adafruit_TinyUSB.h>
#include <Arduino_LSM6DS3.h>

const int mosfetPin = 6;

void fanLogic();

void setup() {
  Serial.begin(9600);    // For serial monitor
  Serial1.begin(9600);   // For serial port

  pinMode(mosfetPin, OUTPUT);

  if (!IMU.begin()) {
    Serial.println("Failed to initialize LSM6DS3!");
    while (1);
  }
}

void loop() {
  float x, y, z;

  // Read accelerometer data
  IMU.readAcceleration(x, y, z);

  // Print accelerometer data for debugging
  Serial.print(x);
  Serial.print(",");
  Serial.print(y);
  Serial.print(",");
  Serial.println(z);

  delay(1000);

  // Calculate the magnitude of acceleration
  float accelMagnitude = sqrt(x * x + y * y + z * z);

  // Print accelerometer magnitude data for debugging
//  Serial.print("Acceleration Magnitude: ");
//  Serial.println(accelMagnitude);

  if (accelMagnitude > 1.5) {
    // Motion detected, run the fan logic
    fanLogic();
  } else {
    // No motion, turn off the fan
    analogWrite(mosfetPin, 0);
  }

  delay(1000); // Sends data every second
}

void fanLogic() {
  // Run the fan at a certain PWM value for 10 seconds
  analogWrite(mosfetPin, 200); // Adjust the PWM value (0-255) 
  delay(10000); // Run at this speed for 10 seconds

  // Turn off the fan for 1 second every 3 seconds within the 10-second period
  for (int i = 0; i < 10; i++) {
    analogWrite(mosfetPin, 0); // Turn off the fan
    delay(1000); // Off for 1 second
    analogWrite(mosfetPin, 200); // Turn on the fan
    delay(2000); // On for 2 seconds
  }

  // Turn off the fan at the end of the 10-second period
  analogWrite(mosfetPin, 0);
}

```{% endraw %}

***Code version 5***

{% raw %}```C++
#include <Arduino.h>
#include <Adafruit_I2CDevice.h>
#include <SPI.h>
#include <Adafruit_TinyUSB.h>
#include <Arduino_LSM6DS3.h>

void fanLogic();

const int mosfetPin = 6;
unsigned long fanStartTime = 0;

void setup() {
  Serial.begin(9600);

  pinMode(mosfetPin, OUTPUT);

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

  if (accelMagnitude > 1.5) {
    // Motion detected, run the fan logic
    fanLogic();
  } else {
    // No motion, turn off the fan
    analogWrite(mosfetPin, 0);
  }

  delay(1000); // Adjust the delay as needed
}

void fanLogic() {
  // Run the fan at a certain PWM value for 10 seconds
  analogWrite(mosfetPin, 200); // Adjust the PWM value (0-255)
  fanStartTime = millis(); // Record the start time

  while (millis() - fanStartTime < 10000) {
    // Send the remaining blow time over the serial port
    Serial.print("Remaining Blow Time: ");
    Serial.println(10 - (millis() - fanStartTime) / 1000);
    delay(1000); // Update every second
  }

  // Turn off the fan at the end of the 10-second period
  analogWrite(mosfetPin, 0);
}
```{% endraw %}

***Code version 6***

{% raw %}```C++
#include <Arduino.h>
#include <Adafruit_I2CDevice.h>
#include <SPI.h>
#include <Adafruit_TinyUSB.h>
#include <Arduino_LSM6DS3.h>

void fanLogic();

const int mosfetPin = 6;
unsigned long fanStartTime = 0;

void setup() {
  Serial.begin(9600);

  pinMode(mosfetPin, OUTPUT);

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
 // Serial.println("Acceleration Magnitude: " + String(accelMagnitude));

  if (accelMagnitude > 1.5) {
    // Motion detected, run the fan logic
    fanLogic();
  } else {
    // No motion, turn off the fan
    analogWrite(mosfetPin, 0);
    Serial.println(0);
  }

  delay(1000); // Adjust the delay as needed
}

void fanLogic() {
  // Run the fan at a certain PWM value for 10 seconds
  analogWrite(mosfetPin, 200); // Adjust the PWM value (0-255)
  fanStartTime = millis(); // Record the start time

  while (millis() - fanStartTime < 10000) {
    // Send the remaining blow time over the serial port
  //  Serial.print("Remaining Blow Time: ");
    Serial.println(10 - (millis() - fanStartTime) / 1000);
    delay(1000); // Update every second
  }

  // Turn off the fan at the end of the 10-second period
  analogWrite(mosfetPin, 0);
}
```{% endraw %}

{% raw %}```HTML
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Fan Blow Time Monitor</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
    <button id="myButton" style="position: absolute; top: 5px; left: 5px;">10 sec Blow</button>
    <button id="myButton" style="position: absolute; top: 50px; left: 5px;">20 sec Blow</button>
    <button id="myButton" style="position: absolute; top: 95px; left: 5px;">30 sec Blow</button>
    <h2>ADA525 - Rapid Prototyping using Computional Tools</h2>
    <h1>Interactive Fan</h1>
  <h2>Remaining Blow Time: <span id="remainingTime"></span> seconds</h2>
    <a>Give that fan a shake, and watch it unleash a breeze of awesomeness!</a>
  <script src="/socket.io/socket.io.js"></script>
  <script>
    const socket = io();

    socket.on('timeUpdate', (remainingTime) => {
      document.getElementById('remainingTime').textContent = remainingTime;
    });
  </script>
</body>
</html>
```{% endraw %}

{% raw %}```CSS
body {
    font-family: Calibri, sans-serif;
    text-align: center;
  }

  #myButton {
    background-color: #007bff;
    color: white;
    padding: 10px 15px;
    border: rgb(35, 34, 34);
    border-radius: 5px;
    cursor: pointer;
    font-family: Calibri, sans-serif;
  }
```{% endraw %}

{% raw %}```Javascript
const express = require('express');
const app = express();
const http = require('http');
const server = http.createServer(app);
const io = require('socket.io')(server);
const { SerialPort } = require("serialport");

const port = 3000;

// Open the serial port connection
const serialPort = new SerialPort({
  path: "COM7",
  baudRate: 9600,
});

// Create an array to store the remaining blow time values
const remainingTimes = [];

// Listen for incoming data from the Arduino
serialPort.on('data', (data) => {
  const remainingTime = parseInt(data.toString());

  // Check if the received data is 0
  if (remainingTime === 0) {
    // Fan is not blowing, display 0 as the remaining time
    io.emit('timeUpdate', 0);
  } else {
    // Push the remaining time value into the array
    remainingTimes.push(remainingTime);

    // Emit the latest remaining time to connected clients
    io.emit('timeUpdate', remainingTimes[remainingTimes.length - 1]);
  }
});

// Handle errors if any occur
serialPort.on('error', (error) => {
  console.error('Error:', error);
});

// Serve the static HTML file
app.use(express.static('public'));

// Start the Express server
server.listen(port, () => {
  console.log('Server started on port ' + port);
});

```{% endraw %}

***References***



https://www.youtube.com/watch?v=Pw1kSS_FIKk&ab_channel=nenioc187
