# Hand-Gesture_Recognition-using-Flex-Sensor
Speech-impaired people can only communicate through sign language. Normal people will find it  challenging because they can't understand sign language. To solve the issue, this project has put  into operation a model that will contribute to closing the communication gap between the mute  and the rest of society. 


#include <Wire.h>

#include<LiquidCrystal_I2C.h>

// Define the pins for the APR33A3 module
#define M1 22

#define M2 23

#define M3 24

#define M4 25

#define M5 26

#define M6 27

#define M7 28

#define M8 29

// Initialize LCD

LiquidCrystal_I2C lcd(0x27, 16, 2);



// Define Flex Sensor Pins

const int flexPin1 = A1;

const int flexPin2 = A2;

const int flexPin3 = A3;

const int flexPin4 = A4;

const int flexPin5 = A5;



// Define Gyro Sensor Pins

const int xInput = A6;

const int yInput = A7;

const int zInput = A8;



// Constants and Variables

int RawMin = 0; 

int RawMax = 1023;

const int sampleSize = 10;

const float VCC = 5;

const float R_DIV = 4700.0;

const float flatResistance = 25000.0;

const float bendResistance = 100000.0;



void setup() {

  Serial.begin(9600);
  
  Wire.begin();
  
  lcd.init(); // initialize the lcd
  
  lcd.backlight();
  
  pinMode(flexPin1, INPUT);
  
  pinMode(flexPin2, INPUT);
  
  pinMode(flexPin3, INPUT);
  
  pinMode(flexPin4, INPUT);
  
  pinMode(flexPin5, INPUT);



  // Initialize the pins as output for APR33A3 module
  
  pinMode(M1, OUTPUT);
  
  pinMode(M2, OUTPUT);
  
  pinMode(M3, OUTPUT);
  
  pinMode(M4, OUTPUT);
  
  pinMode(M5, OUTPUT);
  
  pinMode(M6, OUTPUT);
  
  pinMode(M7, OUTPUT);
  
  pinMode(M8, OUTPUT);



  // Set the pins to HIGH (inactive state) for APR33A3 module
  
  digitalWrite(M1, HIGH);
  
  digitalWrite(M2, HIGH);
  
  digitalWrite(M3, HIGH);
  
  digitalWrite(M4, HIGH);
  
  digitalWrite(M5, HIGH);
  
  
  digitalWrite(M6, HIGH);
  
  digitalWrite(M7, HIGH);
  
  digitalWrite(M8, HIGH);
}




void loop() {

  int xRaw = ReadAxis(xInput);
  
  int yRaw = ReadAxis(yInput);
  
  int zRaw = ReadAxis(zInput);



  long xScaled = map(xRaw, RawMin, RawMax, -3000, 3000);
  
  long yScaled = map(yRaw, RawMin, RawMax, -3000, 3000);
  
  long zScaled = map(zRaw, RawMin, RawMax, -3000, 3000);



  Serial.println("X, Y, Z :: ");
  
  Serial.print(xRaw);
  
  
  Serial.print(", ");
  
  Serial.print(yRaw);
  
  Serial.print(", ");
  
  Serial.println(zRaw);
  
  Serial.println(" :: ");



  int ADCflex1 = analogRead(flexPin1);
  
  int ADCflex2 = analogRead(flexPin2);
  
  int ADCflex3 = analogRead(flexPin3);
  
  int ADCflex4 = analogRead(flexPin4);
  
  int ADCflex5 = analogRead(flexPin5);



  float Vflex1 = ADCflex1 * VCC / 1023.0;
  
  float Vflex2 = ADCflex2 * VCC / 1023.0;
  
  float Vflex3 = ADCflex3 * VCC / 1023.0;
  
  float Vflex4 = ADCflex4 * VCC / 1023.0;
  
  float Vflex5 = ADCflex5 * VCC / 1023.0;



  float Rflex1 = R_DIV * (VCC / Vflex1 - 1.0);
  
  float Rflex2 = R_DIV * (VCC / Vflex2 - 1.0);
  
  float Rflex3 = R_DIV * (VCC / Vflex3 - 1.0);
  
  float Rflex4 = R_DIV * (VCC / Vflex4 - 1.0);
  
  float Rflex5 = R_DIV * (VCC / Vflex5 - 1.0);



  if ((xRaw > 310) and (yRaw > 320) and (zRaw > 260)) {
  
    if ((Rflex1 < 33000) && (Rflex2 < 35000) && (Rflex3 < 30000) && (Rflex4 < 29000) && (Rflex5 < 29000)) {
    
      Serial.println("flex is straight");
      
      lcd.clear();
      
      lcd.setCursor(0, 0);
      
      lcd.print("flex is straight");
      
      playMessage(M6);
    } 
    else if ((Rflex1 > 39000)) {
    
      Serial.println("I am hungry");
      
      lcd.clear();
      
      lcd.setCursor(0, 0);
      
      lcd.print("I am hungry");
      
      playMessage(M1);
    } 
    
    else if ((Rflex2 > 52000)) {
    
      Serial.println("Give me a glass of water");
      
      lcd.clear();
      
      lcd.setCursor(0, 0);
      
      lcd.print("Give me a glass of water");
      
      playMessage(M2);
    } 
    else if ((Rflex3 > 44000)) {
    
      Serial.println("Hi, How are you");
      
      lcd.clear();
      
      lcd.setCursor(0, 0);
      
      lcd.print("Hi, How are you");
      
      playMessage(M3);
    } 
    else if ((Rflex4 > 40000)) {
    
      Serial.println("Nice to meet you");
      
      lcd.clear();
      
      lcd.setCursor(0, 0);
      
      lcd.print("Nice to meet you");
      
      playMessage(M4);
    } 
    //else if ((Rflex5 > 33000)) {
    
      //Serial.println("Good night");
      
      //lcd.clear();
      
      //lcd.setCursor(0, 0);
      
      //lcd.print("Good night");
      
      //playMessage(M5);
    //
    }
  } 
  else {
  
    Serial.println("Not in alignment");
    
    playMessage(M7);
  }
  



  delay(2000);
}




void playMessage(int pin) {

  digitalWrite(pin, LOW);   // Start playing the message
  
  delay(1000);              // Wait for the message to finish
  
  digitalWrite(pin, HIGH);  // Stop playing the message
}




int ReadAxis(int axisPin) {

  long reading = 0;
  
  analogRead(axisPin);
  
  delay(1);



  for (int i = 0; i < sampleSize; i++) {
  
    reading += analogRead(axisPin);
  }
  



  return reading / sampleSize;
}








