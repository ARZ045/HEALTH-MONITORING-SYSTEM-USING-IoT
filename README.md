// include the library code:
#include <LiquidCrystal.h>
// initialize the library with the numbers of the interface pins
LiquidCrystal lcd(12, 11, 5, 4, 3, 2);

#include <SoftwareSerial.h>
SoftwareSerial BTserial(0, 1); // RX | TX

#include <OneWire.h> 
#include <DallasTemperature.h>
// Data wire is plugged into pin 2 on the Arduino 
#define ONE_WIRE_BUS 6
// Setup a oneWire instance to communicate with any OneWire devices  
// (not just Maxim/Dallas temperature ICs) 
OneWire oneWire(ONE_WIRE_BUS); 
// Pass our oneWire reference to Dallas Temperature. 
DallasTemperature sensors(&oneWire);

const int DOUTpin = 9;//the DOUT pin of the CO sensor goes into digital pin D8 of the arduino

int output_Pin = A0;  // initializing the sensor output pin
//initializng other variables
double alpha = 0.75;
double change = 0.0;
int Threshold = 300;// Determine which Signal to "count as a beat", and which to ingore. 

int sensorPin = A1;    // BP sensor - select the input pin for the potentiometer
int sensorValue;  // variable to store the value coming from the sensor
float mmHg;

int buzzer = 13;
String str, str1, str2, str3, str4;

void setup()
{
  // set up the LCD's number of columns and rows:
  lcd.begin(16, 2);
  lcd.setCursor(0,0);
  // Print a message to the LCD.
  lcd.print("Healtcare System");
  pinMode(buzzer, OUTPUT); 
  sensors.begin(); 
  Serial.begin(9600);
  BTserial.begin(9600);
}

void loop()
{
/*..........BP sensor..........*/
   // read the value from the sensor:
  sensorValue = analogRead(sensorPin);
  mmHg = 2.0 * (sensorValue - 680) + 20;
  str1 = String(mmHg);
  if((mmHg <= 89) || (mmHg >= 121))
  {
    Serial.print("BP Sensor Value:");
    Serial.print(mmHg);
    Serial.println("/70");
    digitalWrite(buzzer, HIGH);
    Serial.println("BLOOD PRESSURE ABNORMAL");
  }
  else{
    Serial.print("BP Sensor Value:");
    Serial.print(mmHg);
    Serial.println("/55");
    digitalWrite(buzzer, LOW);
  }
  //delay(500);
 

/*..........Pulserate sensor..........*/
static double oldValue = 0;
static double oldChange = 0;
int rawValue = analogRead (output_Pin); // holds the incoming raw data. Signal value can range from 0-1024
double value = alpha * oldValue + (1 - alpha) * rawValue; // calculating values using the formula
value = value*2 + 20;
Serial.print("Raw pulse sensor value: ");
Serial.println(rawValue); // printing the sensor output value on the screen
Serial.print("Normalised pulse sensor value: ");                                                                                          
Serial.println (value); // printing the heart beat value on the screen
str2 = String(value); //pulse value
if((value <= 50) || (value >= 132)){
  digitalWrite(buzzer, HIGH);
  Serial.println("PULSE RATE ABNORMAL");
}
else{
  digitalWrite(buzzer, LOW);
}
//delay(500);


/*..........Temperature sensor..........*/
// call sensors.requestTemperatures() to issue a global temperature 
 // request to all devices on the bus 
 sensors.requestTemperatures(); // Send the command to get temperature readings 
 Serial.print("Temperature: "); 
 Serial.println(sensors.getTempCByIndex(0)); // Why "byIndex"?  
   // You can have more than one DS18B20 on the same bus.  
   // 0 refers to the first IC on the wire 
 str3 = String(sensors.getTempCByIndex(0));
  if((sensors.getTempCByIndex(0)) >= 38.00){
  digitalWrite(buzzer, HIGH);
  Serial.println("HIGH BODY TEMPERATURE");
 }
 else{
  digitalWrite(buzzer, LOW);
 }
 //delay(500); 
/*..........MQ3 Carbon Mono-oxide sensor..........*/
  int value1= analogRead(A2);//reads the analaog value from the CO sensor's AOUT pin
  int limit= digitalRead(DOUTpin);//reads the digital value from the CO sensor's DOUT pin
  Serial.print("Smoke reading: ");
  Serial.println(value1);//prints the CO value
  if(limit == 1)
  {
    digitalWrite(buzzer, HIGH);
    Serial.println("Low Oxygen Level");
  }
  else{
    digitalWrite(buzzer, LOW);
  }


 str=String(",");
 str1 +=str;
 str1 +=str2;
 str1 +=str3;
 lcd.setCursor(0,1);
 lcd.print(str1);
 Serial.println(str1);
 //delay(100); 
//(every Value has to be seperated through a comma (',') and the message has to
//end with a semikolon (';'))
sensorValue = analogRead(sensorPin);
mmHg = 2.0 * (sensorValue - 680) + 210;
BTserial.print(sensorValue);
BTserial.print(",");
rawValue = analogRead (output_Pin); // holds the incoming raw data. Signal value can range from 0-1024
value = alpha * oldValue + (1 - alpha) * rawValue; // calculating values using the formula
BTserial.print(value);
BTserial.print(",");
BTserial.print(sensors.getTempCByIndex(0));
BTserial.print(",");
value1= analogRead(A2);
BTserial.print(value1);
BTserial.print(",");
Serial.println("                         ");
Serial.println("                         ");
delay(10000);
}
