
const int trigPin = 9;
const int echoPin = 10;
const int buttonPin = 2;

const int redLED = 13;
const int yellowLED = 12;
const int greenLED = 11;

const int IN1 = 5;
const int IN2 = 6;
const int ENA = 3;

void stopMotor() {
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, LOW);
  analogWrite(ENA, 0);
}

void forwardMotor(int speed) {
  digitalWrite(IN1, HIGH);
  digitalWrite(IN2, LOW);
  analogWrite(ENA, speed);
}

void ledsOff() {
  digitalWrite(redLED, LOW);
  digitalWrite(yellowLED, LOW);
  digitalWrite(greenLED, LOW);
}

void setup() {
  Serial.begin(9600);

  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
  pinMode(buttonPin, INPUT_PULLUP);

  pinMode(redLED, OUTPUT);
  pinMode(yellowLED, OUTPUT);
  pinMode(greenLED, OUTPUT);

  pinMode(IN1, OUTPUT);
  pinMode(IN2, OUTPUT);
  pinMode(ENA, OUTPUT);

  stopMotor();
  ledsOff();
}

void loop() {
  // Кнопка не нажата
  if (digitalRead(buttonPin) == HIGH) {
    stopMotor();
    ledsOff();
    delay(100);
    return;
  }

  // Измерение расстояния
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  unsigned long duration = pulseIn(echoPin, HIGH, 30000);

  // Нет ответа от датчика
  if (duration == 0) {
    stopMotor();
    ledsOff();
    Serial.println("Sensor timeout");
    delay(200);
    return;
  }

  float distance = duration * 0.0343 / 2.0;

  Serial.print("Distance: ");
  Serial.print(distance);
  Serial.println(" cm");

  ledsOff();

  if (distance < 20) {
    digitalWrite(redLED, HIGH);
    stopMotor();
    Serial.println("STOP, PWM = 0");
  }
  else if (distance <= 50) {
    digitalWrite(yellowLED, HIGH);
    forwardMotor(120);
    Serial.println("FORWARD, PWM = 120");
  }
  else {
    digitalWrite(greenLED, HIGH);
    forwardMotor(255);
    Serial.println("FORWARD, PWM = 255");
  }

  delay(200);
}
