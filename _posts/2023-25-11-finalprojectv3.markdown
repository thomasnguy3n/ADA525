---
layout: post
title: "Final code - Final Project "
date:   2023-11-25 10:33:30 +0200
author: "TN"
---

***Arduino / C++ code***

{% raw %}```C++
#include <Arduino.h>
#include <Adafruit_I2CDevice.h>
#include <SPI.h>
#include <Adafruit_TinyUSB.h>
#include <Arduino_LSM6DS3.h>

void fanLogic();

const int mosfetPin = 6;
unsigned long fanStartTime = 0;
int blowTime = 10; // Default blow time - 10 seconds

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

  // Check for incoming blow time updates from the serial port
  if (Serial.available()) {
    int newBlowTime = Serial.parseInt();
    if (newBlowTime > 0) {
      blowTime = newBlowTime; // Update the current blow time
    }
  }

  // Check for motion
  if (accelMagnitude > 1.5) {

    fanLogic();
  } else {

    analogWrite(mosfetPin, 0);
    Serial.println(0);
  }

  delay(1000); //Delay time
}

void fanLogic() {

  analogWrite(mosfetPin, 255); // Speed of the fan
  fanStartTime = millis(); // Record the start time of the fan

  while (millis() - fanStartTime < blowTime * 1000) {
    // Send the remaining blow time over the serial port
    Serial.println(blowTime - (millis() - fanStartTime) / 1000);
    delay(1000); 
  }

  // Turn off the fan at the end of the blow time period
  analogWrite(mosfetPin, 0);
}
```{% endraw %}

***Web Server - NODE.JS code***

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
  <h1>Interactive Fan</h1>
  <h2>Remaining Blow Time: <span id="remainingTime"></span> seconds</h2>

  <a>Give that fan a shake, and watch it unleash a breeze of awesomeness!</a>
  <br>
  <br>
  <br>
  <br>
  <img id="fan-container" src="fan.png" width="150" height="150"></img>
  <br>
  <br>
  <br>
  <a>Ignite the Breeze: Set Your Epic Blow Timer!</a>
  <br>
  <br>
  <button id="tenSecButton">10 sec Blow</button>
  <button id="twentySecButton">20 sec Blow</button>
  <button id="thirtySecButton">30 sec Blow</button>
  <button id="sixtySecButton">60 sec Blow</button>
  <br>
  <br>
  <script src="/socket.io/socket.io.js"></script>
  <script>
    const socket = io();
    let blowTime = 10; 

    const tenSecButton = document.getElementById('tenSecButton');
    const twentySecButton = document.getElementById('twentySecButton');
    const thirtySecButton = document.getElementById('thirtySecButton');
    const sixtySecButton = document.getElementById('sixtySecButton');

    // Handle button clicks to update the blow time
    document.getElementById('tenSecButton').addEventListener('click', () => {
      blowTime = 10;
      socket.emit('blowTimeUpdate', blowTime);

      tenSecButton.style.backgroundColor = '#9e0d0d'; 
      twentySecButton.style.backgroundColor = '#9c9d9e';
      thirtySecButton.style.backgroundColor = '#9c9d9e';
      sixtySecButton.style.backgroundColor = '#9c9d9e';
    });

    document.getElementById('twentySecButton').addEventListener('click', () => {
      blowTime = 20;
      socket.emit('blowTimeUpdate', blowTime);

      twentySecButton.style.backgroundColor = '#9e0d0d';
      tenSecButton.style.backgroundColor = '#9c9d9e';
      thirtySecButton.style.backgroundColor = '#9c9d9e';
      sixtySecButton.style.backgroundColor = '#9c9d9e';
    });

    document.getElementById('thirtySecButton').addEventListener('click', () => {
      blowTime = 30;
      socket.emit('blowTimeUpdate', blowTime);

      thirtySecButton.style.backgroundColor = '#9e0d0d';
      twentySecButton.style.backgroundColor = '#9c9d9e';
      tenSecButton.style.backgroundColor = '#9c9d9e';
      sixtySecButton.style.backgroundColor = '#9c9d9e';
    });

    document.getElementById('sixtySecButton').addEventListener('click', () => {
      blowTime = 60;
      socket.emit('blowTimeUpdate', blowTime);

      sixtySecButton.style.backgroundColor = '#9e0d0d';
      thirtySecButton.style.backgroundColor = '#9c9d9e';
      twentySecButton.style.backgroundColor = '#9c9d9e';
      tenSecButton.style.backgroundColor = '#9c9d9e';
    });

    // Receive updated remaining time from the server
    socket.on('timeUpdate', (remainingTime) => {
      document.getElementById('remainingTime').textContent = remainingTime;

      // Start or stop the animation based on remaining time
      const fanElement = document.getElementById('fan-container');
      if (remainingTime === 0) {
        fanElement.style.animationPlayState = 'paused';
      } else {
        fanElement.style.animationPlayState = 'running';
      }
    });

    // Stop the animation when it finishes
    const fanElement = document.getElementById('fan-container');
    fanElement.addEventListener('animationend', () => {
      fanElement.style.animationPlayState = 'paused';
    });
  </script>
</body>
</html>
```{% endraw %}

{% raw %}```CSS
body {
    font-family: Calibri, sans-serif;
    text-align: center;
    background-color:rgb(232, 232, 232)
  }

  #tenSecButton {
    background-color: #9c9d9e;
    color: white;
    padding: 10px 15px;
    border: rgb(35, 34, 34);
    border-radius: 5px;
    cursor: pointer;
    font-family: Calibri, sans-serif;
  }

  #twentySecButton {
    background-color: #9c9d9e;
    color: white;
    padding: 10px 15px;
    border: rgb(35, 34, 34);
    border-radius: 5px;
    cursor: pointer;
    font-family: Calibri, sans-serif;
  }

  #thirtySecButton {
    background-color: #9c9d9e;
    color: white;
    padding: 10px 15px;
    border: rgb(35, 34, 34);
    border-radius: 5px;
    cursor: pointer;
    font-family: Calibri, sans-serif;
  }

  #sixtySecButton {
    background-color: #9c9d9e;
    color: white;
    padding: 10px 15px;
    border: rgb(35, 34, 34);
    border-radius: 5px;
    cursor: pointer;
    font-family: Calibri, sans-serif;
  }

  #tenSecButton:hover {
    background-color: #9e0d0d;
    transition: background-color 0.3s ease-in-out;
  }
  
  #twentySecButton:hover {
    background-color: #9e0d0d;
    transition: background-color 0.3s ease-in-out;
  }

  #thirtySecButton:hover {
    background-color: #9e0d0d;
    transition: background-color 0.3s ease-in-out;
  }

  #sixtySecButton:hover {
    background-color: #9e0d0d;
    transition: background-color 0.3s ease-in-out;
  }


  @keyframes fan-rotation {
    0% {
      transform: rotate(0deg);
    }
    100% {
      transform: rotate(360deg);
    }
  }


  
  #fan-container {
    animation: fan-rotation 2s linear infinite;
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
}, (err) => {
  if (err) {
    console.error('Error opening serial port:', err);
  } else {
    console.log('Serial port connected successfully!');
  }
});

// Create an array to store the remaining blow time values
let remainingTimes = [];

// Listen for incoming data from the Arduino
serialPort.on('data', (data) => {
  const remainingTime = parseInt(data.toString());

  // Push the remaining time value into the array
  remainingTimes.push(remainingTime);

  // Emit the latest remaining time to connected clients
  io.emit('timeUpdate', remainingTimes[remainingTimes.length - 1]);
});

// Handle errors if any occur
serialPort.on('error', (error) => {
  console.error('Error:', error);
  io.emit('connectionStatus', 'Disconnected');
});

// Handle blow time updates from the client
io.on('connection', (socket) => {
  socket.on('blowTimeUpdate', (blowTime) => {
    // Update the current blow time value
    blowTime = parseInt(blowTime);
    remainingTimes = []; // Clear the remaining time array

    // Send the updated blow time to the Arduino
    serialPort.write(blowTime + '\n');
    console.log('Blowtime: ' + blowTime);

    // Broadcast the updated blow time to all connected clients
    io.emit('timeUpdate', blowTime);

      // Send the initial connection status to the client

  });
});

// Serve the static HTML file
app.use(express.static('public'));

// Start the Express server
server.listen(port, () => {
  console.log('Server started on port ' + port);
});
```{% endraw %}