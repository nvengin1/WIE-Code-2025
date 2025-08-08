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



