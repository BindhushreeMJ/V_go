# V_go
Pesticide sprinkler and automated drip irrigation 
//Sensors and motors code
#define BLYNK_PRINT Serial
#include <ESP8266WiFi.h>
#include <BlynkSimpleEsp8266.h>
#include <DHT.h>
char auth[] = "auth token"; //Enter your Blynk auth token
char ssid[] = "ssid"; //Enter your WIFI name
char pass[] = "pssd"; //Enter your WIFI passowrd
DHT dht(D7, 11);
#define IN5  D1
#define IN6 D2
#define IN7  D3
#define IN8 D4
#define DHTPIN D7
#define moist A0
#define DHTTYPE DHT11
int x = 50;
int y = 50;
int Speed;
int distance;
int trigPin = D5;   
int echoPin = D6;   

void setup() 
{
  Serial.begin(9600);
  pinMode(IN5, OUTPUT);
  pinMode(IN6, OUTPUT);
  pinMode(IN7, OUTPUT);
  pinMode(IN8, OUTPUT);
   pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
  dht.begin();
  Blynk.begin(auth, ssid, pass, "blynk.cloud", 80);
}
BLYNK_WRITE(0) {
  int switchState = param.asInt();
    if (switchState == 1)
  {
    digitalWrite(IN7, HIGH);
    digitalWrite(IN8, LOW);
  }
  else
  {
     digitalWrite(IN7, LOW);
    digitalWrite(IN8, LOW); 
  }
}

void loop() 
{
  
  temp();
  Moisture();
  level2();

  delay(200);
}

void temp()
{
  float t = dht.readTemperature();
  float h = dht.readHumidity();
  Serial.print("Temperature:");  
  Serial.print(t);
  Serial.print("C");
  Blynk.virtualWrite(V3, t);
  Serial.print("   Humidity:");  
  Serial.print(h);
  Serial.print("%"); 
  Blynk.virtualWrite(V4, h); 
  delay(200);
}
void Moisture()
{
  int value = analogRead(moist);
  Serial.print("   Moisture: ");
  Serial.println(value);
  Blynk.virtualWrite(V5, value);
  if (value > 600)
  {
    digitalWrite(IN5, HIGH);
    digitalWrite(IN6, LOW);
  }
  else
  {
     digitalWrite(IN5, LOW);
    digitalWrite(IN6, LOW); 
  }
}

void level2()
 {
  long duration, distance_cm;
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);
  duration = pulseIn(echoPin, HIGH);
  distance_cm = duration/16;
  Serial.print("Distance: ");
  Serial.print(distance_cm);
  Serial.println(" cm");
  if (distance_cm > 45) {
Blynk.virtualWrite(V7,HIGH);
} 
  else {
Blynk.virtualWrite(V7,LOW);  }
}
//end of code


//car bot movement code
#define BLYNK_PRINT Serial
#include <ESP8266WiFi.h>
#include <BlynkSimpleEsp8266.h>
char auth[] = "auth token"; //Enter your Blynk auth token
char ssid[] = "ssid"; //Enter your WIFI name
char pass[] = "pssd"; //Enter your WIFI passowrd
#define ENA D0
#define IN1 D1
#define IN2 D2
#define IN3 D3
#define IN4 D5
#define ENB D6
int x = 50;
int y = 50;
int Speed;
int distance;
int trigPin = D4;   
int echoPin = D7;   


void setup() 
{
  Serial.begin(9600);
  pinMode(ENA, OUTPUT);
  pinMode(IN1, OUTPUT);
  pinMode(IN2, OUTPUT);
  pinMode(IN3, OUTPUT);
  pinMode(IN4, OUTPUT);
  pinMode(ENB, OUTPUT);
   pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
  Blynk.begin(auth, ssid, pass, "blynk.cloud", 80);
}
BLYNK_WRITE(V0) 
{
  x = param[0].asInt();
}

BLYNK_WRITE(V1) 
{
  y = param[0].asInt();
}
BLYNK_WRITE(V2) 
{
  Speed = param.asInt();
}


void loop() 
{
  Blynk.run();// Run the blynk function
  direction();// Call the main function
  delay(200);
  level1();
}

void direction() 
{
  if (y > 70) 
  {
    Forward();
    Serial.println("Moving Forward");
  } 
  else if (y < 30) 
  {
    Backward();
    Serial.println("Moving Backward");
  } 
  else if (x < 30)
  {
    Left_turn();
    Serial.println("Turning Left");
  } 
  else if (x > 70) 
  {
    Right_turn();
    Serial.println("Turning Right");
  } 
  else if (x < 70 && x > 30 && y < 70 && y > 30) 
  {
    Stop();
    Serial.println("Stopped");
  }
}


/************** Funtions for Motor movement *****************/
void Forward() 
{
  analogWrite(ENA, Speed);
  analogWrite(ENB, Speed);
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, HIGH);
  digitalWrite(IN3, HIGH);
  digitalWrite(IN4, LOW);
}
void Backward() 
{
  analogWrite(ENA, Speed);
  analogWrite(ENB, Speed);
  digitalWrite(IN1, HIGH);
  digitalWrite(IN2, LOW);
  digitalWrite(IN3, LOW);
  digitalWrite(IN4, HIGH);
}
void Left_turn() 
{
  analogWrite(ENA, Speed);
  analogWrite(ENB, Speed);
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, HIGH);
  digitalWrite(IN3, LOW);
  digitalWrite(IN4, HIGH);
}
void Right_turn() 
{
  analogWrite(ENA, Speed);
  analogWrite(ENB, Speed);
  digitalWrite(IN1, HIGH);
  digitalWrite(IN2, LOW);
  digitalWrite(IN3, HIGH);
  digitalWrite(IN4, LOW);
}
void Stop() 
{
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, LOW);
  digitalWrite(IN3, LOW);
  digitalWrite(IN4, LOW);
}
void level1()
 {
  long duration, distance_cm;

  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  duration = pulseIn(echoPin, HIGH);

  distance_cm = duration/16;

  Serial.print("Distance: ");
  Serial.print(distance_cm);
  Serial.println(" cm");

  if (distance_cm > 45) {
Blynk.virtualWrite(V6,HIGH);
} 
  else {
Blynk.virtualWrite(V6,LOW);  }
}

//end of code
