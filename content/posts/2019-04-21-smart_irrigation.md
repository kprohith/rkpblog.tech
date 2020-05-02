---
toc: true
layout: post
description: Automated Smart Irrigation system using IoT
categories: [markdown]
title: Smart Irrigation System using IoT
---

# Smart Irrigation System using IoT 

Automated Smart Irrigation system using IoT

This repository contains the Arudino sketch for the automated smart irrigation project using iot.

The sketch is written in C++.

Replace your ssid and pass where asked.

Enter your ThingSpeak channel's API WRITE KEY in the updateTS function in place of "xxx"

```c++

#include <SoftwareSerial.h>

int srdata; // variable to store raw sensor data
int prdata; // variable to store mapped sensor data
int pump_status;
SoftwareSerial esp8266(3,4);
#define SSID "xxx"
#define PASS "xxx"

String sendAT(String command, const int timeout)
{
  String response = "";
  esp8266.print(command);
  long int time = millis();
  while((time + timeout) > millis())
  {
    while(esp8266.available())
    {
      char c = esp8266.read();
      response += c;
    }
  }
  Serial.print(response);
  return response;
}


void setup() {
  // put your setup code here, to run once:
Serial.begin(9600); // start the serial communication with baud rate 9600
esp8266.begin(9600);
sendAT("AT+RST\r\n",2000);
sendAT("AT\r\n",1000);
sendAT("AT+CWMODE=1\r\n",1000);
sendAT("AT+CWJAP=\""SSID"\",\""PASS"\"\r\n",2000);
while(!esp8266.find("OK"))
{
  
}
sendAT("AT+CIFSR\r\n",1000);
sendAT("AT+CIPMUX=0\r\n",1000);

pinMode(A0,INPUT); // set the A0 pin as an input
pinMode(8, OUTPUT);
}

void loop() {
  // put your main code here, to run repeatedly:
  srdata=analogRead(A0); // read the sensor data and store it in srdata variable
  prdata=map(srdata,0,1023,100,0); // map the sensor data to range 0 to 100 using y=map(x,from low,from high, to low, to high) syntax
  Serial.print("Sensor Data:"); // Print the words "Sensor Data:" on serial monitor
  Serial.println(prdata); // Print the map sensor data on the serial monitor
  String sensor_value = String(prdata);
  if(prdata < 50)
  {
    digitalWrite(8,LOW);
    pump_status = 100;
  }
  else
  {
    digitalWrite(8,HIGH);
    pump_status = 0;
  }
  String pump = String(pump_status);
  updateTS(sensor_value,pump);
  delay(2000); // set a delay of 2 seconds before the next sensor data is fetched
}

void updateTS(String T, String P)
{
  Serial.println("");
  sendAT("AT+CIPSTART=\"TCP\",\"api.thingspeak.com\",80\r\n",1000);
  delay(2000);
  String cmdlen;
  String cmd = "GET /update?key=xxx&field1="+T+"&field2="+P+"\r\n";
  cmdlen = cmd.length();
  sendAT("AT+CIPSEND="+cmdlen+"\r\n",2000);
  esp8266.print(cmd);
  Serial.println("");
  sendAT("AT+CIPCLOSE\r\n",2000);
  Serial.println("");
  delay(15000);
  
}
```