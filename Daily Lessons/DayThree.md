# Day 3 – Sensors, Servos, Potentiometers, and Libraries

Welcome to **Day Three** of the Arduino Coding Bootcamp!  
Today, we move beyond basic code and wiring into the world of **interacting with the environment**.  
Your Arduino can’t “think” on its own—it responds to **inputs** and controls **outputs**. Sensors provide the Arduino with information about the world (like distance, temperature, or light), and components like servos and LEDs let it act on that information.

We’ll cover:
- **Libraries** – pre-written collections of code that make it much easier to work with complex components.
- **Servos** – motors that allow precise position control.
- **Sensors** – the “eyes and ears” of your project, giving you data to work with.
- **Potentiometers** – simple but powerful input devices that give variable values (like a volume knob).

---
## Mini Review of Day One

### If statements

Block Logic Diagram

<img width="800" height="400" alt="image" src="https://github.com/user-attachments/assets/8d567855-a5cf-418c-b0ad-f31ba1e5a46f" />

Code Example

<img width="899" height="279" alt="{EA514AC0-49CC-4D66-9BDB-29A4A93C2908}" src="https://github.com/user-attachments/assets/8866761f-fefa-4d30-a20d-1907fe4c6682" />

---

### For and While Loops
While Loop

<img width="300" height="300" alt="image" src="https://github.com/user-attachments/assets/0a9f1697-865a-43bc-95c1-99ed72685f85" />

For Loops

<img width="370" height="400" alt="image" src="https://github.com/user-attachments/assets/53ce1e63-df4b-4316-b3d0-123ba6065ef7" />


---
## 1. Libraries

If your Arduino is the **brain** and wiring is the **nervous system**, then **libraries** are the *superpowers* you can instantly add.  
A library is a **pre-written collection of code** (usually in a header file, `.h`) that gives you convenient functions so you don’t have to reinvent the wheel.

### How Libraries Work
- They live in **header files** (e.g., `Servo.h`, `Wire.h`).
- They expose **functions** you can call from your sketch.
- You include them at the top of your file:
```cpp
#include <library_name.h>
```
Analogy: Like borrowing a recipe book—you don’t write the recipe; you just follow it.

Why Libraries Matter Today
We’ll touch hardware that is much easier with libraries:

Servos → Servo.h handles precise timing signals for angles.

Ultrasonic distance sensors → libraries like NewPing.h measure distance without manual pulse timing.

I²C devices → Wire.h handles two-wire comms (many sensors use this).

Potentiometers → typically read directly with analogRead(), but often appear alongside other library-based parts.

Without libraries: you’d write and debug low-level timing/communication code.  
With libraries: you focus on what your project should do (logic/behavior), not how every signal is generated.

### Installing a Library (Arduino IDE)
- Sketch → Include Library → Manage Libraries…
- Search (e.g., “Servo”, “NewPing”).
- Click Install.
- Include at the top of your sketch:
```cpp
#include <Servo.h>
```

### Example: Servo Control (with vs. without a library)

Without a library (conceptual pseudo-code):
```cpp
// Manually generate pulses ~500–2500 µs every 20 ms for angle control
digitalWrite(servoPin, HIGH);
delayMicroseconds(1500); // ~90°
digitalWrite(servoPin, LOW);
delay(20);               // repeat forever at correct frequency
```
You’d need to map angles to microsecond pulses and maintain timing.

With the Servo library:
```cpp
#include <Servo.h>

Servo myServo;

void setup() {
  myServo.attach(9);     // control pin
}

void loop() {
  myServo.write(90);     // center
  delay(1000);
  myServo.write(0);      // 0°
  delay(1000);
}
```
The library hides the timing details; you just say the angle.

### Standard vs. Special Libraries

Standard/Core (common C/C++ & Arduino):
- math.h – extra math functions
- string.h – C-style string utilities
- time.h – time utilities
- Wire.h – Arduino I²C/TWI communication

Special/Hardware-specific:
- Servo.h – servo motors
- NewPing.h – ultrasonic sensors (HC-SR04, etc.)
- Others you’ll find via Library Manager or device docs

### Beginner’s Mental Model
Translator analogy: You decide what to say; the library “speaks” the device’s language for you.  
Microwave vs. campfire: Both cook food. One is fast, clean, reliable (a library); the other takes more time and expertise.

### Pro Tips
- Read the examples: File → Examples → [LibraryName] often has a working sketch you can run immediately.
- Check the docs: Look for required pin connections, voltage notes, and API functions.
- Keep sketches clean: Put #include lines at the top; group related code in functions.

---


## 2. Servo Motors

If DC motors are like **spinning fans**, servo motors are like **robotic arms** — precise, controlled, and able to hold a position.  
They don’t just spin endlessly; instead, they rotate to **specific angles**, usually between **0° and 180°** (some specialized servos can rotate further or even continuously).

A servo motor contains:
- **A small DC motor** – provides the movement.
- **A control circuit** – interprets commands.
- **A feedback potentiometer** – tells the motor when it has reached the desired angle.
- **Gears** – reduce speed and increase torque.

### How Servo Motors Work
Servos respond to **Pulse Width Modulation (PWM)** signals.  
- A pulse of ~1 ms → 0°  
- A pulse of ~1.5 ms → 90°  
- A pulse of ~2 ms → 180°  
The servo repeats this check about every 20 milliseconds.

Manually generating these pulses is tricky. That’s why we use the **Servo library**, which handles the timing for us.

---

### Using the Servo Library
Include the library at the top of your sketch:
```cpp
#include <Servo.h>
```

**Basic Servo Setup:**
```cpp
#include <Servo.h>

Servo myServo; // Create a Servo object

void setup() {
  myServo.attach(9); // Attach the servo signal pin to Arduino pin 9
}

void loop() {
  myServo.write(0);   // Move to 0 degrees
  delay(1000);        // Wait 1 second
  myServo.write(90);  // Move to 90 degrees
  delay(1000);
  myServo.write(180); // Move to 180 degrees
  delay(1000);
}
```
The library sends the correct pulses so you can simply say the angle you want.

---

### Real-World Uses for Servo Motors
Even though most hobby servos rotate only 180°, they are extremely useful for:
- **Robotic arms** – position control for joints.
- **Camera gimbals** – tilt/pan for smooth video.
- **RC vehicles** – steering mechanisms.
- **Animatronics** – moving parts in props or displays.
- **Door locks or levers** – precise opening/closing movement.

---

### Demo:

<img width="50" height="50" alt="image" src="https://github.com/user-attachments/assets/5bc35294-32f0-4ec3-bd18-d9de2f612fb6" />

### Mini Breakout 1 – Servo Warm-Up
**Goal:** Learn to move a servo to specific angles using the `Servo.h` library.

**Instructions:**
1. Connect a servo motor to Arduino pin **9** (signal), **5V** (Vcc), and **GND**.
2. Include the `Servo.h` library.
3. In `loop()`, move the servo to:
   - 0°
   - 90°
   - 180°
   - back to 0°
4. Add a `delay(1000)` between each move.

**Stretch Goal:** Make the servo sweep smoothly (small increments) from 0° to 180° and then back to 0° using a `for` loop.

** Hint: you might have to use two for loops!

---

## 3. Ultrasonic Sensor (HC-SR04) — No Extra Library

The **HC-SR04 ultrasonic sensor** measures distance by using sound waves—similar to how bats and dolphins navigate.  
Instead of light, it sends out an **ultrasonic pulse** (a sound too high-pitched for human ears) and waits for the echo to bounce back from an object.

---

### 3.1 The Physics Behind It

- **Sound propagation:** Sound travels through air at roughly **343 meters per second** at room temperature (~0.0343 cm/µs).  
- **Echo timing:** The sensor emits a burst of sound at 40 kHz (40,000 vibrations per second) through its **transmitter**.  
- When the sound hits an object, part of it bounces back toward the **receiver**.
- The **time between sending and receiving** tells us how far away the object is.

Because the sound has to travel **to the object and back**, the formula for distance is:

<img width="288" height="128" alt="image" src="https://github.com/user-attachments/assets/af89d838-fd54-417c-ae28-a051e1415a12" />


<img width="397" height="308" alt="image" src="https://github.com/user-attachments/assets/be763bac-e63e-4fa6-b608-ca670da52083" />

---

### 3.2 Understanding TRIG and ECHO Pins

<img width="351" height="275" alt="image" src="https://github.com/user-attachments/assets/a612906d-ae3e-4298-90fe-d0bca8711680" />

The HC-SR04 has **four pins**:  
1. **VCC** — Power (5V)  
2. **GND** — Ground (0V)  
3. **TRIG** — Trigger pin (**OUTPUT** from Arduino)  
4. **ECHO** — Echo pin (**INPUT** to Arduino)

**TRIG pin role:**  
- The Arduino sends a very short, **10 microsecond HIGH pulse** to TRIG.
- This tells the sensor to emit a sound burst.

**ECHO pin role:**  
- The sensor sets ECHO **HIGH** while waiting for the sound to return.
- The length of time ECHO stays HIGH = the sound’s **round-trip time** in microseconds.
- The Arduino measures this using the `pulseIn()` function.

**Why this matters:**  
- Without TRIG, the sensor won’t send a pulse—no measurement is possible.
- Without reading ECHO, the Arduino has no data about how long the sound took to return, meaning it can’t calculate distance.

---

### 3.3 How Arduino Receives Data
<img width="450" height="225" alt="image" src="https://github.com/user-attachments/assets/8fa877aa-bbe4-4912-8674-a115fbd3422c" />

- **Step 1:** Arduino sends HIGH to TRIG for 10 µs → sensor emits sound.
- **Step 2:** ECHO goes HIGH, and the Arduino starts counting microseconds.
- **Step 3:** When the echo returns, ECHO goes LOW → Arduino stops counting.
- **Step 4:** Arduino applies the **speed of sound formula** to compute distance.

All of this happens in a fraction of a second, allowing the Arduino to take dozens of measurements per second.

---

### 3.4 Minimal Code (Single Reading)

```cpp
const int TRIG_PIN = 9;
const int ECHO_PIN = 10;

void setup() {
  Serial.begin(9600);
  pinMode(TRIG_PIN, OUTPUT);
  pinMode(ECHO_PIN, INPUT);
}

void loop() {
  // Trigger the sensor
  digitalWrite(TRIG_PIN, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIG_PIN, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIG_PIN, LOW);

  // Measure echo time
  unsigned long duration = pulseIn(ECHO_PIN, HIGH);

  // Convert to cm
  float distanceCm = (duration * 0.0343) / 2;

  // Print
  Serial.print("Distance: ");
  Serial.print(distanceCm);
  Serial.println(" cm");

  delay(200);
}
```

---

### 3.5 Understanding the Math in Code

- `pulseIn(ECHO_PIN, HIGH)` returns **microseconds** ECHO stayed HIGH.
- Multiply by **0.0343 cm/µs** → one-way speed of sound in cm.
- Divide by **2** → because the sound travels to the object and back.

---

### 3.6 Using the Serial Monitor (TinkerCAD)

In TinkerCAD:  
- Start simulation → click **Serial Monitor** (top-right).  
- Watch distance updates in real-time as you move an object.

---

### 3.7 Range Calculations

**Max range**: ~4 meters (ECHO HIGH for ~23,500 µs)  
**Min range**: ~2 cm (ECHO HIGH for ~117 µs)  

If you set a timeout in `pulseIn()` (e.g., `pulseIn(ECHO_PIN, HIGH, 25000)`), you can limit the maximum distance measured.

---

### 3.8 Example: Range-Based Reactions

```cpp
float distance = (pulseIn(ECHO_PIN, HIGH) * 0.0343) / 2;

if (distance < 10) {
  Serial.println("Too close!");
} else if (distance < 30) {
  Serial.println("Within range");
} else {
  Serial.println("Too far");
}
```

---

### 3.9 Quick Troubleshooting

- **Always 0 cm?** TRIG not wired or not pulsing.
- **Always “Too far”?** Object out of range or poor reflection surface.
- **Jittery readings?** Average several measurements.

---

**Summary:**  
The TRIG pin is your “speaker switch,” starting the ultrasonic pulse.  
The ECHO pin is your “stopwatch,” telling Arduino how long the sound took to return.  
By measuring that time and applying the speed of sound, you can accurately measure distances without any special libraries.

## Demo

<img width="50" height="50" alt="image" src="https://github.com/user-attachments/assets/5bc35294-32f0-4ec3-bd18-d9de2f612fb6" />

## Mini Breakout 2 – Ultrasonic Distance Reader
**Goal:** Measure distances with the HC-SR04 ultrasonic sensor and print to the Serial Monitor.

**Instructions:**
1. Connect **Trig** to Arduino pin **7**, **Echo** to pin **6**, **Vcc** to 5V, **GND** to GND.
2. Write code to:
   - Send a 10µs pulse from Trig.
   - Measure the Echo return time.
   - Convert the time into **distance in cm** using:
    
     <img width="190" height="140" alt="image" src="https://github.com/user-attachments/assets/af89d838-fd54-417c-ae28-a051e1415a12" />

3. Print the distance every 500 ms in the Serial Monitor.

**Stretch Goal:** Display “Object too close!” when distance < 40 cm.

---


## 4. Potentiometer — Variable Resistor Basics

<img width="520" height="345" alt="image" src="https://github.com/user-attachments/assets/98b1bf58-b43e-419e-b48f-4906ab5b3072" />


A **potentiometer** (often called a “pot”) is a **three-terminal variable resistor**.  
You’ve probably used one today without realizing it—volume knobs, light dimmers, joystick controls, and even some touch-sensitive lamps all use potentiometers.

---

### 4.1 The Physics Behind It

**Core principle:**  
A potentiometer changes its **resistance** as you rotate its shaft or slider. This changes how much **voltage** appears at its output terminal, based on **Ohm’s Law**:

<img width="424" height="312" alt="image" src="https://github.com/user-attachments/assets/b6ee7376-08bb-4d77-9098-a1cde429bd62" />


$V = I \times R$

**Construction:**
- Inside is a **resistive track** (often made of carbon or conductive plastic).
- A **wiper** (connected to the middle terminal) moves across the track when you turn the knob or slide the control.
- Two outer pins connect to the ends of the resistive track.
- The **position** of the wiper determines how much resistance is between it and each end.

**Voltage Divider Effect:**
When you connect a potentiometer between 5V and GND:
- One end is **high voltage** (5V),
- The other end is **low voltage** (0V),
- The wiper outputs a voltage **somewhere in between**, proportional to its position.

This is a classic **voltage divider**:
\[
V_\text{out} = V_\text{in} \times \frac{R_2}{R_1 + R_2}
\]

---

### 4.2 How It Works with Arduino

Arduino has an **Analog-to-Digital Converter (ADC)** that reads voltages between **0V and 5V** and converts them to an integer between **0 and 1023** (`analogRead()` function).

Example:
- Wiper at **0%** → ~0V → `analogRead()` returns 0.
- Wiper at **50%** → ~2.5V → `analogRead()` returns ~512.
- Wiper at **100%** → ~5V → `analogRead()` returns ~1023.

---

### 4.3 Real-World Uses

Potentiometers are everywhere in electronics:
- **Volume controls** in audio devices.
- **Light dimmers** for lamps.
- **Position control** for servo motors or robotics.
- **Menu navigation knobs** in embedded systems.
- **Game controllers** (joysticks are basically two potentiometers—one for X-axis, one for Y-axis).

---

### 4.4 Basic Wiring with Arduino

<img width="496" height="255" alt="image" src="https://github.com/user-attachments/assets/cf22dfec-045a-4570-891c-08c53a375741" />


**Pins:**
- **Left pin** → 5V  
- **Right pin** → GND  
- **Middle pin (wiper)** → Arduino **analog input** (e.g., A0)

<img width="489" height="428" alt="image" src="https://github.com/user-attachments/assets/077ae2f4-8209-4129-bd2a-fc3e54b39427" />


---

### 4.5 Minimal Code Example

```cpp
int potPin = A0; // Middle pin of potentiometer

void setup() {
  Serial.begin(9600); // For viewing readings
}

void loop() {
  int wiper = analogRead(wiper); // 0 to 1023
  float voltage = (potValue / 1023.0) * 5.0; // Convert to volts

  Serial.print("Wiper Value: ");
  Serial.print(wiper);
  Serial.print(" | Voltage: ");
  Serial.println(voltage);

  delay(200);
}
```

---

### Reading values of the potentiometer to display on the serial monitor

```cpp
// ================================================
// POTENTIOMETER → SERIAL MONITOR (VOLT METER)
// Shows: knobReading (0..1023) and knobVoltage (0.00..5.00 V)
//
// WIRING:
// - Pot outer pins: 5V and GND
// - Pot middle pin (wiper): A0
//
// WHY 0..1023?
// - Arduino Uno uses a 10-bit ADC (Analog-to-Digital Converter).
// - 10-bit = 2^10 = 1024 possible steps: 0 through 1023.
//   * 0    ≈ 0.00 V at A0
//   * 1023 ≈ 5.00 V at A0 (default reference)
// - Each step is about 5.00 V / 1023 ≈ 0.00488 V (4.88 mV).
//
// HOW WE CONVERT TO VOLTS:
// knobVoltage = (knobReading / 1023.0) * 5.0
//   (Divide by 1023.0 to keep decimals, then multiply by 5.0 V)
//
// OPEN: Tools → Serial Monitor (set 9600 baud).
// ================================================

int knobPin = A0;  // middle pin of the potentiometer

void setup() {
  Serial.begin(9600);
}

void loop() {
  int   knobReading = analogRead(knobPin);        // 0..1023
  float knobVoltage = (knobReading / 1023.0) * 5.0; // 0.00..5.00 V

  Serial.print("Knob Reading: ");
  Serial.print(knobReading);        // 0..1023
  Serial.print("  |  Voltage: ");
  Serial.print(knobVoltage, 2);     // show 2 decimals (e.g., 2.47)
  Serial.println(" V");

  delay(150); // small pause so the text is readable
}

```
---

### 4.6 Example: Controlling LED Brightness

**Big idea:**
We read the knob’s position using analogRead() on an A pin (like A0). We set the LED brightness using analogWrite() on a PWM pin (like D9). The knob gives us numbers 0..1023. The LED brightness wants 0..255. We’ll just divide by 4 to convert (because 1023 ÷ 4 ≈ 255).

**Why “A” pins?**
Pins A0–A5 are analog inputs. They connect to the Arduino’s Analog-to-Digital Converter (ADC). analogRead(A0) measures the voltage on A0 (0 to 5 V) and returns a number 0..1023. On Uno, A0–A5 can also act as normal digital pins, but only A0–A5 can do analogRead().

**Why PWM pins for the LED?**
analogWrite() on Uno is not real analog; it’s PWM — fast ON/OFF that looks like dimming to your eyes. PWM only works on specific digital pins labeled with ~ (on Uno: 3, 5, 6, 9, 10, 11). So we put the LED on D9 (a PWM pin) and call analogWrite(9, value);.

**Wiring**
Potentiometer: one outer pin → 5V, the other outer pin → GND, middle (wiper) → A0. LED: D9 (PWM) → 220–330 Ω resistor → LED anode, LED cathode → GND

**Big idea:**
We read the knob’s position using analogRead() on an A pin (like A0). We set the LED brightness using analogWrite() on a PWM pin (like D9). The knob gives us numbers 0..1023. The LED brightness wants 0..255. We’ll just divide by 4 to convert (because 1023 ÷ 4 ≈ 255).

Why “A” pins?

Pins A0–A5 are analog inputs. They connect to the Arduino’s Analog-to-Digital Converter (ADC). analogRead(A0) measures the voltage on A0 (0 to 5 V) and returns a number 0..1023. On Uno, A0–A5 can also act as normal digital pins, but only A0–A5 can do analogRead().

Why PWM pins for the LED?
analogWrite() on Uno is not real analog; it’s PWM — fast ON/OFF that looks like dimming to your eyes.
PWM only works on specific digital pins labeled with ~ (on Uno: 3, 5, 6, 9, 10, 11).
So we put the LED on D9 (a PWM pin) and call analogWrite(9, value);.

Wiring

Potentiometer: one outer pin → 5V, the other outer pin → GND, middle (wiper) → A0
LED: D9 (PWM) → 220–330 Ω resistor → LED anode, LED cathode → GND

```cpp
/* =====================================================
 POTENTIOMETER -> PWM BRIGHTNESS (BEGINNER EXAMPLE)
 Goal: Read the knob on A0 (0..1023) and convert it to
       a PWM brightness number for the LED on D9 (0..255).

 WIRING:
 - Potentiometer: one outer pin -> 5V, other outer pin -> GND, middle pin -> A0
 - LED: D9 (PWM) -> 220–330 Ω resistor -> LED anode; LED cathode -> GND
 ===================================================== */

int knobPin = A0;   // A0 is an ANALOG INPUT pin (goes to the ADC)
int ledPin  = 9;    // D9 is a PWM OUTPUT pin (has a "~" on the board)

void setup() {
  pinMode(ledPin, OUTPUT);   // we will control this LED with analogWrite()
  Serial.begin(9600);        // open Serial Monitor at 9600 baud to see values
}

void loop() {
  // 1) Read the knob position as a number 0..1023 from the ADC on A0
  int knobReading = analogRead(knobPin);   // 0 (min) … 1023 (max)

  // 2) Convert that to a PWM brightness number 0..255
  int knobPWM = knobReading / 4;           // 0..255


  // 3) Send the PWM value to the LED on D9
  analogWrite(ledPin, knobPWM);

  // 4) Also show the real voltage at A0, for learning (0.00..5.00 V)
  float knobVoltage = (knobReading / 1023.0) * 5.0;

  // 5) Print everything in one line so students can see the conversion
  Serial.print("Knob Reading (0..1023): ");
  Serial.print(knobReading);
  Serial.print("  |  Voltage (0..5V): ");
  Serial.print(knobVoltage, 2);  // two decimals, e.g., 2.47
  Serial.print(" V  |  PWM (0..255): ");
  Serial.println(knobPWM);

  delay(120); // small pause so the Serial Monitor is readable
}
```

---

### 4.7 Tips
- If readings are noisy, use `delay(5)` or average multiple readings.
- Potentiometers **wear over time**; avoid continuous heavy rotation.
- For fine control, use multi-turn precision potentiometers.



## Demo

<img width="50" height="50" alt="image" src="https://github.com/user-attachments/assets/5bc35294-32f0-4ec3-bd18-d9de2f612fb6" />

## Mini Breakout 3 – Potentiometer Voltage Monitor
**Goal:** Read a potentiometer and display voltage values.

**Instructions:**
1. Connect left pin to **5V**, right pin to **GND**, middle pin to **A0**.
2. Use `analogRead()` to get a raw value (0–1023).
3. Convert to voltage and print:
   ```
   Raw: 512 | Voltage: 2.50V
   ```


---

# 5. Breakout Activities 

Now that we’ve learned **libraries**, **servo motors**, **ultrasonic sensors**, and **potentiometers**, it’s time to practice!  
We’ll start with **small, focused challenges** and build toward **multi-component mini-projects**.  
All activities will be simulated in **TinkerCAD Circuits**.

---

<img width="50" height="50" alt="image" src="https://github.com/user-attachments/assets/5bc35294-32f0-4ec3-bd18-d9de2f612fb6" />

## Servo Controlled by Potentiometer
**Goal:** Control servo position using potentiometer input.

**Instructions:**
1. Combine **Activity 1** and **Activity 3** wiring.
2. Map potentiometer range (0–1023) to servo angles (0–180).
3. Turn the knob to move the servo in real time.


---

<img width="50" height="50" alt="image" src="https://github.com/user-attachments/assets/5bc35294-32f0-4ec3-bd18-d9de2f612fb6" />

## Distance-Activated Servo Gate
**Goal:** Open a gate (servo) when an object is detected nearby.

**Instructions:**
1. Place ultrasonic sensor facing forward.
2. If object distance < 10 cm:
   - Move servo to 90° (open).
   - Wait 3 seconds.
   - Return to 0° (closed).
3. Otherwise, keep servo at 0°.


---


# 6. Wrap-Up

Today we moved beyond basic Arduino coding and into the world of **hardware control and sensing**.  
By combining **libraries** with new hardware, we learned how to make the Arduino *interact with the real world*.

**Key Takeaways:**
- **Libraries** are pre-written code collections that make controlling hardware easier and faster.
- **Servo Motors** provide precise rotational movement (0°–180°) and are easy to control with the `Servo.h` library.
- **Ultrasonic Sensors** measure distance by timing sound pulses (Trig pin sends, Echo pin receives) and use basic physics to calculate how far away objects are.
- **Potentiometers** are variable resistors that change voltage output, making them perfect for input controls like knobs and sliders.
- Using the **Serial Monitor** is critical for debugging, viewing sensor values, and confirming that your logic works.

**What We Practiced:**
1. Installing and using libraries.
2. Moving a servo to fixed positions and sweeping angles.
3. Measuring distances with ultrasonic sensors and converting timing to centimeters.
4. Reading analog values from a potentiometer and mapping them to control other devices.
5. Combining multiple components into functional mini-projects.

**Why This Matters:**
Today’s skills are building blocks for real-world engineering:
- Robotics arms and grippers → **Servos**
- Obstacle detection in autonomous vehicles → **Ultrasonic Sensors**
- User control interfaces → **Potentiometers**
- Efficient programming → **Libraries**


## Next-Up
- Motors, Putting it all together with Robotic Platforms
  
