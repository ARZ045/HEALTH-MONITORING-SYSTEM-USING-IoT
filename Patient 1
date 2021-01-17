// include the library code:
#include <LiquidCrystal.h>
// initialize the library with the numbers of the interface pins
LiquidCrystal lcd(12, 11, 5, 4, 3, 2);

#include <OneWire.h> 
#include <DallasTemperature.h>
// Data wire is plugged into pin 2 on the Arduino 
#define ONE_WIRE_BUS 6
// Setup a oneWire instance to communicate with any OneWire devices  
// (not just Maxim/Dallas temperature ICs) 
OneWire oneWire(ONE_WIRE_BUS); 
// Pass our oneWire reference to Dallas Temperature. 
DallasTemperature sensors(&oneWire);

int output_Pin = A0;  // initializing the sensor output pin
//initializng other variables
double alpha = 0.75;
double change = 0.0;
int Threshold = 300;// Determine which Signal to "count as a beat", and which to ingore. 

int sensor_pin = 10; //digital pin
int relay = 9;

int sensoroutput; // the analog pin to piezo electric sensor
int ledoutput = 8; // pin connected to LED
int THRESHOLD = 75;

int buzzer = 13;
String str, str1, str2, str3;

void setup()
{
  // set up the LCD's number of columns and rows:
  lcd.begin(16, 2);
  lcd.setCursor(0,0);
  // Print a message to the LCD.
  lcd.print("Healtcare System");
  pinMode(relay, OUTPUT);
  pinMode(ledoutput, OUTPUT); 
  pinMode(buzzer, OUTPUT); 
  sensors.begin(); 
  Serial.begin(9600);
}

void loop()
{
/*..........Moisture sensor(Glucose level)..........*/
  if(digitalRead(sensor_pin) == HIGH)
  {
   Serial.println("NO GLUCOSE");
   lcd.setCursor(0,1);
   lcd.print("No GLUCOSE    ");
   digitalWrite(buzzer, HIGH);
   Message2();
   digitalWrite(relay, HIGH);
   delay(500);
  }
  else if(digitalRead(sensor_pin) == LOW)
  {
   lcd.setCursor(0,1);
   lcd.print("          ");
   digitalWrite(buzzer, LOW);
   digitalWrite(relay, LOW);
   delay(500);
  }
  delay(500);


/*..........Pulserate sensor..........*/
static double oldValue = 0;
static double oldChange = 0;
int rawValue = analogRead (output_Pin); // holds the incoming raw data. Signal value can range from 0-1024
double value = alpha * oldValue + (1 - alpha) * rawValue; // calculating values using the formula
value = value - 40;
Serial.print("Raw sensor value: ");
Serial.println(rawValue); // printing the sensor output value on the screen
Serial.print("Normalised sensor value: ");                                                                                          
Serial.println (value); // printing the heart beat value on the screen
str1 = String(value); //pulse value
if((value <= 50) || (value >= 132)){
  digitalWrite(buzzer, HIGH);
  Message3();
}
else{
  digitalWrite(buzzer, LOW);
}
delay(500);


/*..........Temperature sensor..........*/
// call sensors.requestTemperatures() to issue a global temperature 
 // request to all devices on the bus 
 sensors.requestTemperatures(); // Send the command to get temperature readings 
 Serial.print("Temperature: "); 
 Serial.println(sensors.getTempCByIndex(0)); // Why "byIndex"?  
   // You can have more than one DS18B20 on the same bus.  
   // 0 refers to the first IC on the wire 
 str2 = String(sensors.getTempCByIndex(0));
 if((sensors.getTempCByIndex(0)) >= 38.00){
  digitalWrite(buzzer, HIGH);
  Message4();
 }
 else{
  digitalWrite(buzzer, LOW);
 }
 delay(500); 


//Piezo Electric Sensor - Pressure Measurement for monitoring Diabetes patient
sensoroutput = analogRead(A1);  // function to read analog voltage from sensor
Serial.print("Piezo electric sensor: ");
Serial.println(sensoroutput);
str3 = String(sensoroutput); //piezo electric sensor
if (sensoroutput >= THRESHOLD)                    // function to check voltage level from sensor
{
digitalWrite(ledoutput, HIGH);
Serial.println("Pressure applied");
delay(100); // to make the LED visible
}
else{
digitalWrite(ledoutput, LOW);
Serial.println("No pressure applied");
} 
delay(500);

 str=String(",");
 str1 +=str;
 str1 +=str2;
 Serial.println(str1);
 lcd.setCursor(0,1);
 lcd.print(str1);
 delay(100);
 Message1();
 
 Serial.println("                         ");
 Serial.println("                         ");
delay(10000);
}

void Message1(void)
{
  Serial.println("AT\r\n");delay(800);
  Serial.println("AT+CMGF=1\r\n");delay(800);
  Serial.print("AT+CMGS=");delay(500);
  Serial.write('"');delay(500);
  Serial.print("8056007170");delay(500);     //Change number here     
  Serial.write('"');
  Serial.print("\r\n");delay(500);
  Serial.print(str1);delay(500);//17
  Serial.write(26);
  delay(500);
}

void Message2(void)
{
  Serial.println("AT\r\n");delay(800);
  Serial.println("AT+CMGF=1\r\n");delay(800);
  Serial.print("AT+CMGS=");delay(500);
  Serial.write('"');delay(500);
  Serial.print("8056007170");delay(500);     //Change number here
  Serial.write('"');
  Serial.print("\r\n");delay(500);
  Serial.print("NO GLUCOSE");delay(500);//17
  Serial.write(26);
  delay(500);
}

void Message3(void)
{
  Serial.println("AT\r\n");delay(800);
  Serial.println("AT+CMGF=1\r\n");delay(800);
  Serial.print("AT+CMGS=");delay(500);
  Serial.write('"');delay(500);
  Serial.print("8056007170");delay(500);     //Change number here
  Serial.write('"');
  Serial.print("\r\n");delay(500);
  Serial.print("PULSE RATE ABNORMAL");delay(500);//17
  Serial.write(26);
  delay(500);
}

void Message4(void)
{
  Serial.println("AT\r\n");delay(800);
  Serial.println("AT+CMGF=1\r\n");delay(800);
  Serial.print("AT+CMGS=");delay(500);
  Serial.write('"');delay(500);
  Serial.print("8056007170");delay(500);     //Change number here
  Serial.write('"');
  Serial.print("\r\n");delay(500);
  Serial.print("HIGH BODY TEMPERATURE");delay(500);//17
  Serial.write(26);
  delay(500);
}
