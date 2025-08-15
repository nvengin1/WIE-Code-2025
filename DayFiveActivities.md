Welcome to **Day 5**! Today is all practice: wiring, testing, and writing your own code based on what you learned in Days 1–4. Each activity below gives you a **goal**, **what to build**, and **checks** to know you’re done. Keep your wiring tidy, label your pins in comments, and test often.

## Activity 1 — 3 LEDs (Practice with Outputs)
**Goal:** Wire 3 LEDs (each with a resistor) to three digital pins and write your own blink patterns.
- Create a simple **traffic light** sequence (Green → Yellow → Red) with different on-times.
- Add a short **custom pattern** (any “dance” you like).
- Print the current step to Serial Monitor (optional).

---

## Activity 2 — Servo (Practice with Motion)
**Goal:** Control a micro servo using the Servo library.
- Sweep **0° → 180° → 0°** 
- Print the angle as it moves (optional).

---

## Activity 3 — Ultrasonic (Practice with Sensing)
**Goal:** Read distance and show it in the Serial Monitor.
- Trigger a pulse; measure echo time with `pulseIn`.
- Convert to **cm** (out-and-back → divide by 2).
- Print: distance (cm).

---

## Activity 4 — Combo (Ultrasonic + Servo + LEDs)
**Goal:** Build a “smart gate.”
- If **distance < threshold** (e.g., 25 cm): **open** gate (servo), **Red LED ON**, **Green LED OFF**.
- Else: **close** gate, **Green LED ON**, **Red LED OFF**.
- Print the distance and state to the Serial Monitor.

---

## Bonus Ideas
- Reuse and adapt your **Tinkercad** code from earlier lessons for these real-hardware prompts.
- Try different thresholds, step sizes, and LED patterns; observe behavior live and iterate quickly.

---

# Driving the Robot

### Things to know

## 🔌 Pinout Table

<img width="440" height="183" alt="image" src="https://github.com/user-attachments/assets/3d528723-226c-42f8-8f2d-418e5282f7c2" />

| Channel | Controls          | Direction Pin | Speed Pin (PWM) |
| ------- | ----------------- | ------------- | --------------- |
| M1      | Left Wheels (x2)  | D4            | D5              |
| M2      | Right Wheels (x2) | D2            | D6              |


Although you have 4 motors total, they are **grouped in pairs** (left and right) — so you only need 2 control channels.


### Setup Motor Pins

Start by defining which pins control each motor side:

```cpp
#define M1_DIR 4  // Direction pin for Left motors
#define M1_SPD 5  // Speed (PWM) pin for Left motors

#define M2_DIR 2  // Direction pin for Right motors
#define M2_SPD 6  // Speed (PWM) pin for Right motors
```

Then, in `setup()`, configure them as OUTPUTs:

```cpp
void setup() {
  pinMode(M1_DIR, OUTPUT); pinMode(M1_SPD, OUTPUT);
  pinMode(M2_DIR, OUTPUT); pinMode(M2_SPD, OUTPUT);

}
```

### Drive Forward (Example)

This makes both sides spin forward for 2 seconds:

```cpp
void loop() {
  digitalWrite(M1_DIR, HIGH);   // try HIGH or LOW depending on wiring
  analogWrite(M1_SPD, 200);     // speed (0–255)

  digitalWrite(M2_DIR, HIGH);   // try HIGH or LOW
  analogWrite(M2_SPD, 200);

  delay(2000);                  // move for 2 seconds

  stopAll();                    // then stop
  delay(1000);
}

void stopAll() {
  analogWrite(M1_SPD, 0);
  analogWrite(M2_SPD, 0);
}
```

## Activities

### Activity 5 - Driving the Robot
Write the functions to make the robot drive in reverse, turn left, and turn right. Note that for mission-focused applications, ensure your turn right and turn left functions enable the robot to make 90-degree turns. It is up to you to find the correct timing (delay()) and speed (PWM) to make the most accurate 90 rotations


### Activity 6 — Servo + Ultrasonic Scan (Increase Field of View)

**Goal:**  Sweep the servo left↔right to “scan” the area, and print live distances to the Serial Monitor to confirm the sensor is working.

### Wiring (use these exact pins)
- **Servo (signal)** → **A3**     
- **Ultrasonic TRIG** → **D12**  
- **Ultrasonic ECHO** → **D13**

### Tasks
1. **Initialize hardware**
   - `Servo.attach(A3);`
   - Set `trigPin = 12`, `echoPin = 13`, `Serial.begin(9600);`
2. **Write a sweep loop**
   - Move the servo from **0° → 180° → 0°** in small steps (e.g., 5°).
   - Use a short `delay(10–25 ms)` between steps so the servo can keep up.
3. **Write Code to print the distance from the ultrasonic sensor in cm**
4. **Print to Serial Monitor**
   - Print lines like: "Angle: 60°, Distance: 42.7 cm".
   - Verify the numbers change as you present obstacles at different angles.


* Understand how an 8x16 LED Matrix works and what it can display.
* Learn how to wire and control the LED matrix using two digital pins (SCL and SDA).
* Use Arduino code to display basic images like a smiley face.
* Understand how hexadecimal arrays light up pixels on the matrix.
* Learn how to use a **modulus tool** to create your own patterns.
* Learn how to animate sequences like "start → forward → stop" with delays.

---

## What Is an LED Matrix?

<img width="933" height="305" alt="image" src="https://github.com/user-attachments/assets/b9b7e387-ab39-4260-b71d-16b530cffe42" />

An **LED matrix** is a grid of small LEDs arranged in rows and columns. The 8x16 matrix has:

* **8 rows** tall
* **16 columns** wide

So, 8 × 16 = **128 LEDs total**!

Each LED can be turned **ON** or **OFF** individually using special data called **binary** or **hexadecimal**.

### Real-World Examples

* Emoji faces 🤖
* Arrow indicators ↔️
* Animations or icons 🎮

---

## How It Works Internally

<img width="640" height="302" alt="image" src="https://github.com/user-attachments/assets/f8e45506-98b1-47c3-9206-3f943ab9a79e" />

The display uses a special chip called **AiP1640** to light up the pixels. The Arduino talks to this chip using just **2 pins**:

| Function | Pin |
| -------- | --- |
| Clock    | A5  |
| Data     | A4  |

This is similar to I2C communication but follows its own custom timing. The Arduino sends bytes to the AiP1640 chip, and each **bit** in those bytes turns ON or OFF an LED in a row.

### How Bits Light Up LEDs

Each LED row is controlled by **1 byte** = 8 bits.

Example:

```cpp
0b10101010 → ON, OFF, ON, OFF, ON, OFF, ON, OFF
```

Or using HEX:

```cpp
0xAA = 0b10101010
```

Each pattern is **16 bytes long**, one for each column!

---

## Step 1: Wire the LED Matrix (Already done for you)

<img width="540" height="223" alt="image" src="https://github.com/user-attachments/assets/a6856ece-154b-4342-96bf-9dff5195267a" />

| LED Matrix Pin | Connect To | Description  |
| -------------- | ---------- | ------------ |
| GND            | GND        | Ground       |
| VCC            | 5V         | Power Supply |
| SCL            | A5         | Clock Pin    |
| SDA            | A4         | Data Pin     |

📌 **Note:** These two pins are not I2C pins. This module just uses the same pins but with a different protocol.

---

## Step 2: Design Your Pattern

<img width="405" height="380" alt="image" src="https://github.com/user-attachments/assets/d51d864f-64ee-4f54-8e46-9f01385204cc" />

Go to [dotmatrixtool.com](http://dotmatrixtool.com/#)

### How to use the tool:

1. Set **width to 16** and **height to 8**
2. Select **Big Endian**
3. Draw your face or pattern
4. Click **Generate** → Get a list of **16 HEX numbers**

Example (Smiley Face):

```cpp
unsigned char smile[] = {
  0x00, 0x00, 0x1c, 0x02, 0x02, 0x02, 0x5c, 0x40,
  0x40, 0x5c, 0x02, 0x02, 0x02, 0x1c, 0x00, 0x00
};
```

Each HEX value controls one column of the matrix.

**When Generating design, only copy the HEX value line. For Example:**

<img width="332" height="234" alt="image" src="https://github.com/user-attachments/assets/925f42c8-8719-44f8-8032-eac4da00bc0b" />

The hex values: 

<img width="980" height="252" alt="image" src="https://github.com/user-attachments/assets/c9eb84b7-e8b0-4f53-87c6-2d24c5b8cdf5" />

```cpp
unsigned char smile[] = {
0x00, 0x00, 0x40, 0x20, 0x10, 0x08, 0x04, 0xfa, 0x04, 0x08, 0x10, 0x20, 0x40, 0x00, 0x00, 0x00
};
```


---

## Step 3: Full Template Example (Smiley Face)

<img width="482" height="441" alt="image" src="https://github.com/user-attachments/assets/f4120c0d-04a4-4f1b-99d8-a61cd79e144c" />


```cpp
unsigned char smile[] = {
  0x00, 0x00, 0x1c, 0x02, 0x02, 0x02, 0x5c, 0x40,
  0x40, 0x5c, 0x02, 0x02, 0x02, 0x1c, 0x00, 0x00
};

unsigned char clear[] = {
  0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,
  0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00
};

#define SCL_Pin  A5  // Clock pin
#define SDA_Pin  A4  // Data pin

void setup(){
  pinMode(SCL_Pin, OUTPUT);
  pinMode(SDA_Pin, OUTPUT);
  matrix_display(clear); // Clear matrix on startup
}

void loop(){
  matrix_display(smile);  // Show smile face
  delay(2000);            // Wait for 2 seconds
  matrix_display(clear);  // Clear the screen
  delay(2000);
}

void matrix_display(unsigned char matrix_value[]) {
  IIC_start();            // Start transmission
  IIC_send(0xc0);         // Set address to start from 0
  for(int i = 0; i < 16; i++) {
    IIC_send(matrix_value[i]);  // Send each byte
  }
  IIC_end();              // End transmission
  IIC_start();
  IIC_send(0x8A);         // Set brightness
  IIC_end();
}

void IIC_start() {
  digitalWrite(SCL_Pin,HIGH);
  delayMicroseconds(3);
  digitalWrite(SDA_Pin,HIGH);
  delayMicroseconds(3);
  digitalWrite(SDA_Pin,LOW);
  delayMicroseconds(3);
}

void IIC_send(unsigned char send_data) {
  for(char i = 0; i < 8; i++) {
    digitalWrite(SCL_Pin,LOW);
    delayMicroseconds(3);
    digitalWrite(SDA_Pin, send_data & 0x01);
    delayMicroseconds(3);
    digitalWrite(SCL_Pin,HIGH);
    delayMicroseconds(3);
    send_data >>= 1;
  }
}

void IIC_end() {
  digitalWrite(SCL_Pin,LOW);
  delayMicroseconds(3);
  digitalWrite(SDA_Pin,LOW);
  delayMicroseconds(3);
  digitalWrite(SCL_Pin,HIGH);
  delayMicroseconds(3);
  digitalWrite(SDA_Pin,HIGH);
  delayMicroseconds(3);
}
```

Upload this and your LED Matrix should show a smile!

---

## Extension Challenge — Animated LED Patterns

Let’s take it to the next level! What if your robot could *talk* with animations?

### Goal:

Display multiple patterns **in sequence**, such as:

>  Start →  Move Forward →  Stop

Here is the continued and refined section right after your Extension Challenge — perfect for the end of Lesson 4 on your GitHub Page. This teaches students how to build their own LED animations step-by-step, and prepares them for advanced experimentation.

You can copy this markdown and paste it directly into your `.md` lesson file on GitHub:

---

## Final Challenge — Custom LED Matrix Animation 

You've learned how to show a smiley or an arrow, but now it’s your turn to be the **creator**!

### Example Layout:

```cpp
unsigned char frame1[] = { /* First pattern HEX */ };
unsigned char frame2[] = { /* Second pattern HEX */ };
unsigned char frame3[] = { /* Third pattern HEX */ };
unsigned char clear[]  = {0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,
                          0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00};
```

### Animation Loop:

```cpp
void loop() {
  matrix_display(frame1);
  delay(1000);           // Show first frame for 1 second

  matrix_display(frame2);
  delay(1000);           // Show second frame

  matrix_display(frame3);
  delay(1000);           // Show third frame

  matrix_display(clear); // Clear screen between loops
  delay(500);
}
```

---

## Final Activity

###  Objectives

* Combine **all previous skills**: motors, ultrasonic sensor, servo, and LED matrix.
* Build a robot that **drives forward** and **reacts** when it detects an obstacle.
* Display **animated warnings** using the LED matrix.
* Use `if` statements, `for` loops, sensor readings, and modular code effectively.
* Navigate the arena successfully
