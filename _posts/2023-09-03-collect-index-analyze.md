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
1. Arduino IDE
2. Docker
3. 

### Software Installation ###

## Configuration Section ##

### Configuring Home Assistant data collection ###

### Long term Home Assistant data storage ###

### Creating Home Assistant alerts ###

### Automating routine tasks ###