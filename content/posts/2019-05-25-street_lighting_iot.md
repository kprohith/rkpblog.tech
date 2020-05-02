---
toc: true
layout: post
description: Automatic street lighting system which senses amount of ambient light and accordingly switches on/off lights.
categories: [markdown]
title: Automated Street Lighting using Arduino - An IoT implementation
---

# Automated Street Lighting using Arduino - An IoT implementation

Automatic street lighting system which senses amount of ambient light and accordingly switches on/off lights. Built using 
* an Arduino Uno and
* a Raspberry Pi 3B+.

The status of the entire system is updated in real-time onto a cloud based IoT dashboard(ThingSpeak) which can be monitored from anywhere in the world.

This repository contains the Arudino sketch for the automated street lighting project.

The sketch is written in C++.

Replace your ssid and pass where asked.

Enter your ThingSpeak channel's API WRITE KEY in the updateTS function in place of "xxx"

```c++
#include<SoftwareSerial.h> //include the software serial library
int sensor; //varialbe to store sensor values
int data; //variable to store mapped sensro values
int Light_status=10; //var to store light status values
int threshold=38;

SoftwareSerial esp8266(3,4); //set the software serial pins RX=3, TX=4
//definition of variables
#define SSID "xxx" //replace with ssid of wifi
#define PASS "xxx" //replace with password of wifi

String sendAT(String command, const int timeout)
{
  String response="";
  esp8266.print(command);
  long int time = millis();
  while((time + timeout) > millis()){
    while(esp8266.available()){
      char c = esp8266.read();
      response += c;
    }
  }
  Serial.print(response);
  return response;
}

void connectwifi(){
  sendAT("AT\r\n",1000); //
  sendAT("AT+CWMODE=1\r\n",1000); 
  //call sendAT function to set esp8266 to station mode
  sendAT("AT+CWJAP=\""SSID"\",\""PASS"\"\r\n",2000);
  //AT command to connect with the wifi network
  while(!esp8266.find("OK")){
    //wait for connection
    
  }
  sendAT("AT+CIFSR\r\n",1000); //AT command to print IP address on serial monitor
  sendAT("AT+CIPMUX=0\r\n",1000); //AT command to set esp8266 to multiple connections
}


void setup() {
  // put your setup code here, to run once:
  Serial.begin(9600); //begin the serial communication with baud rate 9600
  esp8266.begin(9600);
  sendAT("AT+RST\r\n",2000); //call sendAT function to send reset AT command
  connectwifi(); //call connectwifi function to connect to wifi
  pinMode(8,OUTPUT);
}

void loop() {
  // put your main code here, to run repeatedly:
  sensor=analogRead(A0); //read raw sensor data and store it
  data=map(sensor,0,1023,0,100); //map the raw sensor values and store the result in data
  if(data<threshold){ //check of sensor value is less than threshold
    digitalWrite(8,HIGH); //switch on the light
    Light_status = 100; //update Light status variable to 100
  }
  else{
    digitalWrite(8,LOW); //switch off the light
    Light_status=10; //update the light_status variable to 10
  }
  String sensor_value = String(data); //convert int to str
  Serial.print("Light Intensity:");
  Serial.println(data); //print lighht status on serial monitor

  String threshold1= String(threshold); //convert int to str
  Serial.print("Threshold:");
  Serial.println(threshold); // print threshold on serial monitor

  updateTS(sensor_value,Light_status,threshold); //call function toupdate Thingspeak
  delay(3000);
}

  void updateTS(String S,String L, String T){
  Serial.println("");
  sendAT("AT+CIPSTART=\"TCP\",\"api.thingspeak.com\",80\r\n",1000);
  delay(2000);
  String cmdlen;
  String cmd = "GET /update?key=xxx&field1="+L+"&field2="+S+"field3="+T+"\r\n"; //update data to thingspeak
  cmdlen = cmd.length();
  sendAT("AT+CIPSEND="+cmdlen+"\r\n",2000);
  esp8266.print(cmd);
  sendAT("AT+CIPCLOSE\r\n",2000);
  Serial.println("");
  delay(1000);
}
```