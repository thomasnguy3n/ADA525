---
layout: post
title: "Codes Post 2 - Final Project "
date:   2023-11-23 10:33:30 +0200
author: "TN"
---

***Code version 7***

{% raw %}```CSS
body {
    font-family: Calibri, sans-serif;
    text-align: center;
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

  #tenSecButton:hover {
    background-color: #666;
    transition: background-color 0.3s ease-in-out;
  }
  
  #twentySecButton:hover {
    background-color: #666;
    transition: background-color 0.3s ease-in-out;
  }

  #tenSecButton.active {
    background-color: #0d9e4f;
  }

  #tenSecButton.active:hover {
    background-color: #666;
    transition: background-color 0.3s ease-in-out;
  }
  
  #twentySecButton.active {
    background-color: #0d9e4f;
  }

  #twentySecButton.active:hover {
    background-color: #666;
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
  <button id="tenSecButton" style="position: absolute; top: 5px; left: 5px;">10 sec Blow</button>
  <button id="twentySecButton" style="position: absolute; top: 50px; left: 5px;">20 sec Blow</button>

  <h2>ADA525 - Rapid Prototyping using Computional Tools</h2>
  <h1>Interactive Fan</h1>
  <h2>Remaining Blow Time: <span id="remainingTime"></span> seconds</h2>

  <a>Give that fan a shake, and watch it unleash a breeze of awesomeness!</a>
  <br>
  <br>
  <img id="fan-container" src="fan.png" width="150" height="150"></img>

  <script src="/socket.io/socket.io.js"></script>
  <script>
    const socket = io();

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

***Code version 8***

{% raw %}```CSS

```{% endraw %}

{% raw %}```CSS

```{% endraw %}