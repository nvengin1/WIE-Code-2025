### Mini Breakout Activity One

```cpp

// Pin 13 has an LED connected on most Arduino boards.
// give it a name:
int led = 13;
int pushButton = 2;

// the setup routine runs once when you press reset:
void setup() {

  Serial.begin(9600);
  pinMode(pushButton, INPUT);
    pinMode(led, OUTPUT);

}

// the loop routine runs over and over again forever:
void loop() {
  
  int buttonState = digitalRead(pushButton);
  Serial.println(buttonState);
  if (buttonState == 1) {
      digitalWrite(led, HIGH);   // turn the LED on (HIGH is the voltage level)
  } else {
    digitalWrite(led, LOW);    // turn the LED off by making the voltage LOW
  }
  delay(1);        // delay in between reads for stability
  
  

  
}
```

---

## Activity 1 - Simple Button Control

Steps
Wire an LED to D9 with a resistor to GND.

Wire button: D2 ↔ GND across the breadboard gap.

Upload code.

```cpp
const int buttonPin = 2;
const int ledPin    = 9;

void setup() {
  pinMode(buttonPin, INPUT_PULLUP); // default HIGH, pressed LOW
  pinMode(ledPin, OUTPUT);
}

void loop() {
  int pressed = (digitalRead(buttonPin) == LOW);
  digitalWrite(ledPin, pressed ? HIGH : LOW);
}
```

### How it works
Internal pull-up keeps the pin HIGH until the button ties it to GND (LOW).

We map LOW → ON, HIGH → OFF.

If you want INPUT instead: add a 10 kΩ pull-down from D2→GND and wire the button from D2→5V. Then use pinMode(buttonPin, INPUT); and change logic to pressed = (digitalRead(buttonPin) == HIGH);.


---

## Activity 2 - If-Else Color Control


Steps
LEDs: red → D9, green → D10.

Same button wiring as Activity 1.

Upload code.

```cpp
const int buttonPin = 2;
const int redPin    = 9;
const int greenPin  = 10;

void setup() {
  pinMode(buttonPin, INPUT_PULLUP);
  pinMode(redPin, OUTPUT);
  pinMode(greenPin, OUTPUT);
}

void loop() {
  bool pressed = (digitalRead(buttonPin) == LOW);

  if (pressed) {
    digitalWrite(redPin, HIGH);
    digitalWrite(greenPin, LOW);
  } else {
    digitalWrite(redPin, LOW);
    digitalWrite(greenPin, HIGH);
  }
}
```

---

### Activity 3 - If-Else-If Pattern (count button presses)

Key note: use edge detection (change from released - > pressed) + counter

 Steps
LEDs: red D9, yellow D11, green D10.

Button as before.

Upload code.

```cpp
const int buttonPin = 2;
const int redPin = 9, yellowPin = 11, greenPin = 10;

int mode = 1;                 // 1=red, 2=yellow, 3=green
int lastState = HIGH;         // last read of the button (INPUT_PULLUP)
unsigned long lastChange = 0;
const unsigned long debounceMs = 25;

void showMode(int m){
  digitalWrite(redPin,    m==1 ? HIGH : LOW);
  digitalWrite(yellowPin, m==2 ? HIGH : LOW);
  digitalWrite(greenPin,  m==3 ? HIGH : LOW);
}

void setup(){
  pinMode(buttonPin, INPUT_PULLUP);
  pinMode(redPin, OUTPUT);
  pinMode(yellowPin, OUTPUT);
  pinMode(greenPin, OUTPUT);
  showMode(mode);
}

void loop(){
  int reading = digitalRead(buttonPin);
  unsigned long now = millis();

  // detect stable change
  if (reading != lastState && (now - lastChange) > debounceMs) {
    lastChange = now;
    lastState = reading;

    // falling edge: HIGH->LOW means a press
    if (reading == LOW) {
      mode++;
      if (mode > 3) mode = 1;
      showMode(mode);
    }
  }
}
```
---

## Activity 4 - For Loop LED Chase


Goal
Create a running light over 4 LEDs, then reverse.

LEDs on D5, D6, D9, D10.

Upload code.

```cpp
const int leds[] = {5, 6, 9, 10};
const int N = sizeof(leds)/sizeof(leds[0]);

void setup(){
  for (int i=0;i<N;i++) pinMode(leds[i], OUTPUT);
}

void loop(){
  // forward
  for (int i=0;i<N;i++){
    digitalWrite(leds[i], HIGH); delay(120);
    digitalWrite(leds[i], LOW);
  }
  // reverse (skip the ends to avoid double-flash there)
  for (int i=N-2;i>=1;i--){
    digitalWrite(leds[i], HIGH); delay(120);
    digitalWrite(leds[i], LOW);
  }
}
```
---

## Activity 5 - While Loop Hold

Goal
Create a running light over 4 LEDs, then reverse.

Steps
LED on D9.

Button as before.

Upload code.

```cpp
const int buttonPin = 2;
const int ledPin    = 9;

void setup(){
  pinMode(buttonPin, INPUT_PULLUP);
  pinMode(ledPin, OUTPUT);
}

void loop(){
  if (digitalRead(buttonPin) == LOW) {   // pressed
    while (digitalRead(buttonPin) == LOW) {
      digitalWrite(ledPin, HIGH); delay(80);
      digitalWrite(ledPin, LOW);  delay(80);
      // loop condition re-checks the button so we don't get stuck
    }
    digitalWrite(ledPin, LOW); // ensure off on exit
  }
}
```

--- 

## Activity 6 - Morse Code "SOS" with Button Start/Stop

Goal
Use built-in LED (D13) to flash SOS (... --- ...), running only while button is held.

Timing
Dot = 200 ms ON, 200 ms OFF
Dash = 600 ms ON, 200 ms OFF
Letter gap = 600 ms (after S or O)


```cpp
const int buttonPin = 2;
const int LED = 13;

const int unit = 200; // ms

void blinkUnit(int onMs){
  digitalWrite(LED, HIGH); delay(onMs);
  digitalWrite(LED, LOW);  delay(unit);
}

bool stillPressed(){ return digitalRead(buttonPin) == LOW; }

void letter_S(){
  for(int i=0;i<3 && stillPressed();i++) blinkUnit(unit);   // dot dot dot
  if (stillPressed()) delay(3*unit); // letter gap
}

void letter_O(){
  for(int i=0;i<3 && stillPressed();i++) blinkUnit(3*unit); // dash dash dash
  if (stillPressed()) delay(3*unit); // letter gap
}

void setup(){
  pinMode(buttonPin, INPUT_PULLUP);
  pinMode(LED, OUTPUT);
}

void loop(){
  if (stillPressed()){
    letter_S();
    if (!stillPressed()) return;
    letter_O();
    if (!stillPressed()) return;
    letter_S();
  }
}
```

How it works:

We check the button between each dot/dash/letter so releasing stops immediately.


---

## Activity 7 - Pushbutton Memory (Simple "match-on-time")

Goal
Arduino lights a short sequence (2–3 steps). During each lit step, you must press once to “match.” If you match all steps → success blink.

Steps
Three LEDs: D9 (red), D11 (yellow), D10 (green).
Button as before.
Upload code.

```cpp
const int buttonPin = 2;
const int leds[] = {9, 11, 10};   // red, yellow, green
const int N = sizeof(leds)/sizeof(leds[0]);
const int seq[] = {0, 1, 2};      // light order by index
const int L = sizeof(seq)/sizeof(seq[0]);

const int onMs = 600, gapMs = 300;
const unsigned long debounceMs = 25;

int lastState = HIGH;
unsigned long lastChange = 0;

bool waitForSinglePressDuring(int ms){
  // window where the LED is lit; we want exactly one press
  bool gotPress = false;
  unsigned long start = millis();
  while (millis() - start < (unsigned long)ms){
    int r = digitalRead(buttonPin);
    unsigned long now = millis();
    if (r != lastState && (now - lastChange) > debounceMs){
      lastChange = now;
      lastState  = r;
      if (r == LOW){            // falling edge = press
        if (gotPress) return false; // more than one press = fail
        gotPress = true;
      }
    }
  }
  // also require they release before leaving this step
  while (digitalRead(buttonPin) == LOW) { /* wait release */ }
  return gotPress; // exactly one press during window
}

void allOff(){
  for (int i=0;i<N;i++) digitalWrite(leds[i], LOW);
}

void successBlink(){
  for(int k=0;k<3;k++){
    for(int i=0;i<N;i++) digitalWrite(leds[i], HIGH);
    delay(200);
    allOff();
    delay(150);
  }
}

void setup(){
  pinMode(buttonPin, INPUT_PULLUP);
  for (int i=0;i<N;i++) pinMode(leds[i], OUTPUT);
}

void loop(){
  bool ok = true;
  for (int step=0; step<L; step++){
    int pin = leds[seq[step]];
    digitalWrite(pin, HIGH);
    if (!waitForSinglePressDuring(onMs)) ok = false;
    digitalWrite(pin, LOW);
    delay(gapMs);
    if (!ok) break;
  }

  if (ok) successBlink();
  else { // simple fail cue: flash the current LED quickly
    for (int i=0;i<4;i++){ for(int j=0;j<N;j++) digitalWrite(leds[j], HIGH); delay(80);
                           allOff(); delay(80); }
  }

  delay(600); // short pause before next round
}
```

How it works:

Each step lights one LED for onMs.

The player must press once while it’s lit. Multiple presses or no press = fail.

After all steps matched → success animation.

Keep it simple: this version doesn’t grow the sequence; it just checks timing.

---


## Activity 8 - Button + LED Dimming (PWM)

Goal
Each press increases brightness by 50 (0–255). If >255, wrap to 0.

Steps
LED on D9 (PWM).

Button as before.

Upload code.

```cpp
const int buttonPin = 2;
const int pwmPin    = 9;

int brightness = 0;                 // 0..255
int lastState  = HIGH;
unsigned long lastChange = 0;
const unsigned long debounceMs = 25;

void apply(){
  analogWrite(pwmPin, brightness);
}

void setup(){
  pinMode(buttonPin, INPUT_PULLUP);
  pinMode(pwmPin, OUTPUT);
  apply();
}

void loop(){
  int r = digitalRead(buttonPin);
  unsigned long now = millis();

  if (r != lastState && (now - lastChange) > debounceMs){
    lastChange = now;
    lastState  = r;

    if (r == LOW){ // falling edge = pressed
      brightness += 50;            // step size
      if (brightness > 255) brightness = 0;
      apply();
    }
  }
}
```

Notes:

PWM range is 0–255. A step of 50 gives levels 0, 50, 100, 150, 200, 250, then wrap to 0.

Use actual PWM pins (3, 5, 6, 9, 10, 11 on Uno).

---

## Common Pitfalls (All Activities)
Button not across the breadboard gap → it’s always connected and never changes.

Forgot the LED resistor → LED stays dark or burns out.

Using INPUT without an external resistor → input floats (random). Prefer INPUT_PULLUP.

Counting presses without edge detection → one press becomes many counts (bounce). Use the patterns above.


