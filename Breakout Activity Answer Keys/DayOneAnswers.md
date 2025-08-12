### Completed Mini Breakout One

```cpp
// C++ code
//
void setup()
{
  Serial.begin(9600); // open the serial port at 9600 bps:
  
  bool isAllowedIn;


/*AND Boolean Example
Bouncer checks the following*/

bool onGuestList = 0; //guest list condition
bool shoes = 1; // proper shoes condition
isAllowedIn = onGuestList&&shoes; /*assign a new value to isAllowedIn using AND boolean operation*/ 

//check the value of isAllowedIn
Serial.print("\nIs this person allowed in?\t");
Serial.print(isAllowedIn);
 
  
delay(1000);
//OR Boolean Example

int hasPaid = 20; //paying at the door condition
bool money = hasPaid >= 30;
isAllowedIn = onGuestList || money;/*assign a new value to isAllowedIn using OR boolean operation*/ 

//check the value of isAllowedIn
Serial.print("\nIs this person allowed in?\t");
Serial.print(isAllowedIn);
  
//NOT Boolean Example

bool backpack = 1;//backpack condition
isAllowedIn = !backpack; /*assign a new value to isAllowedIn using NOT boolean operation*/ 

//check the value of isAllowedIn
Serial.print("\nIs this person allowed in?\t");
Serial.print(isAllowedIn);
  
}

void loop()
{

}
```
---

### Completed Mini Breakout Two

```cpp
void setup()
{
  Serial.begin(9600); // open the serial port at 9600 bps:
  
  //Mini Breakout Two
  //Practice using different variable data types by generating
  //information about someone trying to enter the event
  
  //First name -- string
  String first = "John";
  Serial.print("\n");
  Serial.print(first);
  
  //Last name -- string
  String last = "Doe";
  Serial.print("\n");
  Serial.print(last);
	
  //Full name -- string (try to generate this using the previous two strings
  String FullName = first + " " + last;
  Serial.print("\n");
  Serial.print(FullName);
  
  //Proper shoes -- bool
  bool haveShoes = 1;
  Serial.print("\n\n");
  Serial.print(shoes);
  
  //Amount paid -- float or int (depending on quantity)
  int dollarPayment = 5;
  Serial.print("\n\n");
  Serial.print(dollarPayment);
  
  float centsPayment = .75;
  Serial.print("\n");
  Serial.print(centsPayment);
  
}

void loop()
{

}

```
---

