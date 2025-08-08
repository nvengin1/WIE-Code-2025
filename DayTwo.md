# Day 2 – Decision-Making, Loops, Functions, and Wiring Basics

> **Welcome back!**  
> Yesterday we explored how to talk to your Arduino’s pins and make them act.  
> Today, we’re going beyond simply turning things on and off — we’ll learn how to make your Arduino **think, decide, repeat tasks, and stay organized**.

---

## Why This Matters
When you write code for an Arduino, you’re not just telling it “do this.”  
You’re building **logic**:  
- **Decision-making** – Should the LED be on or off based on the sensor?  
- **Repetition** – How can I blink something 100 times without typing 100 commands?  
- **Organization** – How do I keep my code clean so I can actually read it later?  

---

## Today’s Roadmap
1. **If / Else If / Else** – Teach your Arduino to make choices.  
2. **For, While, Do-While** – Efficiently repeat actions without writing the same line over and over.  
3. **Functions** – Group related code into reusable “mini-programs” for cleaner, smarter sketches.  
4. **Coding Etiquette** – Learn how to comment, indent, and name things so your code makes sense to others (and future you).  
5. **More TinkerCAD** – Put these concepts into practice with simulations.  
6. **Wiring** – Physical connections that match your virtual designs.

---

## The Big Picture
By the end of today, you’ll be able to:
- Write code that **reacts** to input and changes behavior on the fly.
- Use loops to save time and prevent repetitive typing.
- Build and call your own functions for organization and reuse.
- Apply professional habits in your Arduino code.
- Confidently translate your TinkerCAD simulations to real-world wiring.

---

> **Tip for Today:**  
> If Day 1 was learning the alphabet, Day 2 is forming sentences.  
> You’re now writing *logic* that makes your Arduino smarter, more responsive, and easier to manage.

# 1. Conditionals, Loops, Functions, Arrays & Strings

> **Big idea:**  
> Day 1 taught you *how* to talk to pins.  
> **Day 2** is about *thinking*: making your Arduino **decide**, **repeat**, **organize**, and **remember** information.

---

## 1.1 Conditionals — Teaching Arduino to Make Decisions

**What are they?**  
Conditionals are "decision points" in your code — moments where the program must choose between two or more paths based on what’s happening.

 
Imagine you’re at a crosswalk:  
- **If** the light is green → you walk.  
- **Else** (the light is red) → you wait.  

Your Arduino can do the same:  
- If a sensor reading is high, turn a motor on.  
- Else, keep it off.

  
Think about an automatic door:  
- If someone is detected by the motion sensor, it opens.  
- Else, it stays closed.

**Why important?**  
Without decisions, Arduino would just run the same fixed script forever — no interactivity.

---

## 1.2 Loops — Repeating Without Copy–Paste

**What are they?**  
Loops let your Arduino run the same block of instructions multiple times without writing the same code over and over.

Telling a kid to “jump rope 10 times” vs. telling them “jump rope” 10 separate times in a row. One is efficient; one wastes breath.

A washing machine runs cycles — wash, rinse, spin — and repeats certain actions automatically. That’s a loop in real life.

**Why important?**  
Loops save time and make code flexible: change one number, and your Arduino could blink 3 times instead of 30.

---

## 1.3 Functions — Reusable Mini-Programs

**What are they?**  
A function is a named block of code you can "call" whenever you need it.

A recipe card. Instead of rewriting the steps to make a sandwich every time you’re hungry, you follow the recipe you already have.

Think about a TV remote’s volume button — it’s the same internal steps every time, but you just press the button instead of building a new remote.

**Why important?**  
Functions keep code organized, reusable, and easier to fix.

---

## 1.4 Arrays — Keeping Many Things in One Box

**What are they?**  
Arrays let you store multiple values of the same type under one name.

A row of mailboxes — each has a number (index) so you can quickly find the one you need.

Instead of having a variable for `led1`, `led2`, `led3`, you have one container called `ledPins` that holds all three numbers.

**Why important?**  
They make it possible to do the same thing to many items without repeating code.

---

## 1.5 Strings — Handling Text

**What are they?**  
Strings store text like `"Hello"` or `"Arduino"`.  

A string is like a beaded bracelet — each letter is a bead, strung together in order.

Your phone stores your name as a string in its contact list.

**Why important?**  
They let Arduino handle words, sentences, and messages for displays, serial communication, or Morse code.

---

## 1.6 Very Basic Code Examples

### IF / ELSE
```cpp
int temp = 25;

if (temp > 30) {
  digitalWrite(13, HIGH); // LED ON
} else {
  digitalWrite(13, LOW);  // LED OFF
}
```

### FOR loop
```cpp
for (int i = 0; i < 3; i++) {
  digitalWrite(13, HIGH);
  delay(200);
  digitalWrite(13, LOW);
  delay(200);
}
```
# 2. Loops — How to Write `for` and `while` (properly!)

> Loops let your Arduino repeat actions **without** copy–pasting code.
> Think of them as a **metronome**: they count beats (iterations) and tell your code what to do on each beat.

---

## 2.1 `for` Loops — Counted Repetition

**When to use:**  
You know *how many times* you want to repeat something (e.g., blink 10 times, step through 3 LEDs, run 100 readings).

**Shape of a `for` loop (read left → right):**
```cpp
for (INITIALIZE;  CONDITION;  STEP) {
  // body: runs once per iteration if CONDITION is true
}
```
- **INITIALIZE** – create/set a counter *once* (e.g., `int i = 0`)
- **CONDITION** – loop continues **while this is true** (e.g., `i < 5`)
- **STEP** – how the counter changes each time (e.g., `i++` adds 1)

“Start at 0, keep going while you’re under 5, add 1 each time.”

### Basic examples
Blink exactly 3 times:
```cpp
for (int i = 0; i < 3; i++) {
  digitalWrite(13, HIGH); delay(200);
  digitalWrite(13, LOW);  delay(200);
}
```

Walk through an array of pins (`0..n-1` indexing):
```cpp
int leds[] = {3, 5, 6};
int n = 3;

for (int i = 0; i < n; i++) {      // i = 0,1,2
  pinMode(leds[i], OUTPUT);
  digitalWrite(leds[i], HIGH); delay(150);
  digitalWrite(leds[i], LOW);
}
```

**Indexing rule (burn this in):**  
Arrays start at **0**. The last index is **length - 1**.  
If `n == 3`, valid indices are `0, 1, 2`.

**Common off-by-one bug:**  
- ✅ `i < n` (correct)  
- ❌ `i <= n` (goes one past the end!)

---

## 2.2 `while` Loops — Repeat *until* something changes

**When to use:**  
You **don’t know** how many times you’ll repeat. You loop **while** a condition remains true (e.g., keep reading until a button is pressed).

**Shape of a `while` loop:**
```cpp
while (CONDITION) {
  // body: runs again as long as CONDITION is true
}
```

### Basic examples
Wait until button on pin 2 is pressed:
```cpp
while (digitalRead(2) == LOW) {
  // do nothing; still waiting
}
digitalWrite(13, HIGH); // button pressed → turn LED on
```

Count sensor samples until a threshold is hit:
```cpp
int count = 0;
int sensor = analogRead(A0);

while (sensor < 700) {      // keep sampling until bright
  count++;
  delay(10);
  sensor = analogRead(A0);  // update condition INSIDE loop
}
Serial.println(count);       // how many readings it took
```

> **Important:** In a `while` loop, make sure something inside the loop **changes the condition**.  
> If not, you’ve made an **infinite loop** (Arduino seems “stuck”).

---

## 2.3 `for` vs `while` — Which to pick?

- Use **`for`** when the number of iterations is known or tied to an index (`0..n-1`), especially with arrays.
- Use **`while`** when the number of iterations is unknown and depends on real-time conditions (button, sensor, serial input).

**Same behavior, two styles (blink 5 times):**
```cpp
// for
for (int i = 0; i < 5; i++) { digitalWrite(13, !digitalRead(13)); delay(200); }

// while
int j = 0;
while (j < 5) { digitalWrite(13, !digitalRead(13)); delay(200); j++; }
```

---

## 2.4 Stepping, skipping, and going backwards

- **Step by 2:** `for (int i = 0; i < 10; i += 2) { ... }` → 0,2,4,6,8  
- **Count down:** `for (int i = 5; i > 0; i--) { ... }` → 5,4,3,2,1  
- **Skip iterations:**  
  - `continue;` → skip to the next iteration  
  - `break;` → exit the loop entirely

Example: light only even-indexed LEDs:
```cpp
int leds[] = {3,5,6,9};
for (int i = 0; i < 4; i++) {
  if (i % 2 == 1) continue; // skip odd indices
  digitalWrite(leds[i], HIGH); delay(100);
  digitalWrite(leds[i], LOW);
}
```

---

## 2.5 Common mistakes (and fixes)

- **Forgetting to update the condition (while):**
  ```cpp
  // ❌ sensor never changes → infinite loop
  while (sensor < 500) { /* ... */ }

  // ✅ update inside the loop
  while (sensor < 500) { sensor = analogRead(A0); }
  ```

- **Off-by-one with arrays:**
  ```cpp
  int n = 3;
  // ❌ i <= n goes out of bounds (0..3)
  for (int i = 0; i <= n; i++) { /* ... */ }

  // ✅ use i < n  (0..2)
  for (int i = 0; i < n; i++) { /* ... */ }
  ```

---


# 3. Coding Etiquette — Commenting, Indentation, Naming

> Writing code is like leaving notes for **future you** and **other people** who will read your work.  
> Even if your Arduino understands messy code, *humans* reading it will struggle — and humans fix bugs.

---

## 3.1 Commenting — Talking to Humans, Not the Computer

**What is a comment?**  
Text in your code that the Arduino **ignores** but explains what the code is doing.  

- **Single-line:** `// your note here`  
- **Multi-line:**  
  ```cpp
  /* This
     spans
     multiple lines */
  ```

**Good etiquette:**
```cpp
// Blink LED on pin 13 three times
for (int i = 0; i < 3; i++) {
  digitalWrite(13, HIGH); // LED ON
  delay(200);
  digitalWrite(13, LOW);  // LED OFF
  delay(200);
}
```

**Bad etiquette:**
```cpp
// turn on
digitalWrite(13, HIGH);
// delay
delay(200);
// turn off
digitalWrite(13, LOW);
// delay
delay(200);
```
- **Why bad?** Each comment just repeats the code.  
  Comments should explain **why**, not just **what**.

---

## 3.2 Indentation — Lining Things Up for Readability

**Indentation** = spacing code so that its **structure** is easy to see.  
Arduino IDE auto-indents with `Ctrl+T` (or `Cmd+T` on Mac).

**Good indentation:**
```cpp
void loop() {
  for (int i = 0; i < 3; i++) {
    digitalWrite(13, HIGH);
    delay(200);
    digitalWrite(13, LOW);
    delay(200);
  }
}
```

**Bad indentation (works, but hurts your eyes):**
```cpp
void loop(){for(int i=0;i<3;i++){digitalWrite(13,HIGH);delay(200);digitalWrite(13,LOW);delay(200);}}
```
- **Why bad?** Hard to see where loops start/end, easy to miss semicolons or braces.

---

## 3.3 Naming — Clear and Consistent

**Good etiquette:**
- Use descriptive names: `ledPin`, `buttonPin`, `sensorValue`
- Follow one style and stick to it: `camelCase` or `snake_case`
- Constants in **ALL_CAPS**: `const int LED_PIN = 13;`

**Bad etiquette:**
```cpp
int x = 13;
int y = 2;
int z = 500;
digitalWrite(x, HIGH);
delay(z);
digitalWrite(x, LOW);
```
- **Why bad?** No clue what `x`, `y`, or `z` mean without scrolling up.  

---

## 3.4 When Bad Etiquette Breaks Compilation

Sometimes bad etiquette isn’t just ugly — it **won’t compile**:

**Example 1 — Mismatched braces/brackets:**
```cpp
void loop() {
  if (true) {
    digitalWrite(13, HIGH);
  // Missing closing brace for if → compilation error
}
```
**Error:** *expected ‘}’ before end of input*

---

**Example 2 — Inconsistent naming:**
```cpp
int LedPin = 13;
digitalWrite(ledpin, HIGH); // ledpin is not the same as LedPin
```
**Error:** *‘ledpin’ was not declared in this scope*

---

**Example 3 — Bad indentation hiding missing semicolon:**
```cpp
int ledPin = 13
void loop() {
  digitalWrite(ledPin, HIGH);
}
```
**Error:** *expected ‘;’ before ‘void’*

---

## 3.5 Quick Do/Don’t Table

| **Do**                                | **Don’t**                                 |
|---------------------------------------|--------------------------------------------|
| Use clear variable names (`ledPin`)   | Use `a`, `b`, `x1` with no meaning         |
| Indent blocks inside `{}`             | Write everything on one line               |
| Comment the **why** of tricky code    | Comment obvious things only                 |
| Match case exactly (`ledPin` vs `LedPin`) | Change capitalization mid-program       |
| Use `Ctrl+T` to auto-format            | Leave braces/spacing random                 |

---

# 4. Wiring — Connecting Your Arduino to the Real World

> If code is the **brain**, wiring is the **nervous system** — it’s how your Arduino senses and controls the physical world.  
> Bad wiring is like sending your brain’s signals to the wrong muscles — things won’t work, or they’ll work *very* strangely.

---

## 4.1 Anatomy of an Arduino Uno for Wiring

An Arduino Uno has a variety of pins, each with a specific purpose:

| **Pin Type**     | **Label**                  | **Purpose** |
|------------------|----------------------------|-------------|
| **Digital Pins** | `0`–`13`                   | ON/OFF signals (HIGH or LOW). Can be INPUT or OUTPUT. Pins with `~` also support PWM (`analogWrite()` style output). |
| **Analog Pins**  | `A0`–`A5`                  | Read variable voltages (0–5V) and convert to numbers with `analogRead()`. |
| **Power Pins**   | `5V`, `3.3V`                | Provide fixed voltage to power sensors, modules, LEDs (within current limits). |
| **Ground (GND)** | `GND`                       | Reference point for all voltages — must be connected for a circuit to work. |
| **VIN**          | `VIN`                       | Supply voltage to the board (if powering from external source). |

**Key fact:** Digital pins can also act as inputs. Analog pins can be used as digital pins (`A0` = digital pin 14).

---

## 4.2 Ground — The Unsung Hero

Think of **Ground (GND)** as the "zero point" in your electrical world.  
If electricity is water in a pipe:
- **Voltage** = water pressure
- **Current** = flow of water
- **Ground** = drain back to the tank

Without a path to ground, the "water" has nowhere to go — your circuit won’t run.

---

## 4.3 Breadboard Basics

A breadboard is your prototyping playground.

- **Rows:** Horizontal power rails (`+` and `–`), often along the top and bottom, used to distribute 5V/3.3V and GND.
- **Columns:** Groups of 5 holes connected vertically in the center area — components and jumper wires go here.
- **Gap in the Middle:** Separates the left and right sides so you can plug in ICs or connect across with wires.

**Analogy:** Imagine the breadboard as a neighborhood:
- Houses in the same **column group** share the same plumbing (electrical connection).
- Power rails are like main streets carrying electricity across the whole board.

---

## 4.4 Wiring Rules of Thumb

1. **Match the circuit to the voltage** — sensors rated for 3.3V can be damaged by 5V.
2. **Always share ground** — Arduino GND must connect to the ground of any sensor or external device.
3. **Short = Bad** — directly connecting 5V to GND without a load (like a resistor) will cause overheating or damage.
4. **Use resistors with LEDs** — to limit current (220–330 Ω typical for 5V LEDs).
5. **Double-check polarity** — many devices (LEDs, motors, sensors) have a positive and negative side.

---

## 4.5 Physics in 60 Seconds — Why Wiring Works

- **Voltage (V)** = electrical "pressure" pushing electrons through the circuit.
- **Current (I)** = flow of electrons, measured in Amps.
- **Resistance (R)** = opposition to current.
- **Ohm’s Law:** `V = I × R`

Example: An LED might drop 2V and want 20mA.  
Resistor needed: `(5V - 2V) ÷ 0.02A = 150 Ω` (so use 220 Ω to be safe).

---


For those attending on Friday, you will get exposure to hands-on wiring!

---

# 5. Using a Pushbutton with Arduino

---

## 5.1 What Is a Pushbutton?

A pushbutton is a **momentary switch** — it only changes state while you press it.  
- When **not pressed**, the circuit is **open** → no current flows.  
- When **pressed**, the circuit **closes** → current flows.  
- In TinkerCAD, you can simulate pressing it by clicking it during the simulation.

---

### Physics Behind It
Think of it like a **drawbridge** for electricity:
- Bridge **up** → no cars (electrons) can cross → circuit open.
- Bridge **down** → cars (electrons) can cross → circuit closed.

Inside a pushbutton are **springy metal contacts**:
- Pressing the button pushes these contacts together, completing the circuit between two pins.
- Releasing the button lets the spring push them apart again.

---

## 5.2 The 4 Legs of a Pushbutton

A standard pushbutton has **four legs** arranged in pairs:
```
| 1   2 |
| 3   4 |
```
- Pins **1 & 2** are connected internally.
- Pins **3 & 4** are connected internally.
- The button **connects both sides** only when pressed.

---

## 5.3 Wiring in Arduino (TinkerCAD)

We’ll use the Arduino Uno’s **internal pull-up resistor** to make wiring simpler.

**Wiring:**
1. One side of the button → Arduino digital pin (e.g., Pin 2).
2. Other side of the button → **GND**.
3. No extra resistor is needed — the `INPUT_PULLUP` mode uses Arduino’s built-in 20–50kΩ resistor.

**Breadboard Tip:**
- Make sure the button is placed across the center “gap” of the breadboard so each side is isolated until pressed.

---

## 5.4 How INPUT_PULLUP Works

Normally, floating inputs can randomly read HIGH or LOW.  
Using `INPUT_PULLUP`:
- Arduino keeps the pin **HIGH** by default (connected weakly to +5V through resistor).
- Pressing the button connects the pin to **GND** → reads **LOW**.

This means:
- **Pressed = LOW**
- **Not pressed = HIGH**

---

## 5.5 Example Code — Button Test

```cpp
// Pin setup
const int buttonPin = 2; // Button connected to Pin 2

void setup() {
  Serial.begin(9600);          // Start Serial Monitor
  pinMode(buttonPin, INPUT_PULLUP); // Enable internal pull-up
}

void loop() {
  int buttonState = digitalRead(buttonPin);

  if (buttonState == LOW) {
    Serial.println("Button is PRESSED");
  } else {
    Serial.println("Button is NOT pressed");
  }

  delay(200); // Small delay to make it readable
}
```

---

### 5.6 Try It in TinkerCAD
1. Wire the pushbutton so one leg is to **Pin 2**, the opposite leg is to **GND**.
2. Upload and run the code above.
3. Open the Serial Monitor in TinkerCAD.
4. Press the button in the simulation — you should see `"Button is PRESSED"` messages.

---


