# Day 4 – DC Motors, Motor Control, and Navigation Basics

Welcome to **Day Four** of the Arduino Coding Bootcamp!  
Today we shift from controlling small devices like servos to powering **DC motors**—the kind used to drive wheels on robots, conveyor belts, fans, and countless other moving systems.  
If servos are great for precise angle control, **DC motors** are all about continuous, powerful rotation — perfect for **robotic platforms**.

---

## Review of Previous Content

### Ultrasonic Sensor (HC-SR04) — Physics → Wiring → Code

**How it works (quick physics):**
- Sends an ultrasonic **pulse** at **40 kHz** (transmitter).
- Waits for the **echo** reflected from an object (receiver).
- Measures **time of flight** in **microseconds (µs)**.
- Converts time to distance using the speed of sound (~**0.0343 cm/µs**).
- Divide by **2** because the pulse travels **to** the object and **back**.

**Distance formula:**

**Typical wiring:**
- `VCC → 5V`
- `GND → GND`
- `TRIG → D9` (OUTPUT)
- `ECHO → D10` (INPUT)

**Minimal code (prints distance in cm):**

```cpp
// --- HC-SR04: distance in centimeters ---
const int trigPin = 9;
const int echoPin = 10;

void setup() {
  Serial.begin(9600);
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
}

void loop() {
  // Trigger a 10 µs pulse
  digitalWrite(trigPin, LOW);  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH); delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  // Measure echo pulse width (µs). Optional timeout prevents "hang".
  unsigned long duration = pulseIn(echoPin, HIGH, 25000UL); // ~4+ m
  if (duration == 0) {
    Serial.println("No echo (timeout).");
  } else {
    float distance = (duration * 0.0343f) / 2.0f;
    Serial.print("Distance: ");
    Serial.print(distance, 1);
    Serial.println(" cm");
  }

  delay(200);
}
```

**Why the `f` at the end of numbers like `0.0343f`?**

In C++, numbers with a decimal (like `0.0343`) are treated as **double** by default (a type of number that uses more memory and precision).  
If you put an **`f`** at the end, it tells the compiler:  
> "Make this a **float** instead of a double."

Why use `float` here?
- `float` uses less memory (4 bytes instead of 8).
- Many Arduino math functions are optimized for `float`.
- It avoids mixing `float` and `double` types, which can cause the Arduino to do extra work.

**Example:**

```cpp
float speedOfSound = 0.0343f;  // centimeters per microsecond
```


## Servo Motors — Basics → Wiring → Code

### What a servo is (quick basics)
- A **servo motor** rotates to a **specific angle** you command (commonly 0°–180°).
- Inside the plastic case are:
  - A small **DC motor** (provides the spin),
  - A set of **gears** (adds torque, slows the speed),
  - A tiny **control board** (reads your signal and decides what to do),
  - A **feedback potentiometer** attached to the output shaft (tells the control board the current angle).
- You tell the servo “what angle to go to” by sending it a **repeating control pulse** on a single **signal** wire:
  - ~**1.0 ms** pulse ≈ **0°** (one extreme),
  - ~**1.5 ms** pulse ≈ **90°** (center),
  - ~**2.0 ms** pulse ≈ **180°** (other extreme).
- These pulses repeat roughly every **20 ms** (≈ **50 Hz**).  
  The **Arduino Servo library** does all this precise timing for you so you can just say “go to 90°”.

---



### Typical wiring (3-wire servo)
- **Brown/Black** → **GND**
- **Red** → **+5 V** (or **external 5–6 V servo supply**)
- **Orange/Yellow/White** → **Signal** (Arduino digital pin, e.g., **D9**)

> **Tip:** If using an external supply, connect **servo +5 V** to the supply’s +5 V (not to Arduino 5 V).  
> But **always** connect the grounds together (Arduino GND ↔ supply GND ↔ servo GND).


### Arduino Libraries — What they are & how the Servo library helps (beginner-friendly)
- **What is a library?**  
  Think of a library as a **toolbox** of pre-built functions someone else wrote for you.  
  Instead of building a hammer from scratch, you just **grab the hammer** and use it.
- **Why use them?**  
  Libraries save time and reduce bugs. They hide tricky details (like precise pulse timings) behind **simple functions**.
- **How to use a library (analogy):**  
  If your sketch is a **kitchen**, a library is a **ready-made appliance**.  
  You **plug it in** (`#include <Servo.h>`), then **press a button** (`servo.write(90)`) instead of cooking every step by hand.
- **How the Servo library works (in simple terms):**
  - It sets up a **50 Hz** repeating cycle.
  - It generates the **1.0–2.0 ms** control pulses **at the right time**, every cycle.
  - You just call **`servo.write(angle);`** and it handles the timing safely in the background.

**Including and installing libraries**
- Many core libraries (like **Servo**) already come with the Arduino IDE.  
- For others, use **Sketch → Include Library → Manage Libraries…** to search and install.
- To use a library in code, add at the **top** of your sketch:
  ```cpp
  #include <Servo.h>

## 4. Potentiometer — Quick Review (Beginner-Friendly)

**What it is:** A potentiometer (a “pot”) is a **voltage faucet**. When you turn the knob, you choose **how much of the 5V** gets sent to the middle pin called the **wiper**.  
It has **3 pins**:
- One **outer** pin → **5V**
- The other **outer** pin → **GND (0V)**
- The **middle** pin → **Wiper** (outputs a voltage **between 0V and 5V** depending on knob position)

**How Arduino reads it:**  
`analogRead(A0)` measures the wiper voltage and returns a number **0..1023**:
- **0V → ~0**
- **~2.5V → ~512**
- **5V → ~1023**  
(That’s a **10-bit ADC**: 0..1023)

**Wiring (quick):**
- Left outer → **5V**
- Right outer → **GND**
- Middle (wiper) → **A0**  
If turning clockwise makes numbers go **down**, swap the **two outer** wires.



### Code example — Read the wiper and print the voltage

```cpp
// POTENTIOMETER DEMO 1: Read the knob and print raw value + voltage
// Wiring:
//   - Pot outer pins -> 5V and GND
//   - Pot middle pin (wiper) -> A0
//
// Open Serial Monitor at 9600 baud to see lines like:
//   Wiper: 0..1023  |  Voltage: 0.00..5.00 V
//
// NOTE about the 'f' at the end of numbers like 1023.0f or 5.0f:
//   In C++, decimals are 'double' by default (8 bytes).
//   Adding 'f' makes them 'float' (4 bytes). On Arduino, 'float' is lighter
//   and avoids hidden double→float conversions (saves memory and time).

int wiperPin = A0;

void setup() {
  Serial.begin(9600);
}

void loop() {
  int   wiperReading = analogRead(wiperPin);            // 0..1023
  float wiperVoltage = (wiperReading / 1023.0f) * 5.0f; // 0.00..5.00 V

  Serial.print("Wiper: ");
  Serial.print(wiperReading);
  Serial.print("  |  Voltage: ");
  Serial.print(wiperVoltage, 2); // two decimals, e.g., 2.47
  Serial.println(" V");

  delay(150); // small pause so text is readable
}
```


---

## What You’ll Learn Today
- **How DC Motors Work** – the basic physics of converting electrical energy into rotational motion.
- **Motor Control** – how to regulate motor speed and direction using Arduino.
- **Power Management** – handling motors that draw more current than Arduino can supply.
- **Protecting Your Circuit** – avoiding damage to sensors and microcontrollers when using higher voltage/current sources.
- **Navigation Concepts** – using motors in coordinated ways for movement and steering.
- **Case Testing** – testing motor setups before full integration into projects.

---

## Why This Matters
- **Robotics**: Wheels, treads, and drive mechanisms almost always rely on DC motors.
- **Automation**: Motors power conveyor belts, fans, pumps, and lifting arms.
- **Engineering Safety**: Motors can easily overload circuits—learning proper power handling is critical.
- **System Integration**: You’ll need to understand how motors interact with other components like sensors, controllers, and batteries.

---

## Key Challenges with DC Motors
1. **High Current Draw** – DC motors can pull hundreds of milliamps or even amps of current, far more than Arduino pins can supply directly.
2. **Back EMF (Electromotive Force)** – When motors stop or change direction suddenly, they generate voltage spikes that can damage electronics.
3. **Voltage Requirements** – Motors may require more voltage than your Arduino operates at.
4. **Interference** – Motors can introduce electrical noise that affects nearby sensors.

---

## Hardware for Safe Motor Use
To control and protect your circuits, we’ll work with:
- **Motor Drivers / H-Bridges** – e.g., L293D or L298N chips that act as intermediaries between Arduino and motor.
- **External Power Supplies** – powering motors from dedicated sources instead of Arduino’s 5V pin.
- **Flyback Diodes** – protect against voltage spikes from motor coils.
- **Capacitors** – reduce noise and improve stability in the power supply.

---

By the end of Day 4, you’ll be able to:
- Wire and control a DC motor safely.
- Adjust speed using PWM (Pulse Width Modulation).
- Change motor direction with an H-Bridge.
- Protect your Arduino and sensors from high-current devices.
- Begin integrating motor control into navigation systems for robotic platforms.

> **Next Step:** We’ll start by understanding the physics of a DC motor, then move into motor driver wiring, speed control with PWM, and finally, using two or more motors to drive a robot in a simulated navigation test.

# 1. DC Motors — How They Work, Powering Them Safely, and Controlling Speed/Direction

DC motors are the **workhorses** of mobile robots. Unlike servos (which move to an angle), a DC motor **spins continuously**, perfect for wheels and drive systems.

---

## 1.1 What’s Inside a DC Motor (Beginner Physics)

- **Stator (fixed magnets)** creates a magnetic field.
- **Rotor/Armature (coil)** sits inside and spins when current flows.
- **Commutator + Brushes** switch current direction through the coil as it spins, keeping torque in the same rotational direction.

<img width="403" height="304" alt="image" src="https://github.com/user-attachments/assets/ccabbd34-809c-4c35-be4e-a45aa3e31c95" />

**Key idea:** A current-carrying wire in a magnetic field experiences a **force** (Lorentz force). Repeating this around the rotor makes continuous rotation.

---

## 1.2 Voltage vs. Current (and why Arduino pins are NOT a motor power source)

- **Voltage (V):** “electrical pressure” (e.g., 3V, 5V, 9V, 12V).  
- **Current (A):** “flow rate” the motor **draws**. DC motors often need **hundreds of mA to a few A**, especially at startup (“stall current”).
- **Arduino I/O pin limit:** ~20–40 mA per pin (tiny). **Never** power a motor directly from an Arduino pin.
- **Arduino 5V pin:** can power sensors and small modules, **not** motors (you’ll brown-out/reset the board).

**Conclusion:** Use a **separate motor power supply** (e.g., 6–9 V battery pack for motors, or as specified by your motor) and a **motor driver** between Arduino and motor.

<img width="399" height="307" alt="image" src="https://github.com/user-attachments/assets/5fa16e47-3906-4b00-b24d-a415d394dbbc" />

---

## 1.3 Why You Need a Motor Driver (H-Bridge)

A **motor driver** is an electronic “middleman” that:
- Lets a low-power signal from Arduino **control** a higher-power motor.
- Handles **direction** (forward/reverse) and **speed** (PWM).
- Protects the microcontroller from **back EMF** (voltage spikes when motors stop/change speed).

Common options:
- **L293D / SN754410** (small robots)
- **L298N** driver board (very common with screw terminals, built-in diodes)
- Newer **MOSFET-based** drivers (more efficient, cooler)

**Back EMF:** Motors are inductors. When current changes fast, they “kick back” voltage.  
Drivers include **flyback diodes** or internal protection to absorb these spikes.

<img width="919" height="510" alt="image" src="https://github.com/user-attachments/assets/c88e2fcd-6400-48c8-b509-8a2d5515b0f2" />


<img width="754" height="687" alt="image" src="https://github.com/user-attachments/assets/3a23f0ab-2a92-456a-8cb5-6193b7f4cbe1" />

---


## 1.4 Power Safety and Protecting Other Hardware

- **Never feed 9V directly** to 5V sensors (e.g., ultrasonic HC-SR04). Sensors expect **5V** (or sometimes **3.3V**).  
- Use **separate power rails**: one for logic (Arduino **5V**) and one for motors (**battery pack**).  
- **Common ground**: connect motor GND and Arduino GND together so signals have a reference.  
- **EMI noise**: motors produce electrical noise; keep sensor wires short and tidy; route motor power away from analog sensors.  
- **Thermal limits**: drivers get warm; ensure adequate current rating


**Notes:**
- Some L298N boards label **ENA/ENB** and provide a jumper.  
  - **Remove the jumper** if you want to use **PWM** from Arduino.  
- **+12V / +Vm** on the driver is **motor power**, **NOT** Arduino 5V.  
- Provide **+5V logic** to the driver (some L298N boards have a 5V regulator you can enable with a jumper; read your board’s silkscreen/manual).

- **ENA** = **Enable A** (controls the power path for **Motor A** outputs).
- **ENB** = **Enable B** (controls the power path for **Motor B** outputs).
- On many L298N boards, **ENA/ENB have little jumpers**:
  - **Jumper ON** → EN tied to 5V → channel is **always enabled** at **full speed** (no PWM speed control).
  - **Jumper OFF** → you must connect ENA/ENB to an **Arduino PWM pin** and use `analogWrite()` to control **speed**.
- When **EN is LOW** → the corresponding motor outputs are **disabled** (motor **coasts**, not powered).
- When **EN is HIGH** (or a PWM waveform) → the driver powers the motor according to **IN pins** and **PWM duty**.

--- 
## 1.5  Example

Note first the pin diagram for the H bridge on tinkercad (different from what you would typically use in a real scenario but for the sake of simulations)

<img width="376" height="212" alt="image" src="https://github.com/user-attachments/assets/4e9a8279-8e61-4057-94ac-892a65486158" />

<img width="811" height="575" alt="image" src="https://github.com/user-attachments/assets/e3cb883d-15f4-42d9-a238-03ce0e783a59" />
Wiring example on tinkerCAD

### What the L293D Pins Mean (tiny mental model)

- The chip has **two motor channels**:
  - **Motor A** uses: `ENA` (enable A), `IN1`, `IN2`, and outputs `OUT1/OUT2`.
  - **Motor B** uses: `ENB` (enable B), `IN3`, `IN4`, and outputs `OUT3/OUT4`.
- **IN pins** choose **direction** (which way the motor spins).
- **EN pins** turn the channel **on/off** and can accept **PWM** for **speed**.
  - `HIGH` on EN → channel enabled (full speed if not using PWM).
  - `LOW` on EN → channel off (motor **coasts**).
  - `analogWrite(EN, 0..255)` → **speed control** (later, if needed).

---

### Wiring 

<img width="387" height="145" alt="image" src="https://github.com/user-attachments/assets/2915cec2-195b-4fd5-a4b2-2811811086b6" />

- **Power**
  - **Vs (pin 8)** → Motor battery **+** (e.g., 6–9 V).  
    *This powers the motors.*  
  - **Vss (pin 16)** → **Arduino 5V** (logic power for the chip).  
  - **GND (pins 4,5,12,13)** → **Common ground** (tie to **Arduino GND** **and** motor battery −).
- **Motor A (top motor in your image)**
  - **OUT1 (pin 3)** and **OUT2 (pin 6)** → the two motor wires.
  - **IN1 (pin 2)** → Arduino **D8**
  - **IN2 (pin 7)** → Arduino **D7**
  - **ENA (pin 1)** → Arduino **D9** (enable / PWM)
- **Motor B (bottom motor in your image)**
  - **OUT3 (pin 11)** and **OUT4 (pin 14)** → the two motor wires.
  - **IN3 (pin 10)** → Arduino **D5**
  - **IN4 (pin 15)** → Arduino **D4**
  - **ENB (pin 9)** → Arduino **D3** (enable / PWM)

> **Important:** The **grounds must all be connected together** (Arduino GND ↔ L293D GND ↔ battery −).  
> **Tip:** 9V rectangular batteries are weak for motors; a 4xAA or 6xAA pack works much better.


### Direction Cheat Sheet (for each motor)

- **Forward:** `INx1 = HIGH`, `INx2 = LOW`
- **Reverse:** `INx1 = LOW`,  `INx2 = HIGH`
- **Brake:** set **both IN pins the same** (both HIGH or both LOW)
- **Coast:** set the **EN pin LOW** (channel off)

---

## Code

```cpp
// ---------------------------------------------------------
// L293D + TWO DC MOTORS (Forward → Reverse → Stop)
// Matches the wiring shown in your image.
//
// Motor A pins (top motor):
//   ENA -> D9   | IN1 -> D8 | IN2 -> D7 | OUT1/OUT2 -> motor wires
// Motor B pins (bottom motor):
//   ENB -> D3   | IN3 -> D5 | IN4 -> D4 | OUT3/OUT4 -> motor wires
//
// Power:
//   Vs (pin 8)  -> Motor battery + (e.g., 6-9V)
//   Vss (pin16) -> Arduino 5V (logic)
//   GND (4,5,12,13) -> Common ground with Arduino GND and battery -
//
// EN pins:
//   HIGH  = channel enabled (full speed if not using PWM)
//   LOW   = channel off (coast)
//   PWM   = speed control: analogWrite(EN, 0..255)
// ---------------------------------------------------------
int enableA = 9;   // ENA (enable/speed) for Motor A
int in1Pin  = 8;   // IN1 (direction) for Motor A
int in2Pin  = 7;   // IN2 (direction) for Motor A

int enableB = 3;   // ENB (enable/speed) for Motor B
int in3Pin  = 5;   // IN3 (direction) for Motor B
int in4Pin  = 4;   // IN4 (direction) for Motor B

void setup() {
  // Tell Arduino these are outputs (we send signals out to the driver)
  pinMode(enableA, OUTPUT);
  pinMode(in1Pin,  OUTPUT);
  pinMode(in2Pin,  OUTPUT);

  pinMode(enableB, OUTPUT);
  pinMode(in3Pin,  OUTPUT);
  pinMode(in4Pin,  OUTPUT);

  // Safe start: everything off
  analogWrite(enableA, 0);   // channel A off (coast)
  analogWrite(enableB, 0);   // channel B off (coast)
  digitalWrite(in1Pin, LOW);
  digitalWrite(in2Pin, LOW);
  digitalWrite(in3Pin, LOW);
  digitalWrite(in4Pin, LOW);
}

void loop() {
  // DEMO: drive forward for 3 seconds, then go backwards for 3 seconds
  // (You will add reverse/turn/brake in your breakout activity.)
  forwardMotors(200);  // try values 100..220 to see what happens
  delay(3000);
  reverseMotors(200);
  delay(3000);

}

void forwardMotors(int speedPWM) {
  // keep speed in range 0..255
  if (speedPWM < 0)   speedPWM = 0;
  if (speedPWM > 255) speedPWM = 255;

  // Set both motors to "forward" direction
  digitalWrite(in1Pin, HIGH);  // Motor A: IN1=HIGH
  digitalWrite(in2Pin, LOW);   //          IN2=LOW

  digitalWrite(in3Pin, HIGH);  // Motor B: IN3=HIGH
  digitalWrite(in4Pin, LOW);   //          IN4=LOW

  // Enable channels with PWM for speed
  analogWrite(enableA, speedPWM);  // ENA controls Motor A speed
  analogWrite(enableB, speedPWM);  // ENB controls Motor B speed
}

// reverseMotors(speedPWM)
// Purpose: spin BOTH motors in the reverse direction using an L293D/L298N-style H-bridge.
// Inputs:
//   speedPWM = 0..255  (0 = stop/coast, 255 = full speed)
// How it works:
//   - An H-bridge changes motor direction by flipping which side of the motor is tied HIGH vs LOW.
//   - FORWARD used INx1=HIGH, INx2=LOW (for each motor).
//   - REVERSE simply swaps them: INx1=LOW, INx2=HIGH.
//   - ENA/ENB stay enabled with PWM to set the speed.
//
// Notes:
//   • If "reverse" spins the wheels the wrong way for your robot, just swap the two wires on that motor,
//     or redefine which pair of IN pins is considered "forward" vs "reverse".
//   • Setting ENA/ENB to 0 makes the motor COAST (no active braking).
//   • For an ACTIVE BRAKE you would set both IN pins for a motor to the SAME level (both HIGH or both LOW),
//     while ENA/ENB are HIGH — that's a different function (brakeMotors) you can write later.
// ---------------------------------------------------------
void reverseMotors(int speedPWM) {
  // Clamp to valid PWM range
  if (speedPWM < 0)   speedPWM = 0;
  if (speedPWM > 255) speedPWM = 255;

  // ----- Motor A: reverse (flip polarity through the H-bridge) -----
  // Forward was IN1=HIGH, IN2=LOW
  // Reverse is  IN1=LOW,  IN2=HIGH
  digitalWrite(in1Pin, LOW);
  digitalWrite(in2Pin, HIGH);

  // ----- Motor B: reverse (same idea) -----
  digitalWrite(in3Pin, LOW);
  digitalWrite(in4Pin, HIGH);

  // Enable both channels with PWM to set speed
  analogWrite(enableA, speedPWM);
  analogWrite(enableB, speedPWM);
}

```

---

## 1.6 Common Pitfalls (and fixes)

- **Motor won’t spin / resets Arduino:**  
  - Cause: drawing motor power from Arduino 5V.  
  - Fix: use **separate motor supply** and **common ground**.

- **Everything glitches when motor starts:**  
  - Cause: voltage sag or noise.  
  - Fix: bigger battery pack, decoupling caps near driver, separate power rails, tidy wiring.

- **Motor gets hot, driver shuts down:**  
  - Cause: too much current for driver.  
  - Fix: pick a driver with higher current rating or use a gearmotor with lower stall current.

- **No reverse:**  
  - Cause: both direction pins set the same or wired wrong.  
  - Fix: check **IN1/IN2** logic and truth table; verify wiring.

---

## Breakout Activity 1

We have learned how to move forward (both motors going one direction) and move backwards (both motors spinning in the opposite direction). But how would we code our "robot cars" for turning?

Create the following functions:

void turnRight()
void turnLeft()
void reverseRight()
void reverseLeft()


---

## 1.9 Quick Checklist Before Power-On

- ✅ Motor power (battery pack) connected to **driver’s motor VIN**, **NOT** Arduino 5V.  
- ✅ Arduino **5V** to driver **5V logic** (if required by your board).  
- ✅ **Common GND** between motor supply and Arduino.  
- ✅ Flyback diodes (if using discrete transistors) or integrated protection (driver).  
- ✅ ENA/ENB PWM jumpers configured correctly.  
- ✅ Wires short and tidy; sensors on separate, clean 5V if possible.

---

# 2. Learning to Drive (Mission-Focused Control)

Driving is **not** just “spin motors.” It’s: *go to a goal, avoid trouble, and keep the robot alive*.  
This section shows how to turn sensors into **control decisions** for a simple two-wheel (differential) robot using DC motors.

---

## 2.1 Control Loop Basics (Sense → Decide → Act → Repeat)

Your robot runs a fast loop:

1. **Sense** — read inputs (ultrasonic distance, potentiometer “speed knob”, buttons).
2. **Decide** — apply logic (if too close → slow/stop; else → drive forward).
3. **Act** — set motor speed/direction (PWM & H-bridge pins).
4. **Repeat** — a few dozen times per second.

**Example:** A human driver constantly glances at the road (sense), decides “speed up / slow down / steer,” and moves pedals/wheel (act) — over and over.

---

## 2.2 Mission Goals Drive the Logic

Pick a **simple mission** first; keep the code short and clear.

- **Patrol forward** until something is **closer than 20 cm**, then **stop**.
- Add a **turn/avoid** behavior if blocked (e.g., pivot left for 500 ms, then try forward again).
- Scale **speed with a knob** (potentiometer) so you can demo slow/fast runs safely.

> Start with one mission. When that works, add another behavior (e.g., “if blocked twice, reverse and turn right”).

---

## 2.3 Ways to use the hardware we've learned

You don’t need full robotics math to combine a couple signals:

- **Ultrasonic** → sets a **safety distance**.
- **Potentiometer** → sets a **target speed** (0–255 PWM).
- **Button** → acts as **Emergency Stop (E-Stop)** (held/pushed = stop everything).

Rule example:
- If **E-Stop pressed** → motors off.
- Else if **distance < stopThreshold** → stop.
- Else if **distance < slowThreshold** → cap speed at a smaller PWM.
- Else → allow knob-selected PWM.


---

## Breakout Activities Motors + Sensors \

> Use your existing motor + H-bridge setup. Keep wiring the same across activities.
> The bullets below describe behavior; you will choose pins and write the code.



### Activity 1 — Distance-Based Speed Control

**Goal:** Drive forward by default. Slow down near obstacles. Stop when too close.

**Instructions:**
- Read distance from the ultrasonic sensor continuously.
- If distance **> 200 cm** → drive **fast**.
- **Bonus challenge** If distance **100–200 cm** → drive **slow**. *(choose lower PWM value)*
- If distance **≤ 100 cm** → **stop** both motors.

**Good Practice:**
- Print the live distance and chosen speed (FAST/SLOW/STOP) to the Serial Monitor.

---

### Activity 2 — Obstacle Avoidance (Stop → Turn → Go)

**Goal:** Move forward until an obstacle is detected at a close distance, then avoid it.

**Instructions:**
- Drive forward by default.
- When the ultrasonic sensor reports **≤ 75 cm**:
  - **Stop** both motors.
  - **Turn in place** for about **3 seconds** (pick left or right).
  - After turning, **resume forward** motion.
- Repeat this behavior continuously.

Wiring LEDs into your circuits (good practice for live troubleshooting)
**Bonus Challenge (Status LEDs):**
- While **distance > 100 cm** → **GREEN LED ON**, **RED LED OFF**.
- While **distance ≤ 100 cm** (stopped/turning) → **RED LED ON**, **GREEN LED OFF**.

---

### Activity 3 — Emergency Brake Button

**Goal:** Add a manual “brake” to immediately stop the robot.

**Instructions:**
- Drive forward at a steady speed by default.
- When the **pushbutton is pressed** → **stop** the motors immediately.
- When the **pushbutton is released** → **resume forward** motion. (remember the while loop we used)

**Bonus Challenge (Status LEDs):**
- **GREEN LED ON** while moving forward, **RED LED OFF**.
- **RED LED ON** while stopped by the button, **GREEN LED OFF**.

---

### Activity 4 — Speed Knob (Potentiometer → Motor PWM)

**Goal:** Control motor speed with a knob.

**Instructions:**
- Read the potentiometer (wiper) value continuously.
- Convert the knob reading to a **PWM speed** for the motors.
- As you turn the knob:
  - Far left → **stop** (PWM ≈ 0).
  - Far right → **fast** (PWM near maximum).
  - Anywhere in between → **proportional speed**.

**Bonus Challenge:**
- Status LEDs: **GREEN ON** when speed > 0, **RED ON** when stopped.


---


# 3. Case Testing — Planning Your Driving Tests

Before running your robot, it’s important to plan out **what you’re going to test** and **what you expect to happen**.  
This helps you catch mistakes early and makes sure your robot works the way you think it will.

---

## 3.1 Why Plan Tests?

- You’ll find problems **before** they cause bigger issues.  
- You’ll know exactly what’s working and what’s not.  
- It’s easier to make small fixes instead of guessing what’s wrong.  
- Your robot will behave more predictably during demos.

---

## 3.2 How to Plan a Test

Each test should have:
1. **Name** — something simple that tells you the purpose (e.g., “Stop before wall”).
2. **Setup** — what you need in place before starting (e.g., obstacle at 20 cm).
3. **What You’ll Do** — the steps you’ll take during the test.
4. **What You Expect** — the exact behavior you hope to see.
5. **What Happened** — write down if it worked or not.

---

## 3.3 Example Test Table

| Test Name           | Setup                                        | What You’ll Do                               | What You Expect                                       | What Happened |
|---------------------|----------------------------------------------|-----------------------------------------------|-------------------------------------------------------|---------------|
| Stop before wall    | Place a box 20 cm in front of the robot      | Turn on robot and let it drive forward        | Robot stops before hitting box                        | Pass / Fail   |
| Slow near wall      | Place box ~30 cm in front                    | Turn on robot at max speed                    | Robot slows down as it gets close to the box          | Pass / Fail   |
| Avoid obstacle      | Place box directly in front at 10 cm         | Start robot                                   | Robot stops, turns left for a moment, then continues  | Pass / Fail   |
| Emergency stop test | Button connected as E-Stop                   | Drive forward, press button                   | Motors stop instantly                                 | Pass / Fail   |
| No reading safety   | Cover ultrasonic sensor with your hand       | Start robot                                   | Robot does not move and stays stopped                 | Pass / Fail   |
| Turning check       | Lift robot wheels off table                  | Run pivot-turn code                           | Wheels spin in opposite directions for a smooth turn  | Pass / Fail   |

---

## 3.4 Tips for Running Tests

- Start with **wheels lifted off the ground** so you can watch motor behavior safely.
- Test **one thing at a time** — don’t test “stop” and “avoid” at the same time in your first run.
- Use the **Serial Monitor** to print important values:
  - Distance from ultrasonic sensor
  - Speed (PWM value)
  - Whether the robot is moving or stopped

Example debug print:
```cpp
Serial.print("Distance: ");
Serial.print(distance);
Serial.print(" cm   PWM: ");
Serial.println(speed);
```

---

## 3.5 Common Issues and Fixes

- **Robot resets when driving:** Motors need more current than the Arduino can supply → use separate motor battery and make sure grounds are connected.
- **Weird distance spikes:** Sensor might be aimed wrong or reading soft objects → aim at flat surfaces and try averaging several readings.
- **One wheel spins faster:** Small mechanical differences → adjust PWM slightly for balance.
- **E-Stop not working:** Check wiring and make sure you set the button pin as `INPUT_PULLUP` in code.

---

**Bottom line:** Good driving behavior comes from good testing.  
Plan your tests, write down what happens, and make changes one step at a time.


## Friday Activities Preview!

<img width="515" height="246" alt="image" src="https://github.com/user-attachments/assets/8c4a8f7a-5514-4be4-bc06-c1727a3f9ae5" />


<img width="484" height="383" alt="image" src="https://github.com/user-attachments/assets/e2f34c99-c7fd-4d99-9452-2ad7dfbb1327" />


