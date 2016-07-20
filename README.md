# aws-mqtt-websockets

Middleware to use the AWS IOT service through websockets on the ESP8266.

## Changelog

* 1.0.alpha - Stable
* 0.3 - Implementation of circular buffer
* 0.2 - Auto reconnections
* 0.1 - Has known limitations and was not extensively tested

## Motivation

We cannot use the AWS MQTT service directly because the ESP8266 lacks support for TLS 1.2. We need to use websockets as a transport layer for MQTT through SSL, which is supported by the ESP8266.

This allows us to change the state of ESP8266 devices in real time, without using the AWS Restful API or an inefficient busy-waiting approach.

## Dependencies

| Library                   | Link                                                            | Use                 |
|---------------------------|-----------------------------------------------------------------|---------------------|
|aws-sdk-arduino            |https://github.com/svdgraaf/aws-sdk-arduino                      |aws signing functions|
|arduinoWebSockets          |https://github.com/Links2004/arduinoWebSockets                   |websocket comm impl  |
|Paho MQTT for Arduino      |https://projects.eclipse.org/projects/technology.paho/downloads  |mqtt comm impl       |

## Installation

1. Configure your Arduino IDE to compile and upload programs to ESP8266 (Arduino core for ESP8266 - details https://github.com/esp8266/Arduino)*
2. Install all dependencies as Arduino Libraries
3. Install aws-mqtt-websockets as an Arduino Library
4. Configure the example file with your AWS credentials and endpoints
5. Compile, upload and run!

The library was tested with 2.1.0-rc2 stage version of Arduino core for ESP8266 and with the most up to date GitHub code (see instruction in their page to use the git version) - No matter what version you chose, you may need to change it with the fix below

## Memory Leak

The Arduino Core for ESP8266 may have an issue that leads the program to lose heap space each time it connects and disconnects from a server. We've noticed this issue here (https://github.com/odelot/aws-mqtt-websockets/issues/2#issuecomment-205034367) and it seems that we were not the first one to notice that. Luckly, there is a fix (found by @daniele-salvagni, thanks :-) ): 

FIX: https://github.com/esp8266/Arduino/issues/230#issuecomment-116816950 

## Limitation

* needs to be stress tested
* may work in Arduino out of AWS environment (turn off SSL and set Path) 

## Usage

It is transparent. It is the same as the usage of Paho. There is just some changes in the connection step. See the example for details. Things you should edit in the example:
* ssid and password to connect to WiFi
* domain/endpoint for your AWS IOT service
* region of your AWS IOT service
* AWS access key \*\*
* AWS secret key

 It is good practice to create a new user (and grant only permissions to the IOT service). Avoid using the root user's credentials.
 
 ```
 //AWS IOT config, change these:
char wifi_ssid[]       = "your-ssid";
char wifi_password[]   = "your-password";
char aws_endpoint[]    = "your-endpoint.iot.eu-west-1.amazonaws.com";
char aws_key[]         = "your-iam-key";
char aws_secret[]      = "your-iam-secret-key";
char aws_region[]      = "eu-west-1";
const char* aws_topic  = "$aws/things/your-device/shadow/update";
int port = 443;

//MQTT config
const int maxMQTTpackageSize = 128;
const int maxMQTTMessageHandlers = 1;
 ```
