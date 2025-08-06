# Day 1 – Introduction to Coding and Hardware Control with Arduino

Welcome to Day 1 of the WIE Coding Bootcamp! Today we will cover the fundamentals of how coding interacts with hardware using the Arduino Microcontroller. This day is foundational — we’ll focus on programming structure, hardware concepts, and controlling simple outputs like LEDs and motors.

---

## 1. Basics of Coding

###  Description:
Learn what code is, how microcontrollers understand it, and what makes a program work.

###  Image Placeholder:
_Add a visual comparing natural language vs machine code vs Arduino code._

###  Code Block Placeholder:
_Add sample code snippet for a simple `setup()` and `loop()` structure._

###  Resources:
- [ ] Link to slides or handout (optional)

---

## 2. Arduino Micro

###  Description:
Overview of the Arduino Micro board — layout, pins, power, and how it communicates.

###  Image Placeholder:
_Add labeled diagram of Arduino Micro board._

###  Resources:
- 

---

## 3. Variables

###  Description:
Understand what variables are, data types (`int`, `float`, etc.), and where to define them (global vs local).

###  Code Block Placeholder:
```cpp
int ledPin = 13;
float voltage = 3.3;
```

###  Image Placeholder:
_Add diagram showing variable scope (global vs local)._

###  Activity:
- [ ] Link to TinkerCAD activity for changing LED brightness using a variable

---

## 4. Analog vs Digital

###  Description:
Difference between analog and digital signals. Which pins on the Arduino handle each?

| Type    | Description                     | Arduino Function        |
|---------|---------------------------------|--------------------------|
| Digital | On/Off (1 or 0)                 | `digitalWrite()` / `digitalRead()` |
| Analog  | Range of values (0–255 or 0–1023) | `analogWrite()` / `analogRead()`   |

###  Image Placeholder:
_Add Arduino pinout highlighting analog and digital pins._

---

## 5. Delay

###  Description:
Using the `delay()` function to control timing between actions.

###  Code Block Placeholder:
```cpp
digitalWrite(ledPin, HIGH);
delay(1000); // wait 1 second
digitalWrite(ledPin, LOW);
delay(1000);
```

###  Image Placeholder:
_Add a visual timeline showing delay pauses between actions._

---

## 6. Motor Function

###  Description:
Introduction to controlling DC motors with Arduino using digital signals. Basics of direction and speed control.

###  Code Block Placeholder:
```cpp
#define motorPin1 4
#define motorPin2 5

void setup() {
  pinMode(motorPin1, OUTPUT);
  pinMode(motorPin2, OUTPUT);
}

void loop() {
  digitalWrite(motorPin1, HIGH);
  digitalWrite(motorPin2, LOW);
}
```

###  Image Placeholder:
_Add diagram of motor wired to L298N or similar motor driver._

---

## 7. PWM (Pulse Width Modulation)

###  Description:
Learn how PWM simulates analog output using digital pins. Use PWM to control brightness or motor speed.

###  Code Block Placeholder:
```cpp
analogWrite(motorPin1, 128); // 50% speed
```

###  Image Placeholder:
_Add waveform diagram showing different duty cycles._
<img width="478" height="301" alt="image" src="https://github.com/user-attachments/assets/1848184e-3e5d-4530-9420-344329ec757c" />

---

## 8. Breakout Activity: LED Blinker

###  Objective:
Create a basic LED circuit on your breadboard and control blinking with code.

###  Code Block Placeholder:
```cpp
void loop() {
  digitalWrite(13, HIGH);
  delay(500);
  digitalWrite(13, LOW);
  delay(500);
}
```

###  TinkerCAD Simulation:
- [ ] Link to LED Blinker TinkerCAD circuit

---

## 9. Breakout Activity: Arduino Motor Controller

###  Objective:
Control the direction of a motor using two digital pins and test speed with PWM.

###  Code Block Placeholder:
```cpp
digitalWrite(motorPin1, HIGH);
digitalWrite(motorPin2, LOW);
analogWrite(speedPin, 200);
```

###  TinkerCAD Simulation:
- [ ] Link to Motor Controller TinkerCAD circuit

---

## 10. Wrap-Up

###  What You Should Know Now:
- Difference between digital and analog
- How to use `pinMode`, `digitalWrite`, `analogWrite`, and `delay`
- Basics of motor control and PWM
- How to define and use variables

###  Homework / Practice:
- [ ] Modify LED blinking speed using variables
- [ ] Create your own function to blink 3 times, then stop
- [ ] TinkerCAD simulation of motor with variable speed

---

**Next Up – Day 2:**
Functions, conditionals, servo motors, and motion!
