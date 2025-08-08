# Day 4 – DC Motors, Motor Control, and Navigation Basics

Welcome to **Day Four** of the Arduino Coding Bootcamp!  
Today we shift from controlling small devices like servos to powering **DC motors**—the kind used to drive wheels on robots, conveyor belts, fans, and countless other moving systems.  
If servos are great for precise angle control, **DC motors** are all about continuous, powerful rotation — perfect for **robotic platforms**.

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

**Key idea:** A current-carrying wire in a magnetic field experiences a **force** (Lorentz force). Repeating this around the rotor makes continuous rotation.

---

## 1.2 Voltage vs. Current (and why Arduino pins are NOT a motor power source)

- **Voltage (V):** “electrical pressure” (e.g., 3V, 5V, 9V, 12V).  
- **Current (A):** “flow rate” the motor **draws**. DC motors often need **hundreds of mA to a few A**, especially at startup (“stall current”).
- **Arduino I/O pin limit:** ~20–40 mA per pin (tiny). **Never** power a motor directly from an Arduino pin.
- **Arduino 5V pin:** can power sensors and small modules, **not** motors (you’ll brown-out/reset the board).

**Conclusion:** Use a **separate motor power supply** (e.g., 6–9 V battery pack for TT gearmotors, or as specified by your motor) and a **motor driver** between Arduino and motor.

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

---

## 1.4 Typical Wiring Patterns

### A) One motor, one direction (VERY simple, for learning)
- **NPN transistor or N-channel MOSFET** as a switch (low-side).
- **Flyback diode** across motor terminals (stripe to +V).
- **PWM** from Arduino to the transistor’s gate/base via a resistor.
- **External motor supply** → motor → transistor → GND.
- **Common ground:** **Must** connect Arduino GND to motor power GND.

> Use this when you only need **on/off or speed**, **not direction**.

### B) Full H-Bridge (speed **and** direction)
For two-pin direction control + one PWM enable:
- Driver inputs: **IN1**, **IN2** (direction), **ENA** (speed PWM)  
- Motor output: **OUT1/OUT2** to the motor  
- Power: **+VM** (motor voltage), **GND** (motor GND), **+5V** (logic supply, depending on board)  
- **Common ground** between Arduino and motor supply is essential.

**Direction truth table (typical):**
- `IN1=HIGH, IN2=LOW`  → Forward
- `IN1=LOW, IN2=HIGH`  → Reverse
- `IN1=LOW, IN2=LOW`   → Coast (motor spins freely)
- `IN1=HIGH, IN2=HIGH` → Brake (both sides driven—stops quickly)

**Speed:** Use `analogWrite(ENA, 0..255)` to set PWM duty cycle.

---

## 1.5 Power Safety and Protecting Other Hardware

- **Never feed 9V directly** to 5V sensors (e.g., ultrasonic HC-SR04). Sensors expect **5V** (or sometimes **3.3V**).  
- Use **separate power rails**: one for logic (Arduino **5V**) and one for motors (**battery pack**).  
- **Common ground**: connect motor GND and Arduino GND together so signals have a reference.  
- **Decoupling capacitors**: add 100 µF (electrolytic) near driver supply input + small 0.1 µF ceramics to reduce noise.  
- **EMI noise**: motors produce electrical noise; keep sensor wires short and tidy; route motor power away from analog sensors.  
- **Thermal limits**: drivers get warm; ensure adequate current rating and heatsinking for your motor load.

---

## 1.6 Basic Examples

### Example 1 — One Direction + PWM Speed (transistor switch)
> For learning only (no reverse). Use a logic-level MOSFET if possible.

```cpp
// ONE MOTOR, ONE DIRECTION (PWM speed), using a MOSFET driver (low-side switch).
// Wiring (simplified):
//   Arduino D9 (PWM) -> gate (through ~100-220Ω resistor)
//   Motor + -> External +Vm (e.g., 6-9V)    Motor - -> MOSFET Drain
//   MOSFET Source -> GND (shared with Arduino GND)
//   Flyback diode across motor (stripe to +Vm)

const int MOTOR_PWM = 9;   // PWM-capable pin

void setup() {
  pinMode(MOTOR_PWM, OUTPUT);
}

void loop() {
  // speed ramp up
  for (int s = 0; s <= 255; s += 5) {
    analogWrite(MOTOR_PWM, s);
    delay(20);
  }
  // hold full speed
  delay(1000);
  // ramp down
  for (int s = 255; s >= 0; s -= 5) {
    analogWrite(MOTOR_PWM, s);
    delay(20);
  }
  // stop briefly
  delay(500);
}
```

### Example 2 — Full H-Bridge (L298N/L293D style): Speed + Direction
> Pins shown are typical; adjust to match your board.

```cpp
// FULL H-BRIDGE CONTROL (e.g., L298N, L293D)
// ENA -> PWM pin for speed control
// IN1, IN2 -> digital pins for direction

const int ENA = 9;   // PWM pin
const int IN1 = 7;   // Direction input 1
const int IN2 = 8;   // Direction input 2

void setup() {
  pinMode(ENA, OUTPUT);
  pinMode(IN1, OUTPUT);
  pinMode(IN2, OUTPUT);

  // Optional: start with motor stopped
  analogWrite(ENA, 0);
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, LOW);
}

void forward(int speedPWM) {
  digitalWrite(IN1, HIGH);
  digitalWrite(IN2, LOW);
  analogWrite(ENA, constrain(speedPWM, 0, 255));
}

void reverse(int speedPWM) {
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, HIGH);
  analogWrite(ENA, constrain(speedPWM, 0, 255));
}

void coast() { // motor freewheels
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, LOW);
  analogWrite(ENA, 0);
}

void brake() { // active brake (short both sides)
  digitalWrite(IN1, HIGH);
  digitalWrite(IN2, HIGH);
  analogWrite(ENA, 0);
}

void loop() {
  // Forward ramp
  for (int s = 0; s <= 255; s += 25) {
    forward(s);
    delay(300);
  }

  brake();   // quick stop
  delay(500);

  // Reverse medium speed
  reverse(150);
  delay(1500);

  coast();   // let it spin down
  delay(700);
}
```

**Notes:**
- Some L298N boards label **ENA/ENB** and provide a jumper.  
  - **Remove the jumper** if you want to use **PWM** from Arduino.  
- **+12V / +Vm** on the driver is **motor power**, **NOT** Arduino 5V.  
- Provide **+5V logic** to the driver (some L298N boards have a 5V regulator you can enable with a jumper; read your board’s silkscreen/manual).

---

## 1.7 Common Pitfalls (and fixes)

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

## 1.8 Planning for Navigation (Two Motors)

For differential (tank-style) drive with **two motors** (Left/Right):
- Use **two H-bridge channels** (e.g., L298N supports 2 motors).
- Control **speed** of each with PWM to go straight/turn.
- Example logic:
  - **Forward:** both motors forward, same PWM.
  - **Turn right:** left motor forward, right motor slower or reverse.
  - **Spin in place:** left forward, right reverse, same PWM.

> We’ll combine this with sensors (e.g., ultrasonic) in later sections to do **case testing** and **basic navigation**.

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

**Analogy:** A human driver constantly glances at the road (sense), decides “speed up / slow down / steer,” and moves pedals/wheel (act) — over and over.

---

## 2.2 Mission Goals Drive the Logic

Pick a **simple mission** first; keep the code short and clear.

- **Patrol forward** until something is **closer than 20 cm**, then **stop**.
- Add a **turn/avoid** behavior if blocked (e.g., pivot left for 500 ms, then try forward again).
- Scale **speed with a knob** (potentiometer) so you can demo slow/fast runs safely.

> Start with one mission. When that works, add another behavior (e.g., “if blocked twice, reverse and turn right”).

---

## 2.3 Light “Sensor Fusion” (Beginner Friendly)

You don’t need full robotics math to combine a couple signals:

- **Ultrasonic** → sets a **safety distance**.
- **Potentiometer** → sets a **target speed** (0–255 PWM).
- **Button** → acts as **Emergency Stop (E-Stop)** (held = stop everything).

Rule example:
- If **E-Stop pressed** → motors off.
- Else if **distance < stopThreshold** → stop.
- Else if **distance < slowThreshold** → cap speed at a smaller PWM.
- Else → allow knob-selected PWM.

---

## 2.4 Safety Interlocks (Protect & Recover)

- **E-Stop Button** (INPUT_PULLUP on pin 2, LOW = pressed): always honored first.
- **Watchdog distance**: if no valid distance for 500 ms → stop (sensor disconnected?).
- **Soft starts**: ramp PWM up/down to reduce brownouts and slipping.
- **Battery check (optional)**: if using an analog divider, slow/stop on low battery.

---

## 2.5 Minimal Two-Motor Helpers (L298N-style)

> Adjust pins to match your wiring.

```cpp
// ---- Motor driver pins (L298N / L293D style) ----
const int ENA = 5;   // Left motor PWM  (must be PWM-capable)
const int IN1 = 7;   // Left motor dir 1
const int IN2 = 8;   // Left motor dir 2

const int ENB = 6;   // Right motor PWM (must be PWM-capable)
const int IN3 = 9;   // Right motor dir 1
const int IN4 = 10;  // Right motor dir 2

// ---- Inputs ----
const int POT_PIN   = A0;  // Speed knob
const int ESTOP_PIN = 2;   // Emergency stop (button to GND, INPUT_PULLUP)

// ---- Ultrasonic (HC-SR04) ----
const int TRIG_PIN = 11;
const int ECHO_PIN = 12;

// ---- Thresholds (cm) ----
int stopThreshold = 15;
int slowThreshold = 30;

// ---- Setup ----
void setup() {
  pinMode(ENA, OUTPUT); pinMode(IN1, OUTPUT); pinMode(IN2, OUTPUT);
  pinMode(ENB, OUTPUT); pinMode(IN3, OUTPUT); pinMode(IN4, OUTPUT);

  pinMode(ESTOP_PIN, INPUT_PULLUP);
  pinMode(TRIG_PIN, OUTPUT);
  pinMode(ECHO_PIN, INPUT);

  Serial.begin(9600);
  // optional: ensure motors are stopped
  stopMotors();
}

// ---- Motor helpers ----
void leftMotor(int pwm, bool forward) {
  digitalWrite(IN1, forward ? HIGH : LOW);
  digitalWrite(IN2, forward ? LOW  : HIGH);
  analogWrite(ENA, constrain(pwm, 0, 255));
}

void rightMotor(int pwm, bool forward) {
  digitalWrite(IN3, forward ? HIGH : LOW);
  digitalWrite(IN4, forward ? LOW  : HIGH);
  analogWrite(ENB, constrain(pwm, 0, 255));
}

void stopMotors() {
  analogWrite(ENA, 0);
  analogWrite(ENB, 0);
  // Optionally “brake”:
  // digitalWrite(IN1, HIGH); digitalWrite(IN2, HIGH);
  // digitalWrite(IN3, HIGH); digitalWrite(IN4, HIGH);
}

// ---- Ultrasonic read (cm) with timeout ----
float readDistanceCm() {
  // trigger
  digitalWrite(TRIG_PIN, LOW); delayMicroseconds(2);
  digitalWrite(TRIG_PIN, HIGH); delayMicroseconds(10);
  digitalWrite(TRIG_PIN, LOW);
  // echo
  unsigned long dur = pulseIn(ECHO_PIN, HIGH, 25000UL); // ~4+ m
  if (dur == 0) return NAN;
  return (dur * 0.0343f) / 2.0f;
}

// ---- Main control loop (Sense → Decide → Act) ----
void loop() {
  // SENSE
  bool estop = (digitalRead(ESTOP_PIN) == LOW);
  int potRaw = analogRead(POT_PIN);                  // 0..1023
  int userPWM = map(potRaw, 0, 1023, 0, 255);        // target speed
  float d = readDistanceCm();                        // cm (NaN if no echo)

  // DECIDE
  int cmdPWM = userPWM;       // start from user request
  bool goFwd = true;          // forward by default
  bool move  = true;          // assume we can move

  if (estop) {
    move = false;
  } else if (isnan(d)) {
    // no reading → be safe
    move = false;
  } else if (d < stopThreshold) {
    move = false;
  } else if (d < slowThreshold) {
    cmdPWM = min(cmdPWM, 120); // cap speed when near obstacle
  }

  // ACT
  if (!move) {
    stopMotors();
  } else {
    leftMotor(cmdPWM, goFwd);
    rightMotor(cmdPWM, goFwd);
  }

  // DEBUG
  Serial.print("PWM="); Serial.print(cmdPWM);
  Serial.print("  d="); Serial.print(isnan(d) ? -1 : d, 1);
  Serial.print("  move="); Serial.println(move ? "Y" : "N");

  delay(50);
}
```

> **Tip:** To *turn*, drive the two sides differently:  
> - **Veer right:** left faster than right.  
> - **Pivot left:** left reverse, right forward (same PWM).  
> Add a simple “avoid” routine if `d < stopThreshold`: pivot for ~400 ms, then try forward again.

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

## Breakout Activities

## Wrap-Up 

## Friday Activites Preview!
