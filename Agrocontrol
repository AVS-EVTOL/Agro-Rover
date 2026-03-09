#include <Servo.h>
#include <DHT.h>

#define DHTPIN 2
#define DHTTYPE DHT11

DHT dht(DHTPIN, DHTTYPE);

// Pins
#define MOISTURE_PIN A0
#define PH_PIN A1

#define WATER_PUMP 7
#define FERTILIZER_PUMP 8

#define SENSOR_SERVO_PIN 9
#define PLANT_SERVO_PIN 10

Servo sensorServo;
Servo plantServo;

// Crop database structure
struct Crop {
  String name;
  int idealMoisture;
  float idealPH;
};

Crop crops[] = {
  {"Wheat", 500, 6.5},
  {"Rice", 650, 6.0},
  {"Corn", 550, 6.8}
};

int selectedCrop = 0;

void setup() {

  Serial.begin(9600);
  dht.begin();

  pinMode(WATER_PUMP, OUTPUT);
  pinMode(FERTILIZER_PUMP, OUTPUT);

  sensorServo.attach(SENSOR_SERVO_PIN);
  plantServo.attach(PLANT_SERVO_PIN);

  Serial.println("AgriRover Started");
}

void loop() {

  Crop current = crops[selectedCrop];

  Serial.print("Selected Crop: ");
  Serial.println(current.name);

  deploySensors();

  int moisture = analogRead(MOISTURE_PIN);
  float phValue = readPH();

  float temp = dht.readTemperature();
  float humidity = dht.readHumidity();

  Serial.print("Moisture: ");
  Serial.println(moisture);

  Serial.print("pH: ");
  Serial.println(phValue);

  adjustSoil(moisture, phValue, current);

  plantSeed();

  delay(10000);
}

void deploySensors() {

  Serial.println("Deploying sensors...");
  sensorServo.write(90);
  delay(2000);
}

float readPH() {

  int raw = analogRead(PH_PIN);
  float voltage = raw * (5.0 / 1023.0);
  float ph = 7 + ((2.5 - voltage) / 0.18);

  return ph;
}

void adjustSoil(int moisture, float ph, Crop crop) {

  if (moisture < crop.idealMoisture) {
    Serial.println("Low moisture - watering...");
    digitalWrite(WATER_PUMP, HIGH);
    delay(3000);
    digitalWrite(WATER_PUMP, LOW);
  }

  if (ph < crop.idealPH - 0.3) {
    Serial.println("Soil too acidic - adding fertilizer...");
    digitalWrite(FERTILIZER_PUMP, HIGH);
    delay(2000);
    digitalWrite(FERTILIZER_PUMP, LOW);
  }
}

void plantSeed() {

  Serial.println("Planting seed...");

  plantServo.write(90);
  delay(2000);

  plantServo.write(0);
}
