# Day 1 – Introduction to Coding and Hardware Control with Arduino

Welcome to Day 1 of the WIE Coding Bootcamp! Today we will cover the fundamentals of how coding interacts with hardware using the Arduino Microcontroller. This day is foundational — we’ll focus on programming structure, hardware concepts, and controlling simple outputs like LEDs and motors.

---

## 1. Basics of Coding & Programming Languages

- **What is code?**  
  Middle ground between human language and machine instructions; a precise way to tell a computer what to do.
- **Why so many languages?**  
  C/C++, Python, Java, Ruby, FORTRAN, R, HTML, Swift, etc.—each optimizes for different goals (speed, control, safety, ecosystem).
- **Higher vs. lower level:**  
  Lower-level (C/C++) ⇒ more control/efficiency; higher-level (Python) ⇒ faster to write/read.
- **Which to use?**  
  Depends on the application:  
  - **Python** – great for beginners, scripting, RPi  
  - **C/C++** – fine control; **Arduino** uses a C/C++ subset  
  - **Java** – general apps; **R** – statistics; **HTML** – web; **Swift** – iOS

### Working with an IDE (Integrated Development Environment)
Before we can write code for Arduino, we need an **IDE** — the software where we create, edit, and upload code to our microcontroller.

- **IDE** = Integrated Development Environment — a coding workspace that combines a text editor, compiler, and upload/debug tools.
- Examples: **VS Code**, **Arduino IDE**, **PyCharm**, **Eclipse**.
- **In this workshop:** we’ll use the **Arduino IDE** because it’s lightweight, beginner-friendly, and designed for Arduino boards.

<img width="1117" height="595" alt="image" src="https://github.com/user-attachments/assets/6707bd7c-1c73-4a8d-a5d7-8b38e6195f31" />

<img width="1115" height="610" alt="image" src="https://github.com/user-attachments/assets/3685bd5b-c7a6-49ca-b194-43a2ca968996" />

---

## 2. What is the Arduino Microcontroller and How Does it Run Code?

<img width="368" height="337" alt="image" src="https://github.com/user-attachments/assets/277175aa-dbce-4c5b-9741-696d2ed1ffeb" />


The Arduino is a **microcontroller board** — a small computer on a single integrated circuit.  
It can **read inputs** (like light levels, button presses, or sensor data) and **produce outputs** (like turning on an LED, spinning a motor, or sending data over USB) based on the code you upload.

---

### How It Processes Code
1. **You write code** in the Arduino IDE using a C/C++-based language called “Arduino.”
2. **The Arduino IDE compiles** your code into **machine language** — the binary 1s and 0s the microcontroller understands.
3. **The compiled program is uploaded** to the Arduino’s onboard flash memory via the USB cable.
4. Once uploaded, the microcontroller’s processor **fetches, decodes, and executes** each instruction over and over.
5. While powered, the Arduino runs your code automatically without needing to be connected to your computer.

6. ### Core Parts of the Arduino Uno
- **Microcontroller** – the “brain” that executes your code
- **14 Digital I/O pins** – can read or send ON/OFF signals (HIGH/LOW), with pins 3, 5, 6, 9, 10, and 11 supporting PWM
- **6 Analog Input pins (A0–A5)** – read varying voltage values for sensors
- **USB port** – for programming and optional power
- **Power jack** – connect an external power supply
- **Voltage regulator** – ensures the board and components get stable voltage
- **Ground (GND) pins** – electrical return path for circuits
- **Reset button** – restarts the program

  <img width="673" height="518" alt="image" src="https://github.com/user-attachments/assets/0f407ea5-feb0-417d-99c1-b1df770302e6" />


---

### From Code to Action (Example)
1. **Input:** A temperature sensor sends an analog voltage to pin A0.
2. **Processing:** The Uno runs your code to compare the sensor reading to a threshold.
3. **Output:** If it’s too hot, the Uno sends a HIGH signal to pin 9, which powers a fan through a transistor or motor driver.

## 3. Boolean Logic

### Description
Boolean logic is the **language of decision-making** for computers and microcontrollers.  
At its core, it boils everything down to **true** or **false** — just like a light switch can only be **ON** or **OFF**.

The Arduino uses Boolean logic to decide **what to do** based on conditions you write in your code.  
Think of it as the “if this, then that” logic you already use every day:

- **If** it’s raining, **then** take an umbrella.
- **If** the door is locked, **then** use a key.

---

### The Three Basic Boolean Operators
1. **AND (`&&`)** – True only if *both* conditions are true.
   - *Example:* “I’ll go to the park **if it’s sunny AND I’m free from work.**”
2. **OR (`||`)** – True if *at least one* condition is true.
   - *Example:* “I’ll order pizza **if I’m hungry OR if my friends want it.**”
3. **NOT (`!`)** – Reverses the condition.
   - *Example:* “I’ll wear sunglasses **if it is NOT cloudy.**”

---

### In Arduino Code
```cpp
int lightLevel = analogRead(A0);  // read sensor value
bool isDark = lightLevel < 300;

if (isDark && digitalRead(2) == HIGH) {
  // Turn on LED if it’s dark AND button is pressed
  digitalWrite(13, HIGH);
} else {
  digitalWrite(13, LOW);
}
```
### Another Example

Imagine Boolean logic as a bouncer at a club:

**AND**: Only lets you in if you’re on the guest list AND wearing proper shoes.

**OR**: Lets you in if you’re on the guest list OR you pay at the door.

**NOT**: The bouncer says, “You can come in only if you are NOT carrying a backpack.”

Just like the bouncer makes quick **yes/no** decisions at the door,
your Arduino makes quick **true/false** decisions in your circuits —
thousands of times per second.

###  Mini Breakout One:

Given the example of the bouncer above and the sample Arduino code, write a series of conditional statements 
(if, else if, else) to describe the bouncer's decisions. 

Start by changing the variable names to be more descriptive (this is not necessary for functionality, but it's very good coding etiquette). 
Then add an expression to the isAllowedIn variable assignment to calculate the desired result.
```cpp
  Serial.begin(9600); // open the serial port at 9600 bps:
  
  bool isAllowedIn;


/*AND Boolean Example
Bouncer checks the following*/

bool XXX; //guest list condition
bool YYY; // proper shoes condition
isAllowedIn; /*assign a new value to isAllowedIn using AND boolean operation*/ 

//check the value of isAllowedIn
Serial.print("\nIs this person allowed in?\t");
Serial.print(isAllowedIn);
  
  
delay(1000);
//OR Boolean Example

int ZZZ; //paying at the door condition
isAllowedIn;/*assign a new value to isAllowedIn using OR boolean operation*/ 

//check the value of isAllowedIn
Serial.print("\nIs this person allowed in?\t");
Serial.print(isAllowedIn);
  
//OR Boolean Example

int AAA = //backpack condition
isAllowedIn; /*assign a new value to isAllowedIn using OR boolean operation*/ 

//check the value of isAllowedIn
Serial.print("\nIs this person allowed in?\t");
Serial.print(isAllowedIn);

```
---
## 4. Variables

### Description
Variables are **named containers** that store data your Arduino code can use, change, and reference.  
They allow you to give values a name so you can **reuse** them and make your programs easier to read and modify.

Think of a variable like a **labeled box**:  
- The label is the **variable name** (e.g., `ledPin`).  
- The contents of the box are the **value** (e.g., `13`).  
- You can take out the value, change it, or replace it during the program.

---

### Data Types in Arduino (C/C++)
Variables have **types**, which define what kind of data they store:

| Type   | Example Value  | Use Case |
|--------|---------------|----------|
| `int`  | `13`           | Whole numbers (pin numbers, counters) |
| `float`| `3.3`          | Decimal numbers (voltages, sensor readings) |
| `bool` | `true` / `false` | Logical true/false flags |
| `char` | `'A'`          | Single characters |
| `String` | `"Hello"`    | Words, phrases, text |

---

### Variable Scope: Global vs Local

- **Global variables** are declared **outside** of any function (before `setup()`).
  - Available anywhere in the program.
  - Good for values used across multiple functions (e.g., pin numbers).
- **Local variables** are declared **inside** a function.
  - Exist only while that function is running.
  - Good for temporary values that don’t need to be shared.

---

### Example 1 — Global and Local Variables
```cpp
// GLOBAL variable — declared before setup()
// Can be used in both setup() and loop()
int ledPin = 13;

void setup() {
  pinMode(ledPin, OUTPUT); // using the global variable here
}

void loop() {
  // LOCAL variable — declared inside loop()
  int delayTime = 500; // milliseconds
  digitalWrite(ledPin, HIGH); // turn LED on
  delay(delayTime);           // wait half a second
  digitalWrite(ledPin, LOW);  // turn LED off
  delay(delayTime);           // wait half a second
}
```

### Mini Breakout Two
```cpp
  // C++ code
//
void setup()
{
  Serial.begin(9600); // open the serial port at 9600 bps:
  
  //Mini Breakout Two
  //Practice using different variable data types by generating
  //information about someone trying to enter the event
  
  //First name -- string
  String first = "John";
  Serial.print("\n\n");
  Serial.print(first);
  
  //Last name -- string
	
  //Full name -- string (try to generate this using the previous two strings
    
  
  //Proper shoes -- bool
  
  //Amount paid -- float or int (depending on quantity)
    
  
  
  
}

void loop()
{

}

```

---
## 5. Basic Syntax and Arduino Structure (setup() vs loop())

Now that we understand **variables** and **logic**, it’s time to look at how Arduino programs are structured.  
Every Arduino sketch has two required functions: `setup()` and `loop()`.  
These are the **entry points** where your code runs — without them, your program won’t compile.

---

### 5.1 The Two Required Functions

#### `setup()`
- Runs **once** when the Arduino starts or resets.
- Used for **initialization** — setting pin modes, starting serial communication, configuring sensors, etc.
- Think of it as “getting the room ready” before the event starts.

#### `loop()`
- Runs **over and over** as long as the Arduino has power.
- Contains the **main behavior** of your program.
- Think of it as “the ongoing event” — repeating the same set of actions until something changes.


<img width="311" height="181" alt="image" src="https://github.com/user-attachments/assets/1851eb64-9034-448d-959a-a53ab32354cc" />

---

### 5.2 Program Flow
1. Arduino powers up or is reset.
2. Code inside `setup()` runs **one time**.
3. Code inside `loop()` runs from top to bottom.
4. When `loop()` finishes, it **starts again at the top** — forever.

---

### 5.3 Minimal Arduino Sketch
```cpp
void setup() {
  // This code runs once
}

void loop() {
  // This code runs repeatedly
}
```

Even an “empty” sketch must have both `setup()` and `loop()` defined.

---

### 5.4 Example — LED Blink 
```cpp
// GLOBAL variable — LED pin number
int ledPin = 13;  

void setup() {
  // Runs once at startup
  pinMode(ledPin, OUTPUT);  // Tell Arduino the LED pin is an output
}

void loop() {
  // Runs repeatedly
  digitalWrite(ledPin, HIGH); // Turn LED on
  delay(500);                 // Wait 500 ms
  digitalWrite(ledPin, LOW);  // Turn LED off
  delay(500);                 // Wait 500 ms
}
```

**Flow here:**
1. `setup()` tells Arduino which pin is controlling the LED.
2. `loop()` turns it on, waits, turns it off, waits — then repeats forever.

---

### 5.5 Adding Variables and Logic to loop()
We can combine what we learned about **variables** and **Boolean logic**.

```cpp
int ledPin = 13;          // global variable
bool ledOn = false;       // start with LED off
int delayTime = 1000;     // 1 second

void setup() {
  pinMode(ledPin, OUTPUT);
}

void loop() {
  if (!ledOn) {                    // if the LED is currently off
    digitalWrite(ledPin, HIGH);    // turn it on
    ledOn = true;                  // update the variable
  } else {                         // otherwise...
    digitalWrite(ledPin, LOW);     // turn it off
    ledOn = false;                 // update the variable
  }
  delay(delayTime);                // wait before toggling again
}
```

---

### 5.6 Key Syntax Rules in Arduino
- **Semicolons (`;`)** end each statement.
- **Curly braces `{}`** group code blocks (e.g., for functions, loops, if statements).
- **Indentation** doesn’t affect how code runs, but makes it readable.
- **Comments**:
  - `//` for single-line comments
  - `/* ... */` for multi-line comments

---

<img width="238" height="282" alt="image" src="https://github.com/user-attachments/assets/db0a4798-3f82-4c5d-897c-2ccc6076f113" />

- _Diagram of Arduino program flow: Power on → setup() once → loop() repeats._  
- _Side-by-side example: setup() = setting the stage, loop() = the performance repeating._

---

## 6. Introduction to `digitalWrite()`, `delay()`, `pinMode()`, and `analogWrite()`

Now that you know how Arduino programs are structured, let’s explore the **core building blocks** you’ll use in almost every project.  
These are the “verbs” of Arduino — they **do** things.

---

### 6.1 `pinMode()`

**What it does:**  
Tells Arduino whether a pin will be used as an **input** or an **output**.

- **INPUT** – read information (button, sensor, etc.)
- **OUTPUT** – send a signal (turn on LED, move motor, etc.)

**Why it’s needed:**  
Pins are like doors — `pinMode()` tells Arduino if the door should be **open to receive things** (input) or **open to send things out** (output).

**When to use:**  
Always in `setup()` before you use a pin.

**Example:**
```cpp
void setup() {
  pinMode(13, OUTPUT);  // Pin 13 will control an LED
}
void loop() {
  // Empty for now
}
```

**Example:**  
Think of a water pipe — you have to decide if it’s going to **let water in** or **send water out** before turning the valve.

---

### 6.2 `digitalWrite()`

**What it does:**  
Sets a digital pin to **HIGH** (on) or **LOW** (off).

- **HIGH** – sends 5V (or 3.3V on some boards)
- **LOW** – sends 0V (ground)

**Why it’s needed:**  
It’s how you actually control devices like LEDs, buzzers, or relays.

**When to use:**  
Any time you want to turn something fully on or fully off.

**Example:**
```cpp
void setup() {
  pinMode(13, OUTPUT);     // LED pin
}
void loop() {
  digitalWrite(13, HIGH);  // Turn LED on
  delay(1000);             // Wait 1 second
  digitalWrite(13, LOW);   // Turn LED off
  delay(1000);             // Wait 1 second
}
```

**Example:**  
Like flipping a light switch — either the light is ON or it’s OFF, no in-between.

---

### 6.3 `delay()`

**What it does:**  
Pauses the program for a certain number of **milliseconds** (1000 ms = 1 second).

**Why it’s needed:**  
Without delays, your Arduino executes code **extremely fast** — so fast you might not see changes or allow hardware to react.

**When to use:**  
When you want a simple pause between actions.

**Example:**
```cpp
void loop() {
  digitalWrite(13, HIGH);  // LED on
  delay(500);              // Wait half a second
  digitalWrite(13, LOW);   // LED off
  delay(500);              // Wait half a second
}
```

**Example:**  
Like telling someone, “Clap your hands, then wait 2 seconds before clapping again.”

> **Tip:** Later you’ll learn how to do timing without `delay()` so you can run multiple things at once.

---

### 6.4 `analogWrite()`

<img width="566" height="246" alt="image" src="https://github.com/user-attachments/assets/7aeb9699-aaee-43d5-bcc0-fe10627d45bc" />

**What it does:**  
Sends an **analog-like signal** using **PWM (Pulse Width Modulation)** on pins marked with a `~` on the Arduino board.

- Values range from **0** (off) to **255** (fully on).
- Controls brightness of LEDs or speed of motors.

**Why it’s needed:**  
Some devices aren’t just “on or off” — you may want *half brightness* or *half speed*.

**When to use:**  
When controlling power level to an output device on a PWM pin.

**Example:**
```cpp
void setup() {
  pinMode(9, OUTPUT);    // PWM pin for LED
}
void loop() {
  analogWrite(9, 128);   // Set LED to ~50% brightness
  delay(1000);           // Wait 1 second
  analogWrite(9, 255);   // Full brightness
  delay(1000);           // Wait 1 second
}
```

**Example:**  
Imagine flicking a light switch on and off really fast — if it’s on most of the time, it **looks bright**; if it’s off more than on, it **looks dim**.

---

### 6.5 Putting It All Together
Here’s a **very basic sketch** that uses all four functions:

```cpp
int ledPin = 9; // Must be a PWM pin for analogWrite

void setup() {
  pinMode(13, OUTPUT);  // On/off LED
  pinMode(ledPin, OUTPUT); // PWM LED
}

void loop() {
  digitalWrite(13, HIGH); // Turn pin 13 LED on
  analogWrite(ledPin, 128); // Set PWM LED to half brightness
  delay(1000);              // Wait 1 second

  digitalWrite(13, LOW);  // Turn pin 13 LED off
  analogWrite(ledPin, 0); // Turn PWM LED off
  delay(1000);            // Wait 1 second
}
```


---



## 7. Breakout Activity: TinkerCAD LED Blinker

## Intro to using TinkerCAD

<img width="741" height="339" alt="image" src="https://github.com/user-attachments/assets/abb17aa0-02ef-455d-bf40-6064dbced825" />

## Exploring Circuits
After singing in to TinkerCAD, you will find a dashboard of your recent designs (if any). You will find the **Circuits** section under **3D Designs**. 

<img width="760" height="473" alt="image" src="https://github.com/user-attachments/assets/ef95f455-b0b0-41e1-a715-1826e353b7f5" />

While on your dashboard, you can scroll through your existing 3D, Codeblocks or Circuits designs. You can also create a new design by clicking the blue + New button in the upper right hand corner of the dashboard and selecting the editor you'd like to open.

Tinkercad’s Circuits editor has a similar layout to its 3D design editor. You’ll find a large window on the left for creating your design. On the right side you’ll see a panel filled with components you can drag and drop into the workspace to create your circuit. 

<img width="698" height="404" alt="image" src="https://github.com/user-attachments/assets/30ca4ad3-87ab-4f5c-878b-4e395e76eaf4" />

Unlike Tinkercad’s 3D design editor, the workspace in Circuits is two-dimensional. You can move your components around by selecting and dragging them, or pan the view around your design by clicking and dragging the empty space around it.

You can also zoom in and out of your design by using the scroll wheel on your mouse, a two-finger gesture on your trackpad, or a key combination of Command + and Command -. 

A “Zoom to fit” button is located in the top left corner of the workspace, which will center and zoom your design to fill the window. Pressing the letter F on your keyboard works as a handy shortcut for this same command.

---

## Challenge: PWM Dimming + Morse Code “HELLO”

**Goal:**  
- Pin **9** (PWM) should **dim** every 500 ms, moving from dim → bright → dim → … continuously.  
- Pin **13** (digital) should blink **Morse code for “HELLO”** on repeat.

### Wiring (already done)
- Pin **13** → LED (with resistor) → GND  
- Pin **9** → LED (with resistor) → GND

---

### Timing Rules

#### A) Dimming on Pin 9 (PWM)
- Update brightness **every 500 ms**.  
- Use `analogWrite(ledAnalog, value)` where `value` is **0–255**.  
- Start low (e.g., 20), **increase by a fixed step** (e.g., 25) until ~255, then **reverse** back down to the minimum, and repeat.

#### B) Morse Code on Pin 13 (digitalWrite)
Morse for **HELLO**:  
- **H** = `....`  
- **E** = `.`  
- **L** = `.-..`  
- **L** = `.-..`  
- **O** = `---`

Use these standard durations (choose one **unit** and stick to it):
- Set **UNIT = 200 ms** (recommended for visibility).
- **DOT** = 1 × UNIT = **200 ms**
- **DASH** = 3 × UNIT = **600 ms**
- **GAP (between dots/dashes within a letter)** = 1 × UNIT = **200 ms** (LED **LOW**)
- **LETTER GAP** (between letters) = 3 × UNIT = **600 ms** (LED **LOW**)
- **WORD GAP** (between words) = 7 × UNIT = **1400 ms** (LED **LOW**) — not strictly needed here, since “HELLO” is one word, but add after each full word loop.

Behavior:
- For a **dot**: LED **HIGH** for DOT, then **LOW** for GAP.  
- For a **dash**: LED **HIGH** for DASH, then **LOW** for GAP.  
- After finishing a letter, replace the last GAP with a **LETTER GAP**.  
- After finishing “HELLO,” add a **WORD GAP**, then repeat.

---

### Suggested Template (students fill in `setup()` and `loop()`) | Feel free to use your own variables or complete this activity with your own approach. There are many "correct" ways to do this. This is just to practice applying the material we learned.

```cpp
// ----- Pins -----
int ledDigital = 13; // Morse LED (digital on/off)
int ledAnalog  = 9;  // Dimming LED (PWM)

// ----- Morse Timing (ms) -----
const int UNIT        = 200;      // base time unit
const int DOT         = 1 * UNIT; // 200 ms
const int DASH        = 3 * UNIT; // 600 ms
const int GAP_INTRA   = 1 * UNIT; // gap between elements in a letter
const int GAP_LETTER  = 3 * UNIT; // gap between letters
const int GAP_WORD    = 7 * UNIT; // gap between words

// ----- PWM Dimming Params -----
const int PWM_MIN     = 20;   // don't start at 0 so it's visible
const int PWM_MAX     = 255;  // full brightness
const int PWM_STEP    = 25;   // change per step
const int PWM_INTERVAL= 500;  // ms between brightness updates

void setup() {
  // TODO: set both pins as OUTPUT with pinMode(...)
}

void loop() {
  // TODO Part A: DIMMING on ledAnalog
  // - Track current brightness and direction (up or down)
  // - Every PWM_INTERVAL ms: update brightness by PWM_STEP
  // - Reverse direction at PWM_MAX or PWM_MIN
  // - Use analogWrite(ledAnalog, brightness);

  // TODO Part B: MORSE "HELLO" on ledDigital
  // - For each letter in HELLO, blink the pattern:
  //     H: DOT DOT DOT DOT
  //     E: DOT
  //     L: DOT DASH DOT DOT
  //     L: DOT DASH DOT DOT
  //     O: DASH DASH DASH
  // - After each element (dot/dash): LOW for GAP_INTRA
  // - After each letter: LOW for GAP_LETTER
  // - After "HELLO": LOW for GAP_WORD, then repeat
}
```

## 8. Wrap-Up

###  What You Should Know Now:
- What code is and why programming languages exist.
- The difference between high-level and low-level languages, and why Arduino uses a C/C++ subset.
- How to identify and use the Arduino IDE (where to write, compile, and upload code).
- Basic structure of an Arduino sketch (`setup()` and `loop()` functions).
- How to use `pinMode()` to set pins as INPUT or OUTPUT.
- The difference between `digitalWrite()` (on/off control) and `analogWrite()` (PWM control for variable output).
- How `delay()` works to pause code execution for a set amount of milliseconds.
- How Boolean logic works in Arduino programs (AND, OR, NOT) and why it’s like making yes/no decisions.

###  Homework / Practice:
- Review slides for the next days, to better understand the next concepts, a good grasp of today's lecture is needed. 
- Download VS Code C++ Installer, Compiler
---

**Next Up – Day 2:**
if, else, if else, for, while, do-while, Functions, custom and built-in, coding etiquette (commenting, indentation, naming), wiring
