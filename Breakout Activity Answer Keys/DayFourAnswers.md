
Using H Bridge. 

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

int forwardSpeed = 200;  // how fast to drive straight
int turnSpeed    = 180;  // how fast to turn/pivot

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
  forwardMotors(forwardSpeed);
  delay(6000);

  turnRight();               // pivot in place to the RIGHT
  delay(6000);

  reverseMotors(forwardSpeed);
  delay(6000);

  turnLeft();                // pivot in place to the LEFT
  delay(6000);

  // Back up in a gentle arc to the RIGHT, then to the LEFT
  reverseRight();
  delay(6000);

  reverseLeft();
  delay(6000);

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

// ---------------------------------------------------------
// turnRight()
// PIVOT RIGHT IN PLACE:
//   Left motor forward, Right motor reverse.
//   Both enabled with turnSpeed so the robot spins around its center.
// ---------------------------------------------------------
void turnRight() {
  // Left motor (A) stop
  digitalWrite(in1Pin, LOW);
  digitalWrite(in2Pin, LOW);
  // Right motor (B) forward
  digitalWrite(in3Pin, HIGH);
  digitalWrite(in4Pin, LOW);

  analogWrite(enableA, 0);
  analogWrite(enableB, turnSpeed);
}

// ---------------------------------------------------------
// turnLeft()
// PIVOT LEFT IN PLACE:
//   Left motor reverse, Right motor forward.
// ---------------------------------------------------------
void turnLeft() {
  // Left motor (A) forward
  digitalWrite(in1Pin, HIGH);
  digitalWrite(in2Pin, LOW);
  // Right motor (B) stop
  digitalWrite(in3Pin, LOW);
  digitalWrite(in4Pin, LOW);

  analogWrite(enableA, turnSpeed);
  analogWrite(enableB, 0);
}

// ---------------------------------------------------------
// reverseRight()
// BACK UP IN A GENTLE RIGHT ARC:
//   Left motor reverse, Right motor STOP (coast).
//   This is not a pivot; it’s a soft steer while reversing.
//   (If you want a pivot while reversing, call turnLeft().)
// ---------------------------------------------------------
void reverseRight() {
  // Left motor (A) reverse
  digitalWrite(in1Pin, LOW);
  digitalWrite(in2Pin, HIGH);
  analogWrite(enableA, turnSpeed);

  // Right motor (B) stop/coast
  analogWrite(enableB, 0);      // ENB=0 → channel off (coast)
  digitalWrite(in3Pin, LOW);
  digitalWrite(in4Pin, LOW);
}

// ---------------------------------------------------------
// reverseLeft()
// BACK UP IN A GENTLE LEFT ARC:
//   Right motor reverse, Left motor STOP (coast).
// ---------------------------------------------------------
void reverseLeft() {
  // Left motor (A) stop/coast
  analogWrite(enableA, 0);      // ENA=0 → channel off (coast)
  digitalWrite(in1Pin, LOW);
  digitalWrite(in2Pin, LOW);

  // Right motor (B) reverse
  digitalWrite(in3Pin, LOW);
  digitalWrite(in4Pin, HIGH);
  analogWrite(enableB, turnSpeed);
}

```
