# gsm-gps-tracker-using-Arduino-
#To avoid rail accidents using latest communication technologies, GSM communication protocols are used to convey the message of crack detection via sms and positioning via GPS
#include <LiquidCrystal.h>
#include <string.h>
#include <ctype.h>
 
LiquidCrystal lcd(7, 8, 9, 10, 11, 12);

#include <SoftwareSerial.h>
#include <string.h> 
#include <TinyGPS.h>
SoftwareSerial Sim900Serial(2, 3);
byte buffer[64];                             // buffer array for data recieve over serial port
int count=0;                                 // counter for buffer array
SoftwareSerial GPS(4, 5);
TinyGPS gps;
unsigned long fix_age;
long lat, lon;
float LAT, LON;
void gpsdump(TinyGPS &gps);
bool feedgps();
void getGPS();
void setup()
{

   pinMode(A4, OUTPUT);
   pinMode(A5, OUTPUT);
   pinMode(A2, OUTPUT);
   pinMode(A3, OUTPUT);
   pinMode(6, OUTPUT);

    pinMode(A0, INPUT);
    pinMode(A1, INPUT);
    
  Sim900Serial.begin(9600);     // the SIM900 baud rate
  GPS.begin(4800);                   // GPS module baud rate  
  Serial.begin(9600);                  // the Serial port of Arduino baud rate.
  delay(500);
  Sim900_Inti();
  lcd.begin(16, 2);
lcd.setCursor(0,0); 
lcd.print("I CARE SOLUTIONS");
lcd.setCursor(0,1); 
lcd.print("EMBEDDED SYSTEM");
delay(2000);
 digitalWrite(A4, HIGH);
 digitalWrite(A5, LOW);
 digitalWrite(A2, HIGH);
 digitalWrite(A3, LOW);
 digitalWrite(6, LOW);
}
 
void loop()
{
int buttonState; 
digitalWrite(6, LOW);  
buttonState=digitalRead(A0);

 if(buttonState==HIGH)
 {
 lcd.clear();
  digitalWrite(6, HIGH); 
  digitalWrite(A4, LOW);
  digitalWrite(A5, LOW);
  digitalWrite(A2, LOW);
  digitalWrite(A3, LOW);  
lcd.setCursor(0,0); 
lcd.print("Railway track ck");
lcd.setCursor(0,1); 
lcd.print("Not connected");
delay(2000);
SendTextMessage();
 }
buttonState=digitalRead(A1);
 if(buttonState==HIGH)
 {
  lcd.clear();
  digitalWrite(6, HIGH); 
  digitalWrite(A4, LOW);
  digitalWrite(A5, LOW);
  digitalWrite(A2, LOW);
  digitalWrite(A3, LOW);  
lcd.setCursor(0,0); 
lcd.print("Railway track ck");
lcd.setCursor(0,1); 
lcd.print("Not connected");
delay(2000);
SendTextMessage();
 }
//  
 
}
void clearBufferArray()                             // function to clear buffer array
{
  for (int i=0; i<count;i )
    { buffer[i]=NULL;}                                // clear all index of array with command NULL
}
void Sim900_Inti(void)
{
  Sim900Serial.println("AT CMGF=1");    // Set GSM shield to sms mode
  Serial.println("AT CMGF=1");
  delay(500);
  Sim900Serial.println("AT CNMI=2,2");
  Serial.println("AT CMGF=1");
  delay(500);
}
void Cmd_Read_Act(void)                       //Function reads the SMS sent to SIM900 shield.
{ 
  char buffer2[64];
  for (int i=0; i<count;i )
  { buffer2[i]=char(buffer[i]);}  
    
  if (strstr(buffer2,"INSTRUCTABLES"))    //Comparing password entered with password stored in program  
  {
      Serial.println("Password Authenticated.");
      Serial.println("Sending reply SMS. ");
      SendTextMessage();            
  }
  
}
void SendTextMessage()
{
  
  
Sim900Serial.print("AT CMGF=1\r");    //Sending the SMS in text mode
delay(100);
Sim900Serial.println("AT CMGS = \"9666509692\"");//The predefined phone number
delay(100);
Sim900Serial.println("Please wait while Module calculates position");//the content of the message
delay(100);
Sim900Serial.println((char)26);//the ASCII code of the ctrl z is 26
delay(100);
Sim900Serial.println();
int counter=0;
GPS.listen();


for (;;)
{
   long lat, lon;
   unsigned long fix_age, time, date, speed, course;
   unsigned long chars;
   unsigned short sentences, failed_checksum;
   long Latitude, Longitude;
    
   // retrieves /- lat/long in 100000ths of a degree
   gps.get_position(&lat, &lon, &fix_age);
   getGPS();
   Serial.print("Latitude : ");
   Serial.print(LAT/1000000,7);
   Serial.print(" :: Longitude : ");
   Serial.println(LON/1000000,7);
//   if (LAT == 0 && LON == 0)
//  {
//    continue;    
//  } 
//  counter ;
//  if (counter<30)
//  {
//    continue;    
//  }
  
  Sim900Serial.print("AT CMGF=1\r");    //Sending the SMS in text mode
  delay(100);
  Sim900Serial.println("AT CMGS = \"9666509692\"");//The predefined phone number
  delay(100);
  Sim900Serial.print("Latitude : ");
  Sim900Serial.print(LAT/1000000,7);
  Sim900Serial.print(" :: Longitude : ");
  Sim900Serial.println(LON/1000000,7);//the content of the message
  delay(100);
  Sim900Serial.println((char)26);//the ASCII code of the ctrl z is 26
  delay(100);
  Sim900Serial.println();
  counter=0;
  break;
 }
 lcd.clear();
 lcd.setCursor(0,0); 
lcd.print("I CARE SOLUTIONS");
lcd.setCursor(0,1); 
lcd.print("EMBEDDED SYSTEM");      
}

void getGPS()
{
   bool newdata = false;
   unsigned long start = millis();
   while (millis() - start < 1000)
   {
      if (feedgps ())
      {
         newdata = true;
       }
     }
     if (newdata)
   {
      gpsdump(gps);
    }
}
bool feedgps()
{
   while (GPS.available())
 {
      if (gps.encode(GPS.read()))
        return true;
      }return 0;
}
void gpsdump(TinyGPS &gps)
{
   gps.get_position(&lat, &lon);
   LAT = lat;
   LON = lon;
   {
      feedgps(); 
    }
  }








