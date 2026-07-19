# Call-Controlled-Water-Pump-Motor
Arduino and SIM800L based Call Controlled Water Pump Motor for Agricultural Irrigation.
/*
===========================================================
 Project      : Call Controlled Water Pump Motor
 Controller   : Arduino UNO
 GSM Module   : SIM800L
 Author       : Akhil
 Description  :
 This project controls a water pump motor using
 a mobile phone call. Every incoming call toggles
 the motor ON or OFF through a relay.
===========================================================
*/

#include <SoftwareSerial.h>

// GSM Module Connections
SoftwareSerial gsm(7, 8);   // RX, TX

// Relay Pin
const int relayPin = 4;

// LED Pins (Optional)
const int greenLED = 5;
const int redLED = 6;

// Buzzer (Optional)
const int buzzer = 3;

bool motorState = false;

void setup()
{
  pinMode(relayPin, OUTPUT);
  pinMode(greenLED, OUTPUT);
  pinMode(redLED, OUTPUT);
  pinMode(buzzer, OUTPUT);

  digitalWrite(relayPin, LOW);
  digitalWrite(greenLED, LOW);
  digitalWrite(redLED, HIGH);

  Serial.begin(9600);
  gsm.begin(9600);

  delay(3000);

  gsm.println("AT");
  delay(1000);

  gsm.println("AT+CLIP=1");
  delay(1000);

  Serial.println("System Ready...");
}

void loop()
{
  if (gsm.available())
  {
    String data = gsm.readString();

    Serial.println(data);

    if (data.indexOf("RING") >= 0)
    {
      toggleMotor();

      gsm.println("ATH");
      delay(1000);
    }
  }
}

void toggleMotor()
{
  motorState = !motorState;

  digitalWrite(relayPin, motorState);

  if (motorState)
  {
    digitalWrite(greenLED, HIGH);
    digitalWrite(redLED, LOW);
  }
  else
  {
    digitalWrite(greenLED, LOW);
    digitalWrite(redLED, HIGH);
  }

  tone(buzzer, 1000);
  delay(300);
  noTone(buzzer);

  Serial.print("Motor Status : ");

  if (motorState)
    Serial.println("ON");
  else
    Serial.println("OFF");
}

/*
# Call Controlled Water Pump Motor

## Overview

This project enables remote control of a water pump motor using a mobile phone call through a GSM network.

The system is built using an Arduino UNO and a SIM800L GSM module. Whenever an authorized user places a call, the Arduino detects the incoming ring and toggles the relay to switch the motor ON or OFF.

---

## Features

- Remote motor control using GSM
- No Internet required
- Low-cost implementation
- Arduino-based automation
- Suitable for agricultural irrigation

---

## Hardware Used

- Arduino UNO
- SIM800L GSM Module
- Relay Module
- Water Pump Motor
- Breadboard
- Jumper Wires
- Power Supply

---

## Software

- Arduino IDE
- Embedded C

---

## Working

1. Call the SIM number.
2. SIM800L detects the incoming call.
3. Arduino reads the RING signal.
4. Relay toggles.
5. Water Pump turns ON/OFF.

---

## Applications

- Agricultural Irrigation
- Smart Farming
- Home Automation
- Industrial Motor Control

---

## Author

Akhil
B.Tech (ECE)
*/
