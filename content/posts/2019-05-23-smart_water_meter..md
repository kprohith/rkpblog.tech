---
toc: true
layout: post
description: Smart Water Meter using IoT
categories: [markdown]
title: Smart Water Meter using an Arduino - An IoT Implementation

---
# Smart Water Meter using an Arduino - An IoT Implementation

Smart Water Meter using IoT

The status of the entire system is updated in real-time onto a cloud based IoT dashboard(ThingSpeak) which can be monitored from anywhere in the world.

This repository contains the Arudino sketch for the automated smart water meter project.

The sketch is written in C++.

Replace your ssid and pass where asked.

Enter your ThingSpeak channel's API WRITE KEY in the updateTS function in place of "xxx"

```c++
#include<SoftwareSerial.h> //include the software serial library
byte statusLed=13;
byte sensorInterrupt=0; //0=digitalpin 2
byte sensorPin=2;

//the hall-effect flow sensor outputs approximately 4.5 pulses per sencond per litre/minute of flow
float calibrationFactor=4.5;
volatile byte pulseCount;
float flowRate;
unsigned int flowMilliLitres;
unsigned long totalMilliLitres;
unsigned long oldTime;
unsigned int frac;
SoftwareSerial esp8266(3,4); //set the software serial pins RX pin =3, TX pin =4

//defination of variables
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
  // initialize a serial connection for reporting values to the host
  Serial.begin(9600);
  esp8266.begin(9600);
  sendAT("AT+RST\r\n",2000); //call sendAT function to send reset AT command
  connectwifi(); //call connectwifi function to connect to wifi
  //set up the status LED line as an output
  pinMode(statusLed,OUTPUT);
  digitalWrite(statusLed,HIGH); //WE HAVE AN ACTIVE-LOW LED attached
  pinMode(sensorPin,INPUT);
  digitalWrite(sensorPin,HIGH);
  pulseCount=0;
  flowRate=0.0;
  flowMilliLitres=0;
  totalMilliLitres=0;
  oldTime=0;

  // The Hall Effect sensor is connected to pin 2 which uses interrupt 0.
  // configured to trigger on a FALLING change(transition from HIGH to LOW state)
  attachInterrupt(sensorInterrupt,pulseCounter,FALLING);

  
}

/* main progrma loop */

void loop() {
  // put your main code here, to run repeatedly:
  if(millis()-oldTime>1000){ //only process counters once per second
    //Disable the interrupt while calculating flow rate and sending the values to host
    detachInterrupt(sensorInterrupt);
    //Because this loop may not complete in exactly 1 second intervals we calculate the number of milliseconds that have passed since the last execution and use
    //that to scale the output based on the number of pulses per second per units of measure(litres/minute in this case) coming from the sensor
    flowRate=((1000.0/(millis()-oldTime))*pulseCount)/calibrationFactor;

    //Note the time this processing pass was executed. 
    //Note that because we have disabled interrupts the
    //millis() function won't actually be incrementing
    //right at this point but it will return the value 
    //it was set to just before interrupts were disabled.
    oldTime=millis();

    //divide the flow rate in litres/minute by 60 to determine how many litres have
    //passed through the sensor in this 1 second interval, then multiply by 1000 to
    //convert to millilitres.
    flowMilliLitres=(flowRate/60)*1000;

    //add the millilitres passed in this second to the cumulative total
    totalMilliLitres+=flowMilliLitres;
    unsigned int frac;

    //print the flow rate for this second in litres/minute

    Serial.print("Flow Rate:");
    Serial.print(int(flowRate)); //print the integer part of variable
    Serial.print("."); //print the decimal point
    //determine the fractional part. The 10 multiplier gives us 1 decimal place.
    frac =(flowRate-int(flowRate))*10;
    Serial.print(frac,DEC); //print the fractional part of the variable
    Serial.print("L/min");

    //print the number of litres flowed in this second
    Serial.print("Current Liquid FLowing:");
    //output separator
    Serial.print(flowMilliLitres);
    Serial.print("mL/sec");

    //print the cumulative total of litres flowed since starting
    Serial.print("output Liquid QUantity:");
    //output separator
    Serial.print(totalMilliLitres);
    Serial.println("mL");

    //reset the pulse counter so we can start incrementing again
    pulseCount=0;

    //enable the interrupt again now that we have finished sending output
    attachInterrupt(sensorInterrupt,pulseCounter,FALLING);
  }
  String F = String(frac);//convert float to string datatype
  String FM = String(flowMilliLitres); // convert int to string datatype
  String TM = String(totalMilliLitres); //convert long to string datatype
  updateTS(F,FM,TM);; //call the function to update ThingSpeak channel
  delay(3000);
}

/*
 * * interrupt service routine
*/
  void pulseCounter(){
   //increment the pulse counter
   pulseCount++;
  }

void updateTS(String F,String FM, String TM)
{
  Serial.println("");
  sendAT("AT+CIPSTART=\"TCP\",\"api.thingspeak.com\",80\r\n",1000);
  delay(2000);
  String cmdlen;
  String cmd = "GET /update?key=xxx&field1="+F+"&field2="+FM+"field3="+TM+"\r\n"; //update data to thingspeak
  cmdlen = cmd.length();
  sendAT("AT+CIPSEND="+cmdlen+"\r\n",2000);
  esp8266.print(cmd);
  sendAT("AT+CIPCLOSE\r\n",2000);
  Serial.println("");
}
```