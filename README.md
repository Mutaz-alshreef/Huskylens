# 🤖 HuskyLens with Arduino Uno

This project demonstrates how to connect and use the **HuskyLens AI camera** with an **Arduino Uno** using `SoftwareSerial`.

---

## 🎥 Demo Video

Watch the video demonstration here:  
👉 [Color Tracking Demo](https://drive.google.com/file/d/1_eGAuPuzJJYSInDWhhMCD6KeKdkXXa_T/view?usp=sharing) 

---

- TX (HuskyLens) → Pin **10** (Arduino RX)
- RX (HuskyLens) → Pin **11** (Arduino TX)
- GND → GND
- VCC → 5V
- It also shows HuskyLens recognizing trained objects or faces.

> ⚠️ Make sure the **protocol type** in HuskyLens settings is set to **Serial 9600**.

---

## 🧩 Wiring

| HuskyLens Pin | Arduino Uno Pin |
|---------------|------------------|
| TX (green)    | D10 (RX)         |
| RX (blue)     | D11 (TX)         |
| GND           | GND              |
| VCC           | 5V               |

> ✅ Uses **SoftwareSerial** because Arduino Uno's hardware serial is occupied by USB.

---

## 🧠 Supported Modes

Ensure HuskyLens is in the correct mode (Face Recognition, Object Tracking, etc.) depending on your application.

---

## 📦 Requirements

- Arduino IDE
- [HuskyLens Library](https://github.com/HuskyLens/HUSKYLENSArduino)
- `SoftwareSerial` (built-in)

---

## 💻 Arduino Code

```cpp
/***************************************************
 HUSKYLENS An Easy-to-use AI Machine Vision Sensor
 <https://www.dfrobot.com/product-1922.html>
 ***************************************************/
#include "HUSKYLENS.h"
#include "SoftwareSerial.h"

HUSKYLENS huskylens;
SoftwareSerial mySerial(10, 11); // RX, TX
// HUSKYLENS green line >> Pin 10; blue line >> Pin 11

void printResult(HUSKYLENSResult result);

void setup() {
    Serial.begin(115200);
    mySerial.begin(9600);
    while (!huskylens.begin(mySerial))
    {
        Serial.println(F("Begin failed!"));
        Serial.println(F("1. Recheck Protocol Type (Serial 9600) in HuskyLens"));
        Serial.println(F("2. Recheck wiring."));
        delay(100);
    }
}

void loop() {
    if (!huskylens.request())
        Serial.println(F("Fail to request data from HUSKYLENS, recheck the connection!"));
    else if (!huskylens.isLearned())
        Serial.println(F("Nothing learned. Press learn button on HUSKYLENS to learn one!"));
    else if (!huskylens.available())
        Serial.println(F("No block or arrow appears on the screen!"));
    else {
        Serial.println(F("###########"));
        while (huskylens.available()) {
            HUSKYLENSResult result = huskylens.read();
            printResult(result);
        }
    }
}

void printResult(HUSKYLENSResult result) {
    if (result.command == COMMAND_RETURN_BLOCK) {
        Serial.println(String() + F("Block:xCenter=") + result.xCenter + F(", yCenter=") + result.yCenter + 
                       F(", width=") + result.width + F(", height=") + result.height + F(", ID=") + result.ID);
    } else if (result.command == COMMAND_RETURN_ARROW) {
        Serial.println(String() + F("Arrow:xOrigin=") + result.xOrigin + F(", yOrigin=") + result.yOrigin + 
                       F(", xTarget=") + result.xTarget + F(", yTarget=") + result.yTarget + F(", ID=") + result.ID);
    } else {
        Serial.println("Object unknown!");
    }
}
