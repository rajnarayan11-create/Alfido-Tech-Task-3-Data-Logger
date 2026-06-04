
# Alfido Tech Internship - Task 3: Data Logger

## Intern Details
* **Name:** Raj Narayan Gaur
* **Domain:** Embedded Systems / IoT Intern
* **Organization:** Alfido Tech

---

## Project Overview
This project is a Virtual Data Logger built using an Arduino Mega 2560 in Cirkit Designer. It reads ambient light levels from a Photoresistor (LDR) sensor and temperature data from an LM393/LM35 Temperature sensor. The collected sensor data is printed in real-time onto the Serial Monitor and concurrently logged into a Micro SD Card with precise timestamps provided by a DS1307 Real-Time Clock (RTC) module.

## Components Used (Virtual Simulation)
* **Microcontroller:** Arduino Mega 2560 R3
* **Storage:** Micro SD Card Module (SPI Interface)
* **Timekeeping:** DS1307 RTC Module (I2C Interface)
* **Sensors:** * LM393/LM35 Temperature Sensor Module
  * Photoresistor (LDR) Light Sensor Module

## Pin Connections (Code-Verified)
* **SD Card Module:** MISO -> 50, MOSI -> 51, SCK -> 52, CS -> 53 (Standard Mega SPI)
* **RTC Module:** SDA -> 20, SCL -> 21 (Standard Mega I2C)
* **LDR Sensor:** Analog Output -> A0
* **Temperature Sensor:** Analog Output -> A1

---

## Circuit Diagram
![Circuit Diagram](apni_circuit_image_ka_exact_naam_yahan_likhein.png)

## Project Video Demonstration
https://github.com/user-attachments/assets/5a1a990c-2608-4b3d-9162-a3988b4a87dd

---

## Source Code

```cpp
#include <SPI.h>
#include <SD.h>
#include <Wire.h>
#include <RTClib.h>

RTC_DS1307 rtc;
const int chipSelect = 53; // Default SPI CS pin for Arduino Mega

void setup() {
  Serial.begin(9600);
  
  // Initialize RTC
  if (!rtc.begin()) {
    Serial.println("Couldn't find RTC module!");
    while (1);
  }
  
  // Initialize SD Card
  if (!SD.begin(chipSelect)) {
    Serial.println("SD Card initialization failed!");
    while (1);
  }
}

void loop() {
  // Read sensor values from exact virtual pins
  int ldrValue = analogRead(A0);  
  int tempValue = analogRead(A1); 
  
  // Convert sensor value to temperature in Celsius
  float temperature = (tempValue * 5.0 / 1024.0) * 100.0;
  
  // Get current time from RTC
  DateTime now = rtc.now();

  // Print output to Serial Monitor for verification
  Serial.print("Temperature (C): ");
  Serial.print(temperature);
  Serial.print(" | LDR Value: ");
  Serial.print(ldrValue);
  Serial.print(" | Time: ");
  Serial.print(now.year());
  Serial.print('/');
  Serial.print(now.month());
  Serial.print('/');
  Serial.print(now.day());
  Serial.print(" ");
  Serial.print(now.hour());
  Serial.print(':');
  Serial.print(now.minute());
  Serial.print(':');
  Serial.println(now.second());

  // Save the formatted logs to the SD Card
  File dataFile = SD.open("datalog.txt", FILE_WRITE);
  if (dataFile) {
    dataFile.print("Temperature (C): ");
    dataFile.print(temperature);
    dataFile.print(" | LDR Value: ");
    dataFile.print(ldrValue);
    dataFile.print(" | Time: ");
    dataFile.print(now.year());
    dataFile.print('/');
    dataFile.print(now.month());
    dataFile.print('/');
    dataFile.print(now.day());
    dataFile.print(" ");
    dataFile.print(now.hour());
    dataFile.print(':');
    dataFile.print(now.minute());
    dataFile.print(':');
    dataFile.println(now.second());
    dataFile.close();
  }

  // Sampling rate control (Logs every 1 second)
  delay(1000); 
}
