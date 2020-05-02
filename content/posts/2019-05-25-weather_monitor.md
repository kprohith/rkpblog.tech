---
toc: true
layout: post
description: Automated Smart Weather Monitor using IoT
categories: [markdown]
title: Automated Weather Monitor using Arduino - an IoT Implementation

---

# Automated Weather Monitor using Arduino - an IoT Implementation

Automated Smart Weather Monitor using IoT

This repository contains the Arudino sketch for the automated smart weather monitor project.

The sketch is written in C++.

Replace your ssid and pass where asked.

Enter your ThingSpeak channel's API WRITE KEY in the updateTS function in place of "xxx"

```c++
#include <dht.h>
#include <SoftwareSerial.h>
dht DHT;
String temp;
String humid;
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
Serial.begin(9600);
esp8266.begin(9600);
Serial.println("Weather Monitor");
sendAT("AT+RST\r\n",2000);
sendAT("AT\r\n",1000);
sendAT("AT+CWMODE=1\r\n",1000);
sendAT("AT+CWJAP=\""SSID"\",\""PASS"\"\r\n",2000);
while(!esp8266.find("OK"))
{
  
}
sendAT("AT+CIFSR\r\n",1000);
sendAT("AT+CIPMUX=0\r\n",1000);
}

void loop() {
  // put your main code here, to run repeatedly:
int tempdata = DHT.read11(5);
Serial.print("Temperature = ");
Serial.print(DHT.temperature);
Serial.print("\t");
Serial.print("Humidity = ");
Serial.println(DHT.humidity);
temp = DHT.temperature;
humid = DHT.humidity;
updateTS(temp,humid);
delay(3000);
}






void updateTS(String T, String H)
{
  Serial.println("");
  sendAT("AT+CIPSTART=\"TCP\",\"api.thingspeak.com\",80\r\n",1000);
  delay(2000);
  String cmdlen;
  String cmd = "GET /update?key=xxx&field1="+T+"&field2="+H+"\r\n";
  cmdlen = cmd.length();
  sendAT("AT+CIPSEND="+cmdlen+"\r\n",2000);
  esp8266.print(cmd);
  Serial.println("");
  sendAT("AT+CIPCLOSE\r\n",2000);
  delay(15000);
}
```