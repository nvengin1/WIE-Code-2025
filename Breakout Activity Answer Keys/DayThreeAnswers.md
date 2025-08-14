## Servo Controlled by Potentiometer

<img width="1654" height="641" alt="image" src="https://github.com/user-attachments/assets/5344a0c0-8e41-4106-917e-db93788c1b95" />

```cpp
#include <Servo.h>

int potentiometerPin = A0; // Middle pin (wiper) of the potentiometer
int servoPin = 9;          // Servo control signal pin

Servo servo; // The servo motor object

void setup() {
  servo.attach(servoPin);
  Serial.begin(9600); // for seeing the values in Serial Monitor
}

void loop() {
  // 1) Read potentiometer position (0 to 1023)
  int potentiometerValue = analogRead(potentiometerPin);

  /* 
     2) Use the map() function to convert this range to a servo angle range (0 to 180 degrees)
     
     map(value, fromLow, fromHigh, toLow, toHigh)
     - value: the number to be converted
     - fromLow/fromHigh: the current range of the value
     - toLow/toHigh: the new range you want
     
     Example:
     potentiometerValue = 0   → map returns 0
     potentiometerValue = 1023 → map returns 180
     potentiometerValue = 512 → map returns about 90
     
     Internally, map() does:
       (value - fromLow) * (toHigh - toLow) / (fromHigh - fromLow) + toLow
  */
  int servoAngle = map(potentiometerValue, 0, 1023, 0, 180);

  // 3) Send angle to servo
  servo.write(servoAngle);

  // 4) Optional: Print values for debugging
  float voltage = (potentiometerValue / 1023.0) * 5.0;
  Serial.print("Potentiometer: "); Serial.print(potentiometerValue);
  Serial.print(" | Voltage: "); Serial.print(voltage, 2);
  Serial.print(" V | Servo Angle: "); Serial.println(servoAngle);

  delay(20); // Small delay for smooth movement
}

```

---

## Distance-Activated Servo "Gate"

<img width="1470" height="624" alt="image" src="https://github.com/user-attachments/assets/4bdb27f8-5e00-42f5-beef-1efd7dc4a2f9" />


```cpp
#include <Servo.h>

// Pin connections for Ultrasonic Sensor (HC-SR04)
int trigPin = 2;  // Trigger pin sends out sound pulse
int echoPin = 3;  // Echo pin receives the reflected pulse

// Servo pin
int servoPin = 9; // Signal pin for servo motor

Servo servo; // Create the servo object

void setup() {
  // Set up ultrasonic sensor pins
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);

  // Attach servo to its pin
  servo.attach(servoPin);

  // Start with the gate closed
  servo.write(0);

  // Start serial monitor (optional for debugging)
  Serial.begin(9600);
}

void loop() {
  // 1) Measure distance using ultrasonic sensor
  long duration, distanceCM;

  // Send a 10 microsecond HIGH pulse on the trigger pin
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  // Measure how long the echo pin stays HIGH (sound wave return time)
  duration = pulseIn(echoPin, HIGH);

  // Convert duration (microseconds) to distance in cm
  // Speed of sound ≈ 0.034 cm per microsecond, divide by 2 for round trip
  distanceCM = duration * 0.034 / 2;

  // Print distance for debugging
  Serial.print("Distance: ");
  Serial.print(distanceCM);
  Serial.println(" cm");

  // 2) Check if object is within 40 cm
  if (distanceCM > 0 && distanceCM < 100) {
    // Open the gate
    servo.write(90);

  } else {
    // Keep gate closed
    servo.write(0);
  }

  delay(100); // Small delay to avoid excessive readings
}

```

---
