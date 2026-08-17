# 🤖 Obstacle-Aware Servo Control via Wokwi Simulator

An Arduino-based embedded system project simulated entirely on **Wokwi**. The system dynamically controls a **Servo Motor** using real-time distance measurements from an **HC-SR04 Ultrasonic Sensor**. If an obstacle is detected within a threshold ($\le 10\text{ cm}$), the system stops the motor and alters its directional heading.

---

## 📌 Project Overview

This project implements automated obstacle detection and collision avoidance logic. It utilizes **Wokwi** for rapid prototyping, circuit layout testing, and real-time C++ execution without physical hardware dependencies.

### Key Features
* Simulated entirely on **Wokwi Simulator**.
* Real-time distance measurement using ultrasonic pulse timing.
* Automatic direction alteration when an obstacle is within **10 cm**.
* Serial Monitor logging for instant distance debugging.
* Built-in `Servo.h` library support (no external installation required in Wokwi).

---

## 🛠️ Hardware Components (Wokwi Virtual Setup)

| Component | Quantity | Description |
| :--- | :---: | :--- |
| **Arduino Uno** | 1 | Microcontroller unit |
| **HC-SR04 Ultrasonic Sensor** | 1 | Ultrasonic distance sensor |
| **Servo Motor** | 1 | Position-controlled actuator |
| **Jumper Wires** | — | Virtual pin interconnections |

---

## 🔌 Circuit Pinout Connections

| Component Pin | Arduino Pin | Wokwi Wire Color (Suggested) |
| :--- | :--- | :--- |
| **Ultrasonic `VCC`** | `5V` | Red |
| **Ultrasonic `GND`** | `GND` | Black |
| **Ultrasonic `Trig`** | `Pin 9` | Green |
| **Ultrasonic `Echo`** | `Pin 8` | Yellow |
| **Servo `VCC` (Power)** | `5V` | Red |
| **Servo `GND` (Ground)** | `GND` | Black |
| **Servo `PWM` (Signal)** | `Pin 10` | Orange |

---

## 💻 Source Code (`sketch.ino`)

```cpp
#include <Servo.h>

// Pin Definitions
const int trigPin = 9;
const int echoPin = 8;
const int servoPin = 10;

// Global Objects & Variables
Servo myServo;
long duration;
int distance;

void setup() {
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
  
  myServo.attach(servoPin);
  myServo.write(0); // Initial heading angle (0 degrees)
  
  Serial.begin(9600);
}

void loop() {
  // Trigger 10us pulse
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  // Measure echo return duration
  duration = pulseIn(echoPin, HIGH);
  distance = duration * 0.034 / 2;

  // Print data to Wokwi Serial Monitor
  Serial.print("Distance: ");
  Serial.print(distance);
  Serial.println(" cm");

  // Obstacle avoidance decision logic
  if (distance <= 10 && distance > 0) {
    myServo.write(180); // Reverse direction / Stop motion
    delay(1000);        // Hold angle
  } else {
    myServo.write(0);   // Default forward angle
  }

  delay(100);
}
