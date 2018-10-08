// Sample for GR-PEACH, GR-LYCHEE
//
// Required hardware: Camera, SD
//
// Public Domain
#include <Stepper.h>

const int stepsPerRevolution = 200;
Stepper myStepper(stepsPerRevolution, 2,3,4,5);
#include <Camera.h>
#include <SD.h>
#include <SPI.h>
#include <RTC.h>
#define PIN_SW 6
#define PIN_LED_BLUE 3
Camera camera(640, 480);
//Camera camera(640, 480, 0); // specify 0 for CVBS with GR-PEACH
//Camera camera(640, 480, 2); // specify 2 for Wireless Camera shield
RTC rtc;

void dateTime(uint16_t* date, uint16_t* time) {
  int year, mon, day, hour, min, sec, week;
  rtc.getDateTime(year, mon, day, hour, min, sec, week);
  *date = FAT_DATE(year, mon, day);
  *time = FAT_TIME(hour, min, sec);
}
void setup() {

  Serial.begin(9600);
  myStepper.setSpeed(60);
  Serial.println("start");
  pinMode(PIN_SW, INPUT);
  pinMode(PIN_LED_GREEN, OUTPUT);
  pinMode(PIN_LED_BLUE, OUTPUT);
  pinMode(PIN_LED_RED, OUTPUT);
  pinMode(2, OUTPUT);
  pinMode(3, OUTPUT);
  pinMode(4, OUTPUT);
  pinMode(5, OUTPUT);

  camera.begin();
  rtc.begin();

  rtc.setDateTime(2017, 6, 17, 14, 1, 0);
  SdFile::dateTimeCallback(&dateTime);

  if (!SD.begin()) {
    Serial.println("Card failed, or not present.");
    digitalWrite(PIN_LED_RED, HIGH);
    while (1)
      ;
  } 
  else {
    Serial.println("Card founded.");
  }
  Serial.println("Click button to take a picture.");
  digitalWrite(PIN_LED_GREEN, HIGH);

}

void loop() {
  static int count = 0;
  if (digitalRead(PIN_SW) == 0) {
    myStepper.step(stepsPerRevolution);
    delay(10);
    char filename[13];
    sprintf(filename, "image%d.jpg", count);
    File file = SD.open(filename, FILE_WRITE);
    if (file) {
      digitalWrite(PIN_LED_BLUE, HIGH);
      size_t size = camera.createJpeg();
      uint8_t* adr = camera.getJpegAdr();
      for (size_t i = 0; i < size; i++) {
        file.write(*adr);
        adr++;
      }
      file.close();
      Serial.print("Saved a picture as ");
      Serial.println(filename);
      digitalWrite(PIN_LED_BLUE, LOW);
      count++;
    } 
    else {
      Serial.println("Failed to open file.");
      digitalWrite(PIN_LED_RED, HIGH);
      while (1)
        ;
    }
  }

}

