# Home automation Setup #


## Summary ##

In this post, we will cover the steps required to set up a data logging and home automation system.

A number of sensors will be used to feed input data into a database.

Along with storing the data, it will also be output in visual displays or by generating audio alerts.

Then, by programming timers, triggers, and other forms of automation we can control the local environment 

using the inputs and outputs of our data logging system.



## Hardware Section ##

### Hardware Required ###
1. Linux server, or docker host
    - database will store received sensor data here
    - can be on premise, or cloud-based
    - recommend at least 100gb of storage space available
2. garden sensor board
    - ESP32 mcu with WiFi and BT
    - DHT22 Temp, Humidity sensor
    - any generic arduino or Raspberry Pi based sensor board will work.
3. RGB LED smart bulb
    - wifi based RGBA lamps work well, and don't require additional hardware.
4. Speaker / Mic combo device
    - Search for 'usb speaker with mic' keywords in Amazon. options start at ~ $10.00 USD
5. USB Hardware buttons
    - can be a fancy foot pedal, or a simple programmable usb press button with two states.
6. TV / Monitor
    - used for displaying the data dashboard
7. Thin client (Raspberry Pi or similar SBC, micro SFF PC, etc.)
    - controls the data dashboard, audio alerts

### Hardware Options ###

- If your already use Zigbee or ZWave to control smart devices those can be used instead of WiFi based smart bulbs.
- The garden sensor board is based on a ESP32 and can send sensor data over USB instead of WiFi. This setup is beyond the scope of this guide.

### Assembling Hardware ###

## Software Section ##

### Software Required ###
1. Docker and Docker Compose - used to containerize the data receiver, HTTP proxy, and flask application
2. Arduino IDE - Used for programming sensor board
3. nginx - Used to proxy HTTP requests from sensorboard and browser to flask application
4. Python flask - used to process sensor board data and send to database. Used to build thin client site (graphs, stats, alerts, etc.)
5. SQLite - Database to store sensor board readings

### Programming Sensor Board ###
1. In Arduino IDE, create a new sketch.
2. In Arduino IDE, download and import all required libraries.
3. Configure Arduino IDE for sensor board configuration
4. Compile and upload sketch to sensor board.
5. Power on, calibration and testing.

### Sensor Reading Server Setup ###
1. Create a project folder to contain docker files (ex: /home/user/docker/sensorServer) 
2. Create a dockerfile in the project folder
3. Create a docker-compose.yaml file in the project folder
    - define services for application and nginx proxy
4. create a flask application foder in the project folder (ex: /home/user/docker/sensorServer/myApp)
5. create a main.py file in the application folder containing all code necessary to receive readings and save to database
6. create a database folder in the application folder (ex: /home/user/docker/sensorServer/myApp)
7. create an nginx folder in the project folder (ex: /home/user/docker/sensorServer)
8. create a dockerfile in the nginx folder
9. Build and run the application (and all its services) using Docker Compose

### Reading dashboard app setup ###
1. create a templates folder in the application folder (ex: /home/user/docker/sensorServer/myApp/templates)
2. define jinja templates for sensorboardStats, sensorboardReadings, sensorboardCreateAlert, sensorboardUpdateAlert, sensorboardDeleteAlert
3. update main.py file to add routes associated to each new template
4. re-build and run the application using Docker Compose
5. setup dashboard thin client to refresh dashboard pages periodically.


## Configuration Section ##

### Configuring Home Assistant data collection ###
1. create a REST sensor in HA for each metric available in the sensorboardAlert API (ambient temp, nutrient temp, moisture, humidity)

### Visualize water levels using Home Assistant ###
1. configure RGB LED lamp in HA
2. create automation to change RGB LED to flashing red on/off at 1hz if moisture level is below watering Threshold.
3. create automation to change RGB LED to RGB value based moisture level, use Orange as dryest, Green would be idea moisture level, and Blue as maximum moisture.

### Creating Home Assistant alerts ###
1. create automation to send push notification to mobile phone if plants were last watered more than a week ago.

### Automating routine tasks ###
5. Create automation that sets a 'recently watered' sensor to True when the moisture level rises from Orange (or lower) to a Green level (or higher).
6. create automation that resets 'recently watered' sensor to False 24 hours after it is set to True.