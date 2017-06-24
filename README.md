# Single Channel LoRaWAN Gateway

Version 4.0.3, June 22, 2017

Author: M. Westenberg (mw12554@hotmail.com)

Copyright: M. Westenberg (mw12554@hotmail.com)

All rights reserved. This program and the accompanying materials
are made available under the terms of the MIT License
which accompanies this distribution, and is available at
https://opensource.org/licenses/mit-license.php
This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

Maintained by Maarten Westenberg (mw12554@hotmail.com)

# Description

This repository contains a proof-of-concept implementation of a single channel LoRaWAN gateway.
The software implements a standard LoRa gateway with the following exceptions on changes:

-  This LoRa gateway is not a full gateway but it implements just a one-channel/one frequency gateway. 
The minimum amount of frequencies supported by a full gateway is 3, most suport 9 or more frequencies.
This software started as a proof-of-concept to prove that a single low-cost RRFM95 chip which was present in almost every
LoRa node in Europe could be used as a cheap alternative to the far more expensive full gateways that were 
making use of the SX1301 chip.

- As the software of this gateway will be used during the development phase of a project or in demo situations, 
the software is flexible and can be easily configured according to environment or customer requirements. 
There are two ways of interacting with the software: 1. Modifying the ESP-sc-gway.h file at compile time allows the 
administrator to set almost all parameters. 2. Using the webinterface (http://<gateway_IP>) will allow administrators to set and reset several of the 
parameters at runtime.


### testing

The single channel gateway has been tested on a gateway with the Wemos D1 Mini, using a HopeRF RFM95W transceiver.  
The LoRa nodes tested are:

- TeensyLC with HopeRF RFM95 radio
- Arduino Pro-Mini (default Armega328 model, 8MHz 3.3V and 16MHz 3.3V)
- ESP8266 basednodes.


## Dependencies

The software is dependent on several pieces of software, the Arduino IDE for ESP8266 
being the most important. Several other libraries are also used by this program, make sure you install those libraries with the IDE:

- gBase64 library, The gBase library is actually a base64 library made 
	by Adam Rudd (url=https://github.com/adamvr/arduino-base64). I changed the name because I had
	another base64 library installed on my system and they did not coexist well.
- Time library (http://playground.arduino.cc/code/time)
- Arduino JSON; Needed to decode downstream messages
- SimpleTimer; ot yet used, but reserved for interrupt and timing
- WiFiManager
- ESP8266 Web Server
- Streaming library, used in the wwwServer part
- AES library (taken from ideetron.nl) for downstream messages
- Time

For convenience, the libraries are also found in this gitshub repository in the libraries directory. However, they are NOT part of the ESP 1channel gateway and may have their own licensing.
However, these libraries are not part of the single-channel Gateway software.

## Connections

See http://things4u.github.io in the hardware section for building and connection instructions


## Configuration

There are two ways of changing the configuration:

1. Changing the ESP-sc-gway.h file at compile-time
2. Run the http://<gateway-IP> web interface to change setting at complie time.

### Connect to WiFi with WiFiManager

The easiest way to configure the Gateway on WiFi is by using the WiFimanager function. This function works out of the box. WiFiManager will put the gateway in accesspoint mode so that you can connect to it as a WiFi accesspoint. 

The standard access point name used by the gateway is "ESP8266 Gway" and its password is "ttnAutoPw". After binding to the access point with your mobile phone or computer, go to htp://192.168.4.1 in a browser and tell the gateway to which WiFi network you want it to connect, and specify the password.

The gateway will then reset and bind to the given network. If all goes well you are now set and the ESP8266 will remember the network that it must connect to. NOTE: As long as the accesspoint that the gateway is bound to is present, the gateway will not any longer work with the wpa list of known access points.
If necessary, you can delete the current access point in the webserver and power cycle the gateway to force it to read the wpa array again.


Note: All settings and the more advanced features can be set by editing the configuration file (see the next section). 


### Editing ESP-sc-gway.h

All user configurable settings are put in the ESP-sc-gway.h file as much as possible.
The most important things to configure to your own environment are:

- Set the _SPREADING factor to the desired SF7, SF8 - SF12 value. Please note that the default frequency used is 868.1 MHz which can be changed in the loraModem.h file.
- In order to have the gateway send downlink messages on the pre-set spreading factor and on the default frequency, you have to set the _STRICT_1Ch parameter to 1. Note that when it is not set to 1, the gateway will respond to downlink requests with the frequency and spreading factor set by the backend server. And at the moment TTN responds to downlink messages for SF9-SF12 in the RX2 timeslot and with frequency 869.525MHz and on SF12 (according to the LoRa standard when sending in the RX2 timeslot). Please 
- static char *wpa[WPASIZE][2] contains the array of known WiFi access points the Gateway will connect to.
Make sure that the dimensions of the array are correctly defined in the WPASIZE settings. 
Note: When the WiFiManager software is enabled (it is by default) there must at least be one entry in the wpa file, wpa[0] is used for storing WiFiManager information.
- Only the sx1276 (and HopeRF 95) radio modules are supported at this time. The sx1272 code should be 
working without much work, but as I do not have one of these modules available I cannot test this.
- This software allows to connect to 2 servers at the same time (as most gateways do BTW). 
Make sure that you set:

 \#define _THINGPORT 1701							// Your UDP server should listen to this port  
 \#define _THINGSERVER "your_server.com"			// Server URL of the LoRa udp.js server program  

- Set the identity parameters for your gateway:  
// Gateway Ident definitions, please set location, email and description.  

\#define _DESCRIPTION "ESP-Gateway"  
\#define _EMAIL "your.email@provider.com"  
\#define _PLATFORM "ESP8266"  
\#define _LAT 52.00  
\#define _LON 5.00  
\#define _ALT 0  


### Lora Radio Defaults:  

- LoRa:   SF9 at 868.1 Mhz
- Server:  
  \#define _TTNSERVER "router.eu.thethings.network"  
  \#define _TTNPORT 1700  
  
  These two settings are mandatory and should point to the standard servers of TTN
  40.114.249.243, port 1700 
  
Edit .h file (ESP-sc-gway.h) to change configuration (look for: "Configure these values!").

### Webserver

The built-in webserver can be used to display status and debugging information. It can be accessed with the following URL: http://YourGatewayIP:80 The webserver shows various configuration settings as well as providing functions to set parameters.

The following parameters can be set using the webServer. 
- Debug Level (0-4)
- CAD mode on or off (STD mode)
- Switch frequency hopping on and off
- When frequency Hoppin is off: Select the frequency the gateway will work on
- When CAD mode is off: Select the Spreading Factor (SF) the gateway will work with



# To DO

The following things are still on my wish list to make to the single channel gateway:
- Receive downstream message with commands form the server. These can be used to configure
  the gateway through downlink messages (such as setting the SF)


# Release Notes

New features in version 4.0.4 (June 24, 2017):

- Review of the _wwwServer.ino file. Repaired some of the bugs causing crashes of the webserver.
- 

New features in version 4.0.3 (June 22, 2017):

- Added CMAC functions so that the sensor functions work as expected over TTN
- Webserver prints a page in chunks now, so that memory usage is lower and more heap is left over for variables
- Webserver does refresh every 60 seconds
- Implemented suggested change of M. for answer to PKT_PULL_RESP
- Updated README.md to correctly displa all headers
- Several small bug fixes

New features in version 4.0.0 (January 26, 2017)):

- Implement both CAD (Channel Activity Detection) and HOP functions (HOP being experimental)
- Message history visible in web interface
- Repaired the WWW server memory leak (due to String assignments)
- Still works on one interrupt line (GPIO15), or can be configured to work with 2 interrupt lines for dio0 and dio1
  for two or more interrupt lines (better performance for automatic SF setting?)
- Webserver with debug level 3 and level 4 (for interrupt testing).
  dynamic setting thorugh the web interface. Level 3 and 4 will show more info
  on sf, rssi, interrupt flags etc.
- Tested on Arduino IDE 1.18.0
- See http://things4u.github.io for documentation

New features in version 3.3.0 (January 1, 2017)):

- Redesign of the Webserver interface
- Use of the SPIFFS filesystem to store SSID, Frequency, Spreading Factor and Framecounter to survice reboots and resets of the ESP8266
- Possibility to set the Spreading Factor dynamically throug the web interface
- Possibility to set the Frequency in the web interface
- Reset the Framecounter in te webinterface

New features in version 3.2.2 (December 29, 2016)):

- Repair the situation where WIFIMANAGER was set to 0 in the ESP-sc-gway.h file. The sketch would not compile which is now repaired
- The compiler would issue a set of warnings related to the ssid and passw setting in the ESP-sc-geway.h file. Compiler was complaining (and it should) because char* were statically initialised and modified in the code.

New features in version 3.2.1 (December 20, 2016)):

- Repair the status messages to the server. All seconds, minutes, hours etc. are now reported in 2 digits. The year is reported in 4 digits.

New features in version 3.2.0 (December 08, 2016)):

- Several bugfixes

New features in version 3.1 (September 29, 2016)):

- In the ESP-sc-gway.h it is possible to set the gateway as sensor node as well. Just set the DevAddr and AppSKey in the _sensor.ino file and be able to forward any sensor or other values to the server as if they were coming from a LoRa node.
- If the #define _STRICT_1CH is set (to 1) then the system will be able to send downlink messages to LoRa nodes that are strict 1-channel devices (all frequencies but frequency 0 are disabled and Spreading Factor (SF) is fixed to one value).
- Code clean-up. The code has been made smaller in the area of loraWait() functions and where the radio is initiated for receiving of transmitting messages.
- Several small bug fixes
- Licensing, the license has been changed to MIT

New features in version 3.0 (September 27, 2016):

- WiFiManager support
- Limited SPIFF (filesystem) support for persistent data storage
- Added functions to webserver. Webserver port now 80 by default (!)

Other

- Supports ABP nodes (TeensyLC and Arduino Pro-mini)
- Supports OTAA functions on TeensyLC and Arduino Pro-Mini (not all of them) for SF7 and SF8.
- Supports SF7, SF8. SF7 is tested for downstream communication
- Listens on configurable frequency and spreading factor
- Send status updates to server (keepalive)
- PULL_DATA messages to server
- It can forward messages to two servers at the same time (and read from them as well)
- DNS support for server lookup
- NTP Support for time sync with internet time servers
- Webserver support (default port 8080)
- .h header file for configuration

Not (yet) supported:

- SF7BW250 modulation
- FSK modulation
- RX2 timeframe messages at frequency 869,525 MHz are not (yet) supported.
- SF9-SF12 downlink messaging available but needs more testing

## Note

There seems to be a lot of variation in Arduino Pro-Mini devices. I have at least 4 different types. 
Most work, and some do not work with the 1ch Gateway mostly due to timing issues so it seems.
The standard 8MHz type with the large chrystal on board seems to be working best.

The code is for testing and development purposes only, and is not meant for production usage. 

Version 1 was originally based on code base of Single Channel gateway for RaspberryPI
which was developed by Thomas Telkamp. Code was ported and extended to run on ESP8266 mcu and provide RTC, Webserver and DNS services.
Version 2.0 adds several enhancements and part have been completely redesigned.
Changes include two-way traffic. The code is also slit over multiple source files which makes editing easier.
Version 3.0 includes WiFi Master support which makes it easy to use the 1-ch gateway for demo purposes where you need to quickly connect the gateway to a WiFi accesspoint (or your mobile phone).It also contains limited SPIFF filesystem support which enables the ESP to store its data in a more persistent way.
Version 4.0 includes CAD and Frequency Hopping support and better web insterface allowing more parameters to be set over the web


# License

The source files of the gateway sketch in this repository is made available under the MIT
license. The libraries included in this repository are included for convenience only and all have their own license, and are not part of the ESP 1ch gateway code.
