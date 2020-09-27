# Bluetooth Communication Test Between Android and Arduino

## Background

An IoT work, for the middle school competition.

One house which consist of many kinds of household appliances (be represented by LEDs).

One application based on Android to remotely control the LEDs through Bluetooth protocol.

## Hardware

Arduino Uno

Bluetooth module HC-05

Android phone with API 29 (Android 10)

## Set up

- ![avatar](images/20200927_HC-05.JPG)

HC-05 TX ----> Arduino 10

HC-05 RX ----> Arduino 11

Arduino Serial ----> PC

## Firmware

On Arduino.

Arduino get command from the phone through Bluetooth, take action, and then send response with 6 digits, which mean the current statement of the LEDs.

```c++
#include <SoftwareSerial.h>

#define l_lr1 2
#define l_lr2 3
#define l_p 4
#define l_br 5
#define l_t 6
#define l_d 7

int status[8]={0,0,0,0,0,0,0,0};

SoftwareSerial bt(10,11);

void switchLight(int l);
void sendStatus();

void setup() {
  pinMode(l_lr1,OUTPUT);
  pinMode(l_lr2,OUTPUT);
  pinMode(l_p,OUTPUT);
  pinMode(l_br,OUTPUT);
  pinMode(l_t,OUTPUT);
  pinMode(l_d,OUTPUT);
  bt.begin(9600);
}

void loop() {
  if(bt.available()){
    int in=bt.read();
    switchLight(in);
    sendStatus();
  }
}

void switchLight(int l){
  if(status[l]==1){
    digitalWrite(l,LOW);
    status[l]=0;
  }
  else if(status[l]==0){
    digitalWrite(l,HIGH);
    status[l]=1;
  }
}

void sendStatus(){
  for(int i=2;i<=7;i++){
  bt.print(char(status[i]+48));
  }
}
```

## Software

On Android

Each time after sending a command, the App will waiting for the response from the Arduino, and then change the color of the buttons.

To download the whole project, click release.

![avatar](images/20200927_BluetoothApp01.JPG)

![avatar](images/20200927_BluetoothApp02.JPG)

## Problem and solution

While communicating, the Bluetooth socket on Android will take the first incoming byte as the beginning signal.

Just send a extra byte at the top of the byte stream.

`bt.print(char(48));`

