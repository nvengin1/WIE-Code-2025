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
