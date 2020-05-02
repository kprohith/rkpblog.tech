---
toc: true
layout: post
description: Automated Smart Building using IoT
categories: [markdown]
title: Smart Presence Sensing System - IoT enabled Smart Building

---

# Smart Presence Sensing System - IoT enabled Smart Building

Automated Smart Building using IoT

This repository contains the Arudino sketch for the automated smart building project.

The sketch is written in C++.

Replace your ssid and pass where asked.

Enter your ThingSpeak channel's API WRITE KEY in the updateTS function in place of "xxx"

```c++
#include<SoftwareSerial.h> //include library
int data;
int data1;
boolean lock = true;
int counter = 0;
int Light_status = 10;
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
  pinMode(10,INPUT);
  pinMode(11,INPUT);
  pinMode(8,OUTPUT);
  pinMode(13,OUTPUT);
  pinMode(12,OUTPUT);
  Serial.print("Calibrating sensor");
  for(int i=0;i<30;i++){
    Serial.print(".");
    delay(1000);
  }
  Serial.println("done");
  Serial.println("Sensor active");
  digitalWrite(13,HIGH);
  digitalWrite(12,HIGH);
  delay(2000);
  digitalWrite(12,LOW);
  delay(50);
}

void loop() {
  // put your main code here, to run repeatedly:
  data = digitalRead(10);
  data1 = digitalRead(11);
  boolean lock=true;

  if(data==HIGH&&lock==true){
    lock=false;
    digitalWrite(12,HIGH);
    delay(500);
    digitalWrite(12,LOW);
    Serial.print("sensor IN");
    counter++;
    if(counter>0){
      digitalWrite(8,HIGH);
      Light_status=100;
      passTS(counter,Light_status);
    }
    delay(4000);
    lock=true;
  }

  if(data1==HIGH&&lock==true){
    lock=false;
    digitalWrite(12,HIGH);
    delay(500);
    digitalWrite(12,LOW);
    Serial.println("sensor OUT");
    counter--;
    if(counter==0){
      digitalWrite(8,LOW);
      Light_status=10;
      
    }
    passTS(counter,Light_status);
    delay(4000);
    lock=true;
  }
}

void passTS(int c, int l){
  String counter1 = String(c); //convert int to str data type
  Serial.print("Number of persons:");
  Serial.println(c);

  String Light_status1 = String(l);
  Serial.print("Light status:");
  Serial.println(l);
  updateTS(counter1,Light_status1);
  delay(3000);
}


void updateTS(String C, String L)
{
  Serial.println("");
  sendAT("AT+CIPSTART=\"TCP\",\"api.thingspeak.com\",80\r\n",1000);
  delay(2000);
  String cmdlen;
  String cmd = "GET /update?key=xxx&field1="+C+"&field2="+L+"\r\n";
  cmdlen = cmd.length();
  sendAT("AT+CIPSEND="+cmdlen+"\r\n",2000);
  esp8266.print(cmd);
  Serial.println("");
  sendAT("AT+CIPCLOSE\r\n",2000);
  Serial.println("");
  delay(1000);
}
```